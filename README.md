
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NovaBank | Student Demo</title>
<style>
:root {
  --navy: #102342;
  --blue: #2864e8;
  --bg: #f3f6fb;
  --muted: #718096;
}
* { box-sizing: border-box; }
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: var(--bg);
  color: var(--navy);
}
header {
  background: white;
  padding: 18px 6%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #e6eaf0;
}
.logo { font-size: 24px; font-weight: bold; }
.logo span { color: var(--blue); }
.demo {
  background: #fff0ce;
  color: #855400;
  padding: 7px 11px;
  border-radius: 8px;
  font-size: 12px;
  font-weight: bold;
}
main { max-width: 1100px; margin: 30px auto; padding: 0 20px; }
h1 { font-size: 28px; margin-bottom: 6px; }
.subtitle { color: var(--muted); margin-bottom: 26px; }
.balance {
  background: linear-gradient(120deg, #102342, #2864e8);
  color: white;
  padding: 28px;
  border-radius: 18px;
  margin-bottom: 22px;
}
.balance p { opacity: .85; margin: 0 0 12px; }
.amount { font-size: 36px; font-weight: bold; }
.account { margin-top: 18px; font-size: 13px; opacity: .85; }
.stats {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
  margin-bottom: 22px;
}
.stat, .panel {
  background: white;
  border-radius: 14px;
  padding: 20px;
  border: 1px solid #e8edf4;
}
.stat p { color: var(--muted); margin: 0 0 12px; }
.stat strong { font-size: 20px; }
.layout {
  display: grid;
  grid-template-columns: 1fr 1.3fr;
  gap: 20px;
}
h2 { font-size: 19px; margin-top: 0; }
label { display: block; margin: 15px 0 7px; font-size: 13px; }
input {
  width: 100%;
  padding: 12px;
  border: 1px solid #dce3ed;
  border-radius: 9px;
  font-size: 15px;
}
button {
  border: 0;
  border-radius: 9px;
  padding: 12px 16px;
  cursor: pointer;
  font-size: 14px;
  font-weight: bold;
}
.primary { background: var(--blue); color: white; width: 100%; margin-top: 18px; }
.secondary { background: #edf3ff; color: var(--blue); }
.notice { color: var(--muted); font-size: 12px; line-height: 1.6; }
#message { margin-top: 12px; font-size: 13px; line-height: 1.5; }
.transaction {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 12px;
  padding: 15px 0;
  border-bottom: 1px solid #edf0f5;
}
.transaction:last-child { border-bottom: 0; }
.tx-name { font-weight: bold; font-size: 14px; }
.tx-date { color: var(--muted); font-size: 12px; margin-top: 5px; }
.positive { color: #15966a; font-weight: bold; }
.negative { color: #df5260; font-weight: bold; }
footer { text-align: center; padding: 30px 15px; color: var(--muted); font-size: 12px; }
@media(max-width:700px) {
  .stats { grid-template-columns: 1fr; }
  .layout { grid-template-columns: 1fr; }
  main { margin-top: 22px; }
  h1 { font-size: 24px; }
}
</style>
</head>
<body>
<header>
  <div class="logo">Nova<span>Bank</span></div>
  <div class="demo">STUDENT DEMO</div>
</header>

<main>
  <h1>Welcome back, Alex!</h1>
  <p class="subtitle">Here is your sample account overview.</p>

  <section class="balance">
    <p>Demo account balance</p>
    <div class="amount" id="balance">$5,240.00</div>
    <div class="account">Account: DEMO-2048 · Sample account</div>
  </section>

  <section class="stats">
    <div class="stat">
      <p>Money received</p>
      <strong id="income">$1,500.00</strong>
    </div>
    <div class="stat">
      <p>Money sent</p>
      <strong id="expenses">$260.00</strong>
    </div>
    <div class="stat">
      <p>Transactions</p>
      <strong id="count">4</strong>
    </div>
  </section>

  <section class="layout">
    <div class="panel">
      <h2>Make a demo transfer</h2>
      <p class="notice">This simulates a transfer locally. No money is sent.</p>

      <form id="transferForm">
        <label for="recipient">Recipient name</label>
        <input id="recipient" maxlength="40"
          placeholder="e.g. Jamie Smith" required>

        <label for="transferAmount">Amount ($)</label>
        <input id="transferAmount" type="number"
          min="0.01" max="5240" step="0.01"
          placeholder="0.00" required>

        <button class="primary" type="submit">
          Simulate transfer
        </button>
      </form>

      <div id="message" role="status" aria-live="polite"></div>
    </div>

    <div class="panel">
      <h2>Recent transactions</h2>
      <div id="transactions"></div>
      <button class="secondary" id="reset" style="margin-top:15px">
        Reset demo
      </button>
    </div>
  </section>
</main>

<footer>
  NovaBank Student Project · Fictional data only ·
  Not a real financial institution
</footer>

<script>
const initialTransactions = [
  { name: "Sample salary", date: "Oct 20, 2026",
    amount: 1500, type: "in" },
  { name: "Grocery store", date: "Oct 19, 2026",
    amount: 85, type: "out" },
  { name: "Coffee shop", date: "Oct 18, 2026",
    amount: 15, type: "out" },
  { name: "Sample refund", date: "Oct 17, 2026",
    amount: 50, type: "in" }
];

let balance = 5240;
let transactions = initialTransactions.map(t => ({...t}));
let income = 1500;
let expenses = 260;

const money = n => "$" + n.toLocaleString("en-US", {
  minimumFractionDigits: 2,
  maximumFractionDigits: 2
});

function render() {
  document.getElementById("balance").textContent = money(balance);
  document.getElementById("income").textContent = money(income);
  document.getElementById("expenses").textContent = money(expenses);
  document.getElementById("count").textContent = transactions.length;

  const list = document.getElementById("transactions");
  list.replaceChildren();

  transactions.forEach(t => {
    const row = document.createElement("div");
    row.className = "transaction";

    const details = document.createElement("div");
    const name = document.createElement("div");
    name.className = "tx-name";
    name.textContent = t.name;

    const date = document.createElement("div");
    date.className = "tx-date";
    date.textContent = t.date;

    details.append(name, date);

    const amount = document.createElement("div");
    amount.className = t.type === "in" ? "positive" : "negative";
    amount.textContent =
      (t.type === "in" ? "+" : "-") + money(t.amount);

    row.append(details, amount);
    list.append(row);
  });
}

document.getElementById("transferForm").addEventListener("submit", e => {
  e.preventDefault();

  const recipient = document.getElementById("recipient").value.trim();
  const amount = Number(document.getElementById("transferAmount").value);
  const message = document.getElementById("message");

  if (!recipient || !Number.isFinite(amount) || amount <= 0) {
    message.textContent = "Enter a valid recipient and amount.";
    return;
  }

  if (amount > balance) {
    message.textContent = "Insufficient demo balance.";
    return;
  }

  balance = Math.round((balance - amount) * 100) / 100;
  expenses = Math.round((expenses + amount) * 100) / 100;

  transactions.unshift({
    name: "Demo transfer to " + recipient,
    date: new Date().toLocaleDateString("en-US", {
      month: "short", day: "numeric", year: "numeric"
    }),
    amount,
    type: "out"
  });

  message.textContent =
    "Demo transfer recorded locally. No real money was sent.";

  document.getElementById("transferForm").reset();
  render();
});

document.getElementById("reset").addEventListener("click", () => {
  balance = 5240;
  income = 1500;
  expenses = 260;
  transactions = initialTransactions.map(t => ({...t}));
  document.getElementById("message").textContent =
    "Demo restored to its original sample data.";
  render();
});

render();
</script>
</body>
</html>
