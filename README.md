<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>BenQ Mac Monitor Finder</title>
  <!-- Tailwind CSS CDN 用於現代、質感的 UI 設計 -->
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-50 text-gray-800 font-sans min-h-screen flex flex-col justify-between">

  <!-- 頂部導覽列 / 語系切換 -->
  <header class="bg-white shadow-sm py-4 px-6 flex justify-between items-center">
    <div class="flex items-center space-x-3">
      <!-- 這裡可自行替換為官方 Logo -->
      <span class="text-xl font-extrabold text-purple-800 tracking-wider">BenQ</span>
      <span class="text-gray-400">|</span>
      <span class="text-gray-600 text-sm font-semibold">Monitor Finder</span>
    </div>
    <!-- 語系切換選單 -->
    <div>
      <select id="langSelect" onchange="switchLanguage(this.value)" class="border border-gray-300 rounded px-3 py-1 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-purple-500">
        <option value="en">English</option>
        <option value="zh">繁體中文</option>
      </select>
    </div>
  </header>

  <!-- 主體推薦工具容器 -->
  <main class="max-w-3xl w-full mx-auto my-12 px-6 flex-grow">
    <div class="bg-white rounded-2xl shadow-xl p-8 transition-all duration-300">
      
      <!-- 進度條 -->
      <div class="w-full bg-gray-200 h-2 rounded-full mb-8">
        <div id="progressBar" class="bg-purple-600 h-2 rounded-full transition-all duration-500" style="width: 25%"></div>
      </div>

      <!-- 問答內容顯示區 -->
      <div id="quiz-container">
        <h2 id="questionTitle" class="text-2xl font-bold mb-6 text-gray-900">載入中...</h2>
        <div id="optionsContainer" class="space-y-3">
          <!-- 選項會透過 JavaScript 動態生成 -->
        </div>
      </div>

      <!-- 結果顯示區 (預設隱藏) -->
      <div id="result-container" class="hidden text-center py-6">
        <div class="inline-flex p-4 bg-purple-100 rounded-full text-purple-600 mb-4">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
        </div>
        <h2 class="text-3xl font-extrabold text-gray-900 mb-2" id="resultTitle">我們為您推薦的螢幕</h2>
        <p class="text-gray-500 mb-8" id="resultSubtitle">根據您的日常 Mac 運作情境，這兩款型號能提供您最頂級的體驗：</p>
        
        <!-- 推薦卡片 -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6 max-w-2xl mx-auto">
          <!-- 主要推薦 -->
          <div class="bg-gradient-to-br from-purple-50 to-indigo-50 border-2 border-purple-500 rounded-xl p-6 shadow-md relative">
            <span class="absolute top-0 right-4 transform -translate-y-1/2 bg-purple-600 text-white text-xs px-3 py-1 rounded-full font-bold uppercase" id="badgePrimary">Best Match</span>
            <h3 class="text-gray-500 text-sm font-semibold uppercase tracking-wider mb-1" id="labelPrimary">主要首選</h3>
            <div class="text-3xl font-black text-purple-900 my-3" id="primaryModel">-</div>
            <p class="text-xs text-gray-500" id="descPrimary">搭載專為 Mac 打造的頂級色彩技術與極窄邊框。</p>
          </div>

          <!-- 次要推薦 -->
          <div class="bg-white border border-gray-200 rounded-xl p-6 shadow-sm">
            <h3 class="text-gray-500 text-sm font-semibold uppercase tracking-wider mb-1" id="labelSecondary">您可能也會喜歡</h3>
            <div class="text-3xl font-black text-gray-800 my-3" id="secondaryModel">-</div>
            <p class="text-xs text-gray-500" id="descSecondary">同樣出色，適合做為第二螢幕或更大尺寸的延伸選擇。</p>
          </div>
        </div>

        <!-- 重來按鈕 -->
        <button onclick="resetQuiz()" class="mt-10 px-8 py-3 bg-gray-900 hover:bg-gray-800 text-white font-bold rounded-lg transition duration-200 shadow" id="btnReset">
          重新挑選
        </button>
      </div>

      <!-- 底部返回按鈕 -->
      <div class="mt-8 pt-6 border-t border-gray-100 flex justify-between items-center" id="navContainer">
        <button id="btnBack" onclick="goBack()" class="text-gray-500 hover:text-gray-900 font-semibold flex items-center space-x-1 transition">
          <span>←</span> <span id="labelBack">返回上一步</span>
        </button>
        <span class="text-sm text-gray-400 font-medium" id="stepCounter">Step 1 of 3</span>
      </div>

    </div>
  </main>

  <!-- 頁尾 -->
  <footer class="bg-gray-100 py-6 text-center text-xs text-gray-400 border-t border-gray-200">
    <p>© 2026 BenQ. All rights reserved. Designed for Mac users.</p>
  </footer>

  <!-- 核心資料與控制邏輯 (JS) -->
  <script>
    // 1. 語系字典包 (日後新增語系，直接在此對應翻譯字典)
    const translations = {
      en: {
        back: "Back",
        stepOf: "Step {current} of {total}",
        primaryLabel: "Best Match",
        secondaryLabel: "You may also like",
        reset: "Restart",
        resultTitle: "Your Perfect Mac Display",
        resultSubtitle: "Based on your preferences, here is the curated selection for your Mac setup:",
        questions: {
          q2: "What do you primarily use your Mac for?",
          q3: "What defines your ideal color experience?",
          q4: "Which display feature matters most to you?",
          q5: "Select your color precision requirement:"
        }
      },
      zh: {
        back: "返回上一步",
        stepOf: "第 {current} 步 (共 {total} 步)",
        primaryLabel: "主要首選",
        secondaryLabel: "您可能也會喜歡",
        reset: "重新挑選",
        resultTitle: "最適合您的 Mac 螢幕",
        resultSubtitle: "根據您的偏好，我們為您的 Mac 工作空間精選了以下型號：",
        questions: {
          q2: "您的 Mac 主要用途是什麼？",
          q3: "哪種色彩體驗最符合您的理想需求？",
          q4: "您最在意的螢幕硬體功能是？",
          q5: "請選擇您對色彩精準度的要求："
        }
      }
    };

    // 2. 螢幕推薦決策資料庫 (由 WED-VPD 篩選規則中萃取)
    // 結構設計上，日後若有更新，只需比對此核心結構進行調整即可，完全不影響程式渲染！
    const quizData = {
      // Q2 層
      "Everyday productivity and browsing": {
        // Q3 層
        "Seamless Mac Experience": {
          qType: "q4",
          options: {
            "High Resolution for detail display": { primary: "ma270s", secondary: "ma270u" },
            "Vibrant Colors and Sharp Contrast": { primary: "ma270up", secondary: "ma320up" },
            "High Refresh Rate for Smooth Motion ＆ Gaming": { primary: "ma320ug", secondary: "rd280ug" },
            "Versatile Multi-Tasking": { primary: "ma270u", secondary: "ma320u" }
          }
        },
        "Professional Color (Adobe RGB) for Photography ＆ Printing ｜ Mac Color Ready": {
          qType: "q5",
          options: {
            "Out-of-the-Box Precision (Factory-Calibrated Color)": { primary: "pd2732u", secondary: "N/A" },
            "Absolute Accuracy (Hardware Calibration for long-term precision)": { primary: "pd2770u", secondary: "sw272u" }
          }
        },
        "Professional Color (DCI-P3／Rec﹒709) for Video ＆ Post-Production ｜ Mac Color Ready": {
          qType: "q5",
          options: {
            "Out-of-the-Box Precision (Factory-Calibrated Color)": { primary: "pv3200u", secondary: "pd2706u" },
            "Absolute Accuracy (Hardware Calibration for long-term precision)": { primary: "pv2750u", secondary: "pv3250u" }
          }
        },
        "Professional Color (sRGB) for Web ＆ Digital Content ｜ Mac Color Ready": {
          qType: "q5",
          options: {
            "Out-of-the-Box Precision (Factory-Calibrated Color)": { primary: "pd2706u", secondary: "pd2706qn" },
            "Absolute Accuracy (Hardware Calibration for long-term precision)": { primary: "pd2770u", secondary: "sw272u" }
          }
        }
      },
      "Graphic design, UI／UX design": {
        "Seamless Mac Experience": {
          qType: "q4",
          options: {
            "High Resolution for detail display": { primary: "ma270s", secondary: "pd2730s" },
            "Vibrant Colors and Sharp Contrast": { primary: "ma270s", secondary: "ma320up" },
            "High Refresh Rate for Smooth Motion ＆ Gaming": { primary: "ma320ug", secondary: "N/A" },
            "Versatile Multi-Tasking": { primary: "ma320u", secondary: "ma270u" }
          }
        },
        "Professional Color (sRGB) for Web ＆ Digital Content ｜ Mac Color Ready": {
          qType: "q5",
          options: {
            "Out-of-the-Box Precision (Factory-Calibrated Color)": { primary: "pd2706u", secondary: "pd2730s" },
            "Absolute Accuracy (Hardware Calibration for long-term precision)": { primary: "pd2770u", secondary: "sw272u" }
          }
        }
      },
      "Software development ＆ coding": {
        "Seamless Mac Experience": {
          qType: "q4",
          options: {
            "High Resolution for detail display": { primary: "ma270s", secondary: "rd280ug" },
            "Vibrant Colors and Sharp Contrast": { primary: "ma270s", secondary: "ma320ug" },
            "High Refresh Rate for Smooth Motion ＆ Gaming": { primary: "rd280ug", secondary: "ma320ug" },
            "Versatile Multi-Tasking": { primary: "rd280ug", secondary: "rd320u" }
          }
        },
        "Professional Color (sRGB) for Web ＆ Digital Content ｜ Mac Color Ready": {
          qType: "q5",
          options: {
            "Out-of-the-Box Precision (Factory-Calibrated Color)": { primary: "pd2706u", secondary: "pd2706qn" },
            "Absolute Accuracy (Hardware Calibration for long-term precision)": { primary: "pd2770u", secondary: "sw272u" }
          }
        },
        "Crisp Text Clarity and Comfort for Long Coding Sessions": {
          qType: "q4",
          options: {
            "High Resolution for detail display": { primary: "rd280ug", secondary: "ma270s" },
            "Vibrant Colors and Sharp Contrast": { primary: "ma270s", secondary: "ma320ug" },
            "High Refresh Rate for Smooth Motion ＆ Gaming": { primary: "rd280ug", secondary: "ma320ug" },
            "Versatile Multi-Tasking": { primary: "rd280ug", secondary: "rd320u" }
          }
        }
      }
      // 注：其餘對應路徑可在日後由編輯者複製以上結構繼續新增擴充。
    };

    // 3. 狀態管理器 (State)
    let currentLang = "en";
    let step = 1; // 1: Q2, 2: Q3, 3: Q4/Q5
    let history = []; // 用於返回上一步
    let selections = {
      q2: null,
      q3: null,
      q4_5: null
    };

    // 4. 初始化
    window.onload = function() {
      renderStep();
    };

    function switchLanguage(lang) {
      currentLang = lang;
      renderStep();
    }

    // 5. 畫面渲染控制
    function renderStep() {
      const trans = translations[currentLang];
      const qContainer = document.getElementById("quiz-container");
      const rContainer = document.getElementById("result-container");
      const navContainer = document.getElementById("navContainer");
      const btnBack = document.getElementById("btnBack");
      const stepCounter = document.getElementById("stepCounter");
      const progressBar = document.getElementById("progressBar");

      // 返回按鈕顯示控制
      if (step === 1) {
        btnBack.classList.add("invisible");
      } else {
        btnBack.classList.remove("invisible");
      }
      document.getElementById("labelBack").innerText = trans.back;

      if (step <= 3) {
        // 顯示問答，隱藏結果
        qContainer.classList.remove("hidden");
        rContainer.classList.add("hidden");
        navContainer.classList.remove("hidden");

        let totalSteps = 3;
        stepCounter.innerText = trans.stepOf.replace("{current}", step).replace("{total}", totalSteps);
        progressBar.style.width = `${(step / totalSteps) * 100}%`;

        if (step === 1) {
          document.getElementById("questionTitle").innerText = trans.questions.q2;
          renderOptions(Object.keys(quizData), (val) => {
            selections.q2 = val;
            history.push({ step: 1, selections: { ...selections } });
            step = 2;
            renderStep();
          });
        } 
        else if (step === 2) {
          document.getElementById("questionTitle").innerText = trans.questions.q3;
          const options = Object.keys(quizData[selections.q2] || {});
          renderOptions(options, (val) => {
            selections.q3 = val;
            history.push({ step: 2, selections: { ...selections } });
            step = 3;
            renderStep();
          });
        } 
        else if (step === 3) {
          const nextConfig = quizData[selections.q2][selections.q3];
          document.getElementById("questionTitle").innerText = trans.questions[nextConfig.qType];
          const options = Object.keys(nextConfig.options);
          renderOptions(options, (val) => {
            selections.q4_5 = val;
            showResult();
          });
        }
      }
    }

    // 渲染選項按鈕
    function renderOptions(options, onClickCallback) {
      const container = document.getElementById("optionsContainer");
      container.innerHTML = "";
      options.forEach(opt => {
        const btn = document.createElement("button");
        btn.className = "w-full text-left p-4 rounded-xl border border-gray-200 hover:border-purple-500 hover:bg-purple-50 hover:shadow-sm active:scale-[0.98] transition-all duration-150 font-medium text-gray-700 flex justify-between items-center";
        btn.innerHTML = `
          <span>${opt}</span>
          <span class="text-purple-400 group-hover:text-purple-600">→</span>
        `;
        btn.onclick = () => onClickCallback(opt);
        container.appendChild(btn);
      });
    }

    // 6. 返回與重置
    function goBack() {
      if (history.length > 0) {
        const lastState = history.pop();
        step = lastState.step;
        selections = lastState.selections;
        renderStep();
      }
    }

    function resetQuiz() {
      step = 1;
      history = [];
      selections = { q2: null, q3: null, q4_5: null };
      renderStep();
    }

    // 7. 顯示結果
    function showResult() {
      const trans = translations[currentLang];
      const qContainer = document.getElementById("quiz-container");
      const rContainer = document.getElementById("result-container");
      const navContainer = document.getElementById("navContainer");
      const progressBar = document.getElementById("progressBar");

      qContainer.classList.add("hidden");
      navContainer.classList.add("hidden");
      rContainer.classList.remove("hidden");
      progressBar.style.width = "100%";

      // 從篩選樹中提取型號
      const leaf = quizData[selections.q2][selections.q3].options[selections.q4_5];
      
      document.getElementById("resultTitle").innerText = trans.resultTitle;
      document.getElementById("resultSubtitle").innerText = trans.resultSubtitle;
      document.getElementById("badgePrimary").innerText = trans.primaryLabel;
      document.getElementById("labelPrimary").innerText = trans.primaryLabel;
      document.getElementById("labelSecondary").innerText = trans.secondaryLabel;
      document.getElementById("btnReset").innerText = trans.reset;

      document.getElementById("primaryModel").innerText = leaf.primary.toUpperCase();
      document.getElementById("secondaryModel").innerText = (leaf.secondary && leaf.secondary !== "N/A") ? leaf.secondary.toUpperCase() : "N/A";
    }
  </script>
</body>
</html>
