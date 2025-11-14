<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
    <title>AI Advanced Fitness & Nutrition Tracker</title>
    
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
                <h1 class="text-3xl font-extrabold text-green-400 mb-2">Fitness & Nutrition Tracker (AI Macros)</h1>
                <p class="text-gray-400 text-sm">Monitor TDEE, track macros, and calculate your **Caloric Balance** for targeted results.</p>
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
                    <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Log New Food Entry (AI Estimated)</h2>
                    <p class="text-sm text-yellow-500 mb-2">
                        💡 **Tip for Accuracy:** For regional dishes like **Dal**, include preparation details (e.g., "Masoor Dal with 1 tsp Ghee") for better AI estimation.
                    </p>
                    <form id="food-form" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                        <input type="hidden" id="food-id">

                        <div>
                            <label for="f-date" class="block text-sm font-medium text-gray-400 mb-1">Date</label>
                            <input type="date" id="f-date" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-item" class="block text-sm font-medium text-gray-400 mb-1">Meal/Item Name</label>
                            <input type="text" id="f-item" placeholder="e.g., Dal with Ghee, Apple, Chicken Curry" required
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>

                        <div>
                            <label for="f-weight" class="block text-sm font-medium text-gray-400 mb-1">Approx. Weight (g)</label>
                            <input type="number" id="f-weight" placeholder="grams" required min="1"
                                class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                        </div>
                        
                        <div class="lg:col-span-1 estimation-container">
                            <label class="block text-sm font-medium text-gray-400 mb-1">Estimated Macros (AI)</label>
                            <div id="f-estimated-macros" class="p-3 bg-gray-700 border border-gray-600 rounded-lg text-sm text-yellow-300 font-mono">
                                <span id="estimation-status" class="text-xs text-gray-400">Enter food details to estimate...</span>
                                <div id="macro-display" class="hidden font-bold">
                                    Kcal: <span id="display-kcal">0</span> | P: <span id="display-protein-g">0</span> | F: <span id="display-fat-g">0</span> | C: <span id="display-carbs-g">0</span>
                                </div>
                            </div>
                            <input type="hidden" id="f-calories">
                            <input type="hidden" id="f-protein">
                            <input type="hidden" id="f-fat">
                            <input type="hidden" id="f-carbs">
                        </div>
                        <div class="sm:col-span-2 lg:col-span-4 mt-2">
                            <button type="submit" id="f-submit-btn"
                                        class="w-full bg-green-600 text-white font-bold py-3 px-4 rounded-xl hover:bg-green-700 transition duration-150 ease-in-out">
                                Get Estimate & Log Entry
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
        const API_MODEL = "gemini-2.5-flash-preview-09-2025";
        const apiKey = ""; // API key is provided by the environment
        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/${API_MODEL}:generateContent?key=${apiKey}`;

        let state = {
            workouts: [],
            foods: [],
            profile: { 
                age: null, gender: null, height: null, weight: null, 
                activity: null, bmr: null, tdee: null
            }
        };
        let currentView = 'nutrition';
        let isFetching = false;


        // --- DOM Element Variables (abbreviated for brevity) ---
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

        const fIdInput = document.getElementById('food-id');
        const fCaloriesInput = document.getElementById('f-calories');
        const fProteinInput = document.getElementById('f-protein');
        const fFatInput = document.getElementById('f-fat');
        const fCarbsInput = document.getElementById('f-carbs');
        const macroDisplay = document.getElementById('macro-display');
        const displayKcal = document.getElementById('display-kcal');
        const displayProtein = document.getElementById('display-protein-g');
        const displayFat = document.getElementById('display-fat-g');
        const displayCarbs = document.getElementById('display-carbs-g');
        const estimationStatus = document.getElementById('estimation-status');

        const workoutList = document.getElementById('workout-list');
        const foodList = document.getElementById('food-list');

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

                modalCancelBtn.addEventListener('click', handleCancel);
                modalConfirmBtn.addEventListener('click', handleConfirm);

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

        // Function for exponential backoff retry logic
        async function fetchWithRetry(url, options, maxRetries = 3) {
            for (let i = 0; i < maxRetries; i++) {
                try {
                    const response = await fetch(url, options);
                    if (!response.ok) {
                        if (response.status === 429 && i < maxRetries - 1) { // 429 Too Many Requests
                            const delay = Math.pow(2, i) * 1000 + Math.random() * 1000;
                            console.warn(`Rate limit hit, retrying in ${delay / 1000}s...`);
                            await new Promise(resolve => setTimeout(resolve, delay));
                            continue;
                        }
                        throw new Error(`HTTP error! status: ${response.status}`);
                    }
                    return response;
                } catch (error) {
                    if (i === maxRetries - 1) throw error;
                    console.error(`Fetch attempt ${i + 1} failed:`, error.message);
                    // Non-rate limit error, but still retry with backoff
                    const delay = Math.pow(2, i) * 1000 + Math.random() * 500;
                    await new Promise(resolve => setTimeout(resolve, delay));
                }
            }
            throw new Error("Failed to fetch content after multiple retries.");
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

        // --- Gemini API Call for Nutrition Estimation (COMPLETED) ---

        const NUTRITION_SCHEMA = {
            type: "OBJECT",
            description: "Estimated nutritional values for the food item in grams (g) and kilocalories (kcal).",
            properties: {
                "estimatedCalories": { "type": "INTEGER", "description": "Total estimated kilocalories (kcal) rounded to the nearest integer." },
                "estimatedProtein": { "type": "INTEGER", "description": "Estimated protein in grams (g) rounded to the nearest integer." },
                "estimatedFat": { "type": "INTEGER", "description": "Estimated fat in grams (g) rounded to the nearest integer." },
                "estimatedCarbs": { "type": "INTEGER", "description": "Estimated carbohydrates in grams (g) rounded to the nearest integer." }
            },
            propertyOrdering: ["estimatedCalories", "estimatedProtein", "estimatedFat", "estimatedCarbs"]
        };

        async function fetchNutritionEstimate(item, weight) {
            isFetching = true;
            estimationStatus.textContent = "🧠 AI Estimating Calories and Macros...";
            macroDisplay.classList.add('hidden');
            fSubmitBtn.disabled = true;
            fSubmitBtn.textContent = 'Estimating... Please Wait';
            
            // UPDATED SYSTEM PROMPT: Enhanced to handle regional/complex dishes
            const systemPrompt = "You are an expert nutritional database specializing in **global and regional cuisine (e.g., Indian, Mexican, East Asian)**. Based on the food item and weight provided, estimate the total calories, protein, fat, and carbohydrates. For dishes like 'dal' or 'curry,' assume common preparations (e.g., cooked with water, oil, or ghee, and standard spices) unless specified otherwise in the item description. Round all values to the nearest integer. Respond ONLY with the requested JSON object.";
            const userQuery = `Estimate the nutrition for: ${item}, weighing ${weight} grams.`;

            try {
                const payload = {
                    contents: [{ parts: [{ text: userQuery }] }],
                    systemInstruction: { parts: [{ text: systemPrompt }] },
                    generationConfig: {
                        responseMimeType: "application/json",
                        responseSchema: NUTRITION_SCHEMA
                    }
                };

                const fetchOptions = {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                };

                const response = await fetchWithRetry(apiUrl, fetchOptions);
                const result = await response.json();

                const jsonString = result.candidates?.[0]?.content?.parts?.[0]?.text;
                if (!jsonString) {
                    throw new Error("API did not return a valid JSON string.");
                }

                const parsedJson = JSON.parse(jsonString);
                
                // Validate and clean up and complete the cut-off logic
                const estimatedData = {
                    calories: parsedJson.estimatedCalories || 0,
                    protein: parsedJson.estimatedProtein || 0,
                    fat: parsedJson.estimatedFat || 0,
                    carbs: parsedJson.estimatedCarbs || 0,
                };

                // Store in hidden fields
                fCaloriesInput.value = estimatedData.calories;
                fProteinInput.value = estimatedData.protein;
                fFatInput.value = estimatedData.fat;
                fCarbsInput.value = estimatedData.carbs;

                // Display to user
                displayKcal.textContent = estimatedData.calories;
                displayProtein.textContent = estimatedData.protein;
                displayFat.textContent = estimatedData.fat;
                displayCarbs.textContent = estimatedData.carbs;

                estimationStatus.textContent = "✅ Estimation Complete!";
                macroDisplay.classList.remove('hidden');

                return estimatedData;

            } catch (error) {
                console.error("Gemini API Error:", error);
                customModal(`AI Estimation failed: ${error.message}. Please check your API key, or try again later with a simpler query.`, 'API Error');

                // Reset inputs and display to reflect failure
                fCaloriesInput.value = 0;
                fProteinInput.value = 0;
                fFatInput.value = 0;
                fCarbsInput.value = 0;
                
                displayKcal.textContent = 0;
                displayProtein.textContent = 0;
                displayFat.textContent = 0;
                displayCarbs.textContent = 0;

                macroDisplay.classList.add('hidden');
                estimationStatus.textContent = "❌ Estimation Failed. Enter food details to estimate...";

                return null;
            } finally {
                isFetching = false;
                fSubmitBtn.disabled = false;
                fSubmitBtn.textContent = 'Get Estimate & Log Entry';
            }
        }

        // --- Core Application Logic (CRUD & Calculations) ---

        function calculateBMR(gender, age, height, weight) {
            // Harris-Benedict Equation
            if (gender === 'male') {
                return (88.362 + (13.397 * weight) + (4.799 * height) - (5.677 * age));
            } else if (gender === 'female') {
                return (447.593 + (9.247 * weight) + (3.098 * height) - (4.330 * age));
            }
            return 0;
        }

        function calculateTDEE(bmr, activityMultiplier) {
            return bmr * parseFloat(activityMultiplier);
        }

        // TDEE/Profile Handling
        function renderProfile() {
            const profile = state.profile;
            pGenderInput.value = profile.gender || '';
            pAgeInput.value = profile.age || '';
            pHeightInput.value = profile.height || '';
            pWeightInput.value = profile.weight || '';
            pActivityInput.value = profile.activity || '';

            if (profile.tdee && profile.bmr) {
                displayBMR.textContent = Math.round(profile.bmr);
                displayTDEE.textContent = Math.round(profile.tdee);
                
                guidanceMaintain.textContent = Math.round(profile.tdee);
                guidanceLoss.textContent = Math.round(profile.tdee - 500);
                guidanceGain.textContent = Math.round(profile.tdee + 250);
                tdeeGuidance.classList.remove('hidden');
            } else {
                displayBMR.textContent = 'N/A';
                displayTDEE.textContent = 'N/A';
                tdeeGuidance.classList.add('hidden');
            }
        }

        profileForm.addEventListener('submit', function(e) {
            e.preventDefault();
            
            const gender = pGenderInput.value;
            const age = parseInt(pAgeInput.value);
            const height = parseInt(pHeightInput.value);
            const weight = parseInt(pWeightInput.value);
            const activity = pActivityInput.value;
            
            if (!gender || isNaN(age) || isNaN(height) || isNaN(weight) || !activity) {
                customModal('Please fill in all TDEE profile fields.', 'Input Error');
                return;
            }
            
            const bmr = calculateBMR(gender, age, height, weight);
            const tdee = calculateTDEE(bmr, activity);
            
            state.profile = {
                gender, age, height, weight, activity,
                bmr: Math.round(bmr), 
                tdee: Math.round(tdee)
            };
            
            saveState();
            renderProfile();
            updateStats();
            customModal('Profile and TDEE calculated and saved successfully!', 'Success');
        });

        // Workout Logic
        function addOrUpdateWorkout(item) {
            if (item.id) {
                const index = state.workouts.findIndex(w => w.id === item.id);
                if (index !== -1) {
                    state.workouts[index] = item;
                }
            } else {
                item.id = Date.now().toString(); // Simple unique ID
                state.workouts.push(item);
            }
            state.workouts.sort((a, b) => new Date(b.date) - new Date(a.date));
            saveState();
            renderWorkouts();
            updateStats();
        }

        function renderWorkouts() {
            const list = workoutList;
            list.innerHTML = '';
            const data = state.workouts;
            document.getElementById('w-no-data-message').classList.toggle('hidden', data.length > 0);

            data.forEach(item => {
                const row = document.createElement('tr');
                row.classList.add('data-row', 'text-sm');
                row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap">${item.date}</td>
                    <td class="px-6 py-4 whitespace-nowrap">${item.activity}</td>
                    <td class="px-6 py-4 whitespace-nowrap">${item.sets}</td>
                    <td class="px-6 py-4 whitespace-nowrap">${item.reps}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-red-400 font-semibold">${item.calories}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-center">
                        <button onclick="editWorkout('${item.id}')" class="text-indigo-400 hover:text-indigo-600 mr-3">Edit</button>
                        <button onclick="deleteItem('${item.id}', 'workout')" class="text-red-400 hover:text-red-600">Delete</button>
                    </td>
                `;
                list.appendChild(row);
            });
        }

        workoutForm.addEventListener('submit', function(e) {
            e.preventDefault();
            const id = wIdInput.value;
            const sets = parseInt(wSetsInput.value);
            const reps = parseInt(wRepsInput.value);
            const totalReps = sets * reps;
            const caloriesBurned = Math.round(totalReps * 1.5); // Formula: Total Reps * 1.5 kcal

            const newWorkout = {
                id: id || null,
                date: wDateInput.value,
                activity: wActivityInput.value,
                sets: sets,
                reps: reps,
                calories: caloriesBurned
            };

            addOrUpdateWorkout(newWorkout);
            workoutForm.reset();
            wIdInput.value = '';
            wSubmitBtn.textContent = 'Add Workout';
            customModal(id ? 'Workout updated successfully!' : 'Workout logged successfully!', 'Success');
        });

        window.editWorkout = function(id) {
            const workout = state.workouts.find(w => w.id === id);
            if (!workout) return;

            switchView('workout');

            wIdInput.value = workout.id;
            wDateInput.value = workout.date;
            wActivityInput.value = workout.activity;
            wSetsInput.value = workout.sets;
            wRepsInput.value = workout.reps;
            wSubmitBtn.textContent = 'Update Workout';
        };

        // Food Logic
        function addOrUpdateFood(item) {
            if (item.id) {
                const index = state.foods.findIndex(f => f.id === item.id);
                if (index !== -1) {
                    state.foods[index] = item;
                }
            } else {
                item.id = Date.now().toString(); // Simple unique ID
                state.foods.push(item);
            }
            state.foods.sort((a, b) => new Date(b.date) - new Date(a.date));
            saveState();
            renderFoods();
            updateStats();
        }

        function renderFoods() {
            const list = foodList;
            list.innerHTML = '';
            const data = state.foods;
            document.getElementById('f-no-data-message').classList.toggle('hidden', data.length > 0);

            data.forEach(item => {
                const row = document.createElement('tr');
                row.classList.add('data-row', 'text-sm');
                row.innerHTML = `
                    <td class="px-4 py-3 whitespace-nowrap">${item.date}</td>
                    <td class="px-4 py-3 whitespace-nowrap">${item.item}</td>
                    <td class="px-4 py-3 whitespace-nowrap">${item.weight}</td>
                    <td class="px-4 py-3 whitespace-nowrap text-green-400 font-semibold">${item.calories}</td>
                    <td class="px-4 py-3 whitespace-nowrap">${item.protein}</td>
                    <td class="px-4 py-3 whitespace-nowrap">${item.carbs}</td>
                    <td class="px-4 py-3 whitespace-nowrap">${item.fat}</td>
                    <td class="px-4 py-3 whitespace-nowrap text-center">
                        <button onclick="editFood('${item.id}')" class="text-indigo-400 hover:text-indigo-600 mr-3">Edit</button>
                        <button onclick="deleteItem('${item.id}', 'food')" class="text-red-400 hover:text-red-600">Delete</button>
                    </td>
                `;
                list.appendChild(row);
            });
        }

        window.editFood = function(id) {
            const food = state.foods.find(f => f.id === id);
            if (!food) return;

            switchView('nutrition');

            fIdInput.value = food.id;
            fDateInput.value = food.date;
            fItemInput.value = food.item;
            fWeightInput.value = food.weight;
            
            fCaloriesInput.value = food.calories;
            fProteinInput.value = food.protein;
            fFatInput.value = food.fat;
            fCarbsInput.value = food.carbs;
            
            displayKcal.textContent = food.calories;
            displayProtein.textContent = food.protein;
            displayFat.textContent = food.fat;
            displayCarbs.textContent = food.carbs;
            
            estimationStatus.textContent = "✅ Macros Loaded from Log";
            macroDisplay.classList.remove('hidden');

            fSubmitBtn.textContent = 'Update Entry';
        };

        // Form Submission Logic
        foodForm.addEventListener('submit', async function(e) {
            e.preventDefault();

            const item = fItemInput.value.trim();
            const weight = parseInt(fWeightInput.value);
            const id = fIdInput.value;

            // Check if we need to fetch an estimate (for new entries or if macros are missing)
            if (!id || (fCaloriesInput.value == 0 && fProteinInput.value == 0)) {
                
                // Fetch the estimate
                const estimatedData = await fetchNutritionEstimate(item, weight);

                // If estimation failed, do not proceed with logging
                if (!estimatedData) {
                    return;
                }
            } 
            
            // Now use the values stored in hidden fields (either freshly estimated or pre-loaded for edit)
            const newFood = {
                id: id || null,
                date: fDateInput.value,
                item: item,
                weight: weight,
                calories: parseInt(fCaloriesInput.value) || 0,
                protein: parseInt(fProteinInput.value) || 0,
                fat: parseInt(fFatInput.value) || 0,
                carbs: parseInt(fCarbsInput.value) || 0
            };

            // Double check that we have macro data
            if (newFood.calories === 0 && newFood.protein === 0 && newFood.carbs === 0 && newFood.fat === 0) {
                 customModal("Cannot log entry: All estimated macro values are zero. Please try again with a more specific food name.", 'Log Error');
                 return;
            }

            addOrUpdateFood(newFood);
            foodForm.reset();
            fIdInput.value = '';
            
            // Reset estimation display
            fCaloriesInput.value = 0; fProteinInput.value = 0; fFatInput.value = 0; fCarbsInput.value = 0;
            estimationStatus.textContent = "Enter food details to estimate...";
            macroDisplay.classList.add('hidden');

            fSubmitBtn.textContent = 'Get Estimate & Log Entry';

            customModal(id ? 'Food entry updated successfully!' : 'Food entry logged successfully!', 'Success');
        });

        // Delete Logic (COMPLETED)
        window.deleteItem = async function(id, type) {
            const isConfirmed = await customModal(`Are you sure you want to delete this ${type} entry? This action cannot be undone.`, `Confirm Deletion (${type})`, true);
            
            if (isConfirmed) {
                if (type === 'workout') {
                    state.workouts = state.workouts.filter(w => w.id !== id);
                    renderWorkouts();
                } else if (type === 'food') {
                    state.foods = state.foods.filter(f => f.id !== id);
                    renderFoods();
                }
                saveState();
                updateStats();
            }
        };


        // Stats Calculation
        function updateStats() {
            const totalWorkouts = state.workouts.length;
            const totalBurned = state.workouts.reduce((sum, w) => sum + w.calories, 0);
            const totalConsumed = state.foods.reduce((sum, f) => sum + f.calories, 0);
            const totalProtein = state.foods.reduce((sum, f) => sum + f.protein, 0);
            const tdee = state.profile.tdee || 0;
            
            // Calculate Caloric Balance
            let caloricBalance = 'N/A';
            let balanceCardColor = 'bg-gray-600';

            if (tdee > 0) {
                caloricBalance = Math.round(tdee - totalConsumed + totalBurned);
                
                if (caloricBalance < -500) {
                    balanceCardColor = 'bg-red-800'; // Large deficit
                } else if (caloricBalance < 0) {
                    balanceCardColor = 'bg-red-600'; // Small deficit
                } else if (caloricBalance < 250) {
                    balanceCardColor = 'bg-green-600'; // Near maintenance
                } else {
                    balanceCardColor = 'bg-blue-600'; // Surplus/Gain
                }
            }

            document.getElementById('stat-total-workouts').textContent = totalWorkouts;
            document.getElementById('stat-total-burned').textContent = totalBurned;
            document.getElementById('stat-total-consumed').textContent = totalConsumed;
            document.getElementById('stat-total-protein').textContent = totalProtein;
            document.getElementById('stat-tdee').textContent = tdee > 0 ? tdee : 'N/A';
            
            const balanceCard = document.getElementById('stat-balance-card');
            balanceCard.textContent = caloricBalance;
            
            // Update Caloric Balance Card
            balanceCard.className = `text-white p-4 rounded-xl card text-center ${balanceCardColor}`;
            document.getElementById('stat-balance-card').innerHTML = `
                <p class="text-xs font-semibold uppercase opacity-80">Caloric Balance (kcal)</p>
                <p id="stat-balance" class="text-2xl font-bold mt-1">${caloricBalance}</p>
            `;
        }

        // View/Tab Switching
        window.switchView = function(viewId) {
            currentView = viewId;
            document.getElementById('workout-view').classList.add('hidden');
            document.getElementById('nutrition-view').classList.add('hidden');
            document.getElementById('tdee-view').classList.add('hidden');
            
            document.getElementById(`${viewId}-view`).classList.remove('hidden');

            document.querySelectorAll('.tab').forEach(tab => tab.classList.remove('active'));
            document.querySelector(`.tab[onclick="switchView('${viewId}')"]`).classList.add('active');
        }

        // Initialize App
        function init() {
            // Set today's date on date inputs
            const today = new Date().toISOString().split('T')[0];
            wDateInput.value = today;
            fDateInput.value = today;

            loadState();
            switchView(currentView); // Initial view load
        }

        init();
    </script>
</body>
</html>
