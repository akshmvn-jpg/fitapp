<!-- Load Tailwind CSS for modern styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
        /* Since we removed the <html> and <body> tags, apply base styles to the root container */
        .app-container {
            font-family: 'Inter', sans-serif;
            background-color: #0d1117; 
            min-height: 100vh;
            color: #d1d5db; /* Default text color (gray-300) */
            padding: 4px;
        }
        .card {
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.4), 0 4px 6px -4px rgba(0, 0, 0, 0.2);
        }
        /* Dark mode stripe for table rows */
        .data-row:nth-child(odd) {
            background-color: #27303d; /* Slightly lighter dark gray/blue for stripe */
        }
        /* Active Tab Styling */
        .tab.active {
            background-color: #1f2937; /* Gray-800 */
            border-bottom: 3px solid #34d399; /* Green-400 */
            color: #34d399; /* Green-400 */
        }
    </style>

    <!-- NEW: App container starts immediately with content -->
    <div class="app-container">
        <div class="max-w-4xl mx-auto p-4 sm:p-8">
            <!-- Header -->
            <header class="text-center mb-6 p-6 bg-gray-800 rounded-xl card">
                <h1 class="text-4xl font-extrabold text-green-400 mb-2">⚡️ Apex Health & Energy Tracker</h1>
                <p class="text-gray-400">Monitor TDEE, track macros, and calculate your **Caloric Balance** for targeted results.</p>
            </header>

            <!-- Stats Summary (Updated to handle 6 elements better) -->
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
            profile: { // New profile object for TDEE inputs
                age: null,
                gender: null,
                height: null, // cm
                weight: null, // kg
                activity: null, // multiplier
                bmr: null,
                tdee: null
            }
        };
        let currentView = 'workout';

        // --- DOM Elements ---
        const workoutForm = document.getElementById('workout-form');
        const foodForm = document.getElementById('food-form');
        const profileForm = document.getElementById('profile-form'); // New

        // TDEE Profile DOM elements
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


        // Workout DOM elements
        const workoutList = document.getElementById('workout-list');
        const wNoDataMessage = document.getElementById('w-no-data-message');
        const wSubmitBtn = document.getElementById('w-submit-btn');
        const wIdInput = document.getElementById('workout-id');
        const wDateInput = document.getElementById('w-date');
        const wActivityInput = document.getElementById('w-activity');
        const wSetsInput = document.getElementById('w-sets');
        const wRepsInput = document.getElementById('w-reps');

        // Food DOM elements
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

        // Stat DOM elements
        const statTotalWorkouts = document.getElementById('stat-total-workouts');
        const statTotalBurned = document.getElementById('stat-total-burned');
        const statTotalConsumed = document.getElementById('stat-total-consumed');
        const statTotalProtein = document.getElementById('stat-total-protein');
        const statTDEE = document.getElementById('stat-tdee'); // TDEE stat
        const statBalanceCard = document.getElementById('stat-balance-card'); // New card for balance
        const statBalance = document.getElementById('stat-balance'); // New stat for balance

        // View DOM elements
        const workoutView = document.getElementById('workout-view');
        const nutritionView = document.getElementById('nutrition-view');
        const tdeeView = document.getElementById('tdee-view'); // New
        const tabs = document.querySelectorAll('.tab');


        // --- UTILITIES (MODAL) ---

        /** Shows a custom modal for alert or confirmation. */
        function showCustomModal(title, message, isConfirmation = false) {
            const modal = document.getElementById('custom-modal');
            document.getElementById('modal-title').textContent = title;
            document.getElementById('modal-message').textContent = message;

            const confirmBtn = document.getElementById('modal-confirm-btn');
            const cancelBtn = document.getElementById('modal-cancel-btn');

            confirmBtn.textContent = isConfirmation ? 'Delete' : 'OK';
            confirmBtn.className = isConfirmation
                ? 'px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition'
                : 'px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition';

            if (isConfirmation) {
                cancelBtn.classList.remove('hidden');
            } else {
                cancelBtn.classList.add('hidden');
            }

            modal.classList.remove('hidden');
            modal.classList.add('flex');

            return new Promise(resolve => {
                const handleConfirm = () => {
                    cleanup();
                    resolve(true);
                };
                const handleCancel = () => {
                    cleanup();
                    resolve(false);
                };
                const cleanup = () => {
                    modal.classList.remove('flex');
                    modal.classList.add('hidden');
                    confirmBtn.removeEventListener('click', handleConfirm);
                    cancelBtn.removeEventListener('click', handleCancel);
                };

                confirmBtn.addEventListener('click', handleConfirm);
                cancelBtn.addEventListener('click', handleCancel);
            });
        }


        // --- DATA HANDLING ---

        /** Loads state from localStorage. */
        function loadData() {
            try {
                const data = localStorage.getItem(STORAGE_KEY);
                if (data) {
                    const parsed = JSON.parse(data);
                    // Ensure both arrays exist, even if empty
                    state.workouts = Array.isArray(parsed.workouts) ? parsed.workouts : [];
                    state.foods = Array.isArray(parsed.foods) ? parsed.foods : [];
                    // Load profile data, using default if missing or invalid
                    state.profile = parsed.profile && typeof parsed.profile === 'object' ? parsed.profile : state.profile;
                }
            } catch (e) {
                console.error("Error loading from localStorage:", e);
                // Reset state on error
                state = { workouts: [], foods: [], profile: state.profile };
            }
        }

        /** Saves state to localStorage. */
        function saveData() {
            try {
                localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
            } catch (e) {
                console.error("Error saving to localStorage:", e);
                showCustomModal('Error', 'Could not save data to your browser storage.');
            }
        }

        // --- TDEE LOGIC ---

        /** Calculates Basal Metabolic Rate (BMR) using Mifflin-St Jeor Equation. */
        function calculateBMR(gender, age, height, weight) {
            // Inputs must be in: gender ('male'/'female'), age (years), height (cm), weight (kg)
            if (!gender || age <= 0 || height <= 0 || weight <= 0) return 0;

            let bmr;
            // BMR = (10 * weight in kg) + (6.25 * height in cm) - (5 * age in years) + S
            if (gender === 'male') {
                // S = +5 for men
                bmr = (10 * weight) + (6.25 * height) - (5 * age) + 5;
            } else if (gender === 'female') {
                // S = -161 for women
                bmr = (10 * weight) + (6.25 * height) - (5 * age) - 161;
            } else {
                return 0;
            }
            return Math.round(bmr);
        }

        /** Calculates TDEE from BMR and Activity Multiplier. */
        function calculateTDEE(bmr, activityMultiplier) {
            if (bmr <= 0 || !activityMultiplier) return 0;
            return Math.round(bmr * parseFloat(activityMultiplier));
        }

        function updateTDEEProfile(e) {
            e.preventDefault();

            const age = parseInt(pAgeInput.value, 10);
            const height = parseInt(pHeightInput.value, 10);
            const weight = parseInt(pWeightInput.value, 10);
            const gender = pGenderInput.value;
            const activityMultiplier = pActivityInput.value;

            if (!gender || isNaN(age) || isNaN(height) || isNaN(weight) || !activityMultiplier) {
                showCustomModal('Input Error', 'Please fill out all profile fields with valid numbers.');
                return;
            }
            
            if (age <= 0 || height <= 0 || weight <= 0) {
                 showCustomModal('Input Error', 'Age, height, and weight must be positive numbers.');
                 return;
            }


            const bmr = calculateBMR(gender, age, height, weight);
            const tdee = calculateTDEE(bmr, activityMultiplier);

            state.profile = { age, gender, height, weight, activity: activityMultiplier, bmr, tdee };

            saveData();
            renderTDEE();
            renderStats(); // Update dashboard stat
            showCustomModal('Success', `Profile saved! Your calculated TDEE is ${tdee} kcal.`);
        }

        function renderTDEE() {
            const profile = state.profile;

            if (profile.tdee) {
                displayBMR.textContent = profile.bmr.toLocaleString();
                displayTDEE.textContent = profile.tdee.toLocaleString();
                tdeeGuidance.classList.remove('hidden');

                // Guidance (based on TDEE +/- 500 kcal for approx 0.5kg/week change)
                guidanceMaintain.textContent = profile.tdee.toLocaleString();
                guidanceLoss.textContent = Math.max(1000, profile.tdee - 500).toLocaleString(); // Min 1000 kcal for safety
                guidanceGain.textContent = (profile.tdee + 500).toLocaleString();

                // Populate form fields
                pAgeInput.value = profile.age;
                pHeightInput.value = profile.height;
                pWeightInput.value = profile.weight;
                pGenderInput.value = profile.gender;
                pActivityInput.value = profile.activity;

                statTDEE.textContent = profile.tdee.toLocaleString();

            } else {
                displayBMR.textContent = 'N/A';
                displayTDEE.textContent = 'N/A';
                tdeeGuidance.classList.add('hidden');
                statTDEE.textContent = 'N/A';
            }
        }


        // --- GENERAL LOGIC ---

        /** Calculates estimated calories burned based on sets and reps. */
        function calculateWorkoutCalories(sets, reps) {
            // Simplified estimation for strength training: 1.5 kcal per rep.
            const kcalPerRep = 1.5;
            const totalReps = sets * reps;
            return Math.round(totalReps * kcalPerRep);
        }

        /** Switches the active view (Workout, Nutrition, or TDEE). */
        function switchView(viewName) {
            currentView = viewName;

            // Update tab styles
            tabs.forEach(tab => {
                tab.classList.remove('active');
                if (tab.textContent.toLowerCase().includes(viewName)) {
                    tab.classList.add('active');
                }
            });

            // Update view visibility
            workoutView.classList.toggle('hidden', viewName !== 'workout');
            nutritionView.classList.toggle('hidden', viewName !== 'nutrition');
            tdeeView.classList.toggle('hidden', viewName !== 'tdee');

            // Reset forms when switching out of TDEE view
            if (viewName === 'workout') { resetWorkoutForm(); }
            if (viewName === 'nutrition') { resetFoodForm(); }
        }
        // Expose to window for inline onclick handler
        window.switchView = switchView;


        /** Calculates and displays summary stats, including the new Caloric Balance. */
        function renderStats() {
            const totalWorkouts = state.workouts.length;
            const totalBurnedCalories = state.workouts.reduce((sum, w) => sum + parseInt(w.calories || 0, 10), 0);
            const totalConsumedCalories = state.foods.reduce((sum, f) => sum + parseInt(f.calories || 0, 10), 0);
            const totalProtein = state.foods.reduce((sum, f) => sum + parseInt(f.protein || 0, 10), 0);

            statTotalWorkouts.textContent = totalWorkouts.toLocaleString();
            statTotalBurned.textContent = totalBurnedCalories.toLocaleString();
            statTotalConsumed.textContent = totalConsumedCalories.toLocaleString();
            statTotalProtein.textContent = totalProtein.toLocaleString();

            // TDEE and Caloric Balance Calculation
            const tdee = state.profile.tdee || 0;
            const netBalance = (tdee + totalBurnedCalories) - totalConsumedCalories;
            
            let balanceText = 'N/A';
            let balanceBg = 'bg-gray-600';

            if (tdee > 0) {
                // Determine status and background color
                if (netBalance < -100) {
                    // Deficit (Negative, Aiming for Weight Loss)
                    balanceBg = 'bg-red-600';
                    balanceText = `${netBalance.toLocaleString()} Deficit`; 
                } else if (netBalance > 100) {
                    // Surplus (Positive, Aiming for Weight Gain)
                    balanceBg = 'bg-blue-600';
                    balanceText = `+${netBalance.toLocaleString()} Surplus`; 
                } else {
                    // Maintenance (Near Zero)
                    balanceBg = 'bg-teal-600';
                    balanceText = `${netBalance.toLocaleString()} Maintenance`;
                }
            } else {
                 balanceText = 'Profile Required';
            }

            // Update TDEE and Balance stat cards
            statTDEE.textContent = tdee ? tdee.toLocaleString() : 'N/A';
            statBalance.textContent = balanceText;
            
            // Dynamically update the balance card's background color/class
            statBalanceCard.className = `text-white p-4 rounded-xl card text-center ${balanceBg}`;
        }

        // --- WORKOUT CRUD ---

        /** Adds or updates a workout. */
        function addOrUpdateWorkout(data) {
            if (data.id) {
                const index = state.workouts.findIndex(w => w.id === data.id);
                // When updating, recalculate calories just in case sets/reps were changed during edit
                data.calories = calculateWorkoutCalories(data.sets, data.reps);
                if (index !== -1) {
                    state.workouts[index] = { ...data, id: data.id };
                }
            } else {
                data.id = crypto.randomUUID();
                state.workouts.push(data);
            }
            saveData();
            renderWorkouts();
            resetWorkoutForm();
        }

        /** Deletes a workout by ID. */
        async function deleteWorkout(id) {
            const confirmed = await showCustomModal('Confirm Deletion', 'Delete this workout entry?', true);
            if (confirmed) {
                state.workouts = state.workouts.filter(w => w.id !== id);
                saveData();
                renderWorkouts();
            }
        }
        window.deleteWorkout = deleteWorkout;

        /** Populates the form fields to edit an existing workout. */
        window.editWorkout = function(id) {
            if (currentView !== 'workout') switchView('workout');
            const workout = state.workouts.find(w => w.id === id);
            if (workout) {
                wIdInput.value = workout.id;
                wDateInput.value = workout.date;
                wActivityInput.value = workout.activity;
                wSetsInput.value = workout.sets || 0;
                wRepsInput.value = workout.reps || 0;
                wSubmitBtn.textContent = 'Update Workout';
                wSubmitBtn.classList.replace('bg-green-600', 'bg-indigo-600');
                wSubmitBtn.classList.replace('hover:bg-green-700', 'hover:bg-indigo-700');
                workoutForm.scrollIntoView({ behavior: 'smooth' });
            }
        };

        function resetWorkoutForm() {
            workoutForm.reset();
            wIdInput.value = '';
            wSubmitBtn.textContent = 'Add Workout';
            wSubmitBtn.classList.replace('bg-indigo-600', 'bg-green-600');
            wSubmitBtn.classList.replace('hover:bg-indigo-700', 'hover:bg-green-700');
            wDateInput.value = new Date().toISOString().substring(0, 10);
        }

        function renderWorkouts() {
            workoutList.innerHTML = '';
            if (state.workouts.length === 0) {
                wNoDataMessage.classList.remove('hidden');
            } else {
                wNoDataMessage.classList.add('hidden');
                const sortedWorkouts = [...state.workouts].sort((a, b) => new Date(b.date) - new Date(a.date));

                sortedWorkouts.forEach(workout => {
                    const setsDisplay = workout.sets || 'N/A';
                    const repsDisplay = workout.reps || 'N/A';
                    const caloriesDisplay = workout.calories || 0;

                    const row = document.createElement('tr');
                    row.className = 'data-row';
                    row.innerHTML = `
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-200">${workout.date}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${workout.activity}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${setsDisplay}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${repsDisplay}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${caloriesDisplay}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-center text-sm font-medium space-x-2">
                            <button onclick="editWorkout('${workout.id}')" class="text-indigo-400 hover:text-indigo-300 font-medium transition">Edit</button>
                            <button onclick="deleteWorkout('${workout.id}')" class="text-red-400 hover:text-red-300 font-medium transition">Delete</button>
                        </td>
                    `;
                    workoutList.appendChild(row);
                });
            }
            renderStats();
        }

        // --- FOOD CRUD ---

        /** Adds or updates a food entry. */
        function addOrUpdateFood(data) {
            if (data.id) {
                const index = state.foods.findIndex(f => f.id === data.id);
                if (index !== -1) {
                    state.foods[index] = { ...data, id: data.id };
                }
            } else {
                data.id = crypto.randomUUID();
                state.foods.push(data);
            }
            saveData();
            renderFoods();
            resetFoodForm();
        }

        /** Deletes a food entry by ID. */
        async function deleteFood(id) {
            const confirmed = await showCustomModal('Confirm Deletion', 'Delete this food entry?', true);
            if (confirmed) {
                state.foods = state.foods.filter(f => f.id !== id);
                saveData();
                renderFoods();
            }
        }
        window.deleteFood = deleteFood;

        /** Populates the form fields to edit an existing food entry. */
        window.editFood = function(id) {
            if (currentView !== 'nutrition') switchView('nutrition');
            const food = state.foods.find(f => f.id === id);
            if (food) {
                fIdInput.value = food.id;
                fDateInput.value = food.date;
                fItemInput.value = food.item;
                fCaloriesInput.value = food.calories;
                fProteinInput.value = food.protein;
                fFatInput.value = food.fat;
                fWeightInput.value = food.weight || 0;
                fSubmitBtn.textContent = 'Update Food Entry';
                fSubmitBtn.classList.replace('bg-green-600', 'bg-indigo-600');
                fSubmitBtn.classList.replace('hover:bg-green-700', 'hover:bg-indigo-700');
                foodForm.scrollIntoView({ behavior: 'smooth' });
            }
        };

        function resetFoodForm() {
            foodForm.reset();
            fIdInput.value = '';
            fSubmitBtn.textContent = 'Add Food Entry';
            fSubmitBtn.classList.replace('bg-indigo-600', 'bg-green-600');
            fSubmitBtn.classList.replace('hover:bg-indigo-700', 'hover:bg-green-700');
            fDateInput.value = new Date().toISOString().substring(0, 10);
        }

        function renderFoods() {
            foodList.innerHTML = '';
            if (state.foods.length === 0) {
                fNoDataMessage.classList.remove('hidden');
            } else {
                fNoDataMessage.classList.add('hidden');
                const sortedFoods = [...state.foods].sort((a, b) => new Date(b.date) - new Date(a.date));

                sortedFoods.forEach(food => {
                    const weightDisplay = food.weight || 'N/A';
                    const proteinDisplay = food.protein || 0;
                    const caloriesDisplay = food.calories || 0;

                    const row = document.createElement('tr');
                    row.className = 'data-row';
                    row.innerHTML = `
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-200">${food.date}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.item}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${weightDisplay}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${caloriesDisplay}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${proteinDisplay}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-center text-sm font-medium space-x-2">
                            <button onclick="editFood('${food.id}')" class="text-indigo-400 hover:text-indigo-300 font-medium transition">Edit</button>
                            <button onclick="deleteFood('${food.id}')" class="text-red-400 hover:text-red-300 font-medium transition">Delete</button>
                        </td>
                    `;
                    foodList.appendChild(row);
                });
            }
            renderStats();
        }

        // --- EVENT LISTENERS ---

        workoutForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const sets = parseInt(wSetsInput.value, 10);
            const reps = parseInt(wRepsInput.value, 10);

            if (sets <= 0 || reps <= 0) {
                 showCustomModal('Input Error', 'Sets and Avg Reps must be positive numbers.');
                 return;
            }

            const calculatedCalories = calculateWorkoutCalories(sets, reps);

            const data = {
                id: wIdInput.value,
                date: wDateInput.value,
                activity: wActivityInput.value.trim(),
                sets: sets,
                reps: reps,
                calories: calculatedCalories, // Using the calculated value
            };
            addOrUpdateWorkout(data);
        });

        foodForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const calories = parseInt(fCaloriesInput.value, 10);
            const protein = parseInt(fProteinInput.value, 10);
            const fat = parseInt(fFatInput.value, 10);
            const weight = parseInt(fWeightInput.value, 10);

            if (calories <= 0 || protein < 0 || fat < 0 || weight <= 0) {
                 showCustomModal('Input Error', 'Calories and Weight must be positive. Protein and Fat must be zero or positive.');
                 return;
            }

            const data = {
                id: fIdInput.value,
                date: fDateInput.value,
                item: fItemInput.value.trim(),
                calories: calories,
                protein: protein,
                fat: fat,
                weight: weight,
            };
            addOrUpdateFood(data);
        });

        profileForm.addEventListener('submit', updateTDEEProfile); // New listener for TDEE form


        // --- INITIALIZATION ---
        window.onload = () => {
            loadData();
            renderWorkouts();
            renderFoods();
            renderTDEE(); // Render TDEE profile/calculation results

            // Set default date for workout/food forms
            const today = new Date().toISOString().substring(0, 10);
            wDateInput.value = today;
            fDateInput.value = today;
            switchView('workout'); // Start on the workout tab
        };
    </script>
