<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
    <title>Advanced Fitness & Nutrition Tracker - Manual Edition</title>
    
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
        .estimation-container {
            min-height: 90px;
        }
    </style>
</head>
<body>
    <div class="app-container">
        <div class="max-w-4xl mx-auto p-4 sm:p-8">
            <header class="text-center mb-6 p-1 rounded-xl">
                <h1 class="text-3xl font-extrabold text-green-400 mb-2">Fitness & Nutrition Tracker</h1>
                <p class="text-gray-400 text-sm">Manual Entry Edition - No External APIs</p>
            </header>

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
                <div class="bg-yellow-700 text-white p-4 rounded-xl card text-center">
                    <p class="text-xs font-semibold uppercase opacity-80">Your TDEE (kcal)</p>
                    <p id="stat-tdee" class="text-2xl font-bold mt-1">N/A</p>
                </div>
                <div id="stat-balance-card" class="text-white p-4 rounded-xl card text-center bg-gray-600">
                    <p class="text-xs font-semibold uppercase opacity-80">Caloric Balance (kcal)</p>
                    <p id="stat-balance" class="text-2xl font-bold mt-1">N/A</p>
                </div>
            </div>

            <div class="flex border-b border-gray-700 mb-6 overflow-x-auto">
                <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition" onclick="switchView('workout')">
                    Workouts
                </button>
                <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition active" onclick="switchView('nutrition')">
                    Nutrition
                </button>
                <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition" onclick="switchView('tdee')">
                    TDEE / Profile
                </button>
            </div>

            <div id="workout-view" class="hidden">
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
                        </tbody>
                    </table>
                    <p id="w-no-data-message" class="text-center text-gray-500 p-4 hidden">No workouts logged yet. Get moving!</p>
                </div>
            </div>

            <div id="nutrition-view">
                <div class="bg-gray-800 p-6 rounded-xl card mb-8">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Log New Food Entry</h2>
                    <p class="text-sm text-gray-500 mb-2">
                        Enter the details for your food item manually. Get the nutrition facts from the label or a trusted source.
                    </p>
                    <form id="food-form" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                        <input type="hidden" id="food-id">

                        <div class="lg:col-span-1">
                            <label for="f-date" class="block text-sm font-medium text-gray-400 mb-1">Date</label>
                            <input type="date" id="f-date" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div class="lg:col-span-2">
                            <label for="f-item" class="block text-sm font-medium text-gray-400 mb-1">Meal/Item Name</label>
                            <input type="text" id="f-item" placeholder="e.g., Chicken Breast, Pasta" required
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div class="lg:col-span-1">
                            <label for="f-weight" class="block text-sm font-medium text-gray-400 mb-1">Approx. Weight (g)</label>
                            <input type="number" id="f-weight" placeholder="grams" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>
                        
                        <div>
                            <label for="f-calories" class="block text-sm font-medium text-gray-400 mb-1">Calories (kcal)</label>
                            <input type="number" id="f-calories" placeholder="0" required min="0"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-protein" class="block text-sm font-medium text-gray-400 mb-1">Protein (g)</label>
                            <input type="number" id="f-protein" placeholder="0" required min="0"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-fat" class="block text-sm font-medium text-gray-400 mb-1">Fat (g)</label>
                            <input type="number" id="f-fat" placeholder="0" required min="0"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>
                        
                        <div>
                            <label for="f-carbs" class="block text-sm font-medium text-gray-400 mb-1">Carbs (g)</label>
                            <input type="number" id="f-carbs" placeholder="0" required min="0"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>
                        
                        <div class="lg:col-span-4 mt-2">
                            <button type="submit" id="f-submit-btn"
                                    class="w-full bg-green-600 text-white font-bold py-3 px-4 rounded-xl hover:bg-green-700 transition duration-150 ease-in-out">
                                Log Entry
                            </button>
                        </div>
                    </form>
                </div>

                <div class="bg-gray-800 p-6 rounded-xl card overflow-x-auto">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Nutrition History</h2>
                    <table class="min-w-full divide-y divide-gray-700">
                        <thead>
                            <tr>
                                <th class="px-4 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Date</th>
                                <th class="px-4 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Item/Meal</th>
                                <th class="px-4 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Weight (g)</th>
                                <th class="px-4 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Calories (kcal)</th>
                                <th class="px-4 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Protein (g)</th>
                                <th class="px-4 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Carbs (g)</th>
                                <th class="px-4 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Fat (g)</th>
                                <th class="px-4 py-3 text-center text-xs font-medium text-gray-400 uppercase tracking-wider">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="food-list" class="bg-gray-800 divide-y divide-gray-700">
                        </tbody>
                    </table>
                    <p id="f-no-data-message" class="text-center text-gray-500 p-4 hidden">No food logged yet. Track your intake!</p>
                </div>
            </div>

            <div id="tdee-view" class="hidden">
                <div class="bg-gray-800 p-6 rounded-xl card mb-8">
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">TDEE Profile & Calculator</h2>
                    <p class="text-sm text-gray-400 mb-4">Enter your metrics to calculate your Basal Metabolic Rate (BMR) and Total Daily Energy Expenditure (TDEE).</p>
                    
                    <form id="profile-form" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                        
                        <div>
                            <label for="p-gender" class="block text-sm font-medium text-gray-400 mb-1">Gender</label>
                            <select id="p-gender" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                                <option value="" disabled selected>Select Gender</option>
                                <option value="male">Male</option>
                                <option value="female">Female</option>
                            </select>
                        </div>
                        
                        <div>
                            <label for="p-age" class="block text-sm font-medium text-gray-400 mb-1">Age (Years)</label>
                            <input type="number" id="p-age" placeholder="25" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="p-height" class="block text-sm font-medium text-gray-400 mb-1">Height (cm)</label>
                            <input type="number" id="p-height" placeholder="175" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="p-weight" class="block text-sm font-medium text-gray-400 mb-1">Weight (kg)</label>
                            <input type="number" id="p-weight" placeholder="70" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

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
                age: null, gender: null, height: null, weight: null,
                activity: null, bmr: null, tdee: null
            }
        };
        let currentView = 'nutrition';

        // --- DOM Element Variables ---
        const workoutForm = document.getElementById('workout-form');
        const foodForm = document.getElementById('food-form');
        const profileForm = document.getElementById('profile-form');

        const wDateInput = document.getElementById('w-date');
        const wActivityInput = document.getElementById('w-activity');
        const wSetsInput = document.getElementById('w-sets');
        const wRepsInput = document.getElementById('w-reps');
        const wIdInput = document.getElementById('workout-id');
        const wSubmitBtn = document.getElementById('w-submit-btn');

        const fDateInput = document.getElementById('f-date');
        const fItemInput = document.getElementById('f-item');
        const fWeightInput = document.getElementById('f-weight');
        const fSubmitBtn = document.getElementById('f-submit-btn');
        
        // ** API elements removed **

        const fIdInput = document.getElementById('food-id');
        const fCaloriesInput = document.getElementById('f-calories');
        const fProteinInput = document.getElementById('f-protein');
        const fFatInput = document.getElementById('f-fat');
        const fCarbsInput = document.getElementById('f-carbs');
        const fNoDataMessage = document.getElementById('f-no-data-message');


        const workoutList = document.getElementById('workout-list');
        const foodList = document.getElementById('food-list');
        const wNoDataMessage = document.getElementById('w-no-data-message');


        // TDEE Profile elements
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


        // --- Utility Functions ---

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
                    modalConfirmBtn.removeEventListener('click', handleConfirm);
                    modalCancelBtn.removeEventListener('click', handleCancel);
                    resolve(false);
                };
                const handleConfirm = () => {
                    modal.classList.add('hidden');
                    modal.classList.remove('flex');
                    modalConfirmBtn.removeEventListener('click', handleConfirm);
                    modalCancelBtn.removeEventListener('click', handleCancel);
                    resolve(true);
                };

                // Remove old listeners before adding new ones
                modalConfirmBtn.replaceWith(modalConfirmBtn.cloneNode(true));
                modalCancelBtn.replaceWith(modalCancelBtn.cloneNode(true));
                document.getElementById('modal-confirm-btn').addEventListener('click', handleConfirm);
                document.getElementById('modal-cancel-btn').addEventListener('click', handleCancel);


                if (isConfirm) {
                    document.getElementById('modal-cancel-btn').classList.remove('hidden');
                    document.getElementById('modal-confirm-btn').textContent = 'Confirm Delete';
                    document.getElementById('modal-confirm-btn').classList.remove('bg-green-600', 'hover:bg-green-700');
                    document.getElementById('modal-confirm-btn').classList.add('bg-red-600', 'hover:bg-red-700');
                } else {
                    document.getElementById('modal-cancel-btn').classList.add('hidden');
                    document.getElementById('modal-confirm-btn').textContent = 'OK';
                    document.getElementById('modal-confirm-btn').classList.add('bg-green-600', 'hover:bg-green-700');
                    document.getElementById('modal-confirm-btn').classList.remove('bg-red-600', 'hover:bg-red-700');
                }

                modal.classList.remove('hidden');
                modal.classList.add('flex');
            });
        }

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

        function loadState() {
            try {
                const storedState = localStorage.getItem(STORAGE_KEY);
                if (storedState) {
                    const loadedState = JSON.parse(storedState);
                    state.workouts = loadedState.workouts || [];
                    state.foods = loadedState.foods || [];
                    state.profile = loadedState.profile || state.profile;
                }
                // Set current date on initial load
                const today = new Date().toISOString().split('T')[0];
                if (!wDateInput.value) wDateInput.value = today;
                if (!fDateInput.value) fDateInput.value = today;

                updateStats();
                renderWorkouts();
                renderFoods();
                renderProfile();

            } catch (error) {
                console.error("Error loading state from localStorage:", error);
                // Set defaults even if load fails
                const today = new Date().toISOString().split('T')[0];
                if (!wDateInput.value) wDateInput.value = today;
                if (!fDateInput.value) fDateInput.value = today;
            }
        }

        // --- Core Application Logic ---

        function updateStats() {
            const totalWorkouts = state.workouts.length;
            const totalBurned = state.workouts.reduce((sum, w) => sum + w.caloriesBurned, 0);
            const totalConsumed = state.foods.reduce((sum, f) => sum + f.calories, 0);
            const totalProtein = state.foods.reduce((sum, f) => sum + f.protein, 0);
            
            // Use TDEE for balance, or 0 if not set
            const tdee = state.profile.tdee || 0;
            
            // This is a running total balance.
            const balance = totalConsumed - totalBurned - tdee;


            document.getElementById('stat-total-workouts').textContent = totalWorkouts;
            document.getElementById('stat-total-burned').textContent = totalBurned.toFixed(0);
            document.getElementById('stat-total-consumed').textContent = totalConsumed.toFixed(0);
            document.getElementById('stat-total-protein').textContent = totalProtein.toFixed(0);

            document.getElementById('stat-tdee').textContent = state.profile.tdee ? state.profile.tdee.toFixed(0) : 'N/A';

            const balanceText = state.profile.tdee ? balance.toFixed(0) : 'N/A';
            document.getElementById('stat-balance').textContent = balanceText;

            const balanceCard = document.getElementById('stat-balance-card');
            balanceCard.classList.remove('bg-green-600', 'bg-red-600', 'bg-gray-600');

            if (state.profile.tdee) {
                if (balance <= -250) {
                    balanceCard.classList.add('bg-green-600'); // Significant deficit
                } else if (balance >= 250) {
                    balanceCard.classList.add('bg-red-600'); // Significant surplus
                } else {
                    balanceCard.classList.add('bg-gray-600'); // Maintenance/Neutral
                }
            } else {
                balanceCard.classList.add('bg-gray-600');
            }
        }

        function switchView(view) {
            document.getElementById('workout-view').classList.add('hidden');
            document.getElementById('nutrition-view').classList.add('hidden');
            document.getElementById('tdee-view').classList.add('hidden');

            document.getElementById(`${view}-view`).classList.remove('hidden');

            document.querySelectorAll('.tab').forEach(btn => btn.classList.remove('active'));
            document.querySelector(`.tab[onclick="switchView('${view}')"]`).classList.add('active');

            currentView = view;
        }

        // --- Workout Logic ---

        function logWorkout(e) {
            e.preventDefault();

            const date = wDateInput.value;
            const activity = wActivityInput.value.trim();
            const sets = parseInt(wSetsInput.value);
            const reps = parseInt(wRepsInput.value);
            const id = wIdInput.value;

            if (!date || !activity || !sets || !reps) {
                customModal('Please fill in all workout fields.', 'Input Error');
                return;
            }

            // Calculation: (Total Reps) * 1.5 kcal
            const caloriesBurned = (sets * reps) * 1.5;

            if (id) {
                // Update existing workout
                const index = state.workouts.findIndex(w => w.id === id);
                if (index !== -1) {
                    state.workouts[index] = { ...state.workouts[index], date, activity, sets, reps, caloriesBurned };
                }
            } else {
                // Add new workout
                const newWorkout = {
                    id: `w-${new Date().getTime()}`,
                    date,
                    activity,
                    sets,
                    reps,
                    caloriesBurned
                };
                state.workouts.push(newWorkout);
            }

            saveState();
            updateStats();
            renderWorkouts();
            workoutForm.reset();
            wIdInput.value = '';
            wSubmitBtn.textContent = 'Add Workout';
            wDateInput.value = new Date().toISOString().split('T')[0]; // Reset date to today
        }

        function renderWorkouts() {
            workoutList.innerHTML = ''; // Clear existing list

            if (state.workouts.length === 0) {
                wNoDataMessage.classList.remove('hidden');
                return;
            }

            wNoDataMessage.classList.add('hidden');

            const sortedWorkouts = [...state.workouts].sort((a, b) => new Date(b.date) - new Date(a.date));

            sortedWorkouts.forEach(w => {
                const row = document.createElement('tr');
                row.className = 'data-row';
                row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${w.date}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-white">${w.activity}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${w.sets}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${w.reps}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-bold text-red-400">${w.caloriesBurned.toFixed(0)}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-center text-sm font-medium space-x-2">
                        <button onclick="editWorkout('${w.id}')" class="text-blue-400 hover:text-blue-300 transition">Edit</button>
                        <button onclick="deleteWorkout('${w.id}')" class="text-red-400 hover:text-red-300 transition">Delete</button>
                    </td>
                `;
                workoutList.appendChild(row);
            });
        }

        function editWorkout(id) {
            const workout = state.workouts.find(w => w.id === id);
            if (workout) {
                wIdInput.value = workout.id;
                wDateInput.value = workout.date;
                wActivityInput.value = workout.activity;
                wSetsInput.value = workout.sets;
                wRepsInput.value = workout.reps;
                wSubmitBtn.textContent = 'Update Workout';
                window.scrollTo(0, 0); // Scroll to top
            }
        }

        async function deleteWorkout(id) {
            const confirmed = await customModal('Are you sure you want to delete this workout?', 'Confirm Delete', true);
            if (confirmed) {
                state.workouts = state.workouts.filter(w => w.id !== id);
                saveState();
                updateStats();
                renderWorkouts();
            }
        }

        // --- Nutrition Logic ---

        function logFood(e) {
            e.preventDefault();

            const date = fDateInput.value;
            const item = fItemInput.value.trim();
            const weight = parseFloat(fWeightInput.value);
            const calories = parseFloat(fCaloriesInput.value);
            const protein = parseFloat(fProteinInput.value);
            const fat = parseFloat(fFatInput.value);
            const carbs = parseFloat(fCarbsInput.value);
            const id = fIdInput.value;

            if (!date || !item || isNaN(weight) || isNaN(calories) || isNaN(protein) || isNaN(fat) || isNaN(carbs)) {
                customModal('Please fill in all food fields with valid numbers.', 'Input Error');
                return;
            }

            const foodEntry = { date, item, weight, calories, protein, fat, carbs };

            if (id) {
                // Update
                const index = state.foods.findIndex(f => f.id === id);
                if (index !== -1) {
                    state.foods[index] = { ...state.foods[index], ...foodEntry };
                }
            } else {
                // Add new
                foodEntry.id = `f-${new Date().getTime()}`;
                state.foods.push(foodEntry);
            }

            saveState();
            updateStats();
            renderFoods();
            foodForm.reset();
            fIdInput.value = '';
            fSubmitBtn.textContent = 'Log Entry';
            fDateInput.value = new Date().toISOString().split('T')[0]; // Reset date
        }

        function renderFoods() {
            foodList.innerHTML = ''; // Clear existing list

            if (state.foods.length === 0) {
                fNoDataMessage.classList.remove('hidden');
                return;
            }

            fNoDataMessage.classList.add('hidden');

            const sortedFoods = [...state.foods].sort((a, b) => new Date(b.date) - new Date(a.date));

            sortedFoods.forEach(f => {
                const row = document.createElement('tr');
                row.className = 'data-row';
                row.innerHTML = `
                    <td class="px-4 py-4 whitespace-nowrap text-sm text-gray-300">${f.date}</td>
                    <td class="px-4 py-4 whitespace-nowrap text-sm font-medium text-white">${f.item}</td>
                    <td class="px-4 py-4 whitespace-nowrap text-sm text-gray-300">${f.weight.toFixed(0)}g</td>
                    <td class="px-4 py-4 whitespace-nowrap text-sm font-bold text-green-400">${f.calories.toFixed(0)}</td>
                    <td class="px-4 py-4 whitespace-nowrap text-sm text-purple-400">${f.protein.toFixed(1)}g</td>
                    <td class="px-4 py-4 whitespace-nowrap text-sm text-gray-300">${f.carbs.toFixed(1)}g</td>
                    <td class="px-4 py-4 whitespace-nowrap text-sm text-gray-300">${f.fat.toFixed(1)}g</td>
                    <td class="px-4 py-4 whitespace-nowrap text-center text-sm font-medium space-x-2">
                        <button onclick="editFood('${f.id}')" class="text-blue-400 hover:text-blue-300 transition">Edit</button>
                        <button onclick="deleteFood('${f.id}')" class="text-red-400 hover:text-red-300 transition">Delete</button>
                    </td>
                `;
                foodList.appendChild(row);
            });
        }

        function editFood(id) {
            const food = state.foods.find(f => f.id === id);
            if (food) {
                fIdInput.value = food.id;
                fDateInput.value = food.date;
                fItemInput.value = food.item;
                fWeightInput.value = food.weight;
                fCaloriesInput.value = food.calories;
                fProteinInput.value = food.protein;
                fFatInput.value = food.fat;
                fCarbsInput.value = food.carbs;
                fSubmitBtn.textContent = 'Update Entry';
                window.scrollTo(0, 0); // Scroll to top
            }
        }

        async function deleteFood(id) {
            const confirmed = await customModal('Are you sure you want to delete this food entry?', 'Confirm Delete', true);
            if (confirmed) {
                state.foods = state.foods.filter(f => f.id !== id);
                saveState();
                updateStats();
                renderFoods();
            }
        }

        // ** API Food Lookup function removed **

        // --- TDEE/Profile Logic ---

        function saveProfile(e) {
            e.preventDefault();

            const gender = pGenderInput.value;
            const age = parseInt(pAgeInput.value);
            const height = parseInt(pHeightInput.value);
            const weight = parseInt(pWeightInput.value);
            const activity = parseFloat(pActivityInput.value);

            if (!gender || !age || !height || !weight || !activity) {
                customModal('Please fill in all profile fields.', 'Input Error');
                return;
            }

            // Calculate BMR using Mifflin-St Jeor formula
            let bmr;
            if (gender === 'male') {
                bmr = (10 * weight) + (6.25 * height) - (5 * age) + 5;
            } else {
                bmr = (10 * weight) + (6.25 * height) - (5 * age) - 161;
            }

            const tdee = bmr * activity;

            // Save to state
            state.profile = { gender, age, height, weight, activity, bmr, tdee };

            saveState();
            renderProfile(); // Update the display
            updateStats(); // Update the main dashboard stats
            customModal('Profile and TDEE calculation saved!', 'Profile Updated');
        }

        function renderProfile() {
            const { gender, age, height, weight, activity, bmr, tdee } = state.profile;

            if (gender) pGenderInput.value = gender;
            if (age) pAgeInput.value = age;
            if (height) pHeightInput.value = height;
            if (weight) pWeightInput.value = weight;
            if (activity) pActivityInput.value = activity;

            if (bmr && tdee) {
                displayBMR.textContent = bmr.toFixed(0);
                displayTDEE.textContent = tdee.toFixed(0);

                // Show guidance
                guidanceMaintain.textContent = tdee.toFixed(0);
                guidanceLoss.textContent = (tdee - 500).toFixed(0); // 500 kcal deficit
                guidanceGain.textContent = (tdee + 300).toFixed(0); // 300 kcal surplus
                tdeeGuidance.classList.remove('hidden');
            } else {
                displayBMR.textContent = 'N/A';
                displayTDEE.textContent = 'N/A';
                tdeeGuidance.classList.add('hidden');
            }
        }


        // --- Initialization ---
        document.addEventListener('DOMContentLoaded', () => {
            // Load saved state
            loadState();

            // Attach event listeners
            workoutForm.addEventListener('submit', logWorkout);
            foodForm.addEventListener('submit', logFood);
            profileForm.addEventListener('submit', saveProfile);

            // Set initial view
            switchView(currentView);
        });
    </script>

</body>
</html>
