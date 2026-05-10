<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Billing System</title>

<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

<style>
body {
  font-family: "Courier New", monospace;
  background: #fdf6e3;
  padding: 10px;
}

.container {
  max-width: 400px;
  margin: auto;
  background: #fff;
  padding: 15px;
  border: 1px solid #ccc;
}

input, textarea, select, button {
  width: 100%;
  padding: 8px;
  margin: 5px 0;
}

textarea {
  height: 70px;
}

.box {
  border: 1px solid black;
  padding: 5px;
  margin-top: 10px;
}

.output {
  margin-top: 10px;
  white-space: pre-line;
  border-top: 2px dashed black;
  padding-top: 10px;
}
</style>
</head>

<body>

<div class="container" id="receipt">

<h3>BILL COMPUTATION</h3>

<div class="box">
Bill No:
<input type="text" id="billNo" placeholder="Ilagay Bill Number">
</div>

<div class="box">
Due Date:
<input type="date" id="dueDate">
</div>

<select id="mode" onchange="resetForm()">
  <option value="koryente">Koryente</option>
  <option value="tubig">Tubig</option>
</select>

<input type="text" id="name" placeholder="Pangalan">

<textarea id="details" placeholder="Detalye..."></textarea>

<input type="text" id="curr" placeholder="Metro Ngayon" oninput="compute()">

<input type="text" id="prev" placeholder="Metro Dati" oninput="compute()">

<div class="box">
Kabuuang Konsumo:
<input type="text" id="kw">
</div>

<input type="number" id="rate" placeholder="Rate bawat unit" oninput="compute()">

<div class="output" id="result"></div>
<div class="box">

</div>
<button onclick="saveBill()">I-SAVE BILL</button>

<hr>

<h4>SUMMARY</h4>

<div class="box">
Tubig: P <span id="tubigTotal">0.00</span>
</div>

<div class="box">
Koryente: P <span id="koryenteTotal">0.00</span>
</div>

<div class="box">
+/-
<input type="text"
id="adjustment"
placeholder="+10 or -10"
onkeyup="compute()">
</div>

<div class="box">
TOTAL NA BABAYARAN:
<div id="combined">P 0.00</div>
</div>

</div>

<button onclick="printReceipt()">PRINT</button>
<button onclick="downloadImage()">SCREENSHOT</button>

<script>
let tubig = 0;
let koryente = 0;
let currentTotal = 0;

// AUTO DUE DATE
function autoDueDate() {
  let today = new Date();
  let next = new Date(today.setMonth(today.getMonth() + 1));

  let y = next.getFullYear();
  let m = String(next.getMonth()+1).padStart(2,'0');
  let d = String(next.getDate()).padStart(2,'0');

  document.getElementById("dueDate").value =
  y + "-" + m + "-" + d;
}

// GET NUMBER FROM TEXT
function getNumber(val) {
  let match = val.match(/[\d.]+/);
  return match ? parseFloat(match[0]) : 0;
}

// COMPUTE
function compute() {

  let prev =
  getNumber(document.getElementById("prev").value);

  let curr =
  getNumber(document.getElementById("curr").value);

  let rate =
  parseFloat(document.getElementById("rate").value) || 0;

  let consumption = curr - prev;

  if (consumption < 0) {
    consumption = 0;
  }

  let total = consumption * rate;

  currentTotal = total;

  document.getElementById("kw").value =
  consumption.toFixed(2);

  document.getElementById("result").textContent =

curr + "\n" +
"- " + prev + "\n" +
"---------\n" +
"= " + consumption.toFixed(2) + "\n\n" +

consumption.toFixed(2) + "\n" +
"x " + rate + "\n" +
"---------\n" +
"= P " + total.toFixed(2) + "\n\n" +

"TOTAL: P " + total.toFixed(2);
}

// SAVE BILL
function saveBill() {

  let mode =
  document.getElementById("mode").value;

  if (mode === "tubig") {
    tubig = currentTotal;
  } else {
    koryente = currentTotal;
  }

  document.getElementById("tubigTotal").textContent =
  tubig.toFixed(2);

  document.getElementById("koryenteTotal").textContent =
  koryente.toFixed(2);

  updateTotal();
}

// PLUS / MINUS
function updateTotal() {

  let adjustment =
  parseFloat(document.getElementById("adjustment").value) || 0;

  let combined =
  tubig + koryente + adjustment;

  document.getElementById("combined").textContent =
  "P " + combined.toFixed(2);
}

// RESET
function resetForm() {

  document.querySelectorAll("input, textarea")
  .forEach(el => {

    if (
      el.id !== "billNo" &&
      el.id !== "dueDate"
    ) {
      el.value = "";
    }

  });

  document.getElementById("result").textContent = "";

  currentTotal = 0;

  autoDueDate();
}

// PRINT
function printReceipt() {
  window.print();
}

// SCREENSHOT
function downloadImage() {

  html2canvas(
    document.getElementById("receipt"),
    {scale:2}
  ).then(canvas => {

    let link =
    document.createElement("a");

    link.download = "billing.png";

    link.href = canvas.toDataURL();

    link.click();

  });
}

// LOAD
autoDueDate();

</script>

</body>
</html>
