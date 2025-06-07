<html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Roha Burger and Pizza - Sales Register</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f0f0f0; padding: 20px; }
    h1, h2 { color: #d35400; }
    table, th, td { border: 1px solid #ccc; border-collapse: collapse; padding: 8px; }
    table { width: 100%; margin-top: 10px; background: #fff; }
    input, select, button { padding: 6px; margin: 5px 0; }
    .low-stock { background-color: #ffe6e6; }
    .form-group { margin-bottom: 10px; }
    summary { font-weight: bold; font-size: 16px; margin-top: 10px; cursor: pointer; color: #2c3e50; }
    details { background: #fafafa; padding: 10px; margin-top: 5px; border-radius: 5px; }
  </style>
</head>
<body>

  <h1>Roha Burger and Pizza</h1>
  <h2>Sales Register</h2>

  <div class="form-group">
    <label>Date: 
      <input type="date" id="saleDate" />
    </label>
  </div>

  <div class="form-group">
    <label>Category: 
      <select id="categorySelect" onchange="filterByCategory()">
        <option value="All">All</option>
        <option value="Burger">Burger</option>
        <option value="Pizza">Pizza</option>
        <option value="Juice">Juice</option>
        <option value="Shake">Shake</option>
        <option value="Fast Food">Fast Food</option>
        <option value="Hot Drink">Hot Drink</option>
        <option value="Soft Drink">Soft Drink</option>
      </select>
    </label>
  </div>

  <div class="form-group">
    <label>Product:
      <select id="productSelect"></select>
    </label>
    <label>Quantity:
      <input type="number" id="quantityInput" min="1" />
    </label>
    <button onclick="registerSale()">Register Sale</button>
  </div>

  <h2>Inventory</h2>
  <table id="inventoryTable">
    <thead>
      <tr><th>Product</th><th>Category</th><th>Price</th><th>Stock</th></tr>
    </thead>
    <tbody></tbody>
  </table>

  <h2>Sales Reports by Date</h2>
  <div id="salesReports"></div>

  <script>
    const inventory = [
      { name: "Roha Special Pizza (Large)", category: "Pizza", price: 520, quantity: 100 },

      { name: "Roha Special Pizza (Medium)", category: "Pizza", price: 480, quantity: 100 },
      { name: "Roha Special Pizza (Mini)", category: "Pizza", price: 370, quantity: 100 },
      { name: "Chicken Pizza (Large)", category: "Pizza", price: 480, quantity: 100 },
      { name: "Chicken Pizza (Medium)", category: "Pizza", price: 430, quantity: 100 },
      { name: "Chicken Pizza (Mini)", category: "Pizza", price: 340, quantity: 100 },
      { name: "Meat Lover Pizza (Large)", category: "Pizza", price: 450, quantity: 100 },
      { name: "Meat Lover Pizza (Medium)", category: "Pizza", price: 400, quantity: 100 },
      { name: "Meat Lover Pizza (Mini)", category: "Pizza", price: 320, quantity: 100 },
      { name: "Tuna Pizza (Large)", category: "Pizza", price: 450, quantity: 100 },
      { name: "Tuna Pizza (Medium)", category: "Pizza", price: 400, quantity: 100 },
      { name: "Tuna Pizza (Mini)", category: "Pizza", price: 320, quantity: 100 },
      { name: "Tuna with Cheese Pizza (Large)", category: "Pizza", price: 500, quantity: 100 },
      { name: "Tuna with Cheese Pizza (Medium)", category: "Pizza", price: 450, quantity: 100 },
      { name: "Tuna with Cheese Pizza (Mini)", category: "Pizza", price: 350, quantity: 100 },
      { name: "Half Beef / Half Chicken Pizza (Large)", category: "Pizza", price: 480, quantity: 100 },
      { name: "Half Beef / Half Chicken Pizza (Medium)", category: "Pizza", price: 420, quantity: 100 },
      { name: "Half Beef / Half Chicken Pizza (Mini)", category: "Pizza", price: 350, quantity: 100 },
      { name: "Margherita Pizza (Large)", category: "Pizza", price: 450, quantity: 100 },
      { name: "Margherita Pizza (Medium)", category: "Pizza", price: 400, quantity: 100 },
      { name: "Margherita Pizza (Mini)", category: "Pizza", price: 320, quantity: 100 },
      { name: "Special Vegetable Pizza", category: "Pizza", price: 450, quantity: 100 },
      { name: "Vegetable Pizza", category: "Pizza", price: 400, quantity: 100 },
      { name: "Roha Special Combo", category: "Burger", price: 1200, quantity: 100 },
      { name: "Roha Special Burger", category: "Burger", price: 490, quantity: 100 },
      { name: "Mini Special Burger", category: "Burger", price: 390, quantity: 100 },
      { name: "Double Beef Burger", category: "Burger", price: 450, quantity: 100 },
      { name: "Cheese Burger", category: "Burger", price: 380, quantity: 100 },
      { name: "Beef Burger", category: "Burger", price: 350, quantity: 100 },
      { name: "Ham Burger", category: "Burger", price: 350, quantity: 100 },
      { name: "Fasting Burger", category: "Burger", price: 300, quantity: 100 },
      { name: "Roha Special Juice", category: "Juice", price: 200, quantity: 100 },
      { name: "Avocado Juice", category: "Juice", price: 180, quantity: 100 },
      { name: "Mango Juice", category: "Juice", price: 180, quantity: 100 },
      { name: "Papaya Juice", category: "Juice", price: 180, quantity: 100 },
      { name: "Orange Juice", category: "Juice", price: 180, quantity: 100 },
      { name: "Pineapple Juice", category: "Juice", price: 180, quantity: 100 },
      { name: "Roha Special Shake", category: "Shake", price: 250, quantity: 100 },
      { name: "Chocolate Milk Shake", category: "Shake", price: 200, quantity: 100 },
      { name: "Banana Shake", category: "Shake", price: 180, quantity: 100 },
      { name: "Strawberry Shake", category: "Shake", price: 200, quantity: 100 },
      { name: "Avocado Shake", category: "Shake", price: 180, quantity: 100 },
      { name: "Body Builder Shake", category: "Shake", price: 220, quantity: 100 },
      { name: "Smoothies", category: "Shake", price: 200, quantity: 100 },
      { name: "Club Sandwich", category: "Fast Food", price: 370, quantity: 100 },
      { name: "Tuna Sandwich", category: "Fast Food", price: 320, quantity: 100 },
      { name: "Egg Sandwich with Chips", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Vegetable Sandwich", category: "Fast Food", price: 200, quantity: 100 },
      { name: "French Fried", category: "Fast Food", price: 180, quantity: 100 },
      { name: "Shawarma", category: "Fast Food", price: 370, quantity: 100 },
      { name: "Omelette", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Scramble Egg", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Fried Bread with Egg", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Special Fetira", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Fetira", category: "Fast Food", price: 200, quantity: 100 },
      { name: "Chechebsa", category: "Fast Food", price: 200, quantity: 100 },
      { name: "Special Chechebsa", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Special Ful", category: "Fast Food", price: 150, quantity: 100 },
      { name: "Ful", category: "Fast Food", price: 120, quantity: 100 },
      { name: "Chicken Wrap", category: "Fast Food", price: 300, quantity: 100 },
      { name: "Salad Mixed", category: "Fast Food", price: 350, quantity: 100 },
      { name: "Avocado with Salad", category: "Fast Food", price: 300, quantity: 100 },
      { name: "Chikna Tibs", category: "Fast Food", price: 480, quantity: 100 },
      { name: "Tibs Firfir", category: "Fast Food", price: 300, quantity: 100 },
      { name: "Special Tibs Firfir", category: "Fast Food", price: 350, quantity: 100 },
      { name: "Kuanta Firfir", category: "Fast Food", price: 300, quantity: 100 },
      { name: "Shiro", category: "Fast Food", price: 200, quantity: 100 },
      { name: "Special Shiro", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Firfir", category: "Fast Food", price: 200, quantity: 100 },
      { name: "Special Firfir", category: "Fast Food", price: 250, quantity: 100 },
      { name: "Pasta", category: "Fast Food", price: 200, quantity: 100 },
      { name: "Pasta with Meat", category: "Fast Food", price: 280, quantity: 100 },
      { name: "Rice with Meat", category: "Fast Food", price: 280, quantity: 100 },
      { name: "Tea", category: "Hot Drink", price: 40, quantity: 100 },
      { name: "Coffee", category: "Hot Drink", price: 40, quantity: 100 },
      { name: "Espresso", category: "Hot Drink", price: 50, quantity: 100 },
      { name: "Macchiato", category: "Hot Drink", price: 50, quantity: 100 },
      { name: "Milk", category: "Hot Drink", price: 50, quantity: 100 },
      { name: "Nut Tea", category: "Hot Drink", price: 50, quantity: 100 },
      { name: "Soft Drinks", category: "Soft Drink", price: 50, quantity: 100 },
      { name: "1 Liter", category: "Soft Drink", price: 40, quantity: 100 },
      { name: "1/2 Liter", category: "Soft Drink", price: 30, quantity: 100 }
    ];

    let sales = JSON.parse(localStorage.getItem("sales") || "[]");

    function loadInventory(filteredCategory = "All") {
      const tbody = document.querySelector("#inventoryTable tbody");
      tbody.innerHTML = "";

      const productSelect = document.getElementById("productSelect");
      productSelect.innerHTML = "";

      inventory.forEach((item, index) => {
        if (filteredCategory === "All" || item.category === filteredCategory) {
          const row = document.createElement("tr");
          if (item.quantity < 10) row.classList.add("low-stock");
          row.innerHTML = `<td>${item.name}</td><td>${item.category}</td><td>ETB ${item.price}</td><td>${item.quantity}</td>`;
          tbody.appendChild(row);

          const opt = document.createElement("option");
          opt.value = index;
          opt.textContent = `${item.name} (ETB ${item.price})`;
          productSelect.appendChild(opt);
        }
      });
    }

    function filterByCategory() {
      const category = document.getElementById("categorySelect").value;
      loadInventory(category);
    }

    function registerSale() {
      const index = document.getElementById("productSelect").value;
      const quantity = parseInt(document.getElementById("quantityInput").value);
      const date = document.getElementById("saleDate").value;

      if (!date) return alert("Please select a date.");
      if (!quantity || quantity <= 0) return alert("Enter a valid quantity.");

      const product = inventory[index];
      if (product.quantity < quantity) return alert("Not enough stock!");

      product.quantity -= quantity;
      const total = quantity * product.price;

      sales.push({ date, name: product.name, quantity, price: product.price, total });
      localStorage.setItem("sales", JSON.stringify(sales));

      updateSalesReports();
      loadInventory(document.getElementById("categorySelect").value);
    }

    function updateSalesReports() {
      const container = document.getElementById("salesReports");
      container.innerHTML = "";

      const grouped = {};
      sales.forEach(s => {
        if (!grouped[s.date]) grouped[s.date] = [];
        grouped[s.date].push(s);
      });

      for (const [date, salesList] of Object.entries(grouped).sort()) {
        const details = document.createElement("details");
        details.open = true;
        const summary = document.createElement("summary");
        summary.textContent = `Date: ${date}`;
        details.appendChild(summary);

        const table = document.createElement("table");
        table.innerHTML = `
          <thead>
            <tr><th>Product</th><th>Qty</th><th>Price</th><th>Total</th></tr>
          </thead>
        `;
        const tbody = document.createElement("tbody");

        let dailyTotal = 0;
        salesList.forEach(s => {
          dailyTotal += s.total;
          const row = document.createElement("tr");
          row.innerHTML = `<td>${s.name}</td><td>${s.quantity}</td><td>ETB ${s.price}</td><td>ETB ${s.total}</td>`;
          tbody.appendChild(row);
        });

        const totalRow = document.createElement("tr");
        totalRow.innerHTML = `<td colspan="3"><strong>Total</strong></td><td><strong>ETB ${dailyTotal}</strong></td>`;
        tbody.appendChild(totalRow);

        table.appendChild(tbody);
        details.appendChild(table);
        container.appendChild(details);
      }
    }

    document.getElementById("saleDate").valueAsDate = new Date();
    loadInventory();
    updateSalesReports();
  </script>
</body>
</html>
