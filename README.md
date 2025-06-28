# Shreya_Sharma_Grocery_Inventory_Manager
# FreshMart Retail - Inventory Analytics System
# Internship Project by Shreya Sharma
# Objective: Monitor stock, reduce overstock/shortages, and track purchases

import pandas as pd
import matplotlib.pyplot as plt

# 1. Sample Inventory Dataset
inventory_data = {
    "Product ID": [101, 102, 103, 104],
    "Product Name": ["Rice", "Wheat", "Oil", "Milk"],
    "Category": ["Grains", "Grains", "Grocery", "Dairy"],
    "Stock": [120, 80, 50, 30],
    "Price (₹)": [45, 50, 120, 60]
}

inventory_df = pd.DataFrame(inventory_data)
print(" Current Inventory:\n", inventory_df)

# 2. Add New Product
def add_product(pid, name, category, stock, price):
    global inventory_df
    new_row = pd.DataFrame([[pid, name, category, stock, price]],
                           columns=inventory_df.columns)
    inventory_df = pd.concat([inventory_df, new_row], ignore_index=True)
    print(f" Product '{name}' added.")

# 3. Update Stock
def update_stock(pid, new_stock):
    if pid in inventory_df['Product ID'].values:
        inventory_df.loc[inventory_df['Product ID'] == pid, 'Stock'] = new_stock
        print(f" Stock updated for Product ID {pid}.")
    else:
        print(" Product ID not found.")

# 4. Delete Product
def delete_product(pid):
    global inventory_df
    if pid in inventory_df['Product ID'].values:
        inventory_df = inventory_df[inventory_df['Product ID'] != pid]
        print(f" Product ID {pid} deleted.")
    else:
        print(" Product ID not found.")

# 5. Simulate Purchases
purchase_log = []

def record_purchase(pid, quantity):
    global inventory_df, purchase_log
    if pid in inventory_df['Product ID'].values:
        stock = inventory_df.loc[inventory_df['Product ID'] == pid, 'Stock'].values[0]
        if quantity <= stock:
            inventory_df.loc[inventory_df['Product ID'] == pid, 'Stock'] -= quantity
            price = inventory_df.loc[inventory_df['Product ID'] == pid, 'Price (₹)'].values[0]
            total = quantity * price
            purchase_log.append({"Product ID": pid, "Quantity": quantity, "Total Amount": total})
            print(f"Purchase recorded. ₹{total} added.")
        else:
            print(" Insufficient stock.")
    else:
        print(" Product ID not found.")

# 6. Analytics - Stock Alerts
def stock_alerts(threshold=40):
    low_stock = inventory_df[inventory_df['Stock'] <= threshold]
    if not low_stock.empty:
        print(" Low Stock Alert:\n", low_stock)
    else:
        print(" All items sufficiently stocked.")

# 7. Analytics - Sales Summary
def sales_summary():
    if not purchase_log:
        print(" No purchases yet.")
        return
    df = pd.DataFrame(purchase_log)
    summary = df.groupby("Product ID").agg({"Quantity": "sum", "Total Amount": "sum"}).reset_index()
    print(" Sales Summary:\n", summary)

# 8. Visual Analytics
def plot_stock_status():
    plt.figure(figsize=(8,5))
    plt.bar(inventory_df['Product Name'], inventory_df['Stock'], color='skyblue')
    plt.title(" Current Stock Levels")
    plt.xlabel("Product")
    plt.ylabel("Stock Quantity")
    plt.grid(True)
    plt.show()

# Example Usage
add_product(105, "Sugar", "Grocery", 90, 40)
update_stock(104, 25)
record_purchase(101, 10)
record_purchase(104, 5)
stock_alerts()
sales_summary()
plot_stock_status()
