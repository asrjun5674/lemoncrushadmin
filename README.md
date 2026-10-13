<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lemon Crush Admin</title>

<style>
* {
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: linear-gradient(to bottom, #ff1493, #7a007a, #220033);
  color: white;
  padding: 20px;
  margin: 0;
  min-height: 100vh;
}

h1 {
  text-align: center;
  font-size: clamp(32px, 5vw, 60px);
  color: yellow;
  margin: 10px 0 20px;
}

h2 {
  color: yellow;
}

.box {
  background: rgba(255,255,255,0.1);
  padding: 25px;
  border-radius: 25px;
  margin-top: 25px;
}

.card {
  background: rgba(255,255,255,0.1);
  padding: 20px;
  border-radius: 20px;
  margin-top: 15px;
  overflow-wrap: anywhere;
}

.billingInput {
  width: 100%;
  padding: 15px;
  margin-top: 15px;
  border: none;
  border-radius: 15px;
  font-size: 18px;
  color: black;
  background: white;
}

.addBtn {
  background: yellow;
  color: #ff0088;
  width: 100%;
  padding: 15px;
  margin-top: 20px;
  border: none;
  border-radius: 20px;
  font-size: 20px;
  font-weight: bold;
  cursor: pointer;
}

.deleteBtn {
  background: red;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 12px;
  margin-top: 15px;
  cursor: pointer;
  font-size: 16px;
}

table {
  width: 100%;
  margin-top: 20px;
  border-collapse: collapse;
  background: white;
}

th, td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: center;
  font-size: 15px;
  color: black;
}

th {
  background: #ffe066;
}

.total {
  font-size: 26px;
  color: yellow;
  margin-top: 20px;
  text-align: center;
}

.result {
  margin-top: 18px;
  padding: 15px;
  background: rgba(0,0,0,0.25);
  border-radius: 15px;
  line-height: 1.6;
  overflow-wrap: anywhere;
}

.success {
  color: #aaffaa;
}

.error {
  color: #ffaaaa;
}

@media (max-width: 600px) {
  body {
    padding: 10px;
  }

  .box {
    padding: 15px;
  }

  th, td {
    padding: 6px;
    font-size: 12px;
  }
}
</style>
</head>

<body>

<h1>🍋 Lemon Crush Admin</h1>

<!-- BILLING SECTION -->
<div class="box">
  <h2>🧾 Add Billing</h2>

  <input type="text" id="item" class="billingInput"
         placeholder="Item Name">

  <input type="number" id="price" class="billingInput"
         placeholder="Selling Price (₹)" min="0" step="0.01">

  <input type="number" id="cost" class="billingInput"
         placeholder="Cost Price (₹)" min="0" step="0.01">

  <button class="addBtn" onclick="addBill()">Add Bill</button>

  <table>
    <thead>
      <tr>
        <th>Item</th>
        <th>Selling</th>
        <th>Cost</th>
        <th>Profit</th>
      </tr>
    </thead>
    <tbody id="billTable"></tbody>
  </table>

  <div class="total" id="totalProfit">Total Profit: ₹0.00</div>
</div>

<!-- DISCOUNT CODE SECTION -->
<div class="box">
  <h2>🎟️ Discount Code Manager</h2>
  <p>Enter a discount code and the original price to calculate the offer.</p>

  <div class="card">
    <h3>💖 Your Discount Codes</h3>
    <p><strong>HAVECRUSHONLC</strong> — 50% OFF</p>
    <p><strong>RUSHONCRUSH30</strong> — 30% OFF</p>
    <p><strong>LEMONCRUSH10</strong> — 10% OFF</p>
  </div>

  <input type="text" id="discountCode" class="billingInput"
         placeholder="Enter Discount Code">

  <input type="number" id="discountPrice" class="billingInput"
         placeholder="Original Price (₹)" min="0" step="0.01">

  <button class="addBtn" onclick="calculateDiscount()">
    Calculate Discount 💖
  </button>

  <div id="discountResult" class="result" aria-live="polite">
    Your discount calculation will appear here.
  </div>
</div>

<!-- CUSTOMER SUGGESTIONS -->
<div class="box">
  <h2>💌 Customer Suggestions</h2>
  <div id="suggestions">Loading suggestions...</div>
</div>

<script type="module">
import { initializeApp } from
  "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";

import {
  getDatabase,
  ref,
  onValue,
  remove
} from "https://www.gstatic.com/firebasejs/10.12.2/firebase-database.js";

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

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);
const suggestionsRef = ref(db, "suggestions");
const suggestionsDiv = document.getElementById("suggestions");

// LOAD CUSTOMER SUGGESTIONS
onValue(suggestionsRef, (snapshot) => {
  suggestionsDiv.replaceChildren();

  const data = snapshot.val();

  if (!data) {
    suggestionsDiv.textContent = "No suggestions yet.";
    return;
  }

  Object.entries(data).forEach(([key, value]) => {
    const card = document.createElement("div");
    card.className = "card";

    const name = document.createElement("h3");
    name.textContent = value?.name ?? "Anonymous";

    const message = document.createElement("p");
    message.textContent = value?.message ?? "";

    const deleteButton = document.createElement("button");
    deleteButton.className = "deleteBtn";
    deleteButton.textContent = "Delete";

    deleteButton.onclick = async () => {
      if (!confirm("Delete this suggestion?")) return;

      try {
        await remove(ref(db, "suggestions/" + key));
      } catch (error) {
        alert("Could not delete suggestion. Check Firebase permissions.");
        console.error(error);
      }
    };

    card.append(name, message, deleteButton);
    suggestionsDiv.appendChild(card);
  });
}, (error) => {
  suggestionsDiv.textContent =
    "Could not load suggestions. Check Firebase permissions.";
  console.error(error);
});

// BILLING AND PROFIT
let totalProfit = 0;

window.addBill = function () {
  const itemInput = document.getElementById("item");
  const priceInput = document.getElementById("price");
  const costInput = document.getElementById("cost");

  const item = itemInput.value.trim();
  const price = Number(priceInput.value);
  const cost = Number(costInput.value);

  if (
    !item ||
    priceInput.value === "" ||
    costInput.value === "" ||
    !Number.isFinite(price) ||
    !Number.isFinite(cost) ||
    price < 0 ||
    cost < 0
  ) {
    alert("Please fill all fields with valid prices.");
    return;
  }

  const profit = price - cost;
  totalProfit += profit;

  const row = document.createElement("tr");

  [item, price, cost, profit].forEach((value) => {
    const cell = document.createElement("td");

    cell.textContent =
      typeof value === "number"
        ? "₹" + value.toFixed(2)
        : value;

    row.appendChild(cell);
  });

  document.getElementById("billTable").appendChild(row);

  document.getElementById("totalProfit").textContent =
    "Total Profit: ₹" + totalProfit.toFixed(2);

  itemInput.value = "";
  priceInput.value = "";
  costInput.value = "";
};

// DISCOUNT CODES
const discountCodes = {
  "HAVECRUSHONLC": 50,
  "RUSHONCRUSH30": 30,
  "LEMONCRUSH10": 10
};

window.calculateDiscount = function () {
  const code = document.getElementById("discountCode")
    .value.trim().toUpperCase();

  const priceField = document.getElementById("discountPrice");
  const result = document.getElementById("discountResult");

  result.replaceChildren();
  result.className = "result";

  if (!code || priceField.value === "") {
    result.textContent =
      "Please enter a discount code and original price.";
    result.classList.add("error");
    return;
  }

  const price = Number(priceField.value);

  if (!Number.isFinite(price) || price < 0) {
    result.textContent = "Please enter a valid price.";
    result.classList.add("error");
    return;
  }

  if (!Object.prototype.hasOwnProperty.call(discountCodes, code)) {
    result.textContent =
      "❌ Invalid code! Please check your discount code.";
    result.classList.add("error");
    return;
  }

  const percentage = discountCodes[code];
  const discountAmount = price * percentage / 100;
  const finalPrice = price - discountAmount;

  result.classList.add("success");

  const details = [
    "✅ Valid Discount Code: " + code,
    "Discount Percentage: " + percentage + "%",
    "Original Price: ₹" + price.toFixed(2),
    "Discount Amount: ₹" + discountAmount.toFixed(2),
    "Final Price After Discount: ₹" + finalPrice.toFixed(2)
  ];

  details.forEach((detail) => {
    const paragraph = document.createElement("p");
    paragraph.textContent = detail;
    result.appendChild(paragraph);
  });
};
</script>

</body>
</html>
```
