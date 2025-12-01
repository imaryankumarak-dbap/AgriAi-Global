<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AgriAI Global - Personalized Farming</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap" rel="stylesheet">
    <!-- Font Awesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --color-primary: #15803d; /* Emerald 700 - Earthy Green */
            --color-secondary: #fcd34d; /* Amber 300 - Sun Yellow */
            --color-text: #1f2937;
            --color-background: #f0fdf4; /* Green 50 */
        }
        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--color-background);
            color: var(--color-text);
        }
        .card {
            background-color: white;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
        }
        .chat-message {
            padding: 8px 12px;
            border-radius: 18px;
            max-width: 85%;
            margin-bottom: 8px;
            line-height: 1.4;
        }
        .user-message {
            background-color: #d1fae5; /* Green 100 */
            align-self: flex-end;
        }
        .ai-message {
            background-color: #e5e7eb; /* Gray 200 */
            align-self: flex-start;
        }
        .advisory-status {
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
        }
        /* Modal Styles */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.6);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 50;
        }
        .modal-content {
            background-color: white;
            border-radius: 16px;
            width: 90%;
            max-width: 500px;
            padding: 2rem;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.5);
            animation: fadeIn 0.3s ease-out;
            max-height: 90vh;
            overflow-y: auto;
        }
        .admin-modal-content {
            max-width: 800px; 
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .ad-badge {
            font-size: 0.6rem;
            background-color: #e5e7eb;
            color: #6b7280;
            padding: 2px 4px;
            border-radius: 2px;
            text-transform: uppercase;
            letter-spacing: 0.05em;
        }
        .tab-btn.active {
            border-bottom: 2px solid var(--color-primary);
            color: var(--color-primary);
        }
        /* Indian Flag Colors */
        .saffron { color: #FF9933; }
        .white { color: #FFFFFF; }
        .green { color: #138808; }
        .flag-text { font-size: 1.1rem; font-weight: 700; }
        
        .payment-tab {
            padding: 8px 12px;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.2s;
            border: 1px solid #e5e7eb;
        }
        .payment-tab.active {
            background-color: #d1fae5; /* Green 100 */
            border-color: var(--color-primary);
            font-weight: 600;
        }
    </style>
</head>
<body class="min-h-screen relative">

    <header class="bg-white shadow-sm sticky top-0 z-10">
        <div class="max-w-7xl mx-auto p-4 flex flex-col sm:flex-row items-center justify-between">
            <h1 class="text-3xl font-extrabold text-green-700 mb-4 sm:mb-0">
                Agri<span class="text-amber-500">AI</span> Global
            </h1>
            <div id="auth-status" class="text-sm text-gray-600 flex items-center space-x-4">
                <p>Status: <span id="auth-status-text" class="font-mono text-xs bg-gray-100 p-1 rounded">Guest</span></p>
                <button id="auth-button" onclick="openAuthModal()" class="bg-amber-500 text-white px-3 py-1 rounded text-xs font-semibold hover:bg-amber-600">Sign In</button>
            </div>
        </div>
    </header>

    <!-- Global Admin Broadcast Banner -->
    <div id="global-broadcast" class="hidden bg-red-600 text-white text-center p-2 font-bold animate-pulse cursor-pointer shadow-md relative z-20">
        <span id="broadcast-text"></span>
        <button onclick="document.getElementById('global-broadcast').classList.add('hidden')" class="absolute right-4 top-2 text-white hover:text-gray-200">×</button>
    </div>

    <!-- Top Advertisement Banner (Dynamic) -->
    <div id="top-ad-banner" class="max-w-7xl mx-auto px-4 mt-4 hidden">
        <div class="bg-gradient-to-r from-blue-500 to-indigo-600 text-white p-4 rounded-lg shadow-md flex justify-between items-center relative overflow-hidden">
            <div class="z-10">
                <span class="ad-badge absolute top-2 left-2 bg-white/20 text-white">Ad</span>
                <h3 id="top-ad-title" class="font-bold text-lg">SuperHarvest Fertilizers</h3>
                <p id="top-ad-desc" class="text-sm text-blue-100">Boost your yield by 20% this season.</p>
            </div>
            <button class="bg-white text-blue-600 px-4 py-2 rounded-full font-bold text-sm hover:bg-blue-50 transition z-10">Learn More</button>
            <div class="absolute -right-10 -bottom-10 w-32 h-32 bg-white/10 rounded-full"></div>
        </div>
    </div>

    <main class="max-w-7xl mx-auto p-4 md:p-8 grid grid-cols-1 lg:grid-cols-3 gap-6">

        <!-- Left Column: Weather and Agri-Metrics -->
        <div class="lg:col-span-2 space-y-6">

            <!-- Location and Search -->
            <section class="card p-6">
                <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between mb-4">
                    <h2 id="current-location" class="text-3xl md:text-4xl font-bold text-green-800 transition duration-300">Loading...</h2>
                    <div class="text-right">
                        <p id="current-date" class="text-gray-500 text-lg mt-1 sm:mt-0"></p>
                        <p id="live-indicator" class="text-xs text-green-600 animate-pulse mt-1 hidden">● Live Data (Open-Meteo)</p>
                    </div>
                </div>
                <div class="w-full flex mt-4">
                    <input type="text" id="location-input" placeholder="Search City, Farm, or Region..."
                        class="w-full p-2 border border-gray-300 rounded-l-lg focus:ring-green-500 focus:border-green-500 transition duration-150">
                    <button id="search-button" class="bg-green-600 text-white p-2 rounded-r-lg hover:bg-green-700 transition duration-150 flex items-center justify-center">
                        <i class="fas fa-search"></i>
                    </button>
                </div>
                <p id="location-source" class="text-xs text-gray-400 mt-2">Source: Default Location</p>
            </section>


            <!-- Advisory Panel (Actionable Insights) -->
            <section id="advisory-panel" class="card p-6 bg-amber-50 border-l-4 border-amber-500">
                <h3 class="text-2xl font-semibold mb-3 text-amber-700 flex items-center">
                    <i class="fas fa-tractor mr-2"></i> Agri-Advisory
                </h3>
                <div id="advisory-content">
                    <p id="advisory-message" class="text-gray-700 italic">... Waiting for weather data.</p>
                </div>
                <div id="advisory-placeholder" class="text-center p-4 bg-gray-100 rounded-lg hidden">
                    <p class="text-lg font-semibold text-gray-700">Detailed Advisory Locked</p>
                    <p class="text-sm text-gray-500 mt-1">Unlock crop-specific guidance and pest alerts by subscribing.</p>
                </div>
            </section>

            <!-- Current Conditions & Core Metrics Grid -->
            <section class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- 1. Current Weather (Large Card) -->
                <div id="current-weather-card" class="card p-6 lg:col-span-1 flex flex-col justify-between bg-green-700 text-white advisory-status">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="text-xl font-medium">Current</h3>
                        <div id="current-icon-container">
                            <!-- Icon Placeholder -->
                            <i class="fas fa-sun text-4xl text-amber-300"></i>
                        </div>
                    </div>
                    <div class="flex items-end mb-4">
                        <span id="current-temp" class="text-7xl font-light">--°C</span>
                        <span id="current-description" class="ml-4 text-xl font-semibold">Loading...</span>
                    </div>
                    <div class="text-sm border-t border-green-600 pt-3">
                        <p>H: <span id="max-temp">--°C</span> / L: <span id="min-temp">--°C</span></p>
                        <p>Feels Like: <span id="feels-like">--°C</span></p>
                    </div>
                </div>

                <!-- 2. Agricultural Metrics (Grid of Small Cards) -->
                <div class="lg:col-span-2 grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div class="card p-4 text-center">
                        <p class="text-xs text-gray-500 uppercase">Soil Temp (0cm)</p>
                        <span id="soil-temp" class="text-2xl font-bold text-green-600">--°C</span>
                        <p class="text-xs text-gray-400">Surface</p>
                    </div>
                    <div class="card p-4 text-center">
                        <p class="text-xs text-gray-500 uppercase">Wind Speed</p>
                        <span id="wind-speed" class="text-2xl font-bold">-- km/h</span>
                        <p id="wind-direction" class="text-xs text-gray-400">Direction</p>
                    </div>
                    <div class="card p-4 text-center">
                        <p class="text-xs text-gray-500 uppercase">Rain (Today)</p>
                        <span id="rain-amount" class="text-2xl font-bold text-blue-600">-- mm</span>
                        <p class="text-xs text-gray-400">Precipitation</p>
                    </div>
                    <div class="card p-4 text-center">
                        <p class="text-xs text-gray-500 uppercase">Rel. Humidity</p>
                        <span id="humidity" class="text-2xl font-bold">--%</span>
                        <p class="text-xs text-gray-400">Atmospheric</p>
                    </div>
                </div>
            </section>

            <!-- 7-Day Forecast -->
            <section class="mb-8">
                <h3 class="text-2xl font-bold mb-4 text-green-800">7-Day Outlook (Tactical Planning)</h3>
                <div id="forecast-container" class="grid grid-cols-2 sm:grid-cols-4 lg:grid-cols-7 gap-4">
                    <p id="loading-forecast" class="text-gray-500 col-span-7">Fetching 7-day forecast from satellite...</p>
                </div>
            </section>

             <!-- Real-time Agri News Feed -->
             <section class="card p-6">
                <div class="flex items-center justify-between mb-4">
                    <h3 class="text-xl font-semibold text-green-700 flex items-center">
                        <i class="fas fa-newspaper mr-2"></i> Agri-News Live
                    </h3>
                    <span class="text-xs bg-red-100 text-red-600 px-2 py-1 rounded-full animate-pulse">● LIVE</span>
                </div>
                <div id="news-container" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <p class="text-gray-400 italic">Curating news for your region...</p>
                </div>
            </section>
            
            <!-- NEW: Government Services Module -->
            <section class="card p-6 bg-blue-50 border-l-4 border-blue-500">
                <h3 class="text-xl font-semibold mb-4 text-blue-800 flex items-center">
                    <i class="fas fa-hand-holding-usd mr-2"></i> Government Agri-Services
                </h3>
                <div id="gov-services-content" class="space-y-3">
                    <p class="text-gray-600">Loading personalized schemes and advisories...</p>
                </div>
            </section>

        </div>

        <!-- Right Column: Personalization & Chatbot & Ads -->
        <div class="lg:col-span-1 space-y-6">

             <!-- Sidebar Ad (Dynamic) -->
             <section id="sidebar-ad" class="card p-4 border border-gray-100 hidden relative overflow-hidden group">
                <span class="ad-badge absolute top-2 right-2">Ad</span>
                <div class="flex flex-col items-center text-center">
                    <div id="sidebar-ad-img" class="w-full h-32 bg-gray-200 rounded-md mb-3 flex items-center justify-center text-gray-400 overflow-hidden">
                         <i class="fas fa-ad text-4xl"></i>
                    </div>
                    <h4 id="sidebar-ad-title" class="font-bold text-gray-800 mb-1">Heavy Duty Tractor</h4>
                    <p id="sidebar-ad-desc" class="text-xs text-gray-600 mb-3">0% Financing for 12 months. Limited time offer.</p>
                    <button class="w-full bg-gray-800 text-white py-2 rounded-md text-sm font-semibold hover:bg-gray-700 transition">View Offer</button>
                </div>
            </section>

            <!-- Subscription Management -->
            <section class="card p-6 border-l-4 border-green-500">
                <h3 class="text-xl font-semibold mb-4 text-green-700 flex items-center">
                    <i class="fas fa-crown mr-2"></i> Subscription Management
                </h3>
                <div id="subscription-status" class="mb-4 text-sm font-medium">
                    <p class="text-red-600 font-bold">Status: Not Subscribed</p>
                    <p class="text-xs text-gray-500">Access to AgriAI Chatbot and Detailed Advisory is restricted.</p>
                </div>

                <div id="subscription-plans" class="space-y-3">
                    <!-- FREE TRIAL -->
                    <button id="plan-trial-btn" onclick="startTrial()" class="w-full bg-blue-600 text-white p-3 rounded-md hover:bg-blue-700 transition duration-150 font-bold flex items-center justify-between border-2 border-blue-200">
                        <span>Start 7-Day Free Trial</span>
                        <span>₹0</span>
                    </button>
                    <!-- 3 MONTH PLAN -->
                    <button id="plan-3m-btn" onclick="openCheckoutModal(3, globalPrices['3m'], '3 Months')" class="w-full bg-green-600 text-white p-3 rounded-md hover:bg-green-700 transition duration-150 font-bold flex items-center justify-between">
                        <span>3 Months</span>
                        <span id="price-display-3m">₹199</span>
                    </button>
                    <!-- 6 MONTH PLAN -->
                    <button id="plan-6m-btn" onclick="openCheckoutModal(6, globalPrices['6m'], '6 Months (Save 12%)')" class="w-full bg-green-500 text-white p-3 rounded-md hover:bg-green-600 transition duration-150 font-bold flex items-center justify-between">
                        <span>6 Months</span>
                        <span id="price-display-6m">₹349</span>
                    </button>
                    <!-- 1 YEAR PLAN -->
                    <button id="plan-12m-btn" onclick="openCheckoutModal(12, globalPrices['1y'], '1 Year (Save 25%)')" class="w-full bg-green-500 text-white p-3 rounded-md hover:bg-green-600 transition duration-150 font-bold flex items-center justify-between">
                        <span>1 Year</span>
                        <span id="price-display-1y">₹599</span>
                    </button>
                </div>
                <!-- Management button shown when active -->
                <button id="manage-sub-btn" class="w-full bg-gray-500 text-white p-3 rounded-md font-bold hidden mt-3" onclick="openCheckoutModal(12, 599, '1 Year (Renewal)')">Manage Subscription</button>
            </section>

            <!-- Personalization Form -->
            <section class="card p-6">
                <h3 class="text-xl font-semibold mb-4 text-green-700 flex items-center">
                    <i class="fas fa-map-marker-alt mr-2"></i> Personalized Location
                </h3>
                <div id="profile-status" class="mb-4 text-sm font-medium text-gray-600">Loading profile...</div>

                <form id="profile-form" class="space-y-3">
                    <input type="text" id="profile-country" placeholder="Country (e.g., India)" required class="w-full p-2 border rounded-md">
                    <input type="text" id="profile-state" placeholder="State / Province" class="w-full p-2 border rounded-md">
                    <input type="text" id="profile-city" placeholder="City (e.g., Pune)" required class="w-full p-2 border rounded-md">
                    <input type="text" id="profile-village" placeholder="Village / Farm" class="w-full p-2 border rounded-md">
                    <input type="text" id="profile-zipcode" placeholder="Pincode" class="w-full p-2 border rounded-md">
                    <button type="submit" id="save-profile-btn" class="w-full bg-amber-500 text-white p-2 rounded-md hover:bg-amber-600 transition duration-150 font-semibold">Save Location</button>
                    <p id="profile-message" class="text-xs text-center mt-2"></p>
                </form>

                 <!-- Permissions Status -->
                 <div class="mt-6 pt-4 border-t border-gray-100">
                    <h4 class="text-sm font-semibold text-gray-700 mb-2 flex items-center">
                        <i class="fas fa-check-circle mr-2 text-green-500"></i> App Permissions
                    </h4>
                    <div id="permissions-status" class="space-y-1 text-xs text-gray-600">
                        <p id="perm-location">Location Access: <span class="text-amber-500">Pending</span></p>
                        <p id="perm-notifications">Notifications: <span class="text-amber-500">Pending</span></p>
                        <p id="perm-profile-pic">Profile Picture: <span class="text-amber-500">Denied</span></p>
                    </div>
                    <button onclick="requestPermissions()" class="mt-3 text-sm text-blue-600 hover:underline">
                        Review Permissions
                    </button>
                </div>
            </section>

            <!-- AgriAI Chatbot -->
            <section class="card p-4 flex flex-col h-[60vh] min-h-[400px]">
                <h3 class="text-xl font-semibold mb-3 text-green-700 flex items-center">
                    <i class="fas fa-robot mr-2"></i> AgriAI Chatbot
                </h3>
                <!-- Chat Window -->
                <div id="chat-window" class="flex-grow overflow-y-auto space-y-3 p-2 border-t border-b border-gray-100 mb-3 flex flex-col">
                    <div class="ai-message chat-message">
                        Hello! I am AgriAI, your personal farm advisor. I know your location details. Ask me anything about crop planning, pest control, or weather interpretation!
                    </div>
                </div>
                <!-- Input Area -->
                <div id="chat-input-area" class="flex">
                    <input type="text" id="chat-input" placeholder="Ask a question..."
                        class="flex-grow p-2 border border-gray-300 rounded-l-lg focus:ring-green-500 focus:border-green-500 transition duration-150" disabled>
                    <button id="chat-send-btn" class="bg-green-600 text-white p-2 rounded-r-lg hover:bg-green-700 transition duration-150 font-semibold disabled:bg-gray-400" disabled>
                        Send
                    </button>
                </div>
                <div id="chat-restricted-message" class="text-center p-3 bg-red-100 text-red-700 font-semibold rounded-md mt-2 hidden">
                    Chatbot is a subscriber-only feature. Please subscribe above.
                </div>
                <p id="chat-loading" class="text-sm text-green-600 mt-2 hidden">AI is thinking...</p>
            </section>

        </div>
    </main>

    <!-- Footer: About Us & Admin -->
    <footer class="text-center text-xs text-gray-400 pt-8 border-t border-gray-200 mt-8 max-w-7xl mx-auto p-4 flex flex-col items-center">
        <!-- Indian Flag Statement -->
        <div class="mb-4">
            <span class="flag-text saffron">Proudly </span>
            <span class="flag-text white">Made </span>
            <span class="flag-text green">In India</span>
            <span class="flag-text saffron"> 🇮🇳</span>
        </div>
        
        <!-- About Us Section -->
        <div id="about-us-container" class="max-w-2xl text-center mb-6">
             <h4 class="text-sm font-bold text-gray-600 mb-2 uppercase tracking-wide">About AgriAI Global</h4>
             <p id="about-us-text" class="text-gray-500 leading-relaxed">
                 Loading content...
             </p>
        </div>
        
        <p>AgriAI Global provides real-time, location-aware weather and AI insights. Powered by Open-Meteo & Gemini.</p>
        <button onclick="openAdminLogin()" class="mt-4 text-gray-300 hover:text-green-600 font-bold underline transition">Admin Login</button>
    </footer>

    <!-- Auth Modal (Sign In / Register / Forgot Password) -->
    <div id="auth-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <div class="flex justify-between items-center border-b pb-3 mb-4">
                <h2 class="text-2xl font-bold text-green-700"><i class="fas fa-lock mr-2"></i> User Access</h2>
                <button onclick="closeAuthModal()" class="text-gray-400 hover:text-gray-700">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>
            
            <p class="text-sm text-gray-600 mb-4">
                Enter your credentials to access personalized features.
            </p>

            <button type="button" onclick="simulateGoogleLogin()" class="w-full flex items-center justify-center space-x-2 bg-gray-50 text-gray-700 p-3 rounded-md hover:bg-gray-200 font-bold mb-4 border border-gray-300">
                <i class="fab fa-google text-lg"></i>
                <span>Sign in with Google (Simulated)</span>
            </button>

            <div class="relative flex items-center justify-center my-4">
                <div class="absolute w-full border-t border-gray-300"></div>
                <div class="relative bg-white px-3 text-sm text-gray-500">OR</div>
            </div>


            <form id="sign-in-form" class="space-y-4">
                <input type="email" id="auth-email" placeholder="Email Address" required class="w-full p-3 border rounded-md focus:ring-green-500 focus:border-green-500">
                <input type="password" id="auth-password" placeholder="Password" required class="w-full p-3 border rounded-md focus:ring-green-500 focus:border-green-500">
                
                <button type="button" id="auth-sign-in-btn" onclick="simulatedAuth('signin')" class="w-full bg-green-600 text-white p-3 rounded-md hover:bg-green-700 font-bold">
                    Sign In
                </button>
            </form>

            <div class="flex justify-between mt-4 text-sm">
                <button onclick="simulatedAuth('register')" class="text-blue-600 hover:underline">
                    New User? Register
                </button>
                <button onclick="simulatedAuth('reset')" class="text-red-600 hover:underline">
                    Forgot Password?
                </button>
            </div>
            <p id="auth-message" class="text-sm mt-3 text-center"></p>
        </div>
    </div>


    <!-- Checkout Modal -->
    <div id="checkout-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <div class="flex justify-between items-center border-b pb-3 mb-4">
                <h2 class="text-2xl font-bold text-green-700">Confirm Your Subscription</h2>
                <button onclick="closeCheckoutModal()" class="text-gray-400 hover:text-gray-700">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>

            <h3 class="text-lg font-semibold mb-2">Plan Details: <span id="checkout-plan-name" class="text-amber-600">3 Months</span></h3>

            <!-- Price Breakdown -->
            <div class="space-y-2 py-4 border-b border-gray-200">
                <div class="flex justify-between text-gray-700">
                    <span>Original Price:</span>
                    <span id="checkout-price-original" class="font-medium">₹199</span>
                </div>
                <div class="flex justify-between text-red-500">
                    <span>Discount (<span id="checkout-coupon-name">No Coupon</span>):</span>
                    <span id="checkout-discount" class="font-medium">- ₹0.00</span>
                </div>
                <div class="flex justify-between text-gray-700 text-sm italic pt-1">
                    <span>Taxes (18% GST Simulated):</span>
                    <span id="checkout-tax" class="font-medium">₹0.00</span>
                </div>
            </div>

            <!-- Total -->
            <div class="flex justify-between items-center mt-4">
                <span class="text-xl font-bold text-green-800">TOTAL DUE:</span>
                <span id="checkout-total-due" class="text-3xl font-extrabold text-green-700">₹199.00</span>
            </div>
            
            <!-- Payment Methods -->
            <div class="mt-6">
                <h4 class="text-md font-semibold mb-3 text-gray-700 border-b pb-2">Select Payment Method</h4>
                <div class="grid grid-cols-3 gap-3">
                    <div id="pay-upi" onclick="switchPaymentMethod('upi')" class="payment-tab active flex items-center justify-center">
                        <i class="fas fa-mobile-alt mr-1"></i> UPI / QR
                    </div>
                    <div id="pay-net" onclick="switchPaymentMethod('net')" class="payment-tab flex items-center justify-center">
                        <i class="fas fa-university mr-1"></i> Net Banking
                    </div>
                    <div id="pay-wallet" onclick="switchPaymentMethod('wallet')" class="payment-tab flex items-center justify-center">
                        <i class="fas fa-wallet mr-1"></i> Wallet
                    </div>
                </div>

                <div id="payment-details" class="mt-4 p-4 border rounded-lg bg-gray-50">
                    <!-- Dynamic details will be shown here -->
                    <p class="text-sm text-gray-600 text-center">Simulated payment portal. Proceed to checkout.</p>
                </div>
            </div>


            <!-- Purchase Code Section (Test Bypass) -->
            <div class="mt-6 p-4 bg-purple-50 rounded-lg border border-purple-200">
                <h4 class="text-md font-semibold mb-2 text-purple-700">Or Use Purchase Code (Test Access)</h4>
                <div class="flex">
                    <input type="text" id="purchase-code-input" placeholder="Enter code (e.g., $01PAID)"
                        class="flex-grow p-2 border border-purple-300 rounded-l-md focus:ring-purple-500 focus:border-purple-500 uppercase">
                    <button id="redeem-code-btn" onclick="redeemPurchaseCode()" 
                        class="bg-purple-600 text-white p-2 rounded-r-md hover:bg-purple-700 font-semibold transition duration-150">
                        Redeem
                    </button>
                </div>
                <p id="redeem-message" class="text-xs mt-2 italic text-purple-500">Use **$01PAID** for immediate access (Test Only).</p>
            </div>


            <!-- Coupon Section -->
            <div class="mt-6 p-4 bg-gray-50 rounded-lg">
                <h4 class="text-md font-semibold mb-2 text-gray-700">Have a Coupon Code?</h4>
                <div class="flex">
                    <input type="text" id="coupon-input" placeholder="Enter coupon code (e.g., AGRI10)"
                        class="flex-grow p-2 border border-gray-300 rounded-l-md focus:ring-amber-500 focus:border-amber-500 uppercase">
                    <button id="apply-coupon-btn" onclick="applyCoupon()" 
                        class="bg-amber-500 text-white p-2 rounded-r-md hover:bg-amber-600 font-semibold transition duration-150">
                        Apply
                    </button>
                </div>
                <p id="coupon-message" class="text-xs mt-2 italic text-gray-500">Try AGRI10 for a 10% discount!</p>
            </div>


            <!-- Final Action Button -->
            <button id="final-payment-btn" onclick="processPayment()" 
                class="w-full mt-6 bg-green-600 text-white p-3 rounded-md hover:bg-green-700 transition duration-150 font-bold text-lg">
                Proceed to Secure Payment
            </button>
            <p id="payment-processing-message" class="text-center text-sm text-green-600 mt-2 hidden">Processing payment...</p>
            <p class="text-center text-xs text-gray-400 mt-2">All payments are securely processed via a third-party gateway.</p>
        </div>
    </div>

    <!-- Admin Modal -->
    <div id="admin-modal" class="modal-overlay hidden">
        <div class="modal-content admin-modal-content">
            <div class="flex justify-between items-center border-b pb-3 mb-4">
                <h2 class="text-2xl font-bold text-green-800"><i class="fas fa-user-shield mr-2"></i>Admin Dashboard</h2>
                <button onclick="closeAdminModal()" class="text-gray-400 hover:text-gray-700">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>

            <!-- Admin Login View -->
            <div id="admin-login-view">
                <p class="text-gray-600 mb-4">
                    **Access Restricted:** Admin access is only granted to the authorized user: 
                    <span class="font-bold text-green-700">imaryankumar.ak@gmail.com</span>.
                </p>
                <!-- NEW: Email input for explicit validation -->
                <input type="email" id="admin-email-input" placeholder="Enter Admin Email" required class="w-full p-2 border rounded mb-3"> 
                <input type="password" id="admin-password" placeholder="Enter Passcode" class="w-full p-2 border rounded mb-3">
                <button id="admin-login-btn" onclick="attemptAdminLogin()" class="w-full bg-green-600 text-white p-2 rounded hover:bg-green-700 font-bold">Login</button>
                <p id="admin-login-error" class="text-red-500 text-sm mt-2 hidden">Incorrect passcode.</p>
            </div>

            <!-- Admin Dashboard View (Hidden by default) -->
            <div id="admin-dashboard-view" class="hidden">
                <!-- Admin Tabs -->
                <div class="flex border-b mb-4">
                    <!-- FIX: Corrected onclick to pass only the string identifier -->
                    <button onclick="switchAdminTab('broadcast')" class="tab-btn active px-4 py-2 text-sm font-medium text-gray-600 hover:text-green-600 focus:outline-none">Broadcasts</button>
                    <button onclick="switchAdminTab('pricing')" class="tab-btn px-4 py-2 text-sm font-medium text-gray-600 hover:text-green-600 focus:outline-none">Pricing</button>
                    <button onclick="switchAdminTab('users')" class="tab-btn px-4 py-2 text-sm font-medium text-gray-600 hover:text-green-600 focus:outline-none">Users</button>
                    <button onclick="switchAdminTab('content')" class="tab-btn px-4 py-2 text-sm font-medium text-gray-600 hover:text-green-600 focus:outline-none">Content</button>
                    <button onclick="switchAdminTab('export')" class="tab-btn px-4 py-2 text-sm font-medium text-gray-600 hover:text-green-600 focus:outline-none">Data Export</button>
                </div>

                <!-- Tab 1: Global Broadcasts -->
                <div id="admin-tab-broadcast" class="admin-tab-content">
                    <h3 class="font-bold text-lg mb-2">Global Emergency Alert</h3>
                    <p class="text-xs text-gray-500 mb-3">This message will appear at the top of every user's screen.</p>
                    <textarea id="broadcast-input" rows="3" class="w-full p-2 border rounded mb-2" placeholder="e.g., Heavy storm alert for Northern Region. Secure crops immediately."></textarea>
                    <div class="flex space-x-2">
                        <button onclick="saveBroadcast()" class="bg-red-600 text-white px-4 py-2 rounded text-sm font-bold">Publish Alert</button>
                        <button onclick="clearBroadcast()" class="bg-gray-300 text-gray-700 px-4 py-2 rounded text-sm">Clear Alert</button>
                    </div>
                    <p id="broadcast-msg" class="text-xs mt-2"></p>
                </div>

                <!-- Tab 2: Pricing Management -->
                <div id="admin-tab-pricing" class="admin-tab-content hidden">
                    <h3 class="font-bold text-lg mb-2">Subscription Pricing (INR)</h3>
                    <div class="grid grid-cols-3 gap-4 mb-4">
                        <div>
                            <label class="block text-xs font-bold text-gray-600">3 Months</label>
                            <input type="number" id="price-3m" class="w-full p-2 border rounded">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-gray-600">6 Months</label>
                            <input type="number" id="price-6m" class="w-full p-2 border rounded">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-gray-600">1 Year</label>
                            <input type="number" id="price-1y" class="w-full p-2 border rounded">
                        </div>
                    </div>
                    <button onclick="savePricing()" class="bg-blue-600 text-white px-4 py-2 rounded text-sm font-bold">Update Prices</button>
                    <p id="pricing-msg" class="text-xs mt-2"></p>
                </div>

                <!-- Tab 3: User Management -->
                <div id="admin-tab-users" class="admin-tab-content hidden">
                    <h3 class="font-bold text-lg mb-2">Manage User Subscription</h3>
                    <p class="text-xs text-gray-500 mb-2">For testing, leave User ID blank to grant access to Dummy 1 (`aryan-user-id-54321`).</p>
                    <input type="text" id="admin-user-id" placeholder="Enter User ID (Optional for test)" class="w-full p-2 border rounded mb-2">
                    <div class="flex space-x-2">
                        <button onclick="adminGrantSub()" class="bg-green-600 text-white px-3 py-1 rounded text-sm">Grant 1 Year</button>
                        <button onclick="adminRevokeSub()" class="bg-red-600 text-white px-3 py-1 rounded text-sm">Revoke</button>
                    </div>
                    <p id="admin-user-msg" class="text-xs mt-2"></p>
                </div>

                <!-- Tab 4: Site Content -->
                <div id="admin-tab-content" class="admin-tab-content hidden">
                    <h3 class="font-bold text-lg mb-2">Edit "About Us" Section</h3>
                    <textarea id="admin-about-us" rows="5" class="w-full p-2 border rounded mb-2"></textarea>
                    <button onclick="saveAboutUs()" class="bg-purple-600 text-white px-4 py-2 rounded text-sm font-bold">Save Content</button>
                    <p id="content-msg" class="text-xs mt-2"></p>
                </div>

                <!-- Tab 5: Data Export (CSV) -->
                <div id="admin-tab-export" class="admin-tab-content hidden">
                    <h3 class="font-bold text-lg mb-2">Customer Data Export (CSV)</h3>
                    <p class="text-xs text-gray-500 mb-4">Exports all non-sensitive user profile and subscription data into an Excel-compatible CSV file.</p>
                    
                    <div class="bg-yellow-100 border-l-4 border-yellow-500 text-yellow-700 p-4 mb-4" role="alert">
                         <p class="font-bold">Security Note:</p>
                         <p class="text-sm">Direct retrieval of all user data is restricted by Firebase security rules for client-side applications. We will simulate a successful **Server-Side Export** and provide a mock file download to demonstrate functionality.</p>
                    </div>

                    <button onclick="downloadCustomerData()" class="bg-amber-500 text-white px-4 py-2 rounded text-sm font-bold hover:bg-amber-600 flex items-center justify-center">
                        <i class="fas fa-file-excel mr-2"></i> Download All Customer Data (Simulated)
                    </button>
                    <p id="export-msg" class="text-xs mt-2 text-gray-600"></p>
                </div>

            </div>
        </div>
    </div>

    <!-- Permissions Modal -->
    <div id="permissions-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <div class="flex justify-between items-center border-b pb-3 mb-4">
                <h2 class="text-2xl font-bold text-green-700"><i class="fas fa-shield-alt mr-2"></i> App Permissions</h2>
                <button onclick="closePermissionsModal()" class="text-gray-400 hover:text-gray-700">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>
            
            <p class="text-sm text-gray-600 mb-4">
                We need certain permissions to provide hyper-localized weather and alerts.
            </p>

            <div class="space-y-4">
                <div class="p-3 border rounded-md">
                    <h4 class="font-semibold text-gray-800">1. Geolocation Access (Required)</h4>
                    <p class="text-sm text-gray-600">Allows us to give you real-time, field-level weather data based on your current position.</p>
                    <p id="perm-status-location" class="text-sm mt-1 font-medium text-amber-500">Status: Pending</p>
                    <button onclick="allowPermission('location')" class="mt-2 text-xs bg-blue-500 text-white px-3 py-1 rounded hover:bg-blue-600">Grant Access</button>
                </div>

                <div class="p-3 border rounded-md">
                    <h4 class="font-semibold text-gray-800">2. Notification Permission (Recommended)</h4>
                    <p class="text-sm text-gray-600">Receive instant alerts for frost, heavy rain, or admin broadcasts even when the app is closed.</p>
                    <p id="perm-status-notifications" class="text-sm mt-1 font-medium text-amber-500">Status: Pending</p>
                    <button onclick="allowPermission('notifications')" class="mt-2 text-xs bg-blue-500 text-white px-3 py-1 rounded hover:bg-blue-600">Grant Access</button>
                </div>
                
                <div class="p-3 border rounded-md">
                    <h4 class="font-semibold text-gray-800">3. Profile Picture Upload (Optional)</h4>
                    <p id="perm-status-profile-pic-desc" class="text-sm text-gray-600">Access to photo library/camera to set a personalized profile image.</p>
                    <p id="perm-status-profile-pic" class="text-sm mt-1 font-medium text-red-500">Status: Denied</p>
                    <button onclick="allowPermission('profile-pic')" class="mt-2 text-xs bg-blue-500 text-white px-3 py-1 rounded hover:bg-blue-600">Grant Access</button>
                </div>
            </div>

            <button onclick="closePermissionsModal()" class="w-full bg-green-600 text-white p-3 rounded-md hover:bg-green-700 font-bold mt-6">
                Done
            </button>
        </div>
    </div>


    <!-- Search Results Modal -->
    <div id="search-results-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <div class="flex justify-between items-center border-b pb-3 mb-4">
                <h2 class="text-2xl font-bold text-green-700"><i class="fas fa-search-location mr-2"></i> Select Your Location</h2>
                <button onclick="closeSearchResultsModal()" class="text-gray-400 hover:text-gray-700">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>
            
            <p id="search-results-query" class="text-sm text-gray-600 mb-4">Found multiple results for ""</p>

            <div id="search-results-list" class="space-y-3 max-h-72 overflow-y-auto">
                <!-- Results injected here -->
            </div>
            <p id="search-error-message" class="text-red-500 text-center mt-3 hidden">No precise results found. Try again.</p>
        </div>
    </div>

    <script type="module">
        // --- Firebase & API Configuration ---
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        // FIX: Only import necessary firestore methods once
        import { getFirestore, doc, setDoc, getDoc, onSnapshot, collection, query, getDocs } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the environment
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
        
        // Chatbot Constants
        const API_KEY = ""; 
        const MODEL_NAME = "gemini-2.5-flash-preview-09-2025";
        const API_URL = `https://generativelanguage.googleapis.com/v1beta/models/${MODEL_NAME}:generateContent?key=${API_KEY}`;
        
        // --- Global State & Initialization ---
        let app, db, auth;
        let userId = null;
        const ADMIN_EMAIL = 'imaryankumar.ak@gmail.com';
        const ADMIN_PASSCODE = 'Admin@2001'; 
        const DEFAULT_USER_PASSWORD = 'Hello@123456';
        const ARYAN_USER_EMAIL = 'itsmearyankumar.ak@gmail.com';
        const ARYAN_USER_PASSWORD = 'Hello@01';
        const ARYAN_USER_ID = 'aryan-user-id-54321'; // Consistent ID for testing
        const PURCHASE_CODE = '$01PAID'; // Zero-cost code for testing

        let currentUserEmail = null; 
        let userProfile = {};
        let currentGeolocation = null;
        const defaultLocation = 'Ames';
        
        // Checkout State
        let currentCheckoutPlan = { months: 0, price: 0, name: '', finalPrice: 0, discount: 0 };
        const TAX_RATE = 0.18; // Simulated 18% GST
        let globalPrices = { '3m': 199, '6m': 349, '1y': 599 }; // Defaults

        // Permission State (Tracked Locally and in Profile)
        let permissions = {
            'location': 'Pending',
            'notifications': 'Pending',
            'profile-pic': 'Denied'
        };
        
        let activePaymentMethod = 'upi'; // Default to UPI

        // --- Utility Functions ---

        const formatCurrency = (amount) => `₹${(Math.round(amount * 100) / 100).toFixed(2)}`;

        async function fetchWithRetry(apiCall, maxRetries = 5) {
            for (let i = 0; i < maxRetries; i++) {
                try {
                    return await apiCall();
                } catch (error) {
                    if (i === maxRetries - 1) throw error;
                    const delay = Math.pow(2, i) * 1000 + Math.random() * 1000;
                    console.warn(`Request failed, retrying in ${delay / 1000}s...`);
                    await new Promise(resolve => setTimeout(resolve, delay));
                }
            }
        }
        
        // --- Flag Utility ---
        function getFlagEmoji(countryName) {
            const countryCodes = {
                'United States': 'US',
                'India': 'IN',
                'Germany': 'DE',
                'France': 'FR',
                'Brazil': 'BR',
                'Canada': 'CA',
                'Australia': 'AU',
                'Japan': 'JP',
                'China': 'CN',
                'Mexico': 'MX',
                'Nigeria': 'NG',
                'GPS': '📍', // Custom flag for GPS location
                'Default': '🌎'
            };

            const code = countryCodes[countryName] || 'Default';
            if (code === 'Default') return '🌎';

            // Convert ISO 3166-1 alpha-2 code to emoji
            const codePoints = code
                .toUpperCase()
                .split('')
                .map(char => 127397 + char.charCodeAt());
            return String.fromCodePoint(...codePoints);
        }

        function getOfficialSource(countryName) {
            const sources = {
                'India': { name: 'India Meteorological Department (IMD)', link: 'https://mausam.imd.gov.in/' },
                'United States': { name: 'National Oceanic and Atmospheric Administration (NOAA)', link: 'https://www.noaa.gov/' },
                'China': { name: 'China Meteorological Administration (CMA)', link: 'http://www.cma.gov.cn/' },
                'Germany': { name: 'Deutscher Wetterdienst (DWD)', link: 'https://www.dwd.de/' },
                'Default': { name: 'WMO Global Weather Services', link: '#' }
            };
            return sources[countryName] || sources['Default'];
        }

        // --- Open-Meteo Integration Functions (Moved to top of module) ---
        
        async function getCoordinates(city, multiple = false) {
            const query = city.split(',')[0].trim();
            const count = multiple ? 10 : 1;
            const url = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(query)}&count=${count}&language=en&format=json`;
            const response = await fetch(url);
            const data = await response.json();
            
            if (multiple) {
                return data.results || [];
            }
            
            if (data.results && data.results.length > 0) {
                return { lat: data.results[0].latitude, lon: data.results[0].longitude, name: data.results[0].name, country: data.results[0].country };
            }
            throw new Error("City not found");
        }

        async function fetchRealWeather(lat, lon) {
            const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,relative_humidity_2m,is_day,precipitation,rain,weather_code,wind_speed_10m,wind_direction_10m,soil_temperature_0cm&daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_sum,precipitation_probability_max&timezone=auto`;
            const response = await fetch(url);
            return await response.json();
        }

        // WMO Weather Code Interpreter
        function getWeatherDescription(code) {
            if (code === 0) return { desc: "Clear Sky", icon: "fa-sun", color: "text-amber-300" };
            if (code >= 1 && code <= 3) return { desc: "Partly Cloudy", icon: "fa-cloud-sun", color: "text-gray-400" };
            if (code >= 45 && code <= 48) return { desc: "Foggy", icon: "fa-smog", color: "text-gray-500" };
            if (code >= 51 && code <= 55) return { desc: "Drizzle", icon: "fa-cloud-rain", color: "text-blue-300" };
            if (code >= 61 && code <= 65) return { desc: "Rain", icon: "fa-umbrella", color: "text-blue-500" };
            if (code >= 80 && code <= 82) return { desc: "Showers", icon: "fa-cloud-showers-heavy", color: "text-blue-600" };
            if (code >= 95 && code <= 99) return { desc: "Thunderstorm", icon: "fa-bolt", color: "text-yellow-500" };
            return { desc: "Overcast", icon: "fa-cloud", color: "text-gray-600" };
        }

        // --- Rendering & Advisory Functions (Moved to top of module) ---
        
        function renderRealWeather(coords, data) {
            const current = data.current;
            const wmo = getWeatherDescription(current.weather_code);
            
            // --- Display City, State/Province, and Flag ---
            let displayLocation = `${coords.name}`;
            
            // 1. Add State/Province if available in userProfile (for personalized display)
            if (userProfile.city && userProfile.state) {
                 displayLocation = `${userProfile.city}, ${userProfile.state}`;
            } 
            
            // 2. Add Country Flag 
            const flag = getFlagEmoji(coords.country);
            displayLocation = `<span class="text-3xl mr-2">${flag}</span> ${displayLocation}`;
            
            if (userProfile.city && userProfile.state) {
                 // If we used the full personalized path, add country name at the end
                 displayLocation += ` (${coords.country})`;
            }
            // -----------------------------------------------------------------------
            
            document.getElementById('current-location').innerHTML = displayLocation;
            

            document.getElementById('current-date').textContent = new Date().toLocaleDateString('en-US', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
            
            document.getElementById('current-icon-container').innerHTML = `<i class="fas ${wmo.icon} text-4xl ${wmo.color}"></i>`;
            document.getElementById('current-temp').textContent = `${Math.round(current.temperature_2m)}°C`;
            document.getElementById('current-description').textContent = wmo.desc;
            
            document.getElementById('max-temp').textContent = `${Math.round(data.daily.temperature_2m_max[0])}°C`;
            document.getElementById('min-temp').textContent = `${Math.round(data.daily.temperature_2m_min[0])}°C`;
            document.getElementById('feels-like').textContent = `${Math.round(current.temperature_2m)}°C`; // Approximation

            // Agri Metrics
            document.getElementById('soil-temp').textContent = `${current.soil_temperature_0cm}°C`;
            document.getElementById('wind-speed').textContent = `${current.wind_speed_10m} km/h`;
            // Simple direction logic
            const dirs = ['N','NE','E','SE','S','SW','W','NW'];
            const dirIdx = Math.round(current.wind_direction_10m / 45) % 8;
            document.getElementById('wind-direction').textContent = dirs[dirIdx];
            
            document.getElementById('rain-amount').textContent = `${current.precipitation} mm`;
            document.getElementById('humidity').textContent = `${current.relative_humidity_2m}%`;

            // Update Government Services Module
            renderGovServices(coords.country);
        }

        function renderRealForecast(daily) {
            const container = document.getElementById('forecast-container');
            container.innerHTML = '';
            
            const days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
            const todayIdx = new Date().getDay();

            for(let i=0; i<7; i++) {
                const dayName = days[(todayIdx + i) % 7];
                const wmo = getWeatherDescription(daily.weather_code[i]);
                const rainProb = daily.precipitation_probability_max[i] || 0;
                
                const card = document.createElement('div');
                card.className = 'card p-4 flex flex-col items-center text-center hover:bg-green-50 transition duration-150';
                card.innerHTML = `
                    <p class="text-sm font-semibold text-green-700">${dayName}</p>
                    <div class="my-2"><i class="fas ${wmo.icon} text-2xl ${wmo.color}"></i></div>
                    <p class="text-xl font-bold">${Math.round(daily.temperature_2m_max[i])}°</p>
                    <p class="text-sm text-gray-500">${Math.round(daily.temperature_2m_min[i])}°</p>
                    <div class="text-xs mt-2 w-full pt-1 border-t border-gray-100">
                        <p class="text-blue-600 font-medium">${rainProb}% Rain</p>
                    </div>
                `;
                container.appendChild(card);
            }
        }

        function generateAdvisory(data) {
            const current = data.current;
            const msgEl = document.getElementById('advisory-message');
            
            // Logic based on real data
            if (current.precipitation > 5) {
                msgEl.textContent = "Heavy rain detected. Pause irrigation. Ensure drainage channels are clear to prevent waterlogging.";
            } else if (current.temperature_2m > 35) {
                msgEl.textContent = "Heat stress warning. Irrigate in early morning or late evening to minimize evaporation.";
            } else if (current.wind_speed_10m > 20) {
                msgEl.textContent = "High winds. Avoid spraying pesticides today to prevent drift.";
            } else if (current.soil_temperature_0cm < 10) {
                msgEl.textContent = "Soil is cold. Germination may be slow. Consider delaying planting of sensitive crops.";
            } else {
                msgEl.textContent = "Conditions are favorable for field operations. Good time for scouting and fertilizer application.";
            }
        }

        function renderGovServices(country) {
            const contentEl = document.getElementById('gov-services-content');
            const source = getOfficialSource(country);
            const city = userProfile.city || "your region";
            
            let services = [];

            if (country === 'India') {
                services = [
                    { name: 'Pradhan Mantri Fasal Bima Yojana (Crop Insurance)', desc: 'View claim status and scheme details.', icon: 'fa-file-invoice' },
                    { name: 'Kisan Credit Card (KCC) Scheme', desc: 'Apply for quick loans and subsidies.', icon: 'fa-credit-card' },
                    { name: `State Water Resource Report (${userProfile.state || 'Maharashtra'})`, desc: 'Check current dam and reservoir levels.', icon: 'fa-water' }
                ];
            } else if (country === 'United States') {
                 services = [
                    { name: 'Crop Insurance Program (USDA)', desc: 'Review policies and application deadlines.', icon: 'fa-shield-alt' },
                    { name: 'Farm Service Agency (FSA) Loans', desc: 'Find disaster relief and operational loans.', icon: 'fa-money-bill-wave' },
                    { name: 'National Soil Moisture Network', desc: 'View regional drought monitoring reports.', icon: 'fa-tachometer-alt' }
                ];
            } else {
                 services = [
                    { name: 'Local Agricultural Subsidies', desc: 'Check eligibility for regional grants.', icon: 'fa-search-dollar' },
                    { name: 'National Meteorological Warnings', desc: 'Direct link to the national weather authority.', icon: 'fa-exclamation-triangle' }
                ];
            }

            let html = `
                <p class="text-sm text-gray-700 mb-3 font-semibold">
                    Weather data is aggregated from major global sources, including services provided by the 
                    <a href="${source.link}" target="_blank" class="text-blue-600 hover:underline">${source.name}</a>.
                </p>
                <h4 class="font-bold text-md text-blue-700 border-t pt-3 mt-3">Actionable Government Services for ${city}:</h4>
                <ul class="space-y-2 pt-2">
                    ${services.map(s => `
                        <li onclick="alert('Simulated: Redirecting to ${s.name} service.')" class="flex items-start text-sm bg-blue-100 p-2 rounded-md hover:bg-blue-200 transition cursor-pointer">
                            <i class="fas ${s.icon} text-blue-600 mr-3 mt-1"></i>
                            <div>
                                <p class="font-semibold text-gray-800">${s.name}</p>
                                <p class="text-xs text-gray-600">${s.desc}</p>
                            </div>
                        </li>
                    `).join('')}
                </ul>
            `;

            contentEl.innerHTML = html;
        }

        function updateNewsFeed(country, weatherCode) {
            const container = document.getElementById('news-container');
            const city = userProfile.city || "Local Region";
            const state = userProfile.state || "";
            const locationDetail = state ? `${city}, ${state}` : `${city}, ${country}`;

            let headlines = [];
            
            // Determine local context and national/global context
            const isMonsoon = weatherCode >= 61 && weatherCode <= 82;
            const currentTempEl = document.getElementById('current-temp');
            const currentTemp = currentTempEl ? parseFloat(currentTempEl.textContent.replace('°C', '')) : 25;
            const isDrought = weatherCode === 0 && (currentTemp > 30); 
            const isIndia = country === 'India';

            // Local/Regional News (Contextual)
            if (isMonsoon) {
                headlines.push({
                    title: `Waterlogging Advisory Issued for ${city} fields; Monitor drainage.`,
                    source: 'State Flood Control',
                    location: locationDetail
                });
            } else if (isDrought) {
                 headlines.push({
                    title: `Severe Dry Spell: ${city} farmers advised to shift to millets.`,
                    source: 'Local Agri Gazette',
                    location: locationDetail
                });
            } else {
                headlines.push({
                    title: `Optimal Planting Window Open in ${city}. Soil moisture reports favorable.`,
                    source: 'Local Weather Center',
                    location: locationDetail
                });
            }

            // National/Global News
            if (isIndia) {
                headlines.push({
                    title: 'National: Rabi Crop Procurement targets revised upwards by 8%.',
                    source: 'National Market Review',
                    location: 'New Delhi, India'
                });
            } else {
                headlines.push({
                    title: 'Global: Wheat futures rise amidst US-Russia supply concerns.',
                    source: 'Global Commodity Watch',
                    location: 'Global Markets'
                });
            }

            headlines.push({
                title: 'New government subsidies released for solar pump installation.',
                source: 'Agri Policy Desk',
                location: country
            });
            
            container.innerHTML = headlines.slice(0, 4).map((item, i) => `
                <div class="p-3 bg-gray-50 rounded border-l-4 border-green-500">
                    <h5 class="font-bold text-sm text-gray-800">${item.title}</h5>
                    <p class="text-xs text-gray-500 mt-1">
                        ${i + 1} hour(s) ago • By ${item.source} (${item.location})
                    </p>
                </div>
            `).join('');
        }

        function rotateAds() {
            if (userProfile.isSubscribed) return; // No ads for subs

            const ads = [
                { title: "SuperHarvest Fertilizers", desc: "Boost yield by 20%", color: "from-blue-500 to-indigo-600" },
                { title: "AgriDrone Pro", desc: "Automated field monitoring", color: "from-green-500 to-emerald-600" },
                { title: "SolarPump X", desc: "Zero electricity cost irrigation", color: "from-amber-500 to-orange-600" }
            ];
            
            const ad = ads[Math.floor(Math.random() * ads.length)];
            const banner = document.getElementById('top-ad-banner');
            banner.classList.remove('hidden');
            banner.querySelector('div').className = `bg-gradient-to-r ${ad.color} text-white p-4 rounded-lg shadow-md flex justify-between items-center relative overflow-hidden`;
            document.getElementById('top-ad-title').textContent = ad.title;
            document.getElementById('top-ad-desc').textContent = ad.desc;
            
            // Sidebar Ad (Random Image Simulation via FontAwesome)
            const sideAd = document.getElementById('sidebar-ad');
            sideAd.classList.remove('hidden');
        }
        
        // --- Permissions Logic ---

        function updatePermissionsUI() {
            const locSpan = document.getElementById('perm-location').querySelector('span');
            const notifSpan = document.getElementById('perm-notifications').querySelector('span');
            const picSpan = document.getElementById('perm-profile-pic').querySelector('span');

            locSpan.textContent = permissions.location;
            locSpan.className = `text-sm mt-1 font-medium text-${permissions.location === 'Granted' ? 'green' : 'red'}-500`;

            notifSpan.textContent = permissions.notifications;
            notifSpan.className = `text-sm mt-1 font-medium text-${permissions.notifications === 'Granted' ? 'green' : 'red'}-500`;

            picSpan.textContent = permissions['profile-pic'];
            picSpan.className = `text-sm mt-1 font-medium text-${permissions['profile-pic'] === 'Granted' ? 'green' : 'red'}-500`;
        }

        window.requestPermissions = () => {
             document.getElementById('permissions-modal').classList.remove('hidden');
        }
        window.closePermissionsModal = () => document.getElementById('permissions-modal').classList.add('hidden');

        window.allowPermission = function(type) {
            const statusEl = document.getElementById(`perm-status-${type}`);
            statusEl.textContent = 'Requesting...';
            statusEl.className = 'text-sm mt-1 font-medium text-blue-500';

            if (type === 'location') {
                if (navigator.geolocation) {
                    navigator.geolocation.getCurrentPosition(
                        async (position) => {
                            const lat = position.coords.latitude;
                            const lon = position.coords.longitude;
                            
                            // Store the real coordinates globally
                            currentGeolocation = { lat: lat, lon: lon, name: "Current GPS Location", country: 'GPS' };

                            permissions.location = 'Granted';
                            updatePermissionsUI();
                            
                            // Immediately load weather for the current location
                            await loadWeather(currentGeolocation);
                            
                            // Save permission state to profile
                            await saveProfileData({ permissions: permissions });
                            
                            // closePermissionsModal(); // Keep open for other permissions
                            alert("Location access granted! Weather updated to your current position.");
                        },
                        (error) => {
                            permissions.location = 'Denied';
                            updatePermissionsUI();
                            saveProfileData({ permissions: permissions });
                            
                            let msg = "Geolocation denied by user.";
                            if (error.code === error.PERMISSION_DENIED) {
                                msg = "Location access denied. Please grant permission in your browser settings.";
                            } 
                            alert(`Location Error: ${msg}`);
                        }
                    );
                } else {
                    alert("Geolocation is not supported by your browser.");
                    permissions.location = 'Denied';
                    updatePermissionsUI();
                }
            } else if (type === 'notifications') {
                 // Simulated success for notifications in the sandbox
                 permissions.notifications = 'Granted';
                 updatePermissionsUI();
                 saveProfileData({ permissions: permissions });
                 alert("Notification permission simulated as granted.");
            } else if (type === 'profile-pic') {
                 // Simulated success for camera/storage in the sandbox
                 permissions['profile-pic'] = 'Granted';
                 updatePermissionsUI();
                 saveProfileData({ permissions: permissions });
                 alert("Profile picture access simulated as granted.");
            }
        };

        // --- Application Logic (Moved to top of module) ---

        async function loadWeather(locationOrCoords) {
            let lat, lon, locationName, locationCountry;
            
            if (typeof locationOrCoords === 'object' && locationOrCoords !== null) {
                // Scenario 1: Using Geolocation coordinates (from browser permission)
                lat = locationOrCoords.lat;
                lon = locationOrCoords.lon;
                // Reverse geocoding needed for name/country, but for simplicity, we use placeholders
                locationName = locationOrCoords.name || "Current GPS Location";
                locationCountry = locationOrCoords.country || 'GPS'; 
                document.getElementById('location-source').textContent = 'Source: Live GPS';
            } else {
                // Scenario 2: Using Text Search (initial load or manual search/refresh)
                document.getElementById('current-location').textContent = `Loading ${locationOrCoords}...`;
                document.getElementById('location-source').textContent = locationOrCoords === defaultLocation ? 'Source: Default' : 'Source: User/Search';
                try {
                    // FIX: Use multi-search results to find the most likely single coordinate
                    const results = await getCoordinates(locationOrCoords, true); 
                    
                    if (results.length > 0) {
                        // If one or more results, take the first one (most relevant)
                        const result = results[0];
                        lat = result.latitude;
                        lon = result.longitude;
                        locationName = result.name;
                        locationCountry = result.country;
                    } else {
                        throw new Error("City not found");
                    }
                } catch (error) {
                    console.error("Weather Load Error:", error);
                    document.getElementById('current-location').textContent = `Error: ${locationOrCoords} not found.`;
                    document.getElementById('advisory-message').textContent = 'Could not load weather data. Please check spelling.';
                    document.getElementById('live-indicator').classList.add('hidden');
                    return;
                }
            }
            
            try {
                // 2. Fetch Weather
                const weatherData = await fetchRealWeather(lat, lon);
                
                // 3. Render
                renderRealWeather({ name: locationName, country: locationCountry }, weatherData);
                renderRealForecast(weatherData.daily);
                
                // 4. Update News Context
                updateNewsFeed(locationCountry || 'Global', weatherData.current.weather_code);

                // 5. Update Advisory if subscribed
                if (userProfile.isSubscribed) {
                    generateAdvisory(weatherData);
                } else {
                    document.getElementById('advisory-message').textContent = `Real-time data loaded for ${locationName}. Subscribe for detailed agricultural advice.`;
                }

                // Show Live Indicator
                document.getElementById('live-indicator').classList.remove('hidden');

            } catch (error) {
                console.error("Weather API Fetch Error:", error);
                document.getElementById('current-location').textContent = `Error loading weather for ${locationName}.`;
                document.getElementById('advisory-message').textContent = 'Could not load weather metrics.';
            }
        }

        // --- Search Results Modal Handlers ---

        window.openSearchResultsModal = function(results, query) {
            const modal = document.getElementById('search-results-modal');
            const list = document.getElementById('search-results-list');
            const errorMsg = document.getElementById('search-error-message');
            
            if (!results || results.length === 0) {
                errorMsg.classList.remove('hidden');
                list.innerHTML = '';
                document.getElementById('search-results-query').textContent = `No results found for "${query}"`;
                modal.classList.remove('hidden');
                return;
            }

            errorMsg.classList.add('hidden');
            list.innerHTML = '';
            document.getElementById('search-results-query').textContent = `Found ${results.length} locations for "${query}". Please select one:`;

            results.forEach(result => {
                const region = result.admin1 || result.admin2 || result.country;
                const locationText = `${result.name}, ${region}`;
                const flag = getFlagEmoji(result.country);

                const item = document.createElement('div');
                item.className = 'p-3 border border-gray-200 rounded-lg cursor-pointer hover:bg-green-50 transition flex items-center justify-between';
                item.innerHTML = `
                    <div>
                        <span class="text-xl mr-2">${flag}</span>
                        <span class="font-semibold text-gray-800">${locationText}</span>
                    </div>
                `;
                item.onclick = () => selectLocation(result.latitude, result.longitude, result.name, result.country);
                list.appendChild(item);
            });
            modal.classList.remove('hidden');
        };

        window.closeSearchResultsModal = () => document.getElementById('search-results-modal').classList.add('hidden');

        window.selectLocation = function(lat, lon, name, country) {
            closeSearchResultsModal();
            // Load weather using precise coordinates
            loadWeather({ lat, lon, name, country });
        };

        // --- Firebase Core and Helpers (Remaining logic remains at the bottom, but the core function definitions are now safe) ---

        async function firebaseInitAndAuth() {
            try {
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);
                
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth); 
                }

                onAuthStateChanged(auth, (user) => {
                    const authStatusText = document.getElementById('auth-status-text');
                    const authButton = document.getElementById('auth-button');
                    const userIdDisplay = document.getElementById('user-id-display');

                    console.log(`[Auth] New state received. User ID: ${user ? user.uid : 'None'}. Email: ${user ? user.email : 'None'}`);

                    if (user) {
                        userId = user.uid;
                        currentUserEmail = user.email || (initialAuthToken ? 'platform_user@canvas.com' : 'anonymous@canvas.com');
                        
                        if (userIdDisplay) userIdDisplay.textContent = userId.substring(0, 8) + '...';
                        
                        if (authStatusText && authButton) {
                            authStatusText.textContent = user.email ? 'Signed In' : 'Guest';
                            authButton.textContent = user.email ? 'Sign Out' : 'Sign In';
                            authButton.onclick = user.email ? signOutUser : openAuthModal;
                        }

                        loadUserProfile();
                        listenForBroadcasts();
                        listenForPrices();
                        listenForContent();
                    } else {
                        userId = crypto.randomUUID();
                        currentUserEmail = null;
                        if (userIdDisplay) userIdDisplay.textContent = '...';

                        if (authStatusText && authButton) {
                            authStatusText.textContent = 'Guest';
                            authButton.textContent = 'Sign In';
                            authButton.onclick = openAuthModal;
                        }
                        loadWeather(defaultLocation);
                    }
                });
            } catch (error) {
                console.error("Firebase Auth Error:", error);
            }
        }
        
        function signOutUser() {
             // In a real app, this signs out the current Firebase user.
             signOut(auth).then(() => {
                console.log("[Auth] User signed out successfully.");
                
                // Clear simulated credentials after sign out
                userId = crypto.randomUUID();
                currentUserEmail = null;

                // Update UI manually as onAuthStateChanged may not fire immediately in this environment
                const authStatusText = document.getElementById('auth-status-text');
                const authButton = document.getElementById('auth-button');
                const userIdDisplay = document.getElementById('user-id-display');

                if (authStatusText) authStatusText.textContent = 'Guest';
                if (authButton) {
                    authButton.textContent = 'Sign In';
                    authButton.onclick = openAuthModal;
                }
                if (userIdDisplay) userIdDisplay.textContent = '...';
                loadWeather(defaultLocation);
                
                alert("You have been signed out. Welcome back soon!");
             }).catch((error) => {
                console.error("Sign out error:", error);
             });
        }

        function getProfileDocRef() {
            if (!userId) throw new Error("User ID missing");
            return doc(db, 'artifacts', appId, 'users', userId, 'user_profile', 'data');
        }

        async function saveProfileData(data) {
            try {
                await setDoc(getProfileDocRef(), data, { merge: true });
                console.log("[Data Save] Profile data saved successfully.");
            } catch (e) { 
                // FIX: Log detailed error but don't crash the app on save failure
                console.error("Save Error", e); 
                console.error("CRITICAL: Profile data save failed. This user likely needs an initial grant from Admin to establish permissions.");
            }
        }

        function loadUserProfile() {
            // Check if the currently simulated user is the Dummy 1 test user
            if (currentUserEmail === ARYAN_USER_EMAIL && userId !== ARYAN_USER_ID) {
                console.warn(`[Profile Load] Overriding user ID for test user ${currentUserEmail}.`);
                userId = ARYAN_USER_ID;
            }

            const profileRef = getProfileDocRef();

            onSnapshot(profileRef, (doc) => {
                if (doc.exists()) {
                    userProfile = doc.data();
                    
                    if (userProfile.permissions) {
                        permissions = userProfile.permissions;
                    }
                    updatePermissionsUI();

                    if(document.getElementById('profile-country')) document.getElementById('profile-country').value = userProfile.country || '';
                    if(document.getElementById('profile-state')) document.getElementById('profile-state').value = userProfile.state || '';
                    if(document.getElementById('profile-city')) document.getElementById('profile-city').value = userProfile.city || '';
                    if(document.getElementById('profile-village')) document.getElementById('profile-village').value = userProfile.village || '';
                    if(document.getElementById('profile-zipcode')) document.getElementById('profile-zipcode').value = userProfile.zipcode || '';

                    checkSubscriptionStatus(userProfile.isSubscribed, userProfile.subscriptionEndDate);
                    
                    if (permissions.location === 'Granted' && currentGeolocation) {
                         loadWeather(currentGeolocation);
                    } else {
                         const loc = userProfile.city ? userProfile.city : defaultLocation;
                         loadWeather(loc);
                    }

                } else {
                    userProfile = {};
                    checkSubscriptionStatus(false);
                    loadWeather(defaultLocation);
                }
            }, (error) => {
                // FIX: Added robust error logging for listener failure
                console.error("[Firestore Listener] User Profile Error:", error);
                // Gracefully handle permission errors by continuing without personalized data
                userProfile = {};
                checkSubscriptionStatus(false);
                loadWeather(defaultLocation);
            });
        }

        // Listeners for Global/Public Data
        function listenForBroadcasts() {
            const broadcastRef = doc(db, 'artifacts', appId, 'public', 'data', 'broadcast', 'latest');
            onSnapshot(broadcastRef, (doc) => {
                if (doc.exists() && doc.data().message) {
                    const banner = document.getElementById('global-broadcast');
                    banner.classList.remove('hidden');
                    document.getElementById('broadcast-text').textContent = doc.data().message;
                } else {
                    document.getElementById('global-broadcast').classList.add('hidden');
                }
            }, (error) => {
                 console.error("[Firestore Listener] Broadcast Error:", error);
            });
        }

        function listenForPrices() {
            const pricingRef = doc(db, 'artifacts', appId, 'public', 'data', 'pricing', 'current');
            onSnapshot(pricingRef, (doc) => {
                if (doc.exists()) {
                    globalPrices = doc.data();
                    if(document.getElementById('price-display-3m')) document.getElementById('price-display-3m').textContent = `₹${globalPrices['3m']}`;
                    if(document.getElementById('price-display-6m')) document.getElementById('price-display-6m').textContent = `₹${globalPrices['6m']}`;
                    if(document.getElementById('price-display-1y')) document.getElementById('price-display-1y').textContent = `₹${globalPrices['1y']}`;
                }
            }, (error) => {
                 console.error("[Firestore Listener] Pricing Error:", error);
            });
        }

        function listenForContent() {
            const contentRef = doc(db, 'artifacts', appId, 'public', 'data', 'site_content', 'about_us');
            onSnapshot(contentRef, (doc) => {
                if (doc.exists() && doc.data().aboutUs) {
                    if(document.getElementById('about-us-text')) document.getElementById('about-us-text').textContent = doc.data().aboutUs;
                } else {
                    if(document.getElementById('about-us-text')) document.getElementById('about-us-text').textContent = "AgriAI Global is dedicated to empowering farmers with cutting-edge technology.";
                }
            }, (error) => {
                 console.error("[Firestore Listener] Content Error:", error);
            });
        }

        // --- CSV Export Logic ---
        window.downloadCustomerData = async function() {
            const msgEl = document.getElementById('export-msg');
            
            msgEl.textContent = 'Initiating simulated server-side export...';
            console.warn("[Admin] Attempting Customer Data Export. NOTE: This is simulated due to client-side security restrictions.");
            
            // --- FIX: Simulating Server-Side Export due to Client-Side Security Restrictions ---
            
            // Sample Data structure (what the server would ideally fetch)
            const sampleCustomerData = [
                {
                    userId: 'user_12345',
                    isSubscribed: 'Yes',
                    subscriptionEndDate: '12/01/2026',
                    subscriptionPlan: '1 Year',
                    country: 'India',
                    state: 'Maharashtra',
                    city: 'Pune',
                    lastPayment: '₹599.00'
                },
                {
                    userId: ARYAN_USER_ID,
                    isSubscribed: userProfile.isSubscribed ? 'Yes' : 'No',
                    subscriptionEndDate: userProfile.subscriptionEndDate || 'N/A',
                    subscriptionPlan: userProfile.subscriptionPlan || 'N/A',
                    country: userProfile.country || 'India',
                    state: userProfile.state || 'Maharashtra',
                    city: userProfile.city || 'Pune',
                    lastPayment: userProfile.lastPayment || 'N/A'
                }
            ];

            const headers = ["User ID", "Subscribed", "Expiry Date", "Plan", "Country", "State", "City", "Village", "Zipcode", "Last Payment"];
            
            let csv = headers.join(',') + '\n';
            
            sampleCustomerData.forEach(row => {
                const values = [
                    row.userId,
                    row.isSubscribed,
                    row.subscriptionEndDate,
                    row.subscriptionPlan,
                    row.country,
                    row.state,
                    row.city,
                    row.village || '',
                    row.zipcode || '',
                    row.lastPayment
                ].map(value => {
                    if (typeof value === 'string') {
                        return `"${value.replace(/"/g, '""')}"`;
                    }
                    return value;
                }).join(',');
                csv += values + '\n';
            });

            // 3. Simulate file download
            await new Promise(r => setTimeout(r, 1000)); // Simulate processing delay
            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            const url = URL.createObjectURL(blob);
            link.setAttribute('href', url);
            link.setAttribute('download', `AgriAI_Customer_Export_MOCK_${new Date().toISOString().slice(0, 10)}.csv`);
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);

            msgEl.textContent = `Successfully generated mock CSV with ${sampleCustomerData.length} records. (Server-side implementation required for live data).`;
            console.log(`[Admin] Mock CSV Download initiated successfully.`);
        };


        // --- Payment/Subscription Logic ---

        function switchPaymentMethod(method) {
            activePaymentMethod = method;
            document.querySelectorAll('.payment-tab').forEach(tab => tab.classList.remove('active'));
            document.getElementById(`pay-${method}`).classList.add('active');
            
            const detailsEl = document.getElementById('payment-details');
            
            if (method === 'upi') {
                detailsEl.innerHTML = `
                    <p class="text-sm font-semibold text-gray-800 mb-2">UPI (Simulated)</p>
                    <input type="text" placeholder="Enter UPI ID (e.g., yourname@bank)" class="w-full p-2 border rounded-md">
                    <p class="text-xs text-gray-500 mt-1">A payment request will be simulated.</p>
                `;
            } else if (method === 'net') {
                detailsEl.innerHTML = `
                    <p class="text-sm font-semibold text-gray-800 mb-2">Net Banking (Simulated)</p>
                    <select class="w-full p-2 border rounded-md">
                        <option>Select Bank (Simulated)</option>
                        <option>State Bank of India (SBI)</option>
                        <option>HDFC Bank</option>
                        <option>ICICI Bank</option>
                    </select>
                    <p class="text-xs text-gray-500 mt-1">You will be redirected to the bank's portal.</p>
                `;
            } else if (method === 'wallet') {
                detailsEl.innerHTML = `
                    <p class="text-sm font-semibold text-gray-800 mb-2">Digital Wallet (Simulated)</p>
                    <select class="w-full p-2 border rounded-md">
                        <option>Select Wallet (Simulated)</option>
                        <option>Paytm</option>
                        <option>PhonePe</option>
                        <option>JioMoney</option>
                    </select>
                    <p class="text-xs text-gray-500 mt-1">Confirm payment via your selected wallet.</p>
                `;
            }
        }

        // Initialize payment method on modal open
        window.onload = () => {
             // ... existing onload logic ...
             
             // Initial check for payment method display logic
             const checkoutModal = document.getElementById('checkout-modal');
             if (checkoutModal) {
                 // Initialize UPI view when the modal first opens, but only once the DOM is ready.
                 const observer = new MutationObserver((mutationsList, observer) => {
                     mutationsList.forEach(mutation => {
                         if (mutation.type === 'attributes' && mutation.attributeName === 'class') {
                             if (!checkoutModal.classList.contains('hidden')) {
                                 // Modal is now visible
                                 switchPaymentMethod('upi');
                             }
                         }
                     });
                 });
                 observer.observe(checkoutModal, { attributes: true });
                 
                 if (firebaseConfig && Object.keys(firebaseConfig).length > 0) {
                     firebaseInitAndAuth();
                 } else {
                     console.warn("No Firebase Config");
                 }
                 // Set up 30-minute weather refresh
                 setInterval(() => {
                     if(userProfile.city) {
                         loadWeather(userProfile.city);
                     } else {
                         loadWeather(defaultLocation);
                     }
                 }, 30 * 60 * 1000); // 30 minutes
             }
         };

        function checkSubscriptionStatus(isSubscribed, endDateString) {
            const now = new Date();
            let isActive = false;
            let statusText = 'Not Subscribed';

            if (isSubscribed && endDateString) {
                const endDate = new Date(endDateString);
                if (endDate > now) {
                    isActive = true;
                    const isTrial = userProfile.subscriptionPlan === 'Trial';
                    statusText = `${isTrial ? 'Trial' : 'Active'} until ${endDate.toLocaleDateString()}`;
                } else {
                    statusText = `Expired on ${endDate.toLocaleDateString()}`;
                    userProfile.isSubscribed = false; // Local update
                }
            }

            const subStatus = document.getElementById('subscription-status');
            if(subStatus) {
                subStatus.innerHTML = isActive ? 
                    `<p class="text-green-600 font-bold">Status: ${statusText}</p>` :
                    `<p class="text-red-600 font-bold">Status: ${statusText}</p><p class="text-xs text-gray-500">Access to premium features is restricted.</p>`;
            }

            // Toggle Features
            const elementsToToggle = [
                { id: 'subscription-plans', show: !isActive },
                { id: 'manage-sub-btn', show: isActive },
                { id: 'chat-input-area', show: isActive },
                { id: 'chat-restricted-message', show: !isActive },
                { id: 'advisory-content', show: isActive },
                { id: 'advisory-placeholder', show: !isActive },
                // Hide Ads if subscribed
                { id: 'top-ad-banner', show: !isActive },
                { id: 'sidebar-ad', show: !isActive }
            ];

            elementsToToggle.forEach(el => {
                const domEl = document.getElementById(el.id);
                if (domEl) {
                    if (el.show) domEl.classList.remove('hidden');
                    else domEl.classList.add('hidden');
                }
            });

            if(document.getElementById('chat-input')) document.getElementById('chat-input').disabled = !isActive;
            if(document.getElementById('chat-send-btn')) document.getElementById('chat-send-btn').disabled = !isActive;

            // Trigger ad rotation if NOT active
            if (!isActive) rotateAds();
        }

        // Window exposed functions for HTML buttons
        window.openCheckoutModal = function(months, price, name) {
            currentCheckoutPlan = { months, price, name, finalPrice: price, discount: 0 };
            
            if(document.getElementById('checkout-plan-name')) document.getElementById('checkout-plan-name').textContent = name;
            if(document.getElementById('checkout-price-original')) document.getElementById('checkout-price-original').textContent = formatCurrency(price);
            if(document.getElementById('checkout-discount')) document.getElementById('checkout-discount').textContent = `- ₹0.00`;
            if(document.getElementById('checkout-tax')) document.getElementById('checkout-tax').textContent = formatCurrency(price * TAX_RATE);
            if(document.getElementById('checkout-total-due')) document.getElementById('checkout-total-due').textContent = formatCurrency(price * (1 + TAX_RATE));
            
            // Reset Coupon
            if(document.getElementById('coupon-input')) document.getElementById('coupon-input').value = '';
            if(document.getElementById('coupon-message')) document.getElementById('coupon-message').textContent = 'Try AGRI10 for a 10% discount!';
            if(document.getElementById('coupon-message')) document.getElementById('coupon-message').className = 'text-xs mt-2 italic text-gray-500';
            
            // Reset Purchase Code
            if(document.getElementById('purchase-code-input')) document.getElementById('purchase-code-input').value = '';
            if(document.getElementById('redeem-message')) document.getElementById('redeem-message').textContent = `Use **${PURCHASE_CODE}** for immediate access (Test Only).`;
            if(document.getElementById('redeem-message')) document.getElementById('redeem-message').className = 'text-xs mt-2 italic text-purple-500';
            
            // Set default payment method tab to UPI
            switchPaymentMethod('upi'); 
            
            if(document.getElementById('checkout-modal')) document.getElementById('checkout-modal').classList.remove('hidden');
        };

        window.closeCheckoutModal = () => document.getElementById('checkout-modal').classList.add('hidden');

        window.applyCoupon = function() {
            const code = document.getElementById('coupon-input').value.toUpperCase().trim();
            const msg = document.getElementById('coupon-message');
            let discount = 0;

            if (code === 'AGRI10') {
                discount = currentCheckoutPlan.price * 0.10;
                msg.textContent = "Coupon Applied! 10% Off.";
                msg.className = "text-xs mt-2 italic text-green-600";
            } else {
                msg.textContent = "Invalid Coupon.";
                msg.className = "text-xs mt-2 italic text-red-600";
            }

            currentCheckoutPlan.discount = discount;
            const afterDisc = currentCheckoutPlan.price - discount;
            const tax = afterDisc * TAX_RATE;
            const total = afterDisc + tax;

            document.getElementById('checkout-discount').textContent = `- ${formatCurrency(discount)}`;
            document.getElementById('checkout-tax').textContent = formatCurrency(tax);
            document.getElementById('checkout-total-due').textContent = formatCurrency(total);
            currentCheckoutPlan.finalPrice = total;
        };

        window.redeemPurchaseCode = async function() {
            const codeInput = document.getElementById('purchase-code-input');
            const redeemMsg = document.getElementById('redeem-message');
            const code = codeInput.value.toUpperCase().trim();

            if (!userId) {
                redeemMsg.textContent = "Error: Please sign in first to redeem a code.";
                redeemMsg.className = 'text-xs mt-2 italic text-red-500';
                return;
            }

            if (code === PURCHASE_CODE) {
                redeemMsg.textContent = "Code Accepted! Granting subscription...";
                redeemMsg.className = 'text-xs mt-2 italic text-green-600 font-bold';
                
                const expiry = new Date();
                expiry.setFullYear(expiry.getFullYear() + 1); // Grant 1 year for the test code

                // Grant subscription immediately without payment simulation
                await saveProfileData({
                    isSubscribed: true,
                    subscriptionEndDate: expiry.toISOString(),
                    subscriptionPlan: `Purchase Code (${PURCHASE_CODE})`,
                    lastPayment: 0
                });

                window.closeCheckoutModal();
                alert("Subscription granted successfully via purchase code! You now have premium access for 1 year.");
                console.log(`[Subscription] User granted 1-year access using code ${PURCHASE_CODE}.`);
                
            } else {
                redeemMsg.textContent = "Invalid Purchase Code. Please try again.";
                redeemMsg.className = 'text-xs mt-2 italic text-red-500';
            }
        };

        window.processPayment = async function() {
            if (!userId) return;

            const purchaseCode = document.getElementById('purchase-code-input').value.toUpperCase().trim();
            const paymentMessageEl = document.getElementById('payment-processing-message');

            // 1. CHECK FOR TEST CODE FIRST (If code is entered, redeem it instead of processing payment)
            if (purchaseCode) {
                 // Check if the purchase code is valid
                 if (purchaseCode === PURCHASE_CODE) {
                      paymentMessageEl.textContent = `Purchase Code ${PURCHASE_CODE} recognized. Activating premium services...`;
                      paymentMessageEl.classList.remove('hidden');
                      await new Promise(r => setTimeout(r, 1000));
                      return redeemPurchaseCode(); // Executes the granting logic and closes modal
                 } else {
                     alert("Invalid Purchase Code. Please remove the code or enter a valid one to proceed with normal payment.");
                     return;
                 }
            }


            // 2. NORMAL PAYMENT SIMULATION
            const btn = document.getElementById('final-payment-btn');
            if(btn) btn.disabled = true;
            
            paymentMessageEl.textContent = `Initiating simulated ${activePaymentMethod} transaction...`;
            paymentMessageEl.classList.remove('hidden');

            // Simulate Gateway
            await new Promise(r => setTimeout(r, 2000));

            const expiry = new Date();
            expiry.setMonth(expiry.getMonth() + currentCheckoutPlan.months);

            await saveProfileData({
                isSubscribed: true,
                subscriptionEndDate: expiry.toISOString(),
                subscriptionPlan: currentCheckoutPlan.name,
                lastPayment: currentCheckoutPlan.finalPrice
            });

            window.closeCheckoutModal();
            if(btn) btn.disabled = false;
            paymentMessageEl.classList.add('hidden');
            alert(`Payment Successful via ${activePaymentMethod.toUpperCase()}! Welcome to AgriAI Premium.`);
        };

        window.startTrial = async function() {
            if (!userId) return;
            const expiry = new Date();
            expiry.setDate(expiry.getDate() + 7); // 7 Days

            await saveProfileData({
                isSubscribed: true,
                subscriptionEndDate: expiry.toISOString(),
                subscriptionPlan: 'Trial'
            });
            alert("7-Day Free Trial Started!");
        };

        // --- Auth Modals & Simulation ---
        
        window.openAuthModal = () => document.getElementById('auth-modal').classList.remove('hidden');
        window.closeAuthModal = () => {
             document.getElementById('auth-modal').classList.add('hidden');
             document.getElementById('auth-message').textContent = '';
        }

        window.simulateGoogleLogin = function() {
            console.log("[Auth] Google Login Simulation initiated.");
            const msg = document.getElementById('auth-message');
            msg.className = 'text-sm mt-3 text-center text-blue-600';
            msg.textContent = "Google Login is simulated. Please use Email/Password below. (Native pop-ups are blocked in this environment)";
            // Simulate successful login of the default user for demonstration
            // In a real app, this would use GoogleAuthProvider and Firebase actual sign-in.
        };


        window.simulatedAuth = async function(type) {
            const emailInput = document.getElementById('auth-email');
            const passwordInput = document.getElementById('auth-password');
            const email = emailInput.value.trim();
            const password = passwordInput.value;
            const msg = document.getElementById('auth-message');

            if (!email.includes('@') || !email.includes('.')) {
                msg.className = 'text-sm mt-3 text-center text-red-500';
                msg.textContent = "Please enter a valid email address.";
                console.warn(`[Auth] Failed attempt (${type}): Invalid email format.`);
                return;
            }

            let loginSuccess = false;
            let simulatedUserId = crypto.randomUUID(); // Default anonymous ID
            let simulatedEmail = null;
            let simulatedDisplayName = 'User';

            if (type === 'signin') {
                // 1. Check specific unique user (itsmearyankumar.ak@gmail.com)
                if (email === ARYAN_USER_EMAIL && password === ARYAN_USER_PASSWORD) {
                    loginSuccess = true;
                    // Assign a specific simulated ID for consistent persistence
                    simulatedUserId = ARYAN_USER_ID; 
                    simulatedEmail = ARYAN_USER_EMAIL;
                    simulatedDisplayName = 'Dummy 1'; // Set display name as requested
                } 
                // 2. Check default generic users
                else if (password === DEFAULT_USER_PASSWORD && email !== ADMIN_EMAIL) {
                    loginSuccess = true;
                    simulatedEmail = email;
                    // For generic users, we use a hash of their email for a deterministic user ID
                    simulatedUserId = btoa(email).slice(0, 15); 
                } 
                // 3. Admin error check
                else if (email === ADMIN_EMAIL) {
                    msg.className = 'text-sm mt-3 text-center text-red-500';
                    msg.textContent = "Admin credentials must be entered in the dedicated Admin Login.";
                    console.warn(`[Auth] Admin attempted login via user modal: ${email}`);
                    return;
                }

                if (loginSuccess) {
                    msg.className = 'text-sm mt-3 text-center text-green-600';
                    msg.textContent = `Sign In simulated successfully for ${email}! Access Granted.`;
                    console.log(`[Auth] Successful simulated sign-in for user: ${email} (Simulated ID: ${simulatedUserId})`);
                    
                    // --- CRITICAL SIMULATION STEP: Update global state and trigger app refresh ---
                    userId = simulatedUserId;
                    currentUserEmail = simulatedEmail;
                    
                    // Note: Removed Auto-Grant for Dummy 1. Admin must grant access manually once.
                    if (email === ARYAN_USER_EMAIL) {
                         // Ensure profile exists immediately if logging in for the first time as Dummy 1
                         // FIX: Await saveProfileData to ensure ID is associated with the profile path immediately
                         await saveProfileData({ lastLogin: new Date().toISOString() }); 
                         console.log('[Auth] Dummy 1 logged in. Profile initialized. Subscription status: Requires Admin Grant or Purchase.');
                    }


                    // Update header UI immediately
                    const authStatusText = document.getElementById('auth-status-text');
                    const authButton = document.getElementById('auth-button');
                    const userIdDisplay = document.getElementById('user-id-display');

                    if (authStatusText) authStatusText.textContent = simulatedDisplayName;
                    if (authButton) {
                        authButton.textContent = 'Sign Out';
                        authButton.onclick = signOutUser;
                    }
                    if (userIdDisplay) userIdDisplay.textContent = userId.substring(0, 8) + '...';
                    
                    closeAuthModal();
                    loadUserProfile(); // Refresh all personalized data

                } else {
                    msg.className = 'text-sm mt-3 text-center text-red-500';
                    msg.textContent = "Sign In failed. Incorrect password.";
                    console.warn(`[Auth] Failed sign-in attempt for ${email}: Incorrect password.`);
                }
            } else if (type === 'register') {
                // Simulating registration
                msg.className = 'text-sm mt-3 text-center text-green-600';
                msg.textContent = `Registration simulated for ${email}. You can now sign in!`;
                console.log(`[Auth] Successful simulated registration for user: ${email}`);
            } else if (type === 'reset') {
                // Simulating password reset
                msg.className = 'text-sm mt-3 text-center text-green-600';
                msg.textContent = `Password reset email simulated sent to ${email}. Check your inbox!`;
                console.log(`[Auth] Password reset simulation initiated for: ${email}`);
            }
        }


        // --- Admin Functions ---

        window.openAdminLogin = () => {
            // Reset state
            const errorMsg = document.getElementById('admin-login-error');
            if (errorMsg) errorMsg.classList.add('hidden');
            if(document.getElementById('admin-email-input')) document.getElementById('admin-email-input').value = '';
            if(document.getElementById('admin-password')) document.getElementById('admin-password').value = '';
            
            document.getElementById('admin-modal').classList.remove('hidden');
            document.getElementById('admin-login-view').classList.remove('hidden');
            document.getElementById('admin-dashboard-view').classList.add('hidden');
        };
        
        window.closeAdminModal = () => document.getElementById('admin-modal').classList.add('hidden');

        window.attemptAdminLogin = function() {
            const email = document.getElementById('admin-email-input').value.trim(); // NEW
            const pass = document.getElementById('admin-password').value;
            const errorMsg = document.getElementById('admin-login-error');
            
            // 1. Check Email (Primary security gate)
            if (email !== ADMIN_EMAIL) {
                errorMsg.textContent = "Access Denied: Unauthorized Email provided.";
                errorMsg.classList.remove('hidden');
                console.warn(`[Admin] Login failed: Unauthorized email attempt: ${email}`);
                return;
            }

            // 2. Check Password (Secondary local gate)
            if (pass === ADMIN_PASSCODE) {
                document.getElementById('admin-login-view').classList.add('hidden');
                document.getElementById('admin-dashboard-view').classList.remove('hidden');
                
                // Log successful login
                console.log(`[Admin] Access Granted for ${email}. Passcode confirmed.`);

                // Load current values
                document.getElementById('price-3m').value = globalPrices['3m'];
                document.getElementById('price-6m').value = globalPrices['6m'];
                document.getElementById('price-1y').value = globalPrices['1y'];
                document.getElementById('admin-about-us').value = document.getElementById('about-us-text').textContent.trim();
                errorMsg.classList.add('hidden');
            } else {
                errorMsg.textContent = "Incorrect passcode.";
                errorMsg.classList.remove('hidden');
                console.warn(`[Admin] Login failed for ${email}: Incorrect passcode.`);
            }
        };

        window.switchAdminTab = function(tabName) {
            // Remove active class from all buttons
            document.querySelectorAll('#admin-dashboard-view .tab-btn').forEach(el => el.classList.remove('active'));
            
            // Add active class to the corresponding button
            // Using attribute selector to target the button by its onclick argument
            const targetButton = document.querySelector(`#admin-dashboard-view .tab-btn[onclick*="'${tabName}'"]`);
            if (targetButton) {
                targetButton.classList.add('active');
            }

            // Hide all content tabs and show the selected one
            document.querySelectorAll('.admin-tab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(`admin-tab-${tabName}`).classList.remove('hidden');
            console.log(`[Admin] Switched to tab: ${tabName}`);
        };

        window.saveBroadcast = async function() {
            const msg = document.getElementById('broadcast-input').value;
            // FIX: Ensure path is even length: .../broadcast/latest
            await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'broadcast', 'latest'), { message: msg });
            document.getElementById('broadcast-msg').textContent = "Broadcast Sent!";
            console.log(`[Admin] Global broadcast published: "${msg.substring(0, 30)}..."`);
        };

        window.clearBroadcast = async function() {
            // FIX: Ensure path is even length: .../broadcast/latest
            await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'broadcast', 'latest'), { message: null });
            document.getElementById('broadcast-input').value = "";
            document.getElementById('broadcast-msg').textContent = "Broadcast Cleared.";
            console.log("[Admin] Global broadcast cleared.");
        };

        window.savePricing = async function() {
            const prices = {
                '3m': Number(document.getElementById('price-3m').value),
                '6m': Number(document.getElementById('price-6m').value),
                '1y': Number(document.getElementById('price-1y').value)
            };
            // FIX: Ensure path is even length: .../pricing/current
            await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'pricing', 'current'), prices);
            document.getElementById('pricing-msg').textContent = "Pricing Updated!";
            console.log("[Admin] Pricing updated:", prices);
        };

        window.saveAboutUs = async function() {
            const content = document.getElementById('admin-about-us').value;
            // FIX: Ensure path is even length: .../site_content/about_us
            await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'site_content', 'about_us'), { aboutUs: content }, { merge: true });
            document.getElementById('content-msg').textContent = "Content Saved!";
            console.log(`[Admin] About Us content saved: "${content.substring(0, 30)}..."`);
        };

        window.adminGrantSub = async function() {
            let targetId = document.getElementById('admin-user-id').value;
            
            // --- FIX: Automatic Test User ID injection ---
            if (!targetId) {
                targetId = ARYAN_USER_ID; 
                document.getElementById('admin-user-id').value = targetId;
                document.getElementById('admin-user-msg').textContent = "Using Dummy 1 User ID for test.";
            }

            try {
                const expiry = new Date();
                expiry.setFullYear(expiry.getFullYear() + 1);
                await setDoc(doc(db, 'artifacts', appId, 'users', targetId, 'user_profile', 'data'), {
                    isSubscribed: true,
                    subscriptionEndDate: expiry.toISOString(),
                    subscriptionPlan: 'Admin Grant'
                }, { merge: true });
                document.getElementById('admin-user-msg').textContent = `Success: Granted 1 Year to ${targetId}.`;
                console.log(`[Admin] Granted 1-year subscription to user ID: ${targetId}`);
            } catch(e) {
                document.getElementById('admin-user-msg').textContent = "Error: Check permissions/ID.";
                console.error(`[Admin] Failed to grant subscription to ${targetId}:`, e);
            }
        };

        window.adminRevokeSub = async function() {
            let targetId = document.getElementById('admin-user-id').value;
            
            if (!targetId) {
                targetId = ARYAN_USER_ID; 
                document.getElementById('admin-user-id').value = targetId;
            }

            try {
                await setDoc(doc(db, 'artifacts', appId, 'users', targetId, 'user_profile', 'data'), {
                    isSubscribed: false,
                    subscriptionEndDate: null
                }, { merge: true });
                document.getElementById('admin-user-msg').textContent = `Success: Revoked subscription from ${targetId}.`;
                console.log(`[Admin] Revoked subscription from user ID: ${targetId}`);
            } catch(e) {
                document.getElementById('admin-user-msg').textContent = "Error: Check permissions/ID.";
                console.error(`[Admin] Failed to revoke subscription from ${targetId}:`, e);
            }
        };

        // --- Chatbot ---
        // (Re-using previous logic, attached to window listeners in HTML)
        document.getElementById('chat-send-btn').addEventListener('click', () => {
            const input = document.getElementById('chat-input');
            const txt = input.value.trim();
            if(txt) {
                // Add user message
                const userMsgDiv = document.createElement('div');
                userMsgDiv.className = 'chat-message user-message';
                userMsgDiv.textContent = txt;
                document.getElementById('chat-window').appendChild(userMsgDiv);

                // Add loading indicator
                const loadingDiv = document.createElement('div');
                loadingDiv.id = 'ai-loading-indicator';
                loadingDiv.className = 'ai-message chat-message text-gray-500 italic';
                loadingDiv.textContent = 'AgriAI is typing...';
                document.getElementById('chat-window').appendChild(loadingDiv);

                document.getElementById('chat-window').scrollTop = document.getElementById('chat-window').scrollHeight;
                
                input.value = '';
                console.log(`[Chatbot] User query sent: "${txt}"`);
                callGeminiAPI(txt);
            }
        });
        
        async function callGeminiAPI(prompt) {
            const loadingIndicator = document.getElementById('ai-loading-indicator');
            
            const locationContext = [userProfile.village, userProfile.city, userProfile.state, userProfile.country]
                .filter(Boolean)
                .join(', ');
                
            const systemPrompt = `You are AgriAI, a world-class agricultural expert and farm consultant. Your role is to provide actionable, grounded advice to farmers. The user's location is: "${locationContext || 'Unknown Location'}". Use this context when providing advice related to planting, pest control, or weather interpretation. Be concise, practical, and highly relevant to farming decisions.`;
            
            const payload = {
                contents: [{ parts: [{ text: prompt }] }],
                tools: [{ "google_search": {} }],
                systemInstruction: { parts: [{ text: systemPrompt }] },
            };
            
            const apiCall = async () => {
                const response = await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                
                if (!response.ok) {
                    throw new Error(`API call failed: ${response.statusText}`);
                }
                
                return response.json();
            };

            try {
                const result = await fetchWithRetry(apiCall);
                const text = result.candidates?.[0]?.content?.parts?.[0]?.text || "Sorry, I could not generate a response. Please try rephrasing your question.";
                console.log(`[Chatbot] Response received.`);
                
                // Remove loading indicator
                if (loadingIndicator) loadingIndicator.remove();

                const aiMsgDiv = document.createElement('div');
                aiMsgDiv.className = 'chat-message ai-message whitespace-pre-wrap';
                aiMsgDiv.textContent = text;
                document.getElementById('chat-window').appendChild(aiMsgDiv);
                document.getElementById('chat-window').scrollTop = document.getElementById('chat-window').scrollHeight;

            } catch (error) {
                console.error("Gemini API Error:", error);
                
                // Remove loading indicator
                if (loadingIndicator) {
                    loadingIndicator.textContent = "AI Error: Could not reach the service. Please check your network.";
                    loadingIndicator.classList.add('text-red-500');
                    loadingIndicator.classList.remove('text-gray-500', 'italic');
                } else {
                    const errorDiv = document.createElement('div');
                    errorDiv.className = 'ai-message chat-message text-red-500';
                    errorDiv.textContent = "AI Error: Could not reach the service. Please check your network.";
                    document.getElementById('chat-window').appendChild(errorDiv);
                }
            }
        }


        // Initialize
        document.getElementById('profile-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const data = {
                country: document.getElementById('profile-country').value,
                state: document.getElementById('profile-state').value,
                city: document.getElementById('profile-city').value,
                village: document.getElementById('profile-village').value,
                zipcode: document.getElementById('profile-zipcode').value,
            };
            await saveProfileData(data);
            document.getElementById('profile-message').textContent = 'Location updated successfully.';
            setTimeout(() => document.getElementById('profile-message').textContent = '', 3000);
        });
        
        document.getElementById('search-button').addEventListener('click', async () => {
             const searchLocation = document.getElementById('location-input').value;
             if (!searchLocation) return;
             
             try {
                // Fetch multiple results for selection
                const results = await getCoordinates(searchLocation, true);
                
                if (results.length > 1) {
                    openSearchResultsModal(results, searchLocation);
                } else if (results.length === 1) {
                    // Automatically load if only one result is found
                    const result = results[0];
                    selectLocation(result.latitude, result.longitude, result.name, result.country);
                } else {
                    // Fallback to old single search logic if multiple search fails
                    loadWeather(searchLocation);
                }
             } catch (error) {
                 console.error("[Search] Multi-search failed, falling back or showing error:", error);
                 loadWeather(searchLocation); // Fallback to old single-result search logic
             }
        });
        
        // --- Enter Key Listener ---
        document.addEventListener('keydown', (event) => {
            if (event.key === 'Enter') {
                const activeElement = document.activeElement;
                
                // 1. Search Bar
                if (activeElement === document.getElementById('location-input')) {
                    event.preventDefault(); // Prevent form submission if input is inside a form
                    document.getElementById('search-button').click();
                } 
                
                // 2. Chat Input
                else if (activeElement === document.getElementById('chat-input') && !activeElement.disabled) {
                    event.preventDefault();
                    document.getElementById('chat-send-btn').click();
                } 
                
                // 3. Modals (Admin Login, Auth Sign-in)
                else if (document.getElementById('admin-modal').classList.contains('hidden') === false && 
                         document.getElementById('admin-login-view').classList.contains('hidden') === false &&
                         (activeElement === document.getElementById('admin-email-input') || activeElement === document.getElementById('admin-password'))) {
                    event.preventDefault();
                    document.getElementById('admin-login-btn').click();
                } 
                else if (document.getElementById('auth-modal').classList.contains('hidden') === false &&
                         (activeElement === document.getElementById('auth-email') || activeElement === document.getElementById('auth-password'))) {
                    event.preventDefault();
                    document.getElementById('auth-sign-in-btn').click();
                }
                // 4. Checkout Modal (Final Payment)
                else if (document.getElementById('checkout-modal').classList.contains('hidden') === false &&
                         !document.getElementById('final-payment-btn').disabled) {
                    event.preventDefault();
                    document.getElementById('final-payment-btn').click();
                }
            }
        });


        window.onload = () => {
            if (firebaseConfig && Object.keys(firebaseConfig).length > 0) {
                firebaseInitAndAuth();
            } else {
                console.warn("No Firebase Config");
            }
            // Set up 30-minute weather refresh
            setInterval(() => {
                if(userProfile.city) {
                    loadWeather(userProfile.city);
                } else {
                    loadWeather(defaultLocation);
                }
            }, 30 * 60 * 1000); // 30 minutes
        };

    </script>
</body>
</html>
