<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Comprehensive Macro and TDEE Tracker</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom font for a modern feel */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
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

        <!-- Header -->
        <header class="text-center mb-8 p-4 bg-gray-800 rounded-xl shadow-2xl">
            <h1 class="text-4xl font-extrabold text-white">Macro & TDEE Tracker</h1>
            <p class="text-gray-400 mt-2">Log food by amount, track exercises, and monitor expenditure.</p>
        </header>

        <!-- BMR / TDEE Calculator and Profile Section -->
        <div class="bg-gray-800 p-6 rounded-xl shadow-2xl mb-8">
            <h2 class="text-xl font-semibold mb-4 text-white border-b border-gray-700 pb-2">Personal Metabolism Calculator</h2>
            
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
                <h2 class="text-xl font-semibold mb-3">Daily Summary</h2>
                
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
                    <h2 class="text-xl font-semibold mb-4 text-white">Log Food Intake</h2>
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
                
                <!-- NEW: Exercise Input Form -->
                <div class="bg-gray-800 p-6 rounded-xl shadow-2xl">
                    <h2 class="text-xl font-semibold mb-4 text-white">Log Exercise</h2>
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
            <h2 class="text-xl font-semibold mb-4 text-white border-b border-gray-700 pb-2">Food Intake History</h2>
            <div id="food-entries-list" class="space-y-3">
                <!-- Food Entries will be dynamically inserted here -->
                <p id="loading-history" class="text-center text-indigo-400 mt-4">Connecting to Database...</p>
            </div>
            <p id="empty-state-food" class="text-center text-gray-400 mt-8 hidden">No food logged yet. Start logging above!</p>
        </div>

        <!-- Exercise History Section -->
        <div class="bg-gray-800 p-6 rounded-xl shadow-2xl">
            <h2 class="text-xl font-semibold mb-4 text-white border-b border-gray-700 pb-2">Exercise History (Sets & Reps)</h2>
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
            query, 
            serverTimestamp,
            setLogLevel 
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // setLogLevel('debug'); // Enable Firestore logging

        // --- GLOBAL VARIABLES (Provided by Canvas Environment) ---
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // --- MOCK FOOD DATABASE (Nutritional values per gram) ---
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
        let exerciseEntries = []; // NEW: Array to hold exercise logs
        let foodCollectionRef = null;
        let exerciseCollectionRef = null; // NEW: Firestore reference for exercises
        let isAuthReady = false;

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
        
        // Food Form (Existing)
        const foodItemEl = document.getElementById('food-item');
        const foodAmountEl = document.getElementById('food-amount');
        const addFoodBtn = document.getElementById('add-food-btn');
        const foodEntriesListEl = document.getElementById('food-entries-list');
        const emptyStateFoodEl = document.getElementById('empty-state-food');
        const loadingHistoryEl = document.getElementById('loading-history');
        
        // Macro Preview (Existing)
        const macroPreviewEl = document.getElementById('macro-preview');
        const previewCaloriesEl = document.getElementById('preview-calories');
        const previewProteinEl = document.getElementById('preview-protein');
        const previewFatEl = document.getElementById('preview-fat');
        const previewCarbsEl = document.getElementById('preview-carbs');

        // NEW: Exercise Form Elements
        const exerciseNameEl = document.getElementById('exercise-name');
        const exerciseSetsEl = document.getElementById('exercise-sets');
        const exerciseRepsEl = document.getElementById('exercise-reps');
        const addExerciseBtn = document.getElementById('add-exercise-btn');
        // NEW: Exercise History Elements
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
        
        // ... (getFoodData and calculateMacros remain the same) ...

        /**
         * Finds the nutritional data based on the input food name (case-insensitive and trimmed).
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

        // --- Data Persistence (localStorage for Profile - remains the same) ---

        function loadProfile() {
            try {
                const raw = localStorage.getItem(PROFILE_STORAGE_KEY);
                if (raw) {
                    const loadedProfile = JSON.parse(raw);
                    loadedProfile.activityFactor = parseFloat(loadedProfile.activityFactor);
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
        
        // --- Firestore Operations (Updated) ---

        /**
         * Sets up the real-time listener for food history (Existing).
         */
        function setupFoodHistoryListener() {
            if (!foodCollectionRef) return;
            
            const q = query(foodCollectionRef);

            onSnapshot(q, (snapshot) => {
                foodEntries = []; 
                snapshot.forEach((doc) => {
                    const data = doc.data();
                    foodEntries.push({
                        id: doc.id,
                        ...data
                    });
                });
                foodEntries.sort((a, b) => (b.date?.seconds || 0) - (a.date?.seconds || 0));

                updateUI();
                loadingHistoryEl.classList.add('hidden');

            }, (error) => {
                console.error("Error listening to food history:", error);
                showMessage("Failed to load food history. Check console for details.", 'error');
            });
        }

        /**
         * Sets up the real-time listener for exercise history (NEW).
         */
        function setupExerciseHistoryListener() {
            if (!exerciseCollectionRef) return;
            
            const q = query(exerciseCollectionRef);

            onSnapshot(q, (snapshot) => {
                exerciseEntries = []; 
                snapshot.forEach((doc) => {
                    const data = doc.data();
                    exerciseEntries.push({
                        id: doc.id,
                        ...data
                    });
                });
                exerciseEntries.sort((a, b) => (b.date?.seconds || 0) - (a.date?.seconds || 0));

                updateUI();
                loadingExerciseHistoryEl.classList.add('hidden');

            }, (error) => {
                console.error("Error listening to exercise history:", error);
                showMessage("Failed to load exercise history. Check console for details.", 'error');
            });
        }
        
        /**
         * Adds a food entry to Firestore (Existing).
         */
        async function addFoodEntryToFirestore(entry) {
            if (!foodCollectionRef) return showMessage('Database not ready.', 'error');
            
            try {
                const { id, ...dataToSave } = entry;
                await addDoc(foodCollectionRef, {
                    ...dataToSave,
                    date: serverTimestamp() 
                });
                showMessage('Meal logged successfully!', 'success');
            } catch (error) {
                console.error("Error adding document: ", error);
                showMessage("Failed to save meal log. Check console.", 'error');
            }
        }

        /**
         * Adds an exercise entry to Firestore (NEW).
         */
        async function addExerciseEntryToFirestore(entry) {
            if (!exerciseCollectionRef) return showMessage('Database not ready.', 'error');
            
            try {
                const { id, ...dataToSave } = entry;
                await addDoc(exerciseCollectionRef, {
                    ...dataToSave,
                    date: serverTimestamp() 
                });
                showMessage('Exercise logged successfully!', 'success');
            } catch (error) {
                console.error("Error adding exercise document: ", error);
                showMessage("Failed to save exercise log. Check console.", 'error');
            }
        }
        
        /**
         * Deletes a food entry from Firestore (Existing).
         */
        async function deleteFoodEntryFromFirestore(id) {
            if (!foodCollectionRef) return showMessage('Database not ready.', 'error');
            
            try {
                await deleteDoc(doc(foodCollectionRef, id));
                showMessage('Meal entry deleted.', 'success');
            } catch (error) {
                console.error("Error deleting document: ", error);
                showMessage("Failed to delete meal log. Check console.", 'error');
            }
        }

        /**
         * Deletes an exercise entry from Firestore (NEW).
         */
        async function deleteExerciseEntryFromFirestore(id) {
            if (!exerciseCollectionRef) return showMessage('Database not ready.', 'error');
            
            try {
                await deleteDoc(doc(exerciseCollectionRef, id));
                showMessage('Exercise entry deleted.', 'success');
            } catch (error) {
                console.error("Error deleting exercise document: ", error);
                showMessage("Failed to delete exercise log. Check console.", 'error');
            }
        }


        // --- UI Rendering ---

        // ... (renderProfile and renderSummary remain the same) ...
        function renderProfile() {
            profileGenderEl.value = profile.gender;
            profileWeightEl.value = profile.weightKg;
            profileHeightEl.value = profile.heightCm;
            profileAgeEl.value = profile.ageYears;
            profileActivityEl.value = profile.activityFactor;

            const results = calculateMetabolism();
            bmrResultEl.textContent = `${results.bmr} kcal`;
            tdeeResultEl.textContent = `${results.tdee} kcal`;
            
            renderSummary();
        }

        function renderSummary() {
            const TDEE = calculateMetabolism().tdee;

            const totalCaloriesConsumed = foodEntries.reduce((sum, e) => sum + e.calories, 0);
            
            const netCalories = TDEE - totalCaloriesConsumed;

            tdeeInSummaryEl.textContent = TDEE;
            totalCaloriesConsumedEl.textContent = totalCaloriesConsumed;
            netCaloriesResultEl.textContent = netCalories;
            
            netCaloriesResultEl.classList.remove('text-red-500', 'text-yellow-300');
            if (netCalories < 0) {
                netCaloriesResultEl.classList.add('text-red-500');
            } else {
                netCaloriesResultEl.classList.add('text-yellow-300');
            }
        }


        /**
         * Renders the list of food entries from Firestore data (Existing).
         */
        function renderFoodEntries() {
            foodEntriesListEl.innerHTML = ''; 
            
            if (foodEntries.length === 0) {
                emptyStateFoodEl.classList.remove('hidden');
                return;
            }
            emptyStateFoodEl.classList.add('hidden');

            foodEntries.forEach(item => {
                const dateString = item.date?.toDate ? item.date.toDate().toLocaleString() : 'Saving...';
                
                const itemEl = document.createElement('div');
                itemEl.className = 'flex justify-between items-center p-4 bg-gray-700 border border-gray-600 rounded-lg transition duration-150 hover:shadow-xl';
                itemEl.innerHTML = `
                    <div class="flex flex-col">
                        <span class="text-lg font-bold text-white">${item.item} (${item.amount}g)</span>
                        <span class="text-xs text-gray-400 mt-0.5">${dateString}</span>
                        <div class="text-xs mt-1 space-x-2">
                            <span class="text-indigo-400">${item.calories} kcal</span>
                            <span class="text-green-400">P:${item.protein}g</span>
                            <span class="text-red-400">F:${item.fat}g</span>
                            <span class="text-yellow-400">C:${item.carbs}g</span>
                        </div>
                    </div>
                    <div class="flex items-center space-x-4">
                        <button data-id="${item.id}" class="delete-food-btn text-red-500 hover:text-red-400 transition duration-150 text-sm">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm4 0a1 1 0 10-2 0v6a1 1 0 102 0V8z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    </div>
                `;
                foodEntriesListEl.appendChild(itemEl);
            });

            document.querySelectorAll('.delete-food-btn').forEach(button => {
                button.addEventListener('click', (e) => {
                    const idToDelete = e.currentTarget.getAttribute('data-id');
                    deleteFoodEntryFromFirestore(idToDelete);
                });
            });
        }

        /**
         * Renders the list of exercise entries from Firestore data (NEW).
         */
        function renderExerciseEntries() {
            exerciseEntriesListEl.innerHTML = ''; 
            
            if (exerciseEntries.length === 0) {
                emptyStateExerciseEl.classList.remove('hidden');
                return;
            }
            emptyStateExerciseEl.classList.add('hidden');

            exerciseEntries.forEach(item => {
                const dateString = item.date?.toDate ? item.date.toDate().toLocaleString() : 'Saving...';
                
                const itemEl = document.createElement('div');
                itemEl.className = 'flex justify-between items-center p-4 bg-gray-700 border border-gray-600 rounded-lg transition duration-150 hover:shadow-xl';
                itemEl.innerHTML = `
                    <div class="flex flex-col">
                        <span class="text-lg font-bold text-white">${item.name}</span>
                        <span class="text-xs text-gray-400 mt-0.5">${dateString}</span>
                        <div class="text-xs mt-1 space-x-3">
                            <span class="text-green-400 font-semibold">${item.sets} sets</span>
                            <span class="text-yellow-400 font-semibold">${item.reps} reps/set</span>
                            <span class="text-indigo-400 font-semibold">= ${item.sets * item.reps} total reps</span>
                        </div>
                    </div>
                    <div class="flex items-center space-x-4">
                        <button data-id="${item.id}" class="delete-exercise-btn text-red-500 hover:text-red-400 transition duration-150 text-sm">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm4 0a1 1 0 10-2 0v6a1 1 0 102 0V8z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    </div>
                `;
                exerciseEntriesListEl.appendChild(itemEl);
            });

            document.querySelectorAll('.delete-exercise-btn').forEach(button => {
                button.addEventListener('click', (e) => {
                    const idToDelete = e.currentTarget.getAttribute('data-id');
                    deleteExerciseEntryFromFirestore(idToDelete);
                });
            });
        }

        /**
         * Displays the calculated macros in the preview box (Existing).
         */
        function renderMacroPreview() {
            const foodName = foodItemEl.value;
            const amount = parseFloat(foodAmountEl.value);

            if (!foodName.trim() || isNaN(amount) || amount <= 0) {
                macroPreviewEl.classList.add('hidden');
                return;
            }

            const macros = calculateMacros(foodName, amount);
            
            previewCaloriesEl.textContent = `${macros.calories} kcal`;
            previewProteinEl.textContent = `P: ${macros.protein}g`;
            previewFatEl.textContent = `F: ${macros.fat}g`;
            previewCarbsEl.textContent = `C: ${macros.carbs}g`;
            
            macroPreviewEl.classList.remove('hidden');
        }


        /**
         * Main function to re-render all dynamic parts of the UI (Updated).
         */
        function updateUI() {
            renderProfile();
            renderFoodEntries();
            renderExerciseEntries(); // NEW
            renderMacroPreview(); 
        }

        // --- Event Handlers ---

        // ... (handleProfileChange and handleLogFoodEntry remain the same) ...
        function handleProfileChange() {
            profile.gender = profileGenderEl.value;
            profile.weightKg = parseFloat(profileWeightEl.value) || 0;
            profile.heightCm = parseFloat(profileHeightEl.value) || 0;
            profile.ageYears = parseFloat(profileAgeEl.value) || 0;
            profile.activityFactor = parseFloat(profileActivityEl.value);

            saveProfile();
            renderProfile();
        }

        function handleLogFoodEntry() {
            const foodName = foodItemEl.value;
            const amount = parseFloat(foodAmountEl.value);

            if (!foodName.trim()) {
                showMessage("Please enter the name of the food item.", 'error');
                return;
            }
            if (isNaN(amount) || amount <= 0) {
                showMessage("Please enter a valid amount (greater than 0 grams).", 'error');
                return;
            }
            
            const newEntry = calculateMacros(foodName, amount);
            
            addFoodEntryToFirestore(newEntry);
            
            foodItemEl.value = '';
            foodAmountEl.value = '100'; 
        }
        
        /**
         * Handles the logic for logging a new exercise entry (NEW).
         */
        function handleLogExerciseEntry() {
            const name = exerciseNameEl.value;
            const sets = parseInt(exerciseSetsEl.value);
            const reps = parseInt(exerciseRepsEl.value);

            if (!name.trim()) {
                showMessage("Please enter the name of the exercise.", 'error');
                return;
            }
            if (isNaN(sets) || sets <= 0 || isNaN(reps) || reps <= 0) {
                showMessage("Sets and Reps must be valid numbers greater than 0.", 'error');
                return;
            }
            
            const newEntry = {
                name: name.trim(),
                sets: sets,
                reps: reps
            };
            
            addExerciseEntryToFirestore(newEntry);
            
            // Clear inputs
            exerciseNameEl.value = '';
            exerciseSetsEl.value = '3';
            exerciseRepsEl.value = '10';
        }


        // --- Initialization ---

        async function initFirebase() {
            try {
                // 1. Initialize Firebase
                const app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // 2. Sign in or use custom token
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // 3. Listen for auth state change to confirm sign-in
                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        userId = user.uid;
                        userIdDisplayEl.textContent = `User ID: ${userId}`;
                        
                        // Food collection reference (Existing)
                        foodCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/food_logs`);
                        
                        // Exercise collection reference (NEW)
                        exerciseCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/exercise_logs`);

                        isAuthReady = true;
                        
                        // 4. Once authenticated, start listening for real-time data
                        setupFoodHistoryListener();
                        setupExerciseHistoryListener(); // NEW
                    } else {
                        userIdDisplayEl.textContent = 'User ID: Not Signed In';
                        loadingHistoryEl.textContent = 'Please log in to see history.';
                        loadingExerciseHistoryEl.textContent = 'Please log in to see history.';
                    }
                });

            } catch (error) {
                console.error("Firebase Initialization or Auth Error:", error);
                loadingHistoryEl.textContent = `Error connecting to Firebase: ${error.message}`;
                loadingExerciseHistoryEl.textContent = `Error connecting to Firebase: ${error.message}`;
            }
        }

        function init() {
            loadProfile();
            updateUI();
            initFirebase();

            // Attach listeners to all profile inputs for real-time calculation
            [profileGenderEl, profileWeightEl, profileHeightEl, profileAgeEl, profileActivityEl].forEach(input => {
                input.addEventListener('input', handleProfileChange);
            });
            
            // Food Form Listeners (Existing)
            addFoodBtn.addEventListener('click', handleLogFoodEntry);
            [foodItemEl, foodAmountEl].forEach(input => {
                input.addEventListener('input', renderMacroPreview);
            });
            foodAmountEl.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    handleLogFoodEntry();
                }
            });

            // NEW: Exercise Form Listeners
            addExerciseBtn.addEventListener('click', handleLogExerciseEntry);
            exerciseRepsEl.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    handleLogExerciseEntry();
                }
            });
        }

        document.addEventListener('DOMContentLoaded', init);

    </script>
</body>
</html>
