<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Image Import and Sales Dashboard</title>
<script src="https://cdn.tailwindcss.com"></script>
<script>
  tailwind.config = {
    theme: {
      extend: {
        colors: {
          'custom-green': '#10B981',
        }
      }
    }
  }
</script>
<style>
  .sticky-footer {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    background: white;
    padding: 1rem;
    box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.1);
    z-index: 10;
  }
  .item-container {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 1rem;
    padding-bottom: 4rem; /* Space for the sticky footer */
  }
  .item-container::-webkit-scrollbar {
    width: 2px;
  }
  .item-container::-webkit-scrollbar-thumb {
    background-color: #a0aec0;
  }
  .sales-tab {
    transition: transform 0.3s ease-in-out;
  }
  .hidden-tab {
    transform: translateY(-100%);
  }
</style>
</head>
<body class="bg-gray-50">
<div class="container mx-auto p-4">
  <div class="flex justify-between items-center">
    <h1 class="text-2xl font-bold mb-4 text-gray-800">Importing an image file from a computer</h1>
    <button id="resetButton" class="bg-red-500 text-white px-4 py-2 rounded shadow" onclick="resetAll()">Reset</button>
  </div>
  <div id="items" class="item-container">
    <!-- Item template will be cloned here -->
  </div>
  <div class="sticky-footer flex justify-between items-center">
    <div>
      <p>Total Price: <span id="totalPrice">₩0</span></p>
    </div>
    <div class="flex">
      <button id="addItem" class="bg-custom-green text-white px-4 py-2 rounded shadow mr-2" onclick="addItem()">Add Item</button>
      <input type="text" id="currentSales" placeholder="current sales" class="border p-2 rounded mr-2" readonly />
      <button id="okButton" class="bg-blue-500 text-white px-4 py-2 rounded shadow" onclick="finalizeSales()">ok</button>
    </div>
  </div>
  <div id="salesTab" class="sales-tab hidden-tab fixed bg-white p-4 shadow-lg rounded-lg text-lg font-semibold text-center" style="width: 200px; right: 1rem; top: 1rem;">
    Final Sales: <span id="finalSales">₩0</span>
  </div>
</div>

<script>
  let items = [];
  let totalSales = 0;

  function updateTotalPrice() {
    const totalPrice = items.reduce((acc, item) => acc + (item.count * item.price), 0);
    document.getElementById('totalPrice').textContent = `₩${totalPrice}`;
  }

  function updateCount(index, delta) {
    const item = items[index];
    item.count = Math.max(0, item.count + delta);
    document.getElementById(`count${index}`).textContent = item.count;
    updateTotalPrice();
  }

  function updatePrice(index, value) {
    const item = items[index];
    item.price = parseInt(value) || 0;
    updateTotalPrice();
  }

  function addItem() {
    const index = items.length;
    items.push({ count: 0, price: 0 });

    const itemContainer = document.createElement('div');
    itemContainer.className = 'flex flex-col items-center p-4 bg-white rounded shadow';
    itemContainer.innerHTML = `
      <input type="file" accept="image/*" class="mb-2" onchange="loadImage(event, ${index})" />
      <div id="imageContainer${index}" class="w-full h-32 bg-gray-300 mb-2"></div>
      <input type="number" placeholder="Price" class="border p-2 rounded mb-2" oninput="updatePrice(${index}, this.value)" />
      <div class="flex items-center">
        <button class="bg-red-500 text-white px-2" onclick="updateCount(${index}, -1)">-</button>
        <span id="count${index}" class="px-4">0</span>
        <button class="bg-green-500 text-white px-2" onclick="updateCount(${index}, 1)">+</button>
      </div>
    `;
    document.getElementById('items').appendChild(itemContainer);
  }

  function loadImage(event, index) {
    const reader = new FileReader();
    reader.onload = function(){
      const output = document.getElementById(`imageContainer${index}`);
      output.style.backgroundImage = `url(${reader.result})`;
      output.style.backgroundSize = 'cover';
      output.style.backgroundPosition = 'center';
    };
    reader.readAsDataURL(event.target.files[0]);
  }

  function resetAll() {
    items.forEach(item => {
      item.count = 0;
    });
    document.querySelectorAll('[id^="count"]').forEach(el => el.textContent = '0');
    updateTotalPrice();
  }

  function finalizeSales() {
    totalSales += parseInt(document.getElementById('totalPrice').textContent.replace('₩', ''));
    document.getElementById('finalSales').textContent = `₩${totalSales}`;
    document.getElementById('currentSales').value = `₩${totalSales}`;
    resetAll();
    toggleSalesTab();
  }

  function toggleSalesTab() {
    const salesTab = document.getElementById('salesTab');
    salesTab.classList.toggle('hidden-tab');
  }

  // Initialize with one item
  addItem();
</script>
</body>
</html>
