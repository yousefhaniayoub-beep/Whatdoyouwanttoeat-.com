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
        region: "Kuwait",
        rating: "4.8",
        address: "12 Grand Avenue, Kuwait City",
        photos: [
          "https://images.unsplash.com/photo-1626645738196-c2a7c87a8f58?auto=format&fit=crop&w=800&q=80",
          "https://images.unsplash.com/photo-1562967914-608f82629710?auto=format&fit=crop&w=800&q=80"
        ],
        menu: [
          { name: "Crispy Fried Chicken Bucket", price: "KWD 5.500" },
          { name: "Spicy Chicken Tender Wrap", price: "KWD 2.250" }
        ]
      },
      {
        id: 3,
        name: "Al-Safa Grill",
        cuisine: "Middle Eastern • Arabic",
        tags: ["arabic", "meat", "chicken"],
        region: "Kuwait",
        rating: "4.9",
        address: "44 Oasis Blvd, Salmiya",
        photos: [
          "https://images.unsplash.com/photo-1529006557810-274b9b2fc783?auto=format&fit=crop&w=800&q=80",
          "https://images.unsplash.com/photo-1555939594-58d7cb561ad1?auto=format&fit=crop&w=800&q=80"
        ],
        menu: [
          { name: "Mixed Lamb & Chicken Grill", price: "KWD 6.750" },
          { name: "Chicken Shawarma Platter", price: "KWD 3.500" }
        ]
      },
      {
        id: 4,
        name: "Pizza Express",
        cuisine: "Italian • Pizza",
        tags: ["pizza"],
        region: "UAE",
        rating: "4.7",
        address: "Dubai Marina Promenade",
        photos: [
          "https://images.unsplash.com/photo-1513104890138-7c749659a591?auto=format&fit=crop&w=800&q=80",
          "https://images.unsplash.com/photo-1604382354936-07c5d9983bd3?auto=format&fit=crop&w=800&q=80"
        ],
        menu: [
          { name: "Wood-fired Margherita Pizza", price: "AED 55.00" },
          { name: "Double Pepperoni Feast", price: "AED 65.00" }
        ]
      },
      {
        id: 5,
        name: "Prime Steakhouse",
        cuisine: "Steakhouse • Meat",
        tags: ["meat"],
        region: "USA",
        rating: "4.9",
        address: "88 Steak House Lane, NYC",
        photos: [
          "https://images.unsplash.com/photo-1544025162-d76694265947?auto=format&fit=crop&w=800&q=80",
          "https://images.unsplash.com/photo-1558030006-450675393462?auto=format&fit=crop&w=800&q=80"
        ],
        menu: [
          { name: "Ribeye Steak (12oz)", price: "$32.00" },
          { name: "Grilled Beef Tenderloin", price: "$29.00" }
        ]
      },
      {
        id: 6,
        name: "Tokyo Roll",
        cuisine: "Japanese • Sushi",
        tags: ["sushi"],
        region: "UAE",
        rating: "4.8",
        address: "JBR Walk, Dubai",
        photos: [
          "https://images.unsplash.com/photo-1579871494447-9811cf80d66c?auto=format&fit=crop&w=800&q=80",
          "https://images.unsplash.com/photo-1611143669185-af224c5e3252?auto=format&fit=crop&w=800&q=80"
        ],
        menu: [
          { name: "Dragon Salmon Roll", price: "AED 58.00" },
          { name: "Fresh Sashimi Set", price: "AED 72.00" }
        ]
      }
    ];

    // AVAILABLE FOOD PREFERENCES
    const FOOD_TYPES = [
      { id: 'chicken', name: 'Chicken', icon: '🍗' },
      { id: 'meat', name: 'Meat & Steak', icon: '🥩' },
      { id: 'burger', name: 'Burgers', icon: '🍔' },
      { id: 'pizza', name: 'Pizza', icon: '🍕' },
      { id: 'arabic', name: 'Arabic Food', icon: '🥙' },
      { id: 'sushi', name: 'Sushi', icon: '🍣' }
    ];

    // AVAILABLE LOCATIONS
    const LOCATIONS = [
      { id: 'all', name: 'All Locations (Global)', flag: '🌐' },
      { id: 'Kuwait', name: 'Kuwait', flag: '🇰🇼' },
      { id: 'USA', name: 'United States', flag: '🇺🇸' },
      { id: 'UAE', name: 'United Arab Emirates', flag: '🇦🇪' }
    ];

    // APP STATE
    let selectedLocation = 'all'; 
    let selectedPreferences = new Set();
    let currentCategory = 'all'; 
    let currentIndex = 0;
    let currentPhotoIndex = 0;

    // DOM Elements
    const card = document.getElementById('swiper-card');
    const cardImg = document.getElementById('card-image');
    const headerName = document.getElementById('header-restaurant-name');
    const cardCuisine = document.getElementById('card-cuisine');
    const imageDotsContainer = document.getElementById('image-dots');
    const badgeLeft = document.getElementById('badge-left');
    const badgeRight = document.getElementById('badge-right');
    const modal = document.getElementById('detail-modal');
    const hintButtons = document.getElementById('hint-buttons-container');
    const emptyState = document.getElementById('empty-state');
    
    const sideDrawer = document.getElementById('side-drawer');
    const drawerBackdrop = document.getElementById('drawer-backdrop');
    const btnHamburger = document.getElementById('btn-hamburger');

    const locationModal = document.getElementById('location-modal');
    const prefModal = document.getElementById('pref-modal');

    // -------------------------------------------------------------
    // FILTER ENGINE
    // -------------------------------------------------------------
    function getFilteredRestaurants() {
      return RESTAURANTS.filter(item => {
        if (selectedLocation !== 'all' && item.region !== selectedLocation) {
          return false;
        }

        if (currentCategory !== 'all') {
          return item.tags.includes(currentCategory);
        }

        if (selectedPreferences.size > 0) {
          const matchesPref = item.tags.some(tag => selectedPreferences.has(tag));
          if (!matchesPref) return false;
        }

        return true;
      });
    }

    // -------------------------------------------------------------
    // LOCATION MODAL LOGIC
    // -------------------------------------------------------------
    function openLocationModal() {
      renderLocationModal();
      locationModal.classList.remove('translate-y-full', 'pointer-events-none');
    }

    function closeLocationModal() {
      locationModal.classList.add('translate-y-full', 'pointer-events-none');
    }

    function renderLocationModal() {
      const container = document.getElementById('location-options-list');
      container.innerHTML = LOCATIONS.map(loc => `
        <button data-loc-id="${loc.id}" class="location-option-btn w-full flex items-center justify-between p-3.5 rounded-2xl border ${selectedLocation === loc.id ? 'bg-orange-500/20 border-orange-500 text-white' : 'bg-gray-800/60 border-white/5 text-gray-300'} transition">
          <div class="flex items-center space-x-3 pointer-events-none">
            <span class="text-xl">${loc.flag}</span>
            <span class="font-bold text-sm">${loc.name}</span>
          </div>
          <div class="w-5 h-5 rounded-full border-2 flex items-center justify-center pointer-events-none ${selectedLocation === loc.id ? 'border-orange-500 bg-orange-500' : 'border-gray-500'}">
            ${selectedLocation === loc.id ? '<div class="w-2 h-2 rounded-full bg-white"></div>' : ''}
          </div>
        </button>
      `).join('');

      document.querySelectorAll('.location-option-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          selectedLocation = btn.getAttribute('data-loc-id');
          renderLocationModal();
        });
      });
    }

    document.getElementById('btn-loc').addEventListener('click', openLocationModal);
    document.getElementById('close-location-modal').addEventListener('click', (e) => {
      e.stopPropagation();
      closeLocationModal();
    });

    // Close on backdrop tap
    locationModal.addEventListener('click', (e) => {
      if (e.target === locationModal) closeLocationModal();
    });

    document.getElementById('apply-location-btn').addEventListener('click', () => {
      closeLocationModal();
      currentIndex = 0;
      loadRestaurant(currentIndex);
    });

    // -------------------------------------------------------------
    // 😋 PREFERENCES MODAL LOGIC
    // -------------------------------------------------------------
    function openPrefModal() {
      renderPrefModal();
      prefModal.classList.remove('translate-y-full', 'pointer-events-none');
    }

    function closePrefModal() {
      prefModal.classList.add('translate-y-full', 'pointer-events-none');
    }

    function renderPrefModal() {
      const container = document.getElementById('pref-options-list');
      container.innerHTML = FOOD_TYPES.map(food => {
        const isSelected = selectedPreferences.has(food.id);
        return `
          <div data-food-id="${food.id}" class="pref-option-item flex items-center justify-between p-3.5 rounded-2xl bg-gray-800/60 border border-white/5 cursor-pointer hover:bg-gray-800 transition">
            <div class="flex items-center space-x-3 pointer-events-none">
              <span class="text-2xl">${food.icon}</span>
              <span class="font-bold text-sm text-white">${food.name}</span>
            </div>
            
            <div class="w-6 h-6 rounded-full border-2 transition-all flex items-center justify-center pointer-events-none ${isSelected ? 'bg-orange-500 border-orange-500 shadow-lg shadow-orange-500/40' : 'border-white/30 bg-transparent'}">
              ${isSelected ? '<i data-lucide="check" class="w-4 h-4 text-white stroke-[3]"></i>' : ''}
            </div>
          </div>
        `;
      }).join('');

      lucide.createIcons();

      document.querySelectorAll('.pref-option-item').forEach(item => {
        item.addEventListener('click', () => {
          const foodId = item.getAttribute('data-food-id');
          if (selectedPreferences.has(foodId)) {
            selectedPreferences.delete(foodId);
          } else {
            selectedPreferences.add(foodId);
          }
          renderPrefModal();
        });
      });
    }

    document.getElementById('btn-pref').addEventListener('click', openPrefModal);
    document.getElementById('close-pref-modal').addEventListener('click', (e) => {
      e.stopPropagation();
      closePrefModal();
    });

    // Close on backdrop tap
    prefModal.addEventListener('click', (e) => {
      if (e.target === prefModal) closePrefModal();
    });

    document.getElementById('clear-pref-btn').addEventListener('click', () => {
      selectedPreferences.clear();
      renderPrefModal();
    });

    document.getElementById('apply-pref-btn').addEventListener('click', () => {
      closePrefModal();
      currentIndex = 0;
      loadRestaurant(currentIndex);
    });

    // Reset All Filters button from Empty State
    document.getElementById('reset-filters-btn').addEventListener('click', () => {
      selectedLocation = 'all';
      selectedPreferences.clear();
      currentCategory = 'all';
      currentIndex = 0;
      loadRestaurant(currentIndex);
    });

    // -------------------------------------------------------------
    // SIDE DRAWER LOGIC
    // -------------------------------------------------------------
    function openDrawer() {
      sideDrawer.classList.remove('-translate-x-full');
      drawerBackdrop.classList.remove('opacity-0', 'pointer-events-none');
    }

    function closeDrawer() {
      sideDrawer.classList.add('-translate-x-full');
      drawerBackdrop.classList.add('opacity-0', 'pointer-events-none');
    }

    btnHamburger.addEventListener('click', openDrawer);
    drawerBackdrop.addEventListener('click', closeDrawer);

    document.querySelectorAll('.category-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        currentCategory = btn.getAttribute('data-category');

        document.querySelectorAll('.category-btn').forEach(b => {
          b.className = "category-btn w-full flex items-center justify-between p-2 rounded-lg hover:bg-white/10 text-gray-300 text-xs font-medium transition";
        });
        btn.className = "category-btn w-full flex items-center justify-between p-2 rounded-lg bg-orange-500 text-white text-xs font-semibold transition";

        currentIndex = 0;
        loadRestaurant(currentIndex);
        closeDrawer();
      });
    });

    // -------------------------------------------------------------
    // HOLD-TO-SHOW LABEL LOGIC FOR TOP BUTTONS
    // -------------------------------------------------------------
    function setupHoldLabel(buttonId, labelId) {
      const btn = document.getElementById(buttonId);
      const label = document.getElementById(labelId);

      const show = () => {
        label.classList.remove('opacity-0');
        label.classList.add('opacity-100');
      };

      const hide = () => {
        label.classList.remove('opacity-100');
        label.classList.add('opacity-0');
      };

      btn.addEventListener('mousedown', show);
      btn.addEventListener('mouseup', hide);
      btn.addEventListener('mouseleave', hide);

      btn.addEventListener('touchstart', show, { passive: true });
      btn.addEventListener('touchend', hide);
      btn.addEventListener('touchcancel', hide);
    }

    setupHoldLabel('btn-pref', 'label-pref');
    setupHoldLabel('btn-loc', 'label-loc');

    // -------------------------------------------------------------
    // TIMED BUTTON HINT LOOP LOGIC
    // -------------------------------------------------------------
    let hintCycleTimeout = null;
    let hintHideTimeout = null;

    function showHints() {
      hintButtons.classList.remove('opacity-0', 'translate-y-2', 'pointer-events-none');
      hintButtons.classList.add('opacity-100', 'translate-y-0', 'pointer-events-auto');

      clearTimeout(hintHideTimeout);
      hintHideTimeout = setTimeout(() => {
        hideHints();
      }, 3000);
    }

    function hideHints() {
      hintButtons.classList.remove('opacity-100', 'translate-y-0', 'pointer-events-auto');
      hintButtons.classList.add('opacity-0', 'translate-y-2', 'pointer-events-none');

      clearTimeout(hintCycleTimeout);
      hintCycleTimeout = setTimeout(() => {
        showHints();
      }, 4000);
    }

    function resetHintTimer() {
      clearTimeout(hintHideTimeout);
      clearTimeout(hintCycleTimeout);
      hideHints();
    }

    setTimeout(showHints, 1000);

    // -------------------------------------------------------------
    // SWIPER & CARD LOGIC
    // -------------------------------------------------------------
    function loadRestaurant(index) {
      const list = getFilteredRestaurants();

      if (list.length === 0) {
        card.classList.add('hidden');
        emptyState.classList.remove('hidden');
        headerName.textContent = "No Matches";
        return;
      } else {
        card.classList.remove('hidden');
        emptyState.classList.add('hidden');
      }

      const item = list[index % list.length];
      currentPhotoIndex = 0;
      
      headerName.textContent = item.name;
      cardCuisine.textContent = item.cuisine;
      updatePhoto();
    }

    function updatePhoto() {
      const list = getFilteredRestaurants();
      if (!list.length) return;

      const item = list[currentIndex % list.length];
      cardImg.src = item.photos[currentPhotoIndex];

      imageDotsContainer.innerHTML = '';
      item.photos.forEach((_, idx) => {
        const dot = document.createElement('div');
        dot.className = `h-1 rounded-full flex-1 transition-all ${idx === currentPhotoIndex ? 'bg-white' : 'bg-white/30'}`;
        imageDotsContainer.appendChild(dot);
      });
    }

    // Tap picture to switch photos
    card.addEventListener('click', () => {
      if (isSwiping) return;
      resetHintTimer();
      const list = getFilteredRestaurants();
      if (!list.length) return;

      const item = list[currentIndex % list.length];
      currentPhotoIndex = (currentPhotoIndex + 1) % item.photos.length;
      updatePhoto();
    });

    function openRestaurantDetail() {
      const list = getFilteredRestaurants();
      if (!list.length) return;

      const item = list[currentIndex % list.length];
      document.getElementById('modal-hero-image').src = item.photos[0];
      document.getElementById('modal-title').textContent = item.name;
      document.getElementById('modal-rating').textContent = `★ ${item.rating}`;
      document.getElementById('modal-address-text').textContent = item.address;

      const menuContainer = document.getElementById('modal-menu-list');
      menuContainer.innerHTML = item.menu.map(m => `
        <div class="flex justify-between items-center p-3.5 bg-gray-800/60 rounded-xl border border-white/5">
          <span class="font-medium text-gray-200 text-sm">${m.name}</span>
          <span class="font-bold text-orange-400 text-sm">${m.price}</span>
        </div>
      `).join('');

      modal.classList.remove('translate-y-full');
    }

    document.getElementById('close-modal').addEventListener('click', () => {
      modal.classList.add('translate-y-full');
      nextRestaurant();
    });

    function nextRestaurant() {
      currentIndex++;
      resetCardPosition();
      loadRestaurant(currentIndex);
    }

    // Gesture handling
    let startX = 0;
    let currentX = 0;
    let isDragging = false;
    let isSwiping = false;

    function handleStart(e) {
      if (getFilteredRestaurants().length === 0) return;
      isDragging = true;
      isSwiping = false;
      startX = e.type.includes('touch') ? e.touches[0].clientX : e.clientX;
      card.classList.add('swiping');
      resetHintTimer();
    }

    function handleMove(e) {
      if (!isDragging) return;
      const x = e.type.includes('touch') ? e.touches[0].clientX : e.clientX;
      currentX = x - startX;

      if (Math.abs(currentX) > 10) isSwiping = true;

      const rotate = currentX * 0.08;
      card.style.transform = `translateX(${currentX}px) rotate(${rotate}deg)`;

      if (currentX > 30) {
        badgeRight.style.opacity = Math.min((currentX - 30) / 70, 1);
        badgeLeft.style.opacity = 0;
      } else if (currentX < -30) {
        badgeLeft.style.opacity = Math.min((-currentX - 30) / 70, 1);
        badgeRight.style.opacity = 0;
      } else {
        badgeLeft.style.opacity = 0;
        badgeRight.style.opacity = 0;
      }
    }

    function handleEnd() {
      if (!isDragging) return;
      isDragging = false;
      card.classList.remove('swiping');

      const threshold = 100;

      if (currentX > threshold) {
        card.style.transform = `translateX(600px) rotate(30deg)`;
        card.style.opacity = '0';
        setTimeout(openRestaurantDetail, 200);
      } else if (currentX < -threshold) {
        card.style.transform = `translateX(-600px) rotate(-30deg)`;
        card.style.opacity = '0';
        setTimeout(nextRestaurant, 200);
      } else {
        resetCardPosition();
      }
    }

    function resetCardPosition() {
      card.style.transform = '';
      card.style.opacity = '1';
      badgeLeft.style.opacity = 0;
      badgeRight.style.opacity = 0;
      currentX = 0;
    }

    // Event listeners
    card.addEventListener('mousedown', handleStart);
    window.addEventListener('mousemove', handleMove);
    window.addEventListener('mouseup', handleEnd);

    card.addEventListener('touchstart', handleStart);
    window.addEventListener('touchmove', handleMove);
    window.addEventListener('touchend', handleEnd);

    document.getElementById('btn-reject').addEventListener('click', (e) => {
      e.stopPropagation();
      resetHintTimer();
      badgeLeft.style.opacity = 1;
      card.style.transform = `translateX(-600px) rotate(-30deg)`;
      card.style.opacity = '0';
      setTimeout(() => {
        badgeLeft.style.opacity = 0;
        nextRestaurant();
      }, 200);
    });

    document.getElementById('btn-accept').addEventListener('click', (e) => {
      e.stopPropagation();
      resetHintTimer();
      badgeRight.style.opacity = 1;
      card.style.transform = `translateX(600px) rotate(30deg)`;
      card.style.opacity = '0';
      setTimeout(() => {
        badgeRight.style.opacity = 0;
        openRestaurantDetail();
      }, 200);
    });

    // Init App
    loadRestaurant(currentIndex);
    lucide.createIcons();
  </script>
</body>
</html>
