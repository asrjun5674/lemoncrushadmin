<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lemon Crush Admin — Local Records + Firebase Suggestions</title>
<style>
:root{--pink:#ff1493;--purple:#520052;--deep:#20002f;--yellow:#ffe34d;--glass:#ffffff18;--green:#c6ffcc;--red:#ffb6c1}*{box-sizing:border-box}body{font-family:Arial,Helvetica,sans-serif;background:linear-gradient(145deg,var(--pink),#7a007a 48%,var(--deep));color:white;padding:18px;margin:0;min-height:100vh}h1{text-align:center;font-size:clamp(30px,5vw,54px);color:var(--yellow);margin:12px 0}h2{color:var(--yellow);margin-top:0}h3{margin-bottom:8px}.subtitle{text-align:center;opacity:.9;margin-top:0}.notice{max-width:1100px;margin:14px auto;background:#1115;border:1px solid #ffffff35;padding:12px 14px;border-radius:12px;line-height:1.5;font-size:13px}.box{max-width:1100px;margin:18px auto;background:var(--glass);padding:20px;border-radius:20px;border:1px solid #ffffff24;box-shadow:0 10px 30px #0002}.card{background:#ffffff16;padding:14px;border-radius:14px;margin-top:12px;overflow-wrap:anywhere}.grid{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px}.billingInput{display:block;width:100%;padding:12px;margin-top:10px;border:0;border-radius:10px;font-size:16px;background:#fff;color:#222}.addBtn{background:var(--yellow);color:#620052;width:100%;padding:13px;margin-top:12px;border:0;border-radius:12px;font-size:16px;font-weight:bold;cursor:pointer}.addBtn:hover{filter:brightness(.96)}.secondaryBtn,.deleteBtn{border:0;padding:9px 12px;border-radius:9px;margin:8px 6px 0 0;cursor:pointer;font-weight:bold}.secondaryBtn{background:#fff;color:#620052}.deleteBtn{background:#d90035;color:white}.customerCard{cursor:pointer;border:2px solid transparent}.customerCard.selected{border-color:var(--yellow);background:#ffff0025}.pill{display:inline-block;background:var(--yellow);color:#500050;border-radius:999px;padding:5px 9px;font-size:12px;font-weight:bold;margin-top:6px}.small{font-size:12px;opacity:.9;line-height:1.5}.result{margin-top:12px;padding:13px;background:#0003;border-radius:11px;line-height:1.6;overflow-wrap:anywhere}.success{color:var(--green)}.error{color:var(--red)}.total{font-size:22px;color:var(--yellow);margin-top:16px;text-align:center}table{width:100%;margin-top:14px;border-collapse:collapse;background:white;color:#222}th,td{border:1px solid #ddd;padding:8px;text-align:center;font-size:13px;color:#222}th{background:#ffe066}button:focus,input:focus{outline:2px solid #ffec80;outline-offset:2px}.hidden{display:none!important}.toolbar{display:flex;flex-wrap:wrap;gap:8px;justify-content:center}.toolbar button{width:auto;margin:4px 0}.code-row{display:flex;justify-content:space-between;align-items:center;gap:10px;flex-wrap:wrap}.code-name{font-weight:bold;overflow-wrap:anywhere}.stat{background:#0002;border-radius:10px;padding:10px;min-width:130px;flex:1}.stats{display:flex;gap:10px;flex-wrap:wrap;margin-top:12px}.login-wrap{max-width:420px;margin:9vh auto}.login-wrap .box{margin:0}.divider{border:0;border-top:1px solid #ffffff35;margin:18px 0}.footer{text-align:center;opacity:.75;font-size:12px;margin:22px 0 8px}@media(max-width:680px){body{padding:9px}.box{padding:14px}.grid{grid-template-columns:1fr}th,td{padding:5px;font-size:11px}.total{font-size:19px}}
</style>
</head>
<body>
<section id="loginScreen" class="login-wrap">
  <div class="box">
    <h1>🍋 Lemon Crush Admin</h1>
    <p class="subtitle">Please sign in to continue</p>
    <form id="loginForm">
      <input id="loginUsername" class="billingInput" type="text" placeholder="Admin username" autocomplete="username" required maxlength="80">
      <input id="loginPassword" class="billingInput" type="password" placeholder="Admin password" autocomplete="current-password" required maxlength="200">
      <button class="addBtn" type="submit">Login</button>
    </form>
    <div id="loginError" class="result error hidden" role="alert">Incorrect username or password.</div>
    <p class="small">This is a local browser login, not Firebase Authentication. Change the username and password in the HTML source before publishing.</p>
  </div>
</section>
<main id="app" class="hidden">
  <h1>🍋 Lemon Crush Admin</h1>
  <p class="subtitle">Customer records · Visits · Discounts · Billing · Suggestions from Firebase</p>
  <div class="notice"><strong>Hybrid mode:</strong> customer names, phone numbers, visits, bills and discount-code usage are stored locally in this browser on this device. Customer suggestions remain connected to your existing Lemon Crush Firebase database at <code>/suggestions</code>, so suggestions submitted on the public Lemon Crush website continue to appear here. This page does not clear or migrate your Firebase data. Clearing browser data may erase local records; use Export Backup regularly. The local login is only a front-end screen lock and does not secure Firebase database rules.</div>
  <div class="toolbar">
    <button class="secondaryBtn" id="logoutBtn" type="button">Log out</button>
    <button class="secondaryBtn" id="exportBtn" type="button">Export Backup (JSON)</button>
    <button class="secondaryBtn" id="importBtn" type="button">Import Backup (JSON)</button>
    <input id="importFile" type="file" accept="application/json,.json" class="hidden">
  </div>

  <section class="box">
    <h2>👤 Customers & Visit Counter</h2>
    <p>Enter a customer's name and phone number. Save/find the customer, then select their card.</p>
    <div class="grid">
      <input id="customerName" class="billingInput" placeholder="Customer name" maxlength="80" autocomplete="name">
      <input id="customerPhone" class="billingInput" type="tel" placeholder="Phone number" maxlength="20" autocomplete="tel">
    </div>
    <button class="addBtn" id="saveCustomerBtn" type="button">Save / Find Customer</button>
    <div id="selectedCustomer" class="result">No customer selected.</div>
    <h3>Saved Customers</h3>
    <input id="customerSearch" class="billingInput" placeholder="Search name or phone">
    <div id="customerList"></div>
    <p class="small">Visits increase only when a bill is completed and saved. Customer matching uses digits from the phone number.</p>
  </section>

  <section class="box">
    <h2>🧾 Bill for Selected Customer</h2>
    <p id="billCustomerHint">Select a customer above first.</p>
    <div class="grid">
      <input id="item" class="billingInput" placeholder="Item name" maxlength="100">
      <input id="price" class="billingInput" type="number" min="0" step="0.01" placeholder="Selling price (₹)">
      <input id="cost" class="billingInput" type="number" min="0" step="0.01" placeholder="Cost price (₹)">
    </div>
    <button class="addBtn" id="addItemBtn" type="button">Add Item to Current Bill</button>
    <div style="overflow-x:auto"><table><thead><tr><th>Item</th><th>Selling</th><th>Cost</th><th>Profit before discount</th><th>Remove</th></tr></thead><tbody id="billTable"></tbody></table></div>
    <div id="billSummary" class="result">No items added yet.</div>
    <input id="billDiscountCode" class="billingInput" placeholder="Discount code (optional)" autocapitalize="characters">
    <button class="addBtn" id="completeBillBtn" type="button">Complete Bill & Save Visit</button>
    <div id="billResult" class="result" aria-live="polite">Completed bill details will appear here.</div>
    <div id="totalProfit" class="total">Session Profit: ₹0.00</div>
    <h3>Recent Saved Bills</h3><div id="recentOrders"></div>
  </section>

  <section class="box">
    <h2>🎟️ Discount Code Manager</h2>
    <div class="card"><div class="code-row"><span class="code-name">INFINITE99</span><span>99% OFF · available after 11 completed visits</span></div><p class="small">Automatically applied to the bill once the customer already has more than 10 completed visits. The visit being billed does not count until the bill is saved.</p><div id="stat-INFINITE99" class="small"></div></div>
    <div class="card"><div class="code-row"><span class="code-name">HAVECRUSHONLC</span><span>50% OFF</span></div><div id="stat-HAVECRUSHONLC" class="small"></div></div>
    <div class="card"><div class="code-row"><span class="code-name">RUSHONCRUSH30</span><span>30% OFF</span></div><div id="stat-RUSHONCRUSH30" class="small"></div></div>
    <div class="card"><div class="code-row"><span class="code-name">LEMONCRUSH10</span><span>10% OFF</span></div><div id="stat-LEMONCRUSH10" class="small"></div></div>
    <p class="small">Use counts increase only when a bill is successfully saved with that code. Codes can be reused; this app tracks usage but does not impose a one-time-per-customer limit.</p>
    <hr class="divider">
    <h3>Calculate a Discount</h3>
    <input id="discountCode" class="billingInput" placeholder="Enter discount code" autocapitalize="characters">
    <input id="discountPrice" class="billingInput" type="number" min="0" step="0.01" placeholder="Original price (₹)">
    <button class="addBtn" id="calculateDiscountBtn" type="button">Calculate Discount 💖</button>
    <div id="discountResult" class="result" aria-live="polite">Your discount calculation will appear here.</div>
  </section>

  <section class="box">
    <h2>💌 Customer Suggestions</h2>
    <p class="small">Connected to the existing Firebase <code>/suggestions</code> path without Firebase Authentication. Suggestions submitted through your public Lemon Crush website appear here. Adding or deleting a suggestion here affects the shared Firebase suggestions list only; customer records, visits, bills and discount usage remain local to this browser.</p>
    <div class="grid"><input id="suggestionName" class="billingInput" placeholder="Customer name (optional)" maxlength="80"><input id="suggestionMessage" class="billingInput" placeholder="Suggestion" maxlength="1000"></div>
    <button class="addBtn" id="addSuggestionBtn" type="button">Save Suggestion</button>
    <div id="suggestionStatus" class="result hidden" aria-live="polite"></div>
    <div id="suggestions"></div>
  </section>
  <p class="footer">Lemon Crush Admin · Local Login · Local Records + Firebase Suggestions</p>
</main>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getDatabase, ref, onValue, remove, push, set } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-database.js";

// Keep this same Firebase project and database path as the existing public Lemon Crush site.
// This admin page uses Firebase only for /suggestions; customers, bills and usage statistics stay local. No Firebase Authentication is used.
const firebaseConfig = {
  apiKey: "AIzaSyCONw7aVKww1VdumdYhrJXYA4n3duIAwD8",
  authDomain: "lemon-crush.firebaseapp.com",
  databaseURL: "https://lemon-crush-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "lemon-crush",
  storageBucket: "lemon-crush.firebasestorage.app",
  messagingSenderId: "149466674530",
  appId: "1:149466674530:web:f72cc273ef8f8e6472a83b",
  measurementId: "G-B3TGW4ZPC2"
};
const firebaseApp = initializeApp(firebaseConfig);
const db = getDatabase(firebaseApp);

'use strict';
// Local-only customer, visit, bill and discount records. Firebase is used only for /suggestions.
const STORAGE_KEY = 'lemonCrushLocalAdmin_v1';
const discountCodes = { HAVECRUSHONLC: 50, RUSHONCRUSH30: 30, LEMONCRUSH10: 10 };
const allCodes = [...Object.keys(discountCodes), 'INFINITE99'];
const $ = id => document.getElementById(id);
const phoneKey = phone => String(phone || '').replace(/\D/g, '');
const money = value => '₹' + Number(value || 0).toFixed(2);
const makeId = () => (globalThis.crypto && crypto.randomUUID) ? crypto.randomUUID() : `${Date.now()}_${Math.random().toString(36).slice(2)}`;
// Change these credentials before deploying to GitHub Pages. This is a UI lock only.
const ADMIN_USERNAME = 'admin';
const ADMIN_PASSWORD = '1234';
const LOGIN_SESSION_KEY = 'lemonCrushAdminLoggedIn_v1';
let adminStarted = false;
let data = loadData();
let selectedKey = '';
let items = [];
let sessionProfit = 0;
let firebaseSuggestions = {};
let suggestionsLoaded = false;
let suggestionsError = '';
let stopSuggestionsListener = null;

function blankData() {
  const codeStats = {};
  allCodes.forEach(code => codeStats[code] = { uses: 0, totalDiscount: 0 });
  return { customers: {}, orders: {}, codeStats, updatedAt: Date.now() };
}
function loadData() {
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    if (!raw) return blankData();
    const parsed = JSON.parse(raw);
    // Whitelist only local records. Firebase suggestions are deliberately excluded.
    const clean = blankData();
    clean.customers = parsed && typeof parsed.customers === 'object' && parsed.customers ? parsed.customers : {};
    clean.orders = parsed && typeof parsed.orders === 'object' && parsed.orders ? parsed.orders : {};
    clean.codeStats = parsed && typeof parsed.codeStats === 'object' && parsed.codeStats ? parsed.codeStats : {};
    clean.updatedAt = Number(parsed?.updatedAt) || Date.now();
    allCodes.forEach(code => clean.codeStats[code] = { uses: 0, totalDiscount: 0, ...(clean.codeStats[code] || {}) });
    return clean;
  } catch (error) {
    console.error('Could not read saved data:', error);
    alert('Saved browser data could not be read. If you have a backup JSON file, use Import Backup.');
    return blankData();
  }
}
function saveData() {
  try {
    data.updatedAt = Date.now();
    // Persist only customers, visits, saved bills and discount statistics to this browser.
    const localOnly = { customers: data.customers, orders: data.orders, codeStats: data.codeStats, updatedAt: data.updatedAt };
    localStorage.setItem(STORAGE_KEY, JSON.stringify(localOnly));
    return true;
  } catch (error) {
    console.error('Could not save local data:', error);
    alert('Could not save. Browser storage may be full or disabled. Export a backup and check browser storage settings.');
    return false;
  }
}
function showMessage(element, text, isError = false) {
  element.textContent = text;
  element.className = 'result ' + (isError ? 'error' : 'success');
}
function selected() { return selectedKey && data.customers[selectedKey] ? data.customers[selectedKey] : null; }
function safeDate(timestamp) { return timestamp ? new Date(Number(timestamp)).toLocaleString() : ''; }
function makeElement(tag, text, className) {
  const el = document.createElement(tag);
  if (text !== undefined) el.textContent = text;
  if (className) el.className = className;
  return el;
}
function updateSelected() {
  const customer = selected();
  if (!customer) {
    $('selectedCustomer').textContent = 'No customer selected.';
    $('selectedCustomer').className = 'result';
    $('billCustomerHint').textContent = 'Select a customer above first.';
    return;
  }
  const visits = Number(customer.visits || 0);
  $('selectedCustomer').textContent = `Selected: ${customer.name} | Phone: ${customer.phone} | Completed visits: ${visits} | ` + (visits > 10 ? '🏆 INFINITE99 unlocked (99% off eligible bills).' : `Visits until reward unlocks: ${11 - visits}`);
  $('selectedCustomer').className = 'result ' + (visits > 10 ? 'success' : '');
  $('billCustomerHint').textContent = `Billing for ${customer.name} (${customer.phone})`;
}
function renderCustomers() {
  const container = $('customerList');
  container.replaceChildren();
  const query = $('customerSearch').value.trim().toLowerCase();
  const entries = Object.entries(data.customers)
    .filter(([key, customer]) => `${customer.name || ''} ${customer.phone || key}`.toLowerCase().includes(query))
    .sort((a, b) => (a[1].name || '').localeCompare(b[1].name || ''));
  if (!entries.length) { container.textContent = 'No customers found.'; return; }
  entries.forEach(([key, customer]) => {
    const card = makeElement('div', undefined, 'card customerCard' + (key === selectedKey ? ' selected' : ''));
    const heading = makeElement('h3', customer.name || 'Unnamed customer');
    const details = makeElement('p', `Phone: ${customer.phone || key} · Completed visits: ${Number(customer.visits || 0)}`);
    card.append(heading, details);
    const uses = customer.discountCodeUses || {};
    const useTotal = Object.values(uses).reduce((sum, count) => sum + Number(count || 0), 0);
    card.appendChild(makeElement('p', `Discount code uses: ${useTotal}`, 'small'));
    if (Number(customer.visits || 0) > 10) card.appendChild(makeElement('span', 'INFINITE99 UNLOCKED · 99% OFF', 'pill'));
    card.addEventListener('click', () => {
      selectedKey = key;
      $('customerName').value = customer.name || '';
      $('customerPhone').value = customer.phone || key;
      renderCustomers(); updateSelected();
    });
    container.appendChild(card);
  });
}
function renderItems() {
  const body = $('billTable'); body.replaceChildren();
  let subtotal = 0, costs = 0;
  items.forEach((item, index) => {
    subtotal += item.price; costs += item.cost;
    const row = document.createElement('tr');
    [item.name, money(item.price), money(item.cost), money(item.price - item.cost)].forEach(value => row.appendChild(makeElement('td', value)));
    const cell = document.createElement('td');
    const button = makeElement('button', 'Remove', 'deleteBtn');
    button.type = 'button'; button.addEventListener('click', () => { items.splice(index, 1); renderItems(); });
    cell.appendChild(button); row.appendChild(cell); body.appendChild(row);
  });
  $('billSummary').textContent = items.length ? `Items: ${items.length} · Subtotal: ${money(subtotal)} · Cost: ${money(costs)} · Profit before discount: ${money(subtotal - costs)}` : 'No items added yet.';
}
function renderOrders() {
  const container = $('recentOrders'); container.replaceChildren();
  const latest = Object.entries(data.orders).sort((a, b) => Number(b[1].time || 0) - Number(a[1].time || 0)).slice(0, 20);
  if (!latest.length) { container.textContent = 'No saved bills yet.'; return; }
  latest.forEach(([key, order]) => {
    const card = makeElement('div', undefined, 'card');
    card.appendChild(makeElement('h3', `${order.customerName || 'Customer'} — ${money(order.finalTotal)}`));
    card.appendChild(makeElement('p', `Phone: ${order.customerPhone || ''} · Visit #${order.visitNumber || '?'}`));
    card.appendChild(makeElement('p', `Code: ${order.discountCode || 'NONE'} (${Number(order.discountPercent || 0)}%) · Discount: ${money(order.discountAmount)}`));
    card.appendChild(makeElement('p', `Cost: ${money(order.costTotal)} · Profit after discount: ${money(order.profit)}`));
    card.appendChild(makeElement('p', safeDate(order.time), 'small'));
    const detailButton = makeElement('button', 'View bill details', 'secondaryBtn');
    detailButton.type = 'button'; detailButton.addEventListener('click', () => {
      const rows = (order.items || []).map(item => `${item.name}: selling ${money(item.price)}, cost ${money(item.cost)}`).join('\n');
      alert(`Bill for ${order.customerName}\nPhone: ${order.customerPhone}\n\n${rows}\n\nSubtotal: ${money(order.subtotal)}\nDiscount: ${money(order.discountAmount)} (${order.discountPercent}%)\nCustomer pays: ${money(order.finalTotal)}\nProfit after discount: ${money(order.profit)}`);
    });
    card.appendChild(detailButton);
    container.appendChild(card);
  });
}
function renderCodeStats() {
  allCodes.forEach(code => {
    const stats = data.codeStats[code] || { uses: 0, totalDiscount: 0 };
    const node = $('stat-' + code);
    if (node) node.textContent = `Saved uses: ${Number(stats.uses || 0)} · Total discount given: ${money(stats.totalDiscount || 0)}`;
  });
}
function renderSuggestions() {
  const container = $('suggestions'); container.replaceChildren();
  if (suggestionsError) {
    container.appendChild(makeElement('p', suggestionsError, 'error'));
    container.appendChild(makeElement('p', 'Check that the Firebase Realtime Database rules allow this admin page to read /suggestions.', 'small'));
    return;
  }
  if (!suggestionsLoaded) { container.textContent = 'Loading suggestions from Firebase…'; return; }
  const entries = Object.entries(firebaseSuggestions).sort((a, b) => Number(b[1]?.time || 0) - Number(a[1]?.time || 0));
  if (!entries.length) { container.textContent = 'No suggestions in Firebase yet.'; return; }
  entries.forEach(([key, suggestion]) => {
    const card = makeElement('div', undefined, 'card');
    card.appendChild(makeElement('h3', suggestion?.name || 'Anonymous'));
    card.appendChild(makeElement('p', suggestion?.message || ''));
    card.appendChild(makeElement('p', safeDate(suggestion?.time), 'small'));
    const button = makeElement('button', 'Delete suggestion', 'deleteBtn');
    button.type = 'button';
    button.addEventListener('click', async () => {
      if (!confirm('Delete this suggestion from the shared Firebase suggestions list?')) return;
      button.disabled = true;
      try {
        // Removes only this single suggestion. Does not clear the database or other records.
        await remove(ref(db, 'suggestions/' + key));
      } catch (error) {
        console.error('Could not delete Firebase suggestion:', error);
        alert('Could not delete this suggestion. Check Firebase permissions and try again.');
        button.disabled = false;
      }
    });
    card.appendChild(button); container.appendChild(card);
  });
}

// Subscribe to the existing shared Firebase suggestions path after the local admin signs in.
// No Firebase Authentication is used. Customer records, bills, visits and discount statistics stay in localStorage.
function attachSuggestionsListener() {
  if (stopSuggestionsListener) return;
  suggestionsLoaded = false;
  suggestionsError = '';
  renderSuggestions();
  stopSuggestionsListener = onValue(ref(db, 'suggestions'), snapshot => {
    firebaseSuggestions = snapshot.val() || {};
    suggestionsLoaded = true;
    suggestionsError = '';
    renderSuggestions();
  }, error => {
    console.error('Could not read Firebase suggestions:', error);
    suggestionsLoaded = true;
    suggestionsError = 'Could not load suggestions from Firebase.';
    renderSuggestions();
  });
}
function refreshAll() { renderCustomers(); updateSelected(); renderItems(); renderOrders(); renderCodeStats(); renderSuggestions(); }
function startAdminSession() {
  $('loginScreen').classList.add('hidden');
  $('app').classList.remove('hidden');
  if (!adminStarted) {
    adminStarted = true;
    attachSuggestionsListener();
  }
  refreshAll();
}
$('loginForm').addEventListener('submit', event => {
  event.preventDefault();
  const username = $('loginUsername').value.trim();
  const password = $('loginPassword').value;
  if (username === ADMIN_USERNAME && password === ADMIN_PASSWORD) {
    sessionStorage.setItem(LOGIN_SESSION_KEY, '1');
    $('loginError').classList.add('hidden');
    $('loginPassword').value = '';
    startAdminSession();
  } else {
    $('loginError').textContent = 'Incorrect username or password. Please try again.';
    $('loginError').classList.remove('hidden');
    $('loginPassword').value = '';
    $('loginPassword').focus();
  }
});
$('logoutBtn').addEventListener('click', () => {
  sessionStorage.removeItem(LOGIN_SESSION_KEY);
  if (stopSuggestionsListener) { stopSuggestionsListener(); stopSuggestionsListener = null; }
  $('app').classList.add('hidden');
  $('loginScreen').classList.remove('hidden');
  $('loginUsername').value = '';
  $('loginPassword').value = '';
  $('loginError').classList.add('hidden');
  $('loginUsername').focus();
});
if (sessionStorage.getItem(LOGIN_SESSION_KEY) === '1') startAdminSession();

$('customerSearch').addEventListener('input', renderCustomers);
$('saveCustomerBtn').addEventListener('click', () => {
  const name = $('customerName').value.trim(), phone = $('customerPhone').value.trim(), key = phoneKey(phone);
  if (!name || !phone || key.length < 5) { alert('Enter a customer name and a valid phone number.'); return; }
  const old = data.customers[key] || {};
  data.customers[key] = { ...old, name, phone, visits: Number(old.visits || 0), discountCodeUses: old.discountCodeUses || {}, createdAt: old.createdAt || Date.now(), updatedAt: Date.now() };
  if (!saveData()) return;
  selectedKey = key; refreshAll();
});
$('addItemBtn').addEventListener('click', () => {
  if (!selected()) { alert('Save/find and select a customer first.'); return; }
  const name = $('item').value.trim(), priceField = $('price'), costField = $('cost');
  const price = Number(priceField.value), cost = Number(costField.value);
  if (!name || priceField.value === '' || costField.value === '' || !Number.isFinite(price) || !Number.isFinite(cost) || price < 0 || cost < 0) { alert('Enter item name, selling price and cost price.'); return; }
  items.push({ name, price, cost }); $('item').value = ''; priceField.value = ''; costField.value = ''; renderItems();
});
$('completeBillBtn').addEventListener('click', () => {
  const customer = selected();
  if (!customer) { alert('Select a customer first.'); return; }
  if (!items.length) { alert('Add at least one item.'); return; }
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  const costTotal = items.reduce((sum, item) => sum + item.cost, 0);
  const previousVisits = Number(customer.visits || 0);
  const enteredCode = $('billDiscountCode').value.trim().toUpperCase();
  let code = '', percent = 0;
  if (previousVisits > 10) { code = 'INFINITE99'; percent = 99; }
  else if (enteredCode) {
    if (!Object.prototype.hasOwnProperty.call(discountCodes, enteredCode)) { alert('Invalid code. Use HAVECRUSHONLC, RUSHONCRUSH30 or LEMONCRUSH10.'); return; }
    code = enteredCode; percent = discountCodes[enteredCode];
  }
  const discountAmount = subtotal * percent / 100;
  const finalTotal = subtotal - discountAmount;
  const profit = finalTotal - costTotal;
  const now = Date.now(), visitNumber = previousVisits + 1;
  const orderId = makeId();
  const order = { customerName: customer.name || '', customerPhone: customer.phone || selectedKey, customerKey: selectedKey, items: items.map(item => ({ ...item })), subtotal, costTotal, discountCode: code || 'NONE', discountPercent: percent, discountAmount, finalTotal, profit, visitNumber, time: now };
  data.orders[orderId] = order;
  customer.visits = visitNumber; customer.lastVisitAt = now; customer.updatedAt = now;
  if (code) {
    data.codeStats[code] ||= { uses: 0, totalDiscount: 0 };
    data.codeStats[code].uses = Number(data.codeStats[code].uses || 0) + 1;
    data.codeStats[code].totalDiscount = Number(data.codeStats[code].totalDiscount || 0) + discountAmount;
    customer.discountCodeUses ||= {};
    customer.discountCodeUses[code] = Number(customer.discountCodeUses[code] || 0) + 1;
  }
  if (!saveData()) { delete data.orders[orderId]; customer.visits = previousVisits; if (code) { data.codeStats[code].uses--; data.codeStats[code].totalDiscount -= discountAmount; customer.discountCodeUses[code]--; } return; }
  sessionProfit += profit; $('totalProfit').textContent = 'Session Profit: ' + money(sessionProfit);
  const result = $('billResult'); result.replaceChildren();
  [ `Bill saved for: ${customer.name}`, `Visit number: ${visitNumber}`, `Discount code: ${code || 'None'}`, `Discount: ${percent}%`, `Subtotal: ${money(subtotal)}`, `Discount amount: ${money(discountAmount)}`, `Customer pays: ${money(finalTotal)}`, `Profit after discount: ${money(profit)}` ].forEach(line => result.appendChild(makeElement('p', line)));
  result.className = 'result success';
  items = []; $('billDiscountCode').value = ''; renderItems(); refreshAll();
  alert(`Bill saved. Visit count is now ${visitNumber}.`);
});
$('calculateDiscountBtn').addEventListener('click', () => {
  const code = $('discountCode').value.trim().toUpperCase(), priceField = $('discountPrice');
  const result = $('discountResult'); result.replaceChildren();
  if (!code || priceField.value === '') { showMessage(result, 'Enter a discount code and original price.', true); return; }
  const price = Number(priceField.value);
  if (!Number.isFinite(price) || price < 0) { showMessage(result, 'Enter a valid price.', true); return; }
  let percent = code === 'INFINITE99' ? 99 : discountCodes[code];
  if (typeof percent !== 'number') { showMessage(result, 'Invalid discount code.', true); return; }
  if (code === 'INFINITE99' && (!selected() || Number(selected().visits || 0) <= 10)) { showMessage(result, 'INFINITE99 requires a selected customer with more than 10 completed visits.', true); return; }
  [`Valid code: ${code}`, `Discount: ${percent}%`, `Original price: ${money(price)}`, `Discount amount: ${money(price * percent / 100)}`, `Final price: ${money(price - price * percent / 100)}`].forEach(line => result.appendChild(makeElement('p', line)));
  result.className = 'result success';
});
$('addSuggestionBtn').addEventListener('click', async () => {
  const name = $('suggestionName').value.trim(), message = $('suggestionMessage').value.trim();
  if (!message) { showMessage($('suggestionStatus'), 'Enter a suggestion first.', true); $('suggestionStatus').classList.remove('hidden'); return; }
  const button = $('addSuggestionBtn'); button.disabled = true;
  try {
    const suggestionRef = push(ref(db, 'suggestions'));
    await set(suggestionRef, { name: name || 'Anonymous', message, time: Date.now() });
    $('suggestionName').value = ''; $('suggestionMessage').value = '';
    showMessage($('suggestionStatus'), 'Suggestion saved to Firebase and shared with the Lemon Crush website.');
    $('suggestionStatus').classList.remove('hidden');
  } catch (error) {
    console.error('Could not save Firebase suggestion:', error);
    showMessage($('suggestionStatus'), 'Could not save suggestion to Firebase. Check database permissions and try again.', true);
    $('suggestionStatus').classList.remove('hidden');
  } finally {
    button.disabled = false;
  }
});
$('exportBtn').addEventListener('click', () => {
  // Backup contains only local records. Firebase suggestions remain in Firebase and are not exported here.
  const localBackup = { backupType: 'lemon-crush-local-records', customers: data.customers, orders: data.orders, codeStats: data.codeStats, updatedAt: data.updatedAt };
  const blob = new Blob([JSON.stringify(localBackup, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob); const link = document.createElement('a');
  link.href = url; link.download = `lemon-crush-local-backup-${new Date().toISOString().slice(0,10)}.json`;
  document.body.appendChild(link); link.click(); link.remove(); URL.revokeObjectURL(url);
});
$('importBtn').addEventListener('click', () => $('importFile').click());
$('importFile').addEventListener('change', async event => {
  const file = event.target.files && event.target.files[0]; if (!file) return;
  try {
    const imported = JSON.parse(await file.text());
    if (!imported || typeof imported !== 'object' || !imported.customers || !imported.orders) throw new Error('This does not look like a Lemon Crush backup JSON file.');
    if (!confirm('Importing replaces the records currently saved in this browser. Export a backup first if you need the current records. Continue?')) { event.target.value = ''; return; }
    // Import only local records. Any suggestions included in an older backup are ignored.
    const clean = blankData();
    clean.customers = imported.customers && typeof imported.customers === 'object' ? imported.customers : {};
    clean.orders = imported.orders && typeof imported.orders === 'object' ? imported.orders : {};
    clean.codeStats = imported.codeStats && typeof imported.codeStats === 'object' ? imported.codeStats : {};
    clean.updatedAt = Number(imported.updatedAt) || Date.now();
    allCodes.forEach(code => clean.codeStats[code] = { uses: 0, totalDiscount: 0, ...(clean.codeStats[code] || {}) });
    data = clean;
    if (saveData()) { selectedKey = ''; items = []; sessionProfit = 0; refreshAll(); alert('Backup imported successfully.'); }
  } catch (error) { alert('Import failed: ' + error.message); }
  finally { event.target.value = ''; }
});

// Warn the owner before closing with unsaved items in the current draft bill.
window.addEventListener('beforeunload', event => {
  if (items.length) { event.preventDefault(); event.returnValue = ''; }
});
</script>
</body>
</html>
