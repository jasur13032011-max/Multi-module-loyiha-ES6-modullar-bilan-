# Multi-module-loyiha-ES6-modullar-bilan-
project/
├── index.html
├── src/
│   ├── math.js
│   ├── format.js
│   ├── history.js
│   ├── ui.js
│   ├── index.js (Barrel file)
│   └── app.js
// Hech qanday tashqi holatni o'zgartirmaydigan sof (pure) funksiyalar
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
export const multiply = (a, b) => a * b;

// Named export
export const formatCurrency = (amount) => {
  return amount.toLocaleString('uz-UZ', { style: 'currency', currency: 'UZS' });
};

// Default export
const defaultFormatter = (num) => num.toLocaleString('uz-UZ');
export default defaultFormatter;
// Amallar tarixini boshqaruvchi klass
class HistoryManager {
  #logs = []; // Private state

  addLog(operation, result) {
    const timestamp = new Date().toLocaleTimeString();
    this.#logs = [...this.#logs, { timestamp, operation, result }];
  }

  getLogs() {
    return [...this.#logs]; // Immutable nusxa qaytarish
  }

  clear() {
    this.#logs = [];
  }
}

export default HistoryManager;
export { add, subtract, multiply } from './math.js';
export { default as formatNumber, formatCurrency } from './format.js';
export { default as HistoryManager } from './history.js';
export const updateDOM = (elementId, value) => {
  const element = document.getElementById(elementId);
  if (element) element.textContent = value;
};

export const renderHistory = (elementId, logs = []) => {
  const element = document.getElementById(elementId);
  if (!element) return;
  
  element.innerHTML = logs
    .map(log => `<li>[${log.timestamp}] ${log.operation} = ${log.result}</li>`)
    .join('');
};
import { add, subtract, formatCurrency, HistoryManager, formatNumber } from './index.js';
import { updateDOM, renderHistory } from './ui.js';

const history = new HistoryManager();
let currentTotal = 0;

// Oddiy amallar translyatsiyasi
const performCalculation = (a, b) => {
  currentTotal = add(a, b);
  history.addLog(`${a} + ${b}`, formatNumber(currentTotal));
  
  updateDOM("result", formatCurrency(currentTotal));
  renderHistory("historyList", history.getLogs());
};

// ⚡ Dynamic Import (Lazy Load) — Og'ir statistik modulni faqat tugma bosilganda yuklash
const loadHeavyAnalytics = async () => {
  try {
    // Agar foydalanuvchiga statistika kerak bo'lsagina tarmoqdan yuklanadi
    const analyticsModule = await import('https://cdn.jsdelivr.net/npm/lodash-es@4.17.21/lodash.min.js');
    console.log("Og'ir kutubxona muvaffaqiyatli yuklandi!");
    
    // Yuklangan kutubxonadan foydalanish (masalan: _.camelCase)
    alert(analyticsModule.camelCase("ogir_statistika_moduli"));
  } catch (error) {
    console.error("Modulni yuklashda xatolik:", error);
  }
};

// Hodisalarni bog'lash (Eksport qilinishi shart emas, app.js kirish nuqtasi)
document.getElementById("calcBtn")?.addEventListener("click", () => performCalculation(150000, 250000));
document.getElementById("heavyBtn")?.addEventListener("click", loadHeavyAnalytics);
<!DOCTYPE html>
<html lang="uz">
<head>
  <meta charset="UTF-8">
  <title>ES Modules Arxitekturasi</title>
</head>
<body>

  <div>
    <h3>Natija: <span id="result">0.00 UZS</span></h3>
    <button id="calcBtn">Hisoblashni Boshlash</button>
    <button id="heavyBtn">Og'ir Grafikni Yuklash (Lazy Load)</button>
  </div>

  <h4>Amallar Tarixi:</h4>
  <ul id="historyList"></ul>

  <script type="module" src="./src/app.js"></script>
</body>
</html>
