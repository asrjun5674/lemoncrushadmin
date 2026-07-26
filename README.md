<!-- SAVE AS admin.html -->
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lemon Crush Admin</title>

<style>
body{
  font-family:Arial, sans-serif;
  background:linear-gradient(to bottom,#ff1493,#7a007a,#220033);
  color:white;
  padding:20px;
  margin:0;
}

h1{
  text-align:center;
  font-size:60px;
  color:yellow;
  margin:10px 0 20px;
}

.box{
  background:rgba(255,255,255,0.1);
  padding:25px;
  border-radius:25px;
  margin-top:25px;
}

.card{
  background:rgba(255,255,255,0.1);
  padding:20px;
  border-radius:20px;
  margin-top:15px;
}

button{
  background:red;
  color:white;
  border:none;
  padding:10px 20px;
  border-radius:12px;
  margin-top:15px;
  cursor:pointer;
  font-size:18px;
}

.billingInput{
  width:100%;
  padding:15px;
  margin-top:15px;
  border:none;
  border-radius:15px;
  font-size:18px;
  color:black;
  background:white;
}

.addBtn{
  background:yellow;
  color:#ff0088;
  width:100%;
  padding:15px;
  font-size:22px;
  font-weight:bold;
}

table{
  width:100%;
  margin-top:20px;
  border-collapse:collapse;
  background:white;
  border-radius:15px;
  overflow:hidden;
}

th,td{
  border:2px solid white;
  padding:12px;
  text-align:center;
  font-size:18px;
  color:black;
  background:white;
}

th{
  background:#ffe066;
}

.total{
  font-size:30px;
  color:yellow;
  margin-top:20px;
  text-align:center;
}

</style>
</head>

<body>

<h1>🍋 Lemon Crush Admin</h1>

<div class="box">
  <h2>Add Billing</h2>

  <input type="text" id="item" class="billingInput" placeholder="Item Name">
  <input type="number" id="price" class="billingInput" placeholder="Selling Price">
  <input type="number" id="cost" class="billingInput" placeholder="Cost Price">

  <button class="addBtn" onclick="addBill()">Add Bill</button>

  <table>
    <tr>
      <th>Item</th>
      <th>Selling</th>
      <th>Cost</th>
      <th>Profit</th>
    </tr>
    <tbody id="billTable"></tbody>
  </table>

  <div class="total" id="totalProfit">Total Profit: ₹0</div>
</div>

<div class="box">
  <h2>Customer Suggestions 💖</h2>
  <div id="suggestions"></div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import {
  getFirestore,
  collection,
  onSnapshot,
  deleteDoc,
  doc
} from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSyCONw7aVKww1VdumdYhrJXYA4n3duIAwD8",
  authDomain: "lemon-crush.firebaseapp.com",
  projectId: "lemon-crush",
  storageBucket: "lemon-crush.firebasestorage.app",
  messagingSenderId: "149466674530",
  appId: "1:149466674530:web:f72cc273ef8f8e6472a83b",
  measurementId: "G-B3TGW4ZPC2"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

const suggestionsDiv = document.getElementById("suggestions");

onSnapshot(collection(db, "suggestions"), (snapshot) => {
  suggestionsDiv.innerHTML = "";

  if (snapshot.empty) {
    suggestionsDiv.innerHTML = "<p>No suggestions yet.</p>";
    return;
  }

  snapshot.forEach((docItem) => {
    const data = docItem.data();

    suggestionsDiv.innerHTML += `
      <div class="card">
        <h3>${data.name}</h3>
        <p>${data.message}</p>
        <button onclick="deleteSuggestion('${docItem.id}')">Delete</button>
      </div>
    `;
  });
});

window.deleteSuggestion = async function(id){
  await deleteDoc(doc(db, "suggestions", id));
};

let totalProfit = 0;

window.addBill = function(){
  let item = document.getElementById("item").value.trim();
  let price = parseInt(document.getElementById("price").value);
  let cost = parseInt(document.getElementById("cost").value);

  if(!item || !price || !cost){
    alert("Fill all fields");
    return;
  }

  let profit = price - cost;
  totalProfit += profit;

  document.getElementById("billTable").innerHTML += `
    <tr>
      <td>${item}</td>
      <td>₹${price}</td>
      <td>₹${cost}</td>
      <td>₹${profit}</td>
    </tr>
  `;

  document.getElementById("totalProfit").innerHTML = "Total Profit: ₹" + totalProfit;

  document.getElementById("item").value = "";
  document.getElementById("price").value = "";
  document.getElementById("cost").value = "";
};
</script>

</body>
</html>
