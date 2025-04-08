import streamlit as st
import datetime

# Initialize Variables
if "total_purchases" not in st.session_state:
    st.session_state.total_purchases = 0.00
if "total_sales" not in st.session_state:
    st.session_state.total_sales = 0.00
if "transactions" not in st.session_state:
    st.session_state.transactions = []

# Streamlit UI
st.title("🎮 Possolli’s Case Tracker")

# Calculate Profit
profit = st.session_state.total_sales - st.session_state.total_purchases

# Display Profit
st.header("💰 Profit Tracker")
st.metric("Total Profit", f"${profit:.2f}")

# Function to Add Transaction
def add_transaction(transaction_type, case_name, price, quantity):
    date = datetime.datetime.now().strftime("%Y-%m-%d")
    total_cost = price * quantity

    if transaction_type == "buy":
        st.session_state.total_purchases += total_cost  # Buying increases cost
    else:
        st.session_state.total_sales += total_cost  # Selling increases revenue

    st.session_state.transactions.append({
        "date": date, "type": transaction_type, "case_name": case_name,
        "price": price, "quantity": quantity, "total": total_cost
    })
    st.success(f"✅ {transaction_type.capitalize()} recorded: {quantity} {case_name}(s) at ${price:.2f} each.")

# Buy/Sell Cases
st.header("➕ Add Transaction")
transaction_type = st.radio("Transaction Type", ["Buy", "Sell"])
case_name = st.text_input("Case Name")
price = st.number_input("Price per Case", min_value=0.01, format="%.2f")
quantity = st.number_input("Quantity", min_value=1, step=1)

if st.button("Add Transaction"):
    add_transaction(transaction_type.lower(), case_name, price, quantity)

# Improved Transaction History
st.header("📜 Transaction History")
if st.session_state.transactions:
    st.write("#### 🔍 Recent Transactions:")
    for t in reversed(st.session_state.transactions):
        transaction_icon = "🟢 Sell" if t["type"] == "sell" else "🔴 Buy"
        st.write(f"📅 **{t['date']}** | {transaction_icon} | **{t['case_name']}** | 🗳 {t['quantity']} | 💰 ${t['price']:.2f} each | 📦 Total: **${t['total']:.2f}**")
else:
    st.write("No transactions recorded yet.")

# Footer
st.markdown("📊 *Track your CS:GO case sales easily with Possolli’s Case Tracker!*")
