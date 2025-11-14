<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <!-- Mandatory viewport tag for responsiveness on all devices -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
    <title>Advanced Fitness & Nutrition Tracker</title>
    
    <!-- Load Tailwind CSS for modern styling and Inter font -->
    <script src="https://cdn.tailwindcss.com/"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
        
        .app-container {
            font-family: 'Inter', sans-serif;
            background-color: #0d1117; /* GitHub Dark Theme Background */
            min-height: 100vh;
            color: #d1d5db; /* Default text color (gray-300) */
            padding: 4px;
        }
        .card {
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.4), 0 4px 6px -4px rgba(0, 0, 0, 0.2);
        }
        /* Dark mode stripe for table rows */
        .data-row:nth-child(odd) {
            background-color: #27303d; 
        }
        /* Active Tab Styling */
        .tab.active {
            background-color: #1f2937; /* Gray-800 */
            border-bottom: 3px solid #34d399; /* Green-400 accent */
            color: #34d399; 
        }
    </style>
</head>
<body>
    <!-- App container starts immediately with content -->
    <div class="app-container">
        <div class="max-w-4xl mx-auto p-4 sm:p-8">
            <header class="text-center mb-6 p-1 rounded-xl">
                <h1 class="text-3xl font-extrabold text-green-400 mb-2">Fitness & Nutrition Tracker</h1>
                <p class="text-gray-400 text-sm">Monitor TDEE, track macros, and calculate your **Caloric Balance** for targeted results.</p>
            </header>

            <!-- Stats Summary -->
            <div class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-6 gap-4 mb-8">
                <div class="bg-indigo-700 text-white p-4 rounded-xl card text-center">
                    <p class="text-xs font-semibold uppercase opacity-80">Total Workouts</p>
                    <p id="stat-total-workouts" class="text-2xl font-bold mt-1">0</p>
                </div>
                <div class="bg-red-700 text-white p-4 rounded-xl card text-center">
                    <p class="text-xs font-semibold uppercase opacity-80">Burned Calories</p>
                    <p id="stat-total-burned" class="text-2xl font-bold mt-1">0</p>
                </div>
                <div class="bg-green-700 text-white p-4 rounded-xl card text-center">
                    <p class="text-xs font-semibold uppercase opacity-80">Consumed Calories</p>
                    <p id="stat-total-consumed" class="text-2xl font-bold mt-1">0</p>
                </div>
                <div class="bg-purple-700 text-white p-4 rounded-xl card text-center">
                    <p class="text-xs font-semibold uppercase opacity-80">Total Protein (g)</p>
                    <p id="stat-total-protein" class="text-2xl font-bold mt-1">0</p>
                </div>
                <!-- TDEE STAT CARD -->
                <div class="bg-yellow-700 text-white p-4 rounded-xl card text-center">
                    <p class="text-xs font-semibold uppercase opacity-80">Your TDEE (kcal)</p>
                    <p id="stat-tdee" class="text-2xl font-bold mt-1">N/A</p>
                </div>
                <!-- CALORIC BALANCE STAT CARD -->
                <div id="stat-balance-card" class="text-white p-4 rounded-xl card text-center bg-gray-600">
                    <p class="text-xs font-semibold uppercase opacity-80">Caloric Balance (kcal)</p>
                    <p id="stat-balance" class="text-2xl font-bold mt-1">N/A</p>
                </div>
            </div>

            <!-- Tab Navigation -->
            <div class="flex border-b border-gray-700 mb-6 overflow-x-auto">
                <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition active" onclick="switchView('workout')">
                    Workouts
                </button>
                <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition" onclick="switchView('nutrition')">
                    Nutrition
                </button>
                <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition" onclick="switchView('tdee')">
                    TDEE / Profile
                </button>
            </div>

            <!-- WORKOUT VIEW -->
            <div id="workout-view">
                <!-- Workout Input Form -->
                <div class="bg-gray-800 p-6 rounded-xl card mb-8">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Log New Workout</h2>
                    <p class="text-sm text-gray-500 mb-4">Calories are calculated automatically: Total Reps &times; 1.5 kcal.</p>
                    <form id="workout-form" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                        <input type="hidden" id="workout-id">

                        <div>
                            <label for="w-date" class="block text-sm font-medium text-gray-400 mb-1">Date</label>
                            <!-- JS will set this on load, but we add a default value for robustness -->
                            <input type="date" id="w-date" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="w-activity" class="block text-sm font-medium text-gray-400 mb-1">Activity</label>
                            <input type="text" id="w-activity" placeholder="e.g., Squats, Bench Press" required
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="w-sets" class="block text-sm font-medium text-gray-400 mb-1">Sets</label>
                            <input type="number" id="w-sets" placeholder="Total Sets" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="w-reps" class="block text-sm font-medium text-gray-400 mb-1">Avg Reps per Set</label>
                            <input type="number" id="w-reps" placeholder="Avg Reps" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div class="lg:col-span-4 mt-2">
                            <button type="submit" id="w-submit-btn"
                                    class="w-full bg-green-600 text-white font-bold py-3 px-4 rounded-xl hover:bg-green-700 transition duration-150 ease-in-out">
                                Add Workout
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Workout History -->
                <div class="bg-gray-800 p-6 rounded-xl card overflow-x-auto">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Workout History</h2>
                    <table class="min-w-full divide-y divide-gray-700">
                        <thead>
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Date</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Activity</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Sets</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Avg Reps</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Calculated Calories (kcal)</th>
                                <th class="px-6 py-3 text-center text-xs font-medium text-gray-400 uppercase tracking-wider">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="workout-list" class="bg-gray-800 divide-y divide-gray-700">
                            <!-- Workout rows will be inserted here -->
                        </tbody>
                    </table>
                    <p id="w-no-data-message" class="text-center text-gray-500 p-4 hidden">No workouts logged yet. Get moving!</p>
                </div>
            </div>

            <!-- NUTRITION VIEW -->
            <div id="nutrition-view" class="hidden">
                <!-- Nutrition Input Form -->
                <div class="bg-gray-800 p-6 rounded-xl card mb-8">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Log New Food Entry</h2>
                    <form id="food-form" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                        <input type="hidden" id="food-id">

                        <div>
                            <label for="f-date" class="block text-sm font-medium text-gray-400 mb-1">Date</label>
                            <input type="date" id="f-date" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-item" class="block text-sm font-medium text-gray-400 mb-1">Meal/Item Name</label>
                            <input type="text" id="f-item" placeholder="e.g., Chicken Salad, Oatmeal" required
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-calories" class="block text-sm font-medium text-gray-400 mb-1">Calories (kcal)</label>
                            <input type="number" id="f-calories" placeholder="kcal" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-weight" class="block text-sm font-medium text-gray-400 mb-1">Approx. Weight (g)</label>
                            <input type="number" id="f-weight" placeholder="grams" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-protein" class="block text-sm font-medium text-gray-400 mb-1">Protein (g)</label>
                            <input type="number" id="f-protein" placeholder="grams" required min="0"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-fat" class="block text-sm font-medium text-gray-400 mb-1">Fat (g)</label>
                            <input type="number" id="f-fat" placeholder="grams" required min="0"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div class="sm:col-span-2 lg:col-span-2 mt-2">
                            <button type="submit" id="f-submit-btn"
                                    class="w-full bg-green-600 text-white font-bold py-3 px-4 rounded-xl hover:bg-green-700 transition duration-150 ease-in-out">
                                Add Food Entry
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Food History -->
                <div class="bg-gray-800 p-6 rounded-xl card overflow-x-auto">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Nutrition History</h2>
                    <table class="min-w-full divide-y divide-gray-700">
                        <thead>
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Date</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Item/Meal</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Weight (g)</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Calories (kcal)</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Protein (g)</th>
                                <th class="px-6 py-3 text-center text-xs font-medium text-gray-400 uppercase tracking-wider">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="food-list" class="bg-gray-800 divide-y divide-gray-700">
                            <!-- Food rows will be inserted here -->
                        </tbody>
                    </table>
                    <p id="f-no-data-message" class="text-center text-gray-500 p-4 hidden">No food logged yet. Track your intake!</p>
                </div>
            </div>

            <!-- TDEE/PROFILE VIEW -->
            <div id="tdee-view" class="hidden">
                <div class="bg-gray-800 p-6 rounded-xl card mb-8">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">TDEE Profile & Calculator</h2>
                    <p class="text-sm text-gray-400 mb-4">Enter your metrics to calculate your Basal Metabolic Rate (BMR) and Total Daily Energy Expenditure (TDEE).</p>
                    
                    <form id="profile-form" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                        
                        <!-- Gender -->
                        <div>
                            <label for="p-gender" class="block text-sm font-medium text-gray-400 mb-1">Gender</label>
                            <select id="p-gender" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                                <option value="" disabled selected>Select Gender</option>
                                <option value="male">Male</option>
                                <option value="female">Female</option>
                            </select>
                        </div>
                        
                        <!-- Age -->
                        <div>
                            <label for="p-age" class="block text-sm font-medium text-gray-400 mb-1">Age (Years)</label>
                            <input type="number" id="p-age" placeholder="25" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <!-- Height -->
                        <div>
                            <label for="p-height" class="block text-sm font-medium text-gray-400 mb-1">Height (cm)</label>
                            <input type="number" id="p-height" placeholder="175" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <!-- Weight -->
                        <div>
                            <label for="p-weight" class="block text-sm font-medium text-gray-400 mb-1">Weight (kg)</label>
                            <input type="number" id="p-weight" placeholder="70" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <!-- Activity Level -->
                        <div class="lg:col-span-2">
                            <label for="p-activity" class="block text-sm font-medium text-gray-400 mb-1">Activity Level</label>
                            <select id="p-activity" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                                <option value="" disabled selected>Select Activity Level</option>
                                <option value="1.2">Sedentary (little or no exercise)</option>
                                <option value="1.375">Lightly active (light exercise 1-3 days/week)</option>
                                <option value="1.55">Moderately active (moderate exercise 3-5 days/week)</option>
                                <option value="1.725">Very active (hard exercise 6-7 days/week)</option>
                                <option value="1.9">Extremely active (hard daily exercise + physical job)</option>
                            </select>
                        </div>

                        <div class="lg:col-span-3 mt-2">
                            <button type="submit" id="p-submit-btn"
                                    class="w-full bg-yellow-600 text-white font-bold py-3 px-4 rounded-xl hover:bg-yellow-700 transition duration-150 ease-in-out">
                                Save Profile & Calculate TDEE
                            </button>
                        </div>
                    </form>

                    <!-- Calculation Results Display -->
                    <div class="mt-8 pt-4 border-t border-gray-700">
                        <div id="tdee-results" class="space-y-3">
                            <p class="text-xl font-semibold text-gray-200">
                                BMR (Basal Metabolic Rate): <span id="display-bmr" class="text-green-400 font-bold">N/A</span> kcal
                            </p>
                            <p class="text-xl font-semibold text-gray-200">
                                TDEE (Total Daily Energy Expenditure): <span id="display-tdee" class="text-yellow-400 font-bold">N/A</span> kcal
                            </p>
                            <div id="tdee-guidance" class="bg-gray-700 p-4 rounded-lg text-sm text-gray-300 hidden">
                                <p><strong>Goal Guidance:</strong></p>
                                <ul class="list-disc list-inside mt-2 space-y-1">
                                    <li>To **Maintain** your weight, consume <span class="text-yellow-400 font-semibold" id="guidance-maintain"></span> kcal per day.</li>
                                    <li>For **Slow Weight Loss** (approx. 0.5kg/week), aim for <span class="text-red-400 font-semibold" id="guidance-loss"></span> kcal per day.</li>
                                    <li>For **Slow Weight Gain** (Muscle building), aim for <span class="text-blue-400 font-semibold" id="guidance-gain"></span> kcal per day.</li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>


            <!-- Custom Modal for Alerts/Confirms -->
            <div id="custom-modal" class="fixed inset-0 bg-gray-900 bg-opacity-70 hidden items-center justify-center z-50">
                <div class="bg-gray-800 rounded-lg p-6 w-11/12 max-w-sm shadow-2xl">
                    <h3 id="modal-title" class="text-xl font-bold mb-4 text-gray-200">Alert</h3>
                    <p id="modal-message" class="mb-6 text-gray-400"></p>
                    <div class="flex justify-end space-x-3">
                        <button id="modal-cancel-btn" class="px-4 py-2 bg-gray-700 text-gray-200 rounded-lg hover:bg-gray-600 transition hidden">Cancel</button>
                        <button id="modal-confirm-btn" class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition">OK</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const STORAGE_KEY = 'healthTrackerState';
        let state = {
            workouts: [],
            foods: [],
            profile: { 
                age: null,
                gender: null,
                height: null, 
                weight: null, 
                activity: null, 
                bmr: null,
                tdee: null
            }
        };
        let currentView = 'workout';

        // --- DOM Element Variables (abbreviated for brevity) ---
        const workoutForm = document.getElementById('workout-form');
        const foodForm = document.getElementById('food-form');
        const profileForm = document.getElementById('profile-form'); 

        const pGenderInput = document.getElementById('p-gender');
        const pAgeInput = document.getElementById('p-age');
        const pHeightInput = document.getElementById('p-height');
        const pWeightInput = document.getElementById('p-weight');
        const pActivityInput = document.getElementById('p-activity');
        const displayBMR = document.getElementById('display-bmr');
        const displayTDEE = document.getElementById('display-tdee');
        const tdeeGuidance = document.getElementById('tdee-guidance');
        const guidanceMaintain = document.getElementById('guidance-maintain');
        const guidanceLoss = document.getElementById('guidance-loss');
        const guidanceGain = document.getElementById('guidance-gain');


        const workoutList = document.getElementById('workout-list');
        const wNoDataMessage = document.getElementById('w-no-data-message');
        const wSubmitBtn = document.getElementById('w-submit-btn');
        const wIdInput = document.getElementById('workout-id');
        const wDateInput = document.getElementById('w-date');
        const wActivityInput = document.getElementById('w-activity');
        const wSetsInput = document.getElementById('w-sets');
        const wRepsInput = document.getElementById('w-reps');

        const foodList = document.getElementById('food-list');
        const fNoDataMessage = document.getElementById('f-no-data-message');
        const fSubmitBtn = document.getElementById('f-submit-btn');
        const fIdInput = document.getElementById('food-id');
        const fDateInput = document.getElementById('f-date');
        const fItemInput = document.getElementById('f-item');
        const fCaloriesInput = document.getElementById('f-calories');
        const fProteinInput = document.getElementById('f-protein');
        const fFatInput = document.getElementById('f-fat');
        const fWeightInput = document.getElementById('f-weight');

        const statTotalWorkouts = document.getElementById('stat-total-workouts');
        const statTotalBurned = document.getElementById('stat-total-burned');
        const statTotalConsumed = document.getElementById('stat-total-consumed');
        const statTotalProtein = document.getElementById('stat-total-protein');
        const statTDEE = document.getElementById('stat-tdee');
        const statBalanceCard = document.getElementById('stat-balance-card');
        const statBalance = document.getElementById('stat-balance');

        const workoutView = document.getElementById('workout-view');
        const nutritionView = document.getElementById('nutrition-view');
        const tdeeView = document.getElementById('tdee-view');

        // --- Utility Functions ---

        /**
         * Custom Modal Implementation (Replaces alert/confirm)
         */
        function customModal(message, title = 'Notification', isConfirm = false) {
            return new Promise(resolve => {
                const modal = document.getElementById('custom-modal');
                const modalTitle = document.getElementById('modal-title');
                const modalMessage = document.getElementById('modal-message');
                const modalConfirmBtn = document.getElementById('modal-confirm-btn');
                const modalCancelBtn = document.getElementById('modal-cancel-btn');

                modalTitle.textContent = title;
                modalMessage.textContent = message;
                
                // Click handlers
                const handleCancel = () => {
                    modal.classList.add('hidden');
                    modal.classList.remove('flex');
                    resolve(false);
                };
                const handleConfirm = () => {
                    modal.classList.add('hidden');
                    modal.classList.remove('flex');
                    resolve(true);
                };

                modalCancelBtn.onclick = handleCancel;
                modalConfirmBtn.onclick = handleConfirm;

                if (isConfirm) {
                    modalCancelBtn.classList.remove('hidden');
                    modalConfirmBtn.textContent = 'Confirm Delete';
                    modalConfirmBtn.classList.remove('bg-green-600', 'hover:bg-green-700');
                    modalConfirmBtn.classList.add('bg-red-600', 'hover:bg-red-700');
                } else {
                    modalCancelBtn.classList.add('hidden');
                    modalConfirmBtn.textContent = 'OK';
                    modalConfirmBtn.classList.add('bg-green-600', 'hover:bg-green-700');
                    modalConfirmBtn.classList.remove('bg-red-600', 'hover:bg-red-700');
                }

                modal.classList.remove('hidden');
                modal.classList.add('flex');
            });
        }

        // Save state to localStorage
        function saveState() {
            try {
                const dataToSave = {
                    workouts: state.workouts,
                    foods: state.foods,
                    profile: state.profile
                };
                localStorage.setItem(STORAGE_KEY, JSON.stringify(dataToSave));
            } catch (error) {
                console.error("Error saving state to localStorage:", error);
            }
        }

        // Load state from localStorage
        function loadState() {
            try {
                const storedState = localStorage.getItem(STORAGE_KEY);
                if (storedState) {
                    const loadedState = JSON.parse(storedState);
                    state.workouts = loadedState.workouts || [];
                    state.foods = loadedState.foods || [];
                    state.profile = loadedState.profile || state.profile;
                    
                    updateStats();
                    renderWorkouts();
                    renderFoods();
                    renderProfile();
                }
            } catch (error) {
                console.error("Error loading state from localStorage:", error);
            }
        }

        // --- TDEE Calculation Logic (Mifflin-St Jeor) ---

        function calculateBMR(weight, height, age, gender) {
            // weight in kg, height in cm, age in years
            if (gender === 'male') {
                return (10 * weight) + (6.25 * height) - (5 * age) + 5;
            } else if (gender === 'female') {
                return (10 * weight) + (6.25 * height) - (5 * age) - 161;
            }
            return 0;
        }

        function calculateTDEE(bmr, activityMultiplier) {
            return bmr * activityMultiplier;
        }

        function updateTDEEProfile(gender, age, height, weight, activityMultiplier) {
            const bmr = calculateBMR(weight, height, age, gender);
            const tdee = calculateTDEE(bmr, parseFloat(activityMultiplier));

            state.profile.gender = gender;
            state.profile.age = age;
            state.profile.height = height;
            state.profile.weight = weight;
            state.profile.activity = parseFloat(activityMultiplier);
            state.profile.bmr = Math.round(bmr);
            state.profile.tdee = Math.round(tdee);

            updateStats(); 
            renderProfile();
        }

        // --- Render Functions ---

        function renderProfile() {
            const profile = state.profile;

            // Fill form inputs
            if (profile.gender) pGenderInput.value = profile.gender;
            if (profile.age) pAgeInput.value = profile.age;
            if (profile.height) pHeightInput.value = profile.height;
            if (profile.weight) pWeightInput.value = profile.weight;
            if (profile.activity) pActivityInput.value = profile.activity;

            // Display results
            if (profile.bmr && profile.tdee) {
                displayBMR.textContent = profile.bmr.toLocaleString();
                displayTDEE.textContent = profile.tdee.toLocaleString();
                tdeeGuidance.classList.remove('hidden');

                // Guidance calculations (0.5 kg/week ~ 500 kcal deficit/surplus per day)
                const loss = profile.tdee - 500;
                const gain = profile.tdee + 500;

                guidanceMaintain.textContent = profile.tdee.toLocaleString();
                guidanceLoss.textContent = loss > 1000 ? loss.toLocaleString() : "1000 (minimum recommended)";
                guidanceGain.textContent = gain.toLocaleString();

            } else {
                displayBMR.textContent = 'N/A';
                displayTDEE.textContent = 'N/A';
                tdeeGuidance.classList.add('hidden');
            }
        }

        function renderWorkouts() {
            workoutList.innerHTML = '';
            // Sort by date descending
            const sortedWorkouts = [...state.workouts].sort((a, b) => new Date(b.date) - new Date(a.date));

            if (sortedWorkouts.length === 0) {
                wNoDataMessage.classList.remove('hidden');
                return;
            }
            wNoDataMessage.classList.add('hidden');

            sortedWorkouts.forEach(workout => {
                const row = document.createElement('tr');
                row.className = 'data-row hover:bg-gray-700 transition duration-100';
                const totalReps = workout.sets * workout.reps;
                const totalCalories = Math.round(totalReps * 1.5); 

                row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-200">${workout.date}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${workout.activity}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${workout.sets}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${workout.reps}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-bold text-red-400">${totalCalories}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium space-x-2 text-center">
                        <button onclick="editWorkout('${workout.id}')" class="text-indigo-400 hover:text-indigo-600 transition">Edit</button>
                        <button onclick="deleteWorkout('${workout.id}')" class="text-red-400 hover:text-red-600 transition">Delete</button>
                    </td>
                `;
                workoutList.appendChild(row);
            });
        }

        function renderFoods() {
            foodList.innerHTML = '';
            // Sort by date descending
            const sortedFoods = [...state.foods].sort((a, b) => new Date(b.date) - new Date(a.date));

            if (sortedFoods.length === 0) {
                fNoDataMessage.classList.remove('hidden');
                return;
            }
            fNoDataMessage.classList.add('hidden');

            sortedFoods.forEach(food => {
                const row = document.createElement('tr');
                row.className = 'data-row hover:bg-gray-700 transition duration-100';

                row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-200">${food.date}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.item}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.weight}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-bold text-green-400">${food.calories}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.protein}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium space-x-2 text-center">
                        <button onclick="editFood('${food.id}')" class="text-indigo-400 hover:text-indigo-600 transition">Edit</button>
                        <button onclick="deleteFood('${food.id}')" class="text-red-400 hover:text-red-600 transition">Delete</button>
                    </td>
                `;
                foodList.appendChild(row);
            });
        }

        // --- Stat Update Function ---

        function updateStats() {
            statTotalWorkouts.textContent = state.workouts.length.toLocaleString();

            let totalBurned = 0;
            state.workouts.forEach(w => {
                const calories = Math.round(w.sets * w.reps * 1.5);
                totalBurned += calories;
            });
            statTotalBurned.textContent = totalBurned.toLocaleString();

            let totalConsumed = 0;
            let totalProtein = 0;
            state.foods.forEach(f => {
                totalConsumed += f.calories;
                totalProtein += f.protein;
            });
            statTotalConsumed.textContent = totalConsumed.toLocaleString();
            statTotalProtein.textContent = totalProtein.toLocaleString();

            const tdeeValue = state.profile.tdee ? state.profile.tdee.toLocaleString() : 'N/A';
            statTDEE.textContent = tdeeValue;

            // Caloric Balance Calculation (Net Daily Balance)
            if (state.profile.tdee) {
                // Net calories from tracking = Food Consumed - Calories Burned
                const netCaloriesTracked = totalConsumed - totalBurned; 
                // Daily Balance = (Net calories tracked) - TDEE
                const dailyBalance = netCaloriesTracked - state.profile.tdee;
                statBalance.textContent = dailyBalance.toLocaleString();

                // Update card color based on balance
                statBalanceCard.classList.remove('bg-gray-600', 'bg-red-700', 'bg-green-700');
                if (dailyBalance > 200) {
                    statBalanceCard.classList.add('bg-green-700'); // Surplus (Weight Gain)
                } else if (dailyBalance < -200) {
                    statBalanceCard.classList.add('bg-red-700'); // Deficit (Weight Loss)
                } else {
                    statBalanceCard.classList.add('bg-gray-600'); // Maintenance Zone
                }

                statBalance.parentNode.querySelector('.text-xs').textContent = 'Caloric Balance (Net - TDEE)';

            } else {
                statBalance.textContent = 'N/A';
                statBalanceCard.classList.remove('bg-red-700', 'bg-green-700');
                statBalanceCard.classList.add('bg-gray-600');
                statBalance.parentNode.querySelector('.text-xs').textContent = 'Caloric Balance (Set TDEE First)';
            }

            saveState();
        }

        // --- Event Handlers & CRUD Logic ---

        // View Switching
        window.switchView = function(viewName) {
            currentView = viewName;

            workoutView.classList.add('hidden');
            nutritionView.classList.add('hidden');
            tdeeView.classList.add('hidden');

            if (viewName === 'workout') workoutView.classList.remove('hidden');
            if (viewName === 'nutrition') nutritionView.classList.remove('hidden');
            if (viewName === 'tdee') tdeeView.classList.remove('hidden');

            document.querySelectorAll('.tab').forEach(tab => {
                tab.classList.remove('active');
            });
            document.querySelector(`.tab[onclick="switchView('${viewName}')"]`).classList.add('active');
        }

        // Workout CRUD
        window.deleteWorkout = async function(id) {
            const confirmed = await customModal("Are you sure you want to delete this workout entry?", "Confirm Deletion", true);
            if (confirmed) {
                state.workouts = state.workouts.filter(w => w.id !== id);
                updateStats();
                renderWorkouts();
                customModal("Workout entry deleted.", "Success");
            }
        }

        window.editWorkout = function(id) {
            const workout = state.workouts.find(w => w.id === id);
            if (workout) {
                wIdInput.value = workout.id;
                wDateInput.value = workout.date;
                wActivityInput.value = workout.activity;
                wSetsInput.value = workout.sets;
                wRepsInput.value = workout.reps;
                wSubmitBtn.textContent = 'Update Workout';
                wSubmitBtn.classList.remove('bg-green-600');
                wSubmitBtn.classList.add('bg-indigo-600');
            }
        }

        workoutForm.addEventListener('submit', (e) => {
            e.preventDefault();

            const isEdit = wIdInput.value !== "";
            const newEntry = {
                date: wDateInput.value,
                activity: wActivityInput.value,
                sets: parseInt(wSetsInput.value),
                reps: parseInt(wRepsInput.value),
            };

            if (isEdit) {
                state.workouts = state.workouts.map(w => w.id === wIdInput.value ? { ...w, ...newEntry } : w);
                customModal("Workout updated successfully.", "Success");
            } else {
                newEntry.id = crypto.randomUUID(); 
                state.workouts.push(newEntry);
                customModal("Workout added successfully.", "Success");
            }

            updateStats();
            renderWorkouts();
            workoutForm.reset();
            wIdInput.value = "";
            wSubmitBtn.textContent = 'Add Workout';
            wSubmitBtn.classList.remove('bg-indigo-600');
            wSubmitBtn.classList.add('bg-green-600');
        });


        // Food CRUD
        window.deleteFood = async function(id) {
            const confirmed = await customModal("Are you sure you want to delete this food entry?", "Confirm Deletion", true);
            if (confirmed) {
                state.foods = state.foods.filter(f => f.id !== id);
                updateStats();
                renderFoods();
                customModal("Food entry deleted.", "Success");
            }
        }

        window.editFood = function(id) {
            const food = state.foods.find(f => f.id === id);
            if (food) {
                fIdInput.value = food.id;
                fDateInput.value = food.date;
                fItemInput.value = food.item;
                fCaloriesInput.value = food.calories;
                fProteinInput.value = food.protein;
                fFatInput.value = food.fat;
                fWeightInput.value = food.weight;
                fSubmitBtn.textContent = 'Update Food Entry';
                fSubmitBtn.classList.remove('bg-green-600');
                fSubmitBtn.classList.add('bg-indigo-600');
            }
        }

        foodForm.addEventListener('submit', (e) => {
            e.preventDefault();

            const isEdit = fIdInput.value !== "";
            const newEntry = {
                date: fDateInput.value,
                item: fItemInput.value,
                calories: parseInt(fCaloriesInput.value),
                protein: parseInt(fProteinInput.value),
                fat: parseInt(fFatInput.value),
                weight: parseInt(fWeightInput.value),
            };

            if (isEdit) {
                state.foods = state.foods.map(f => f.id === fIdInput.value ? { ...f, ...newEntry } : f);
                customModal("Food entry updated successfully.", "Success");
            } else {
                newEntry.id = crypto.randomUUID(); 
                state.foods.push(newEntry);
                customModal("Food entry added successfully.", "Success");
            }

            updateStats();
            renderFoods();
            foodForm.reset();
            fIdInput.value = "";
            fSubmitBtn.textContent = 'Add Food Entry';
            fSubmitBtn.classList.remove('bg-indigo-600');
            fSubmitBtn.classList.add('bg-green-600');
        });

        // Profile/TDEE Calculation Handler
        profileForm.addEventListener('submit', (e) => {
            e.preventDefault();

            const gender = pGenderInput.value;
            const age = parseInt(pAgeInput.value);
            const height = parseInt(pHeightInput.value);
            const weight = parseInt(pWeightInput.value);
            const activity = parseFloat(pActivityInput.value);

            if (gender && age && height && weight && activity) {
                updateTDEEProfile(gender, age, height, weight, activity);
                saveState();
                customModal(`TDEE calculated! Your TDEE is ${state.profile.tdee.toLocaleString()} kcal/day.`, "Success");
            } else {
                customModal("Please fill in all TDEE profile fields.", "Error");
            }
        });

        // --- Initialization ---
        function init() {
            // Set default date inputs to today
            const today = new Date().toISOString().split('T')[0];
            wDateInput.value = today;
            fDateInput.value = today;

            loadState();
            switchView('workout'); 
        }

        window.onload = init;
    </script>
</body>
</html>
