<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FitApp: Comprehensive Macro and TDEE Tracker</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom font for a modern feel */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
        
        /* FIX FOR LAYOUT GAP: Reset default browser margins/padding */
        html, body {
            margin: 0;
            padding: 0;
            height: 100%; 
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: #0a0a0a; 
        }
        /* Custom scrollbar for aesthetic */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-thumb {
            background: #4b5563; 
            border-radius: 10px;
        }
    </style>
</head>
<body class="min-h-screen antialiased bg-gray-950 text-gray-200">

    <div id="app" class="max-w-4xl mx-auto p-4 md:p-8">

        <!-- Main Content Header (Now H2) -->
        <header class="text-center mb-8 p-4 bg-gray-800 rounded-xl shadow-2xl">
            <h2 class="text-4xl font-extrabold text-white">Macro & TDEE Tracker</h2>
            <p class="text-gray-400 mt-2">Log food by amount, track exercises, and monitor expenditure.</p>
        </header>

        <!-- BMR / TDEE Calculator and Profile Section -->
        <div class="bg-gray-800 p-6 rounded-xl shadow-2xl mb-8">
            <h3 class="text-xl font-semibold mb-4 text-white border-b border-gray-700 pb-2">Personal Metabolism Calculator</h3>
            
            <div class="grid grid-cols-2 md:grid-cols-5 gap-4 mb-6">
                
                <!-- Gender -->
                <div>
                    <label for="profile-gender" class="text-sm font-medium text-gray-400 block mb-1">Gender</label>
                    <select id="profile-gender" class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-blue-500 focus:border-blue-500">
                        <option value="male">Male</option>
                        <option value="female">Female</option>
                    </select>
                </div>

                <!-- Weight -->
                <div>
                    <label for="profile-weight" class="text-sm font-medium text-gray-400 block mb-1">Weight (kg)</label>
                    <input type="number" id="profile-weight" value="70" min="1"
                        class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-blue-500 focus:border-blue-500">
                </div>

                <!-- Height -->
                <div>
                    <label for="profile-height" class="text-sm font-medium text-gray-400 block mb-1">Height (cm)</label>
                    <input type="number" id="profile-height" value="175" min="1"
                        class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-blue-500 focus:border-blue-500">
                </div>

                <!-- Age -->
                <div>
                    <label for="profile-age" class="text-sm font-medium text-gray-400 block mb-1">Age (years)</label>
                    <input type="number" id="profile-age" value="30" min="1"
                        class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-blue-500 focus:border-blue-500">
                </div>

                <!-- Activity Level -->
                <div class="col-span-2 md:col-span-1">
                    <label for="profile-activity" class="text-sm font-medium text-gray-400 block mb-1">Activity Level</label>
                    <select id="profile-activity" class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-blue-500 focus:border-blue-500">
                        <option value="1.2">Sedentary (Little or no exercise)</option>
                        <option value="1.375">Light (1-3 days/week)</option>
                        <option value="1.55" selected>Moderate (3-5 days/week)</option>
                        <option value="1.725">High (6-7 days/week)</option>
                        <option value="1.9">Very High (Daily intense exercise)</option>
                    </select>
                </div>

            </div>

            <!-- BMR & TDEE Results -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-center mt-6">
                <div class="p-4 rounded-lg border-2 border-blue-600">
                    <span class="text-xs font-semibold uppercase text-blue-400">Basal Metabolic Rate (BMR)</span>
                    <p id="bmr-result" class="text-3xl font-extrabold text-white mt-1">0 kcal</p>
                </div>
                <div class="p-4 rounded-lg border-2 border-indigo-600">
                    <span class="text-xs font-semibold uppercase text-indigo-400">Total Daily Energy Exp. (TDEE)</span>
                    <p id="tdee-result" class="text-3xl font-extrabold text-white mt-1">0 kcal</p>
                </div>
            </div>
            
            <p id="user-id-display" class="text-xs text-gray-500 mt-4 text-center break-all">User ID: Loading...</p>
        </div>


        <!-- Summary and Input Container (Responsive Layout) -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
            
            <!-- Daily Summary Card (lg:col-span-1) -->
            <div class="lg:col-span-1 bg-gradient-to-br from-blue-800 to-indigo-900 p-6 rounded-xl shadow-lg text-white shadow-blue-900/50 h-fit order-1 lg:order-1">
                <h3 class="text-xl font-semibold mb-3">Daily Summary</h3>
                
                <hr class="border-t border-white border-opacity-30 my-3">

                <!-- TDEE -->
                <div class="flex justify-between items-center mb-2 text-sm text-gray-300">
                    <span>TDEE (Total Burned)</span>
                    <span id="tdee-in-summary" class="font-bold text-lg">0</span>
                </div>
                
                <div class="flex justify-between items-center mb-4 p-2 rounded-md bg-white/10">
                    <span class="text-sm font-medium">Calories Consumed (Intake)</span>
                    <span id="total-calories-consumed" class="text-3xl font-bold text-indigo-300">0</span>
                </div>
                
                <hr class="border-t border-white border-opacity-30 my-3">
                
                <div class="flex justify-between items-center">
                    <span class="text-sm font-medium opacity-80">Net Calories (TDEE - Consumed)</span>
                    <span id="net-calories-result" class="text-3xl font-bold text-yellow-300">0</span>
                </div>
            </div>

            <!-- Input Forms Container (lg:col-span-2) -->
            <div class="lg:col-span-2 grid grid-cols-1 md:grid-cols-2 gap-6 order-2 lg:order-2">

                <!-- Food Input Form -->
                <div class="bg-gray-800 p-6 rounded-xl shadow-2xl">
                    <h3 class="text-xl font-semibold mb-4 text-white">Log Food Intake</h3>
                    <div class="space-y-4">
                        
                        <!-- Food Name Input -->
                        <input type="text" id="food-item" placeholder="Food Item (e.g., Apple, Chicken Breast)"
                            class="w-full p-3 border border-gray-600 rounded-lg focus:ring-indigo-500 focus:border-indigo-500 transition duration-150 bg-gray-700 text-white placeholder-gray-400">
                        
                        <!-- Food Amount Input -->
                        <div>
                            <label for="food-amount" class="text-sm font-medium text-gray-400 block mb-1">Amount Consumed (grams)</label>
                            <input type="number" id="food-amount" value="100" min="1"
                                class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-indigo-500 focus:border-indigo-500 transition duration-150 placeholder-gray-400">
                        </div>

                        <!-- Macro Display Preview -->
                        <div id="macro-preview" class="p-4 bg-gray-700 rounded-lg text-sm hidden">
                            <p class="text-gray-400 mb-2">Estimated Macros for this amount:</p>
                            <div class="flex justify-between">
                                <span id="preview-calories" class="font-bold text-indigo-400">0 kcal</span>
                                <span id="preview-protein" class="font-bold text-green-400">P: 0g</span>
                                <span id="preview-fat" class="font-bold text-red-400">F: 0g</span>
                                <span id="preview-carbs" class="font-bold text-yellow-400">C: 0g</span>
                            </div>
                        </div>

                        <!-- Log Button -->
                        <button id="add-food-btn"
                            class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 rounded-lg transition duration-200 shadow-md shadow-indigo-500/50">
                            Log Meal
                        </button>
                    </div>
                </div>
                
                <!-- Exercise Input Form -->
                <div class="bg-gray-800 p-6 rounded-xl shadow-2xl">
                    <h3 class="text-xl font-semibold mb-4 text-white">Log Exercise</h3>
                    <div class="space-y-4">
                        
                        <!-- Exercise Name Input -->
                        <input type="text" id="exercise-name" placeholder="Exercise Name (e.g., Bench Press, Squats)"
                            class="w-full p-3 border border-gray-600 rounded-lg focus:ring-green-500 focus:border-green-500 transition duration-150 bg-gray-700 text-white placeholder-gray-400">
                        
                        <div class="grid grid-cols-2 gap-4">
                            <!-- Sets Input -->
                            <div>
                                <label for="exercise-sets" class="text-sm font-medium text-gray-400 block mb-1">Sets</label>
                                <input type="number" id="exercise-sets" value="3" min="1"
                                    class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-green-500 focus:border-green-500 transition duration-150 placeholder-gray-400">
                            </div>
                            <!-- Reps Input -->
                            <div>
                                <label for="exercise-reps" class="text-sm font-medium text-gray-400 block mb-1">Reps Per Set</label>
                                <input type="number" id="exercise-reps" value="10" min="1"
                                    class="w-full p-3 border border-gray-600 rounded-lg bg-gray-700 text-white focus:ring-green-500 focus:border-green-500 transition duration-150 placeholder-gray-400">
                            </div>
                        </div>

                        <!-- Log Button -->
                        <button id="add-exercise-btn"
                            class="w-full bg-green-600 hover:bg-green-700 text-white font-bold py-3 rounded-lg transition duration-200 shadow-md shadow-green-500/50">
                            Log Exercise
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Message box for errors (placed centrally) -->
        <div id="message-box" class="hidden p-3 text-sm rounded-lg mb-8" role="alert"></div>

        <!-- Food History Section -->
        <div class="bg-gray-800 p-6 rounded-xl shadow-2xl mb-8">
            <h3 class="text-xl font-semibold mb-4 text-white border-b border-gray-700 pb-2">Food Intake History</h3>
            <div id="food-entries-list" class="space-y-3">
                <!-- Food Entries will be dynamically inserted here -->
                <p id="loading-history" class="text-center text-indigo-400 mt-4">Connecting to Database...</p>
            </div>
            <p id="empty-state-food" class="text-center text-gray-400 mt-8 hidden">No food logged yet. Start logging above!</p>
        </div>

        <!-- Exercise History Section -->
        <div class="bg-gray-800 p-6 rounded-xl shadow-2xl">
            <h3 class="text-xl font-semibold mb-4 text-white border-b border-gray-700 pb-2">Exercise History (Sets & Reps)</h3>
            <div id="exercise-entries-list" class="space-y-3">
                <!-- Exercise Entries will be dynamically inserted here -->
                <p id="loading-exercise-history" class="text-center text-green-400 mt-4">Connecting to Database...</p>
            </div>
            <p id="empty-state-exercise" class="text-center text-gray-400 mt-8 hidden">No exercise logged yet. Start logging above!</p>
        </div>

    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { 
            getAuth, 
            signInAnonymously, 
            signInWithCustomToken, 
            onAuthStateChanged 
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { 
            getFirestore, 
            collection, 
            addDoc, 
            deleteDoc, 
            doc, 
            onSnapshot, 
            serverTimestamp,
            setLogLevel 
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // setLogLevel('debug'); // Enable Firestore logging

        // --- GLOBAL VARIABLES (Provided by Canvas Environment) ---
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // --- MOCK FOOD DATABASE (Nutritional values per gram) ---
        // This simulates a basic local food database for demonstration.
        const MOCK_FOOD_DB = {
            'apple': { name: 'Apple', caloriesPerGram: 0.52, protein: 0.003, fat: 0.002, carbs: 0.14 },
            'chicken breast': { name: 'Chicken Breast (cooked)', caloriesPerGram: 1.65, protein: 0.31, fat: 0.036, carbs: 0.0 },
            'rice': { name: 'White Rice (cooked)', caloriesPerGram: 1.30, protein: 0.027, fat: 0.003, carbs: 0.28 },
            'oats': { name: 'Oatmeal (cooked)', caloriesPerGram: 0.68, protein: 0.024, fat: 0.014, carbs: 0.12 },
            'banana': { name: 'Banana', caloriesPerGram: 0.89, protein: 0.011, fat: 0.003, carbs: 0.23 },
            'salad': { name: 'Garden Salad', caloriesPerGram: 0.15, protein: 0.01, fat: 0.005, carbs: 0.03 },
            'default': { name: 'Unrecognized Food', caloriesPerGram: 1.0, protein: 0.05, fat: 0.03, carbs: 0.15 } // Fallback
        };

        // --- Application State ---
        let db;
        let auth;
        let userId = null;
        let foodEntries = []; 
        let exerciseEntries = []; 
        let foodCollectionRef = null;
        let exerciseCollectionRef = null; 
        
        // Profile state is stored in localStorage to persist BMR/TDEE inputs
        let profile = {
            gender: 'male',
            weightKg: 70,
            heightCm: 175,
            ageYears: 30,
            activityFactor: 1.55, 
        };

        const PROFILE_STORAGE_KEY = 'macro_tracker_user_profile_v2'; 

        // --- DOM Elements ---
        const userIdDisplayEl = document.getElementById('user-id-display');
        // Summary
        const tdeeInSummaryEl = document.getElementById('tdee-in-summary');
        const totalCaloriesConsumedEl = document.getElementById('total-calories-consumed');
        const netCaloriesResultEl = document.getElementById('net-calories-result');
        
        // Food Form
        const foodItemEl = document.getElementById('food-item');
        const foodAmountEl = document.getElementById('food-amount');
        const addFoodBtn = document.getElementById('add-food-btn');
        const foodEntriesListEl = document.getElementById('food-entries-list');
        const emptyStateFoodEl = document.getElementById('empty-state-food');
        const loadingHistoryEl = document.getElementById('loading-history');
        
        // Macro Preview
        const macroPreviewEl = document.getElementById('macro-preview');
        const previewCaloriesEl = document.getElementById('preview-calories');
        const previewProteinEl = document.getElementById('preview-protein');
        const previewFatEl = document.getElementById('preview-fat');
        const previewCarbsEl = document.getElementById('preview-carbs');

        // Exercise Form Elements
        const exerciseNameEl = document.getElementById('exercise-name');
        const exerciseSetsEl = document.getElementById('exercise-sets');
        const exerciseRepsEl = document.getElementById('exercise-reps');
        const addExerciseBtn = document.getElementById('add-exercise-btn');
        // Exercise History Elements
        const exerciseEntriesListEl = document.getElementById('exercise-entries-list');
        const emptyStateExerciseEl = document.getElementById('empty-state-exercise');
        const loadingExerciseHistoryEl = document.getElementById('loading-exercise-history');

        // General UI
        const messageBoxEl = document.getElementById('message-box');

        // BMR/TDEE DOM Elements
        const profileGenderEl = document.getElementById('profile-gender');
        const profileWeightEl = document.getElementById('profile-weight');
        const profileHeightEl = document.getElementById('profile-height');
        const profileAgeEl = document.getElementById('profile-age');
        const profileActivityEl = document.getElementById('profile-activity');
        const bmrResultEl = document.getElementById('bmr-result');
        const tdeeResultEl = document.getElementById('tdee-result');

        // --- Utility Functions ---

        /**
         * Shows a temporary message box for alerts/errors.
         */
        function showMessage(message, type = 'error') {
            messageBoxEl.textContent = message;
            messageBoxEl.classList.remove('hidden', 'bg-red-900', 'text-red-300', 'bg-green-900', 'text-green-300', 'border-red-600', 'border-green-600');
            
            if (type === 'error') {
                messageBoxEl.classList.add('bg-red-900', 'text-red-300', 'border', 'border-red-600');
            } else if (type === 'success') {
                messageBoxEl.classList.add('bg-green-900', 'text-green-300', 'border', 'border-green-600');
            }

            setTimeout(() => {
                messageBoxEl.classList.add('hidden');
            }, 3000);
        }
        
        /**
         * Finds the nutritional data based on the input food name.
         */
        function getFoodData(foodName) {
            const normalizedName = foodName.trim().toLowerCase();
            for (const key in MOCK_FOOD_DB) {
                if (normalizedName.includes(key)) {
                    return MOCK_FOOD_DB[key];
                }
            }
            return MOCK_FOOD_DB['default'];
        }

        /**
         * Calculates macros based on food data and user-provided amount in grams.
         */
        function calculateMacros(foodName, amount) {
            const baseData = getFoodData(foodName);
            
            const calories = baseData.caloriesPerGram * amount;
            const protein = baseData.protein * amount;
            const fat = baseData.fat * amount;
            const carbs = baseData.carbs * amount;

            return {
                item: baseData.name,
                calories: Math.round(calories),
                protein: parseFloat(protein.toFixed(1)),
                fat: parseFloat(fat.toFixed(1)),
                carbs: parseFloat(carbs.toFixed(1)),
                amount: amount,
            };
        }

        /**
         * Calculates BMR and TDEE using the Mifflin-St Jeor Equation.
         */
        function calculateMetabolism() {
            const w = profile.weightKg;
            const h = profile.heightCm;
            const a = profile.ageYears;
            const factor = profile.activityFactor;
            let s = 0; 

            if (profile.gender === 'male') {
                s = 5;
            } else if (profile.gender === 'female') {
                s = -161;
            }
            
            if (isNaN(w) || isNaN(h) || isNaN(a) || w <= 0 || h <= 0 || a <= 0) {
                return { bmr: 0, tdee: 0 };
            }

            const bmr = (10 * w) + (6.25 * h) - (5 * a) + s;
            const tdee = bmr * factor;
            
            return {
                bmr: Math.round(bmr),
                tdee: Math.round(tdee)
            };
        }
        
        /**
         * Gets the current date key (YYYY-MM-DD) for daily log filtering.
         */
        function getCurrentDateKey() {
            return new Date().toISOString().split('T')[0];
        }


        // --- Data Persistence (localStorage for Profile) ---

        function loadProfile() {
            try {
                const raw = localStorage.getItem(PROFILE_STORAGE_KEY);
                if (raw) {
                    const loadedProfile = JSON.parse(raw);
                    // Ensure activity factor is correctly parsed as a number
                    loadedProfile.activityFactor = parseFloat(loadedProfile.activityFactor); 
                    // Update profile with loaded values
                    profile = { ...profile, ...loadedProfile };
                }
            } catch (e) {
                console.error('Failed to load profile from localStorage', e);
            }
        }

        function saveProfile() {
            try {
                localStorage.setItem(PROFILE_STORAGE_KEY, JSON.stringify(profile));
            } catch (e) {
                console.error('Failed to save profile to localStorage', e);
            }
        }

        // --- UI Rendering and Summary ---

        /**
         * Renders the food entries list and updates the daily summary totals.
         */
        function renderFoodEntries() {
            const today = getCurrentDateKey();
            const dailyFood = foodEntries.filter(entry => entry.dateKey === today);
            
            // Calculate totals
            const totalCalories = dailyFood.reduce((sum, entry) => sum + entry.calories, 0);

            // Update Summary Card (Protein/Fat/Carbs are not summed in the summary card for brevity, only total consumed calories)
            totalCaloriesConsumedEl.textContent = totalCalories.toLocaleString();
            
            // Render list
            foodEntriesListEl.innerHTML = '';
            loadingHistoryEl.classList.add('hidden');
            emptyStateFoodEl.classList.toggle('hidden', dailyFood.length > 0);

            if (dailyFood.length > 0) {
                // Sort by timestamp (newest first)
                dailyFood.sort((a, b) => (b.timestamp?.seconds || 0) - (a.timestamp?.seconds || 0));

                dailyFood.forEach(entry => {
                    const entryEl = document.createElement('div');
                    entryEl.className = 'flex justify-between items-center p-3 bg-gray-700 rounded-lg transition duration-150 hover:bg-gray-600';
                    entryEl.innerHTML = `
                        <div>
                            <p class="text-sm font-semibold text-indigo-300">${entry.item} <span class="text-xs text-gray-400">(${entry.amount}g)</span></p>
                            <p class="text-xs text-gray-400 mt-1 space-x-2">
                                <span class="text-indigo-400 font-bold">${entry.calories} kcal</span>
                                <span class="text-green-400">P: ${entry.protein}g</span>
                                <span class="text-red-400">F: ${entry.fat}g</span>
                                <span class="text-yellow-400">C: ${entry.carbs}g</span>
                            </p>
                        </div>
                        <button class="delete-btn text-red-400 hover:text-red-500 transition duration-150 p-1 rounded-full bg-gray-800" data-id="${entry.id}" data-type="food">
                            <!-- Trashcan Icon -->
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                                <path fill-rule="evenodd" d="M8.75 1A2.75 2.75 0 0 0 6 3.75v.5H2.75a.75.75 0 0 0 0 1.5h.54l.87 11.25A2.75 2.75 0 0 0 6.89 19h6.22a2.75 2.75 0 0 0 2.73-2.49L16.71 5.75h.54a.75.75 0 0 0 0-1.5H14v-.5A2.75 2.75 0 0 0 11.25 1h-2.5ZM10 3.75a1.25 1.25 0 0 1 1.25 1.25v.5h-2.5v-.5A1.25 1.25 0 0 1 10 3.75Z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    `;
                    foodEntriesListEl.appendChild(entryEl);
                });
                
                // Add delete listener (needs to be done after rendering)
                foodEntriesListEl.querySelectorAll('.delete-btn').forEach(button => {
                    button.addEventListener('click', (e) => deleteEntry(e.currentTarget.dataset.id, e.currentTarget.dataset.type));
                });
            }

            // Always update the overall summary
            updateSummary();
        }

        /**
         * Renders the exercise entries list.
         */
        function renderExerciseEntries() {
            const today = getCurrentDateKey();
            const dailyExercise = exerciseEntries.filter(entry => entry.dateKey === today);
            
            exerciseEntriesListEl.innerHTML = '';
            loadingExerciseHistoryEl.classList.add('hidden');
            emptyStateExerciseEl.classList.toggle('hidden', dailyExercise.length > 0);

            if (dailyExercise.length > 0) {
                // Sort by timestamp (newest first)
                dailyExercise.sort((a, b) => (b.timestamp?.seconds || 0) - (a.timestamp?.seconds || 0));

                dailyExercise.forEach(entry => {
                    const entryEl = document.createElement('div');
                    entryEl.className = 'flex justify-between items-center p-3 bg-gray-700 rounded-lg transition duration-150 hover:bg-gray-600';
                    entryEl.innerHTML = `
                        <div>
                            <p class="text-sm font-semibold text-green-300">${entry.name}</p>
                            <p class="text-xs text-gray-400 mt-1">
                                ${entry.sets} sets x ${entry.reps} reps
                            </p>
                        </div>
                        <button class="delete-btn text-red-400 hover:text-red-500 transition duration-150 p-1 rounded-full bg-gray-800" data-id="${entry.id}" data-type="exercise">
                            <!-- Trashcan Icon -->
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                                <path fill-rule="evenodd" d="M8.75 1A2.75 2.75 0 0 0 6 3.75v.5H2.75a.75.75 0 0 0 0 1.5h.54l.87 11.25A2.75 2.75 0 0 0 6.89 19h6.22a2.75 2.75 0 0 0 2.73-2.49L16.71 5.75h.54a.75.75 0 0 0 0-1.5H14v-.5A2.75 2.75 0 0 0 11.25 1h-2.5ZM10 3.75a1.25 1.25 0 0 1 1.25 1.25v.5h-2.5v-.5A1.25 1.25 0 0 1 10 3.75Z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    `;
                    exerciseEntriesListEl.appendChild(entryEl);
                });

                // Add delete listener
                exerciseEntriesListEl.querySelectorAll('.delete-btn').forEach(button => {
                    button.addEventListener('click', (e) => deleteEntry(e.currentTarget.dataset.id, e.currentTarget.dataset.type));
                });
            }
        }

        /**
         * Updates the summary card with TDEE, consumed calories, and net calories.
         */
        function updateSummary() {
            const { bmr, tdee } = calculateMetabolism();
            const totalConsumed = foodEntries
                .filter(entry => entry.dateKey === getCurrentDateKey())
                .reduce((sum, entry) => sum + entry.calories, 0);

            const netCalories = tdee - totalConsumed;

            // Update TDEE/BMR in calculator section
            bmrResultEl.textContent = `${bmr.toLocaleString()} kcal`;
            tdeeResultEl.textContent = `${tdee.toLocaleString()} kcal`;
            
            // Update Summary Card
            tdeeInSummaryEl.textContent = tdee.toLocaleString();
            totalCaloriesConsumedEl.textContent = totalConsumed.toLocaleString();
            
            netCaloriesResultEl.textContent = netCalories.toLocaleString();

            // Color code net calories for visual feedback
            netCaloriesResultEl.classList.remove('text-red-400', 'text-yellow-300', 'text-green-400');
            if (netCalories < -100) {
                netCaloriesResultEl.classList.add('text-red-400'); // Significant Calorie surplus
            } else if (netCalories > 100) {
                netCaloriesResultEl.classList.add('text-green-400'); // Significant deficit
            } else {
                netCaloriesResultEl.classList.add('text-yellow-300'); // Maintenance/Small change
            }
        }

        // --- Profile Management ---

        /**
         * Updates the profile object, saves it, and recalculates metabolism.
         */
        function handleProfileChange() {
            profile.gender = profileGenderEl.value;
            profile.weightKg = parseFloat(profileWeightEl.value) || 0;
            profile.heightCm = parseFloat(profileHeightEl.value) || 0;
            profile.ageYears = parseFloat(profileAgeEl.value) || 0;
            profile.activityFactor = parseFloat(profileActivityEl.value) || 1.2;

            saveProfile();
            updateSummary();
        }

        /**
         * Attach profile input listeners and initialize profile state.
         */
        function setupProfileListeners() {
            loadProfile();
            
            // Set input fields to current profile values
            profileGenderEl.value = profile.gender;
            profileWeightEl.value = profile.weightKg;
            profileHeightEl.value = profile.heightCm;
            profileAgeEl.value = profile.ageYears;
            profileActivityEl.value = profile.activityFactor;
            
            // Add event listeners to all profile inputs
            [profileGenderEl, profileWeightEl, profileHeightEl, profileAgeEl, profileActivityEl].forEach(el => {
                el.addEventListener('change', handleProfileChange);
                el.addEventListener('input', handleProfileChange);
            });

            // Initial calculation display
            handleProfileChange();
        }


        // --- Firestore Operations ---

        /**
         * Set up real-time listeners for both food and exercise logs.
         */
        function setupFirestoreListeners() {
            if (!db || !userId) {
                console.error("Firestore or User ID not ready.");
                return;
            }

            // Collection path: /artifacts/{appId}/users/{userId}/food_logs
            foodCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/food_logs`);
            // Collection path: /artifacts/{appId}/users/{userId}/exercise_logs
            exerciseCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/exercise_logs`);
            
            // 1. Food Log Listener
            onSnapshot(foodCollectionRef, (snapshot) => {
                foodEntries = snapshot.docs.map(doc => ({
                    id: doc.id,
                    ...doc.data()
                }));
                renderFoodEntries();
            }, (error) => {
                console.error("Error setting up food log listener:", error);
                showMessage("Failed to load food history. Check console.", 'error');
            });

            // 2. Exercise Log Listener
            onSnapshot(exerciseCollectionRef, (snapshot) => {
                exerciseEntries = snapshot.docs.map(doc => ({
                    id: doc.id,
                    ...doc.data()
                }));
                renderExerciseEntries();
            }, (error) => {
                console.error("Error setting up exercise log listener:", error);
                showMessage("Failed to load exercise history. Check console.", 'error');
            });
        }

        /**
         * Deletes a document from Firestore.
         */
        async function deleteEntry(id, type) {
            if (!id || !type) return;

            let colRef;
            if (type === 'food') {
                colRef = foodCollectionRef;
            } else if (type === 'exercise') {
                colRef = exerciseCollectionRef;
            } else {
                return;
            }

            try {
                await deleteDoc(doc(colRef, id));
                showMessage(`${type} entry successfully deleted!`, 'success');
            } catch (e) {
                console.error(`Error deleting ${type} entry: `, e);
                showMessage(`Failed to delete ${type} entry.`, 'error');
            }
        }


        /**
         * Handles the submission of the food logging form.
         */
        async function logFood() {
            const foodName = foodItemEl.value.trim();
            const amount = parseInt(foodAmountEl.value, 10);

            if (!foodName || isNaN(amount) || amount <= 0) {
                showMessage("Please enter a valid food item and amount (grams).", 'error');
                return;
            }

            // Calculate nutritional data based on the mock DB
            const macroData = calculateMacros(foodName, amount);
            
            const newEntry = {
                ...macroData,
                timestamp: serverTimestamp(),
                dateKey: getCurrentDateKey(), // For easy daily filtering
            };

            try {
                // Add the log to the Firestore collection
                await addDoc(foodCollectionRef, newEntry);
                showMessage(`Logged ${macroData.calories} kcal of ${newEntry.item}!`, 'success');
                foodItemEl.value = ''; // Clear food name
                foodAmountEl.value = '100'; // Reset amount to default
                updateMacroPreview(); // Clear preview after successful log
            } catch (e) {
                console.error("Error adding food log: ", e);
                showMessage("Failed to log food. Check console.", 'error');
            }
        }

        /**
         * Handles the submission of the exercise logging form.
         */
        async function logExercise() {
            const exerciseName = exerciseNameEl.value.trim();
            const sets = parseInt(exerciseSetsEl.value, 10);
            const reps = parseInt(exerciseRepsEl.value, 10);

            if (!exerciseName || isNaN(sets) || sets <= 0 || isNaN(reps) || reps <= 0) {
                showMessage("Please enter a valid exercise name, sets, and reps.", 'error');
                return;
            }

            const newEntry = {
                name: exerciseName,
                sets: sets,
                reps: reps,
                timestamp: serverTimestamp(),
                dateKey: getCurrentDateKey(),
            };

            try {
                // Add the log to the Firestore collection
                await addDoc(exerciseCollectionRef, newEntry);
                showMessage(`Logged ${sets} sets of ${exerciseName}!`, 'success');
                exerciseNameEl.value = '';
                exerciseSetsEl.value = '3';
                exerciseRepsEl.value = '10';
            } catch (e) {
                console.error("Error adding exercise log: ", e);
                showMessage("Failed to log exercise. Check console.", 'error');
            }
        }

        /**
         * Live updates the macro preview based on current input values.
         */
        function updateMacroPreview() {
            const foodName = foodItemEl.value.trim();
            const amount = parseInt(foodAmountEl.value, 10);

            if (!foodName || isNaN(amount) || amount <= 0) {
                macroPreviewEl.classList.add('hidden');
                return;
            }

            const macroData = calculateMacros(foodName, amount);
            
            previewCaloriesEl.textContent = `${macroData.calories} kcal`;
            previewProteinEl.textContent = `P: ${macroData.protein}g`;
            previewFatEl.textContent = `F: ${macroData.fat}g`;
            previewCarbsEl.textContent = `C: ${macroData.carbs}g`;
            
            macroPreviewEl.classList.remove('hidden');
        }


        // --- Main Initialization ---

        async function initApp() {
            try {
                // 1. Initialize Firebase
                if (!firebaseConfig || !Object.keys(firebaseConfig).length) {
                    console.error("Firebase configuration is missing.");
                    userIdDisplayEl.textContent = "Error: Missing Firebase Config";
                    return;
                }

                const app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);
                
                // 2. Handle Authentication
                await new Promise((resolve) => {
                    onAuthStateChanged(auth, async (user) => {
                        if (!user) {
                            if (initialAuthToken) {
                                try {
                                    // Use custom token if provided
                                    const credential = await signInWithCustomToken(auth, initialAuthToken);
                                    userId = credential.user.uid;
                                } catch (e) {
                                    console.warn("Custom token sign-in failed. Signing in anonymously.", e);
                                    await signInAnonymously(auth);
                                    userId = auth.currentUser.uid;
                                }
                            } else {
                                // Fallback to anonymous sign-in
                                await signInAnonymously(auth);
                                userId = auth.currentUser.uid;
                            }
                        } else {
                            userId = user.uid;
                        }
                        
                        userIdDisplayEl.textContent = `User ID: ${userId}`;
                        resolve();
                    });
                });

                
                // 3. Set up Profile (localStorage)
                setupProfileListeners();

                // 4. Set up Firestore Listeners (Data)
                setupFirestoreListeners();

                // 5. Setup Form Listeners
                addFoodBtn.addEventListener('click', logFood);
                addExerciseBtn.addEventListener('click', logExercise);
                
                foodItemEl.addEventListener('input', updateMacroPreview);
                foodAmountEl.addEventListener('input', updateMacroPreview);
                
                updateMacroPreview(); // Initial preview setup

            } catch (e) {
                console.error("Initialization failed:", e);
                userIdDisplayEl.textContent = "Error during initialization.";
                showMessage("An unexpected error occurred during setup.", 'error');
            }
        }

        window.onload = initApp;
    </script>
</body>
</html>
