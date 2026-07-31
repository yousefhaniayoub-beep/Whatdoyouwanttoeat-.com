# Whatdoyouwanttoeat-.com
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Food Swiper App</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>
  <style>
    /* Prevent default mobile drag/scroll behavior */
    html, body {
      height: 100%;
      margin: 0;
      padding: 0;
      overflow: hidden;
      touch-action: pan-y;
      user-select: none;
      -webkit-user-select: none;
      background-color: #000;
    }

    .card {
      transition: transform 0.2s ease, opacity 0.2s ease;
      will-change: transform, opacity;
    }

    .card.swiping {
      transition: none;
    }

    /* Smooth fade animation for action buttons */
    .hint-fade {
      transition: opacity 0.6s ease-in-out, transform 0.6s ease-in-out;
    }

    /* Custom scrollbar for modals */
    ::-webkit-scrollbar {
      width: 4px;
    }
    ::-webkit-scrollbar-thumb {
      background: rgba(255, 255, 255, 0.2);
      border-radius: 4px;
    }
  </style>
</head>
<body class="bg-black text-gray-800 font-sans">

  <!-- Full-Screen App Container -->
  <div class="w-full h-[100dvh] relative overflow-hidden max-w-md mx-auto bg-black">
    
    <!-- SIDE DRAWER BACKDROP OVERLAY -->
    <div id="drawer-backdrop" class="fixed inset-0 bg-black/60 backdrop-blur-sm z-40 opacity-0 pointer-events-none transition-opacity duration-300 max-w-md mx-auto"></div>

    <!-- 1/3 SCREEN SIDE DRAWER MENU -->
    <aside id="side-drawer" class="fixed top-0 left-0 bottom-0 w-[38%] min-w-[130px] max-w-[170px] bg-gray-900/95 backdrop-blur-xl border-r border-white/10 z-50 transform -translate-x-full transition-transform duration-300 ease-in-out flex flex-col justify-between p-3 text-white">
      
      <!-- TOP MENU SECTION -->
      <div>
        <!-- Account Option -->
        <button class="w-full flex items-center space-x-2 p-2 rounded-xl hover:bg-white/10 active:bg-white/20 transition text-left">
          <i data-lucide="user" class="w-4 h-4 text-orange-400 shrink-0 pointer-events-none"></i>
          <span class="text-xs font-bold truncate">Account</span>
        </button>

        <hr class="my-3 border-white/10">

        <!-- FOOD CATEGORY FILTERS (Middle Section) -->
        <div class="space-y-1">
          <p class="text-[10px] font-bold uppercase tracking-wider text-gray-400 px-2 mb-1">Categories</p>
          
          <button data-category="all" class="category-btn w-full flex items-center justify-between p-2 rounded-lg bg-orange-500 text-white text-xs font-semibold transition">
            <span class="truncate">All Foods</span>
            <span class="text-[10px]">🍽️</span>
          </button>
          
          <button data-category="chicken" class="category-btn w-full flex items-center justify-between p-2 rounded-lg hover:bg-white/10 text-gray-300 text-xs font-medium transition">
            <span class="truncate">Chicken</span>
            <span class="text-[10px]">🍗</span>
          </button>

          <button data-category="meat" class="category-btn w-full flex items-center justify-between p-2 rounded-lg hover:bg-white/10 text-gray-300 text-xs font-medium transition">
            <span class="truncate">Meat</span>
            <span class="text-[10px]">🥩</span>
          </button>

          <button data-category="burger" class="category-btn w-full flex items-center justify-between p-2 rounded-lg hover:bg-white/10 text-gray-300 text-xs font-medium transition">
            <span class="truncate">Burger</span>
            <span class="text-[10px]">🍔</span>
          </button>

          <button data-category="pizza" class="category-btn w-full flex items-center justify-between p-2 rounded-lg hover:bg-white/10 text-gray-300 text-xs font-medium transition">
            <span class="truncate">Pizza</span>
            <span class="text-[10px]">🍕</span>
          </button>

          <button data-category="arabic" class="category-btn w-full flex items-center justify-between p-2 rounded-lg hover:bg-white/10 text-gray-300 text-xs font-medium transition">
            <span class="truncate">Arabic</span>
            <span class="text-[10px]">🥙</span>
          </button>
        </div>
      </div>

      <!-- BOTTOM MENU SECTION -->
      <div class="space-y-1 pt-2 border-t border-white/10">
        <button class="w-full flex items-center space-x-2 p-2 rounded-lg hover:bg-white/10 text-gray-300 hover:text-white text-xs transition">
          <i data-lucide="mail" class="w-3.5 h-3.5 text-gray-400 shrink-0 pointer-events-none"></i>
          <span class="truncate">Contact us</span>
        </button>

        <button class="w-full flex items-center space-x-2 p-2 rounded-lg hover:bg-white/10 text-gray-300 hover:text-white text-xs transition">
          <i data-lucide="alert-circle" class="w-3.5 h-3.5 text-gray-400 shrink-0 pointer-events-none"></i>
          <span class="truncate">Report problem</span>
        </button>

        <button class="w-full flex items-center space-x-2 p-2 rounded-lg hover:bg-white/10 text-gray-300 hover:text-white text-xs transition">
          <i data-lucide="settings" class="w-3.5 h-3.5 text-gray-400 shrink-0 pointer-events-none"></i>
          <span class="truncate">Settings</span>
        </button>
      </div>

    </aside>

    <!-- NO MATCHES EMPTY STATE -->
    <div id="empty-state" class="absolute inset-0 z-0 flex flex-col items-center justify-center p-8 text-center bg-gray-950 text-white hidden">
      <div class="w-20 h-20 rounded-full bg-white/5 border border-white/10 flex items-center justify-center text-4xl mb-4">
        🔍
      </div>
      <h3 class="text-xl font-bold mb-2">No Foods Found</h3>
      <p class="text-xs text-gray-400 max-w-xs mb-6">
        No restaurants match your current Location and Food Preferences. Try selecting another region or adding more food preferences!
      </p>
      <button id="reset-filters-btn" class="px-5 py-2.5 bg-orange-500 hover:bg-orange-600 text-white font-bold text-xs rounded-xl shadow-lg transition">
        Reset All Filters
      </button>
    </div>

    <!-- FULL-SCREEN SWIPABLE CARD -->
    <div id="swiper-card" class="card absolute inset-0 w-full h-full overflow-hidden cursor-grab active:cursor-grabbing z-10">
      
      <!-- Full Screen Food Image -->
      <img id="card-image" src="" alt="Food" class="w-full h-full object-cover pointer-events-none">
      
      <!-- Overlay Gradients for readability -->
      <div class="absolute inset-0 bg-gradient-to-b from-black/80 via-transparent to-black/90 pointer-events-none"></div>

      <!-- Image Indicator Dots at top -->
      <div id="image-dots" class="absolute top-16 left-0 right-0 flex justify-center space-x-1.5 px-8 z-10 pointer-events-none">
        <!-- Dots dynamically populated -->
      </div>

      <!-- Cuisine Tag Overlay -->
      <div class="absolute top-24 left-6 z-10 pointer-events-none">
        <span id="card-cuisine" class="text-xs font-bold uppercase tracking-wider bg-orange-500/90 text-white px-3 py-1 rounded-full shadow-lg backdrop-blur-md"></span>
      </div>
    </div>

    <!-- TOP BAR OVERLAY -->
    <header class="absolute top-0 left-0 right-0 w-full px-4 pt-6 pb-2 flex items-center justify-between z-20 pointer-events-auto">
      
      <!-- Top Left Group: Hamburger Menu + 😋 Button -->
      <div class="flex items-center space-x-2">
        <!-- 3 Horizontal Line Small Menu Button -->
        <button id="btn-hamburger" class="w-10 h-10 rounded-full bg-black/40 backdrop-blur-md border border-white/20 text-white shadow-md active:scale-95 transition flex items-center justify-center">
          <i data-lucide="menu" class="w-5 h-5 pointer-events-none"></i>
        </button>

        <!-- Top Left Button Container (😋 Emoji / Preferences) -->
        <div class="relative flex flex-col items-center">
          <!-- Fade-in label above button -->
          <span id="label-pref" class="absolute -top-6 left-1/2 -translate-x-1/2 text-[11px] font-medium text-white/90 bg-black/70 px-2 py-0.5 rounded-md border border-white/10 shadow backdrop-blur-md transition-opacity duration-300 opacity-0 pointer-events-none whitespace-nowrap">
            my Preference
          </span>
          <button id="btn-pref" class="w-10 h-10 rounded-full bg-black/40 backdrop-blur-md border border-white/20 shadow-md active:scale-95 transition flex items-center justify-center text-lg">
            😋
          </button>
        </div>
      </div>

      <!-- Center: Restaurant Name Badge -->
      <div class="px-4 py-2 bg-black/50 backdrop-blur-md border border-white/20 shadow-lg rounded-2xl">
        <h2 id="header-restaurant-name" class="font-bold text-white text-sm tracking-wide text-center truncate max-w-[130px]">
          Loading...
        </h2>
      </div>

      <!-- Top Right Button Container (Location) -->
      <div class="relative flex flex-col items-center">
        <!-- Fade-in label above button -->
        <span id="label-loc" class="absolute -top-6 left-1/2 -translate-x-1/2 text-[11px] font-medium text-white/90 bg-black/70 px-2 py-0.5 rounded-md border border-white/10 shadow backdrop-blur-md transition-opacity duration-300 opacity-0 pointer-events-none whitespace-nowrap">
          Location
        </span>
        <button id="btn-loc" class="w-10 h-10 rounded-full bg-black/40 backdrop-blur-md text-red-400 border border-white/20 shadow-md active:scale-95 transition flex items-center justify-center">
          <i data-lucide="map-pin" class="w-5 h-5 pointer-events-none"></i>
        </button>
      </div>

    </header>

    <!-- DRAG OVERLAY INDICATORS (X and Check) -->
    <div id="badge-left" class="absolute left-8 top-1/2 -translate-y-1/2 flex flex-col items-center opacity-0 transition-opacity duration-150 pointer-events-none z-30">
      <div class="w-24 h-24 rounded-full border-4 border-red-500 flex items-center justify-center text-red-500 font-black text-5xl shadow-2xl bg-black/80 backdrop-blur-md">
        ✕
      </div>
    </div>

    <div id="badge-right" class="absolute right-8 top-1/2 -translate-y-1/2 flex flex-col items-center opacity-0 transition-opacity duration-150 pointer-events-none z-30">
      <div class="w-24 h-24 rounded-full border-4 border-green-500 flex items-center justify-center text-green-500 font-black text-5xl shadow-2xl bg-black/80 backdrop-blur-md">
        ✓
      </div>
    </div>

    <!-- BOTTOM CONTROLS OVERLAY -->
    <footer class="absolute bottom-0 left-0 right-0 w-full px-6 pb-4 pt-2 flex flex-col items-center z-20 pointer-events-auto">
      
      <!-- Timed Action & Direction Buttons -->
      <div id="hint-buttons-container" class="hint-fade opacity-0 translate-y-2 pointer-events-none flex items-center justify-between w-full max-w-xs px-2 mb-3">
        
        <!-- Left Swipe / Reject Button -->
        <div class="flex items-center space-x-2">
          <i data-lucide="arrow-left" class="w-6 h-6 text-red-400 animate-pulse pointer-events-none"></i>
          <button id="btn-reject" class="w-16 h-16 rounded-full bg-black/60 border border-red-500/50 text-red-400 backdrop-blur-md shadow-xl hover:bg-red-500/20 active:scale-90 transition flex items-center justify-center">
            <i data-lucide="x" class="w-9 h-9 stroke-[3] pointer-events-none"></i>
          </button>
        </div>

        <!-- Right Swipe / Accept Button -->
        <div class="flex items-center space-x-2">
          <button id="btn-accept" class="w-16 h-16 rounded-full bg-gradient-to-r from-emerald-400 to-green-500 text-white shadow-xl shadow-green-500/30 hover:opacity-95 active:scale-90 transition flex items-center justify-center">
            <i data-lucide="check" class="w-9 h-9 stroke-[3] pointer-events-none"></i>
          </button>
          <i data-lucide="arrow-right" class="w-6 h-6 text-green-400 animate-pulse pointer-events-none"></i>
        </div>

      </div>

      <!-- Bottom Hint Text -->
      <h3 class="text-xs sm:text-sm font-medium text-white/80 tracking-wider italic text-center drop-shadow-md">
        What do you feel like eating?
      </h3>

    </footer>

    <!-- ========================================================= -->
    <!-- LOCATION SELECTION MODAL                                   -->
    <!-- ========================================================= -->
    <div id="location-modal" class="fixed inset-0 bg-black/70 backdrop-blur-md z-50 transform translate-y-full transition-transform duration-300 ease-out flex flex-col justify-end max-w-md mx-auto pointer-events-none">
      <div class="bg-gray-900 border-t border-white/10 rounded-t-3xl p-6 text-white pointer-events-auto">
        <div class="flex justify-between items-center mb-4">
          <div>
            <h3 class="text-lg font-bold flex items-center">
              <i data-lucide="map-pin" class="w-5 h-5 text-red-400 mr-2 pointer-events-none"></i> Select Location
            </h3>
            <p class="text-xs text-gray-400">Only options in this region will be shown</p>
          </div>
          <button id="close-location-modal" class="p-2 rounded-full bg-white/10 hover:bg-white/20 active:scale-95 transition flex items-center justify-center cursor-pointer">
            <i data-lucide="x" class="w-5 h-5 pointer-events-none"></i>
          </button>
        </div>

        <!-- Location List -->
        <div id="location-options-list" class="space-y-2 mb-6">
          <!-- Dynamically generated -->
        </div>

        <button id="apply-location-btn" class="w-full py-3.5 bg-orange-500 font-bold rounded-xl text-white text-sm shadow-lg active:scale-95 transition">
          Confirm Location
        </button>
      </div>
    </div>

    <!-- ========================================================= -->
    <!-- 😋 FOOD PREFERENCES MODAL                                 -->
    <!-- ========================================================= -->
    <div id="pref-modal" class="fixed inset-0 bg-black/70 backdrop-blur-md z-50 transform translate-y-full transition-transform duration-300 ease-out flex flex-col justify-end max-w-md mx-auto pointer-events-none">
      <div class="bg-gray-900 border-t border-white/10 rounded-t-3xl p-6 text-white pointer-events-auto max-h-[85vh] flex flex-col justify-between">
        
        <div>
          <div class="flex justify-between items-center mb-2">
            <h3 class="text-lg font-bold flex items-center">
              <span class="mr-2 text-xl">😋</span> Food Preferences
            </h3>
            <button id="close-pref-modal" class="p-2 rounded-full bg-white/10 hover:bg-white/20 active:scale-95 transition flex items-center justify-center cursor-pointer">
              <i data-lucide="x" class="w-5 h-5 pointer-events-none"></i>
            </button>
          </div>
          <p class="text-xs text-gray-400 mb-4">
            Selected food types will be recommended in "All Foods". Unselected ones will be excluded. If nothing is selected, everything is recommended!
          </p>

          <!-- Preferences List with Check Circles -->
          <div id="pref-options-list" class="space-y-2 max-h-[45vh] overflow-y-auto pr-1">
            <!-- Dynamically populated -->
          </div>
        </div>

        <div class="pt-4 mt-2 border-t border-white/10 flex items-center space-x-3">
          <button id="clear-pref-btn" class="w-1/3 py-3.5 bg-white/10 hover:bg-white/20 text-gray-300 font-bold rounded-xl text-xs transition">
            Clear All
          </button>
          <button id="apply-pref-btn" class="w-2/3 py-3.5 bg-orange-500 font-bold rounded-xl text-white text-sm shadow-lg active:scale-95 transition">
            Save Preferences
          </button>
        </div>

      </div>
    </div>

    <!-- RESTAURANT DETAIL MODAL (Opens on Swipe Right / Check) -->
    <div id="detail-modal" class="fixed inset-0 bg-gray-900 z-50 transform translate-y-full transition-transform duration-300 ease-out flex flex-col overflow-y-auto max-w-md mx-auto">
      
      <!-- Modal Header -->
      <div class="relative h-72 w-full bg-gray-800">
        <img id="modal-hero-image" src="" class="w-full h-full object-cover">
        <div class="absolute inset-0 bg-gradient-to-t from-gray-900 via-transparent to-black/40"></div>
        <button id="close-modal" class="absolute top-4 right-4 w-10 h-10 rounded-full bg-black/60 text-white flex items-center justify-center backdrop-blur-md border border-white/10 active:scale-95 transition">
          <i data-lucide="x" class="w-6 h-6 pointer-events-none"></i>
        </button>
      </div>

      <!-- Modal Body -->
      <div class="p-6 flex-1 flex flex-col justify-between -mt-6 bg-gray-900 rounded-t-3xl relative z-10 text-white">
        <div>
          <div class="flex justify-between items-start mb-2">
            <h2 id="modal-title" class="text-3xl font-black text-white"></h2>
            <span id="modal-rating" class="flex items-center bg-yellow-500/20 border border-yellow-500/40 text-yellow-400 text-sm font-bold px-3 py-1 rounded-xl">
              ★ 4.8
            </span>
          </div>

          <p id="modal-address" class="text-sm text-gray-400 flex items-center mb-6">
            <i data-lucide="map-pin" class="w-4 h-4 mr-1.5 text-red-400 inline pointer-events-none"></i> 
            <span id="modal-address-text"></span>
          </p>

          <hr class="my-4 border-gray-800">

          <!-- Popular Menu Items -->
          <h3 class="text-lg font-bold text-white mb-4">Popular Menu</h3>
          <div id="modal-menu-list" class="space-y-3">
            <!-- Dynamically populated menu items -->
          </div>
        </div>

        <!-- Call to Action -->
        <button id="modal-directions-btn" class="mt-8 w-full py-4 bg-gradient-to-r from-emerald-500 to-green-600 text-white font-bold rounded-2xl shadow-lg shadow-green-500/20 active:scale-95 transition flex items-center justify-center space-x-2">
          <i data-lucide="navigation" class="w-5 h-5 pointer-events-none"></i>
          <span>Get Directions</span>
        </button>
      </div>
    </div>

  </div>

  <script>
    // Sample Data with Regions/Countries and Food Tags
    const RESTAURANTS = [
      {
        id: 1,
        name: "Burger Craft",
        cuisine: "American • Burgers",
        tags: ["burger", "meat"],
        region: "USA",
        rating: "4.9",
        address: "124 Culinary Way, Downtown",
        photos: [
          "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?auto=format&fit=crop&w=800&q=80",
          "https://images.unsplash.com/photo-1550547660-d9450f859349?auto=format&fit=crop&w=800&q=80"
        ],
        menu: [
          { name: "Double Smoked Bacon Cheeseburger", price: "$14.99" },
          { name: "Truffle Parmesan Fries", price: "$6.50" }
        ]
      },
      {
        id: 2,
        name: "Crispy Chicks",
        cuisine: "Fast Food • Chicken",
        tags: ["chicken"],
        region: "Kuwait"
