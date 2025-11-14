<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fitness & Nutrition Tracker</title>
    <!-- Load Tailwind CSS for modern styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
        body {
            font-family: 'Inter', sans-serif;
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
</head>
<body class="min-h-screen p-4 sm:p-8 bg-gray-900 text-gray-200">

    <div class="max-w-4xl mx-auto">
        <!-- Header -->
        <header class="text-center mb-6 p-6 bg-gray-800 rounded-xl card">
            <h1 class="text-4xl font-extrabold text-green-400 mb-2">📊 Health Dashboard</h1>
            <p class="text-gray-400">Track your activities and nutrition in one place.</p>
        </header>

        <!-- Stats Summary -->
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-8">
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
        </div>

        <!-- Tab Navigation -->
        <div class="flex border-b border-gray-700 mb-6">
            <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition active" onclick="switchView('workout')">
                Workouts
            </button>
            <button class="tab px-4 py-3 text-lg font-semibold text-gray-400 hover:text-green-400 transition" onclick="switchView('nutrition')">
                Nutrition
            </button>
        </div>

        <!-- WORKOUT VIEW -->
        <div id="workout-view">
            <!-- Workout Input Form -->
            <div class="bg-gray-800 p-6 rounded-xl card mb-8">
                <h2 class="text-2xl font-semibold text-gray-200 mb-4 border-b border-gray-700 pb-2">Log New Workout</h2>
                <form id="workout-form" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                    <input type="hidden" id="workout-id">

                    <div>
                        <label for="w-date" class="block text-sm font-medium text-gray-400 mb-1">Date</label>
                        <input type="date" id="w-date" required class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                    </div>

                    <div>
                        <label for="w-activity" class="block text-sm font-medium text-gray-400 mb-1">Activity</label>
                        <input type="text" id="w-activity" placeholder="e.g., Running, Weightlifting" required
                            class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                    </div>

                    <div>
                        <label for="w-duration" class="block text-sm font-medium text-gray-400 mb-1">Duration (min)</label>
                        <input type="number" id="w-duration" placeholder="Minutes" required min="1"
                            class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                    </div>

                    <div>
                        <label for="w-calories" class="block text-sm font-medium text-gray-400 mb-1">Calories Burned</label>
                        <input type="number" id="w-calories" placeholder="kcal" required min="1"
                            class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                    </div>

                    <div class="sm:col-span-2 lg:col-span-4 mt-2">
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
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Duration (min)</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Calories (kcal)</th>
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
                        <label for="f-protein" class="block text-sm font-medium text-gray-400 mb-1">Protein (g)</label>
                        <input type="number" id="f-protein" placeholder="grams" required min="0"
                            class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                    </div>
                    
                    <div class="lg:col-span-2">
                        <label for="f-fat" class="block text-sm font-medium text-gray-400 mb-1">Fat (g)</label>
                        <input type="number" id="f-fat" placeholder="grams" required min="0"
                            class="w-full px-3 py-2 border border-gray-600 bg-gray-700 text-white rounded-lg focus:ring-green-500 focus:border-green-500">
                    </div>
                    
                    <div class="lg:col-span-2 mt-2">
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
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Calories (kcal)</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Protein (g)</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">Fat (g)</th>
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

    <script>
        const STORAGE_KEY = 'healthTrackerState';
        let state = {
            workouts: [],
            foods: []
        };
        let currentView = 'workout'; // Track the current active view

        // --- DOM Elements ---
        const workoutForm = document.getElementById('workout-form');
        const foodForm = document.getElementById('food-form');

        // Workout DOM elements
        const workoutList = document.getElementById('workout-list');
        const wNoDataMessage = document.getElementById('w-no-data-message');
        const wSubmitBtn = document.getElementById('w-submit-btn');
        const wIdInput = document.getElementById('workout-id');
        const wDateInput = document.getElementById('w-date');
        const wActivityInput = document.getElementById('w-activity');
        const wDurationInput = document.getElementById('w-duration');
        const wCaloriesInput = document.getElementById('w-calories');

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

        // Stat DOM elements
        const statTotalWorkouts = document.getElementById('stat-total-workouts');
        const statTotalBurned = document.getElementById('stat-total-burned');
        const statTotalConsumed = document.getElementById('stat-total-consumed');
        const statTotalProtein = document.getElementById('stat-total-protein');

        // View DOM elements
        const workoutView = document.getElementById('workout-view');
        const nutritionView = document.getElementById('nutrition-view');
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
                }
            } catch (e) {
                console.error("Error loading from localStorage:", e);
                state = { workouts: [], foods: [] };
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

        // --- CORE LOGIC ---

        /** Switches the active view (Workout or Nutrition). */
        function switchView(viewName) {
            currentView = viewName;

            // Update tab styles
            tabs.forEach(tab => {
                if (tab.textContent.toLowerCase().includes(viewName)) {
                    tab.classList.add('active');
                } else {
                    tab.classList.remove('active');
                }
            });

            // Update view visibility
            if (viewName === 'workout') {
                workoutView.classList.remove('hidden');
                nutritionView.classList.add('hidden');
                resetWorkoutForm();
            } else {
                workoutView.classList.add('hidden');
                nutritionView.classList.remove('hidden');
                resetFoodForm();
            }
        }
        // Expose to window for inline onclick handler
        window.switchView = switchView;


        /** Calculates and displays summary stats. */
        function renderStats() {
            const totalWorkouts = state.workouts.length;
            const totalBurnedCalories = state.workouts.reduce((sum, w) => sum + parseInt(w.calories, 10), 0);
            const totalConsumedCalories = state.foods.reduce((sum, f) => sum + parseInt(f.calories, 10), 0);
            const totalProtein = state.foods.reduce((sum, f) => sum + parseInt(f.protein, 10), 0);

            statTotalWorkouts.textContent = totalWorkouts.toLocaleString();
            statTotalBurned.textContent = totalBurnedCalories.toLocaleString();
            statTotalConsumed.textContent = totalConsumedCalories.toLocaleString();
            statTotalProtein.textContent = totalProtein.toLocaleString();
        }

        // --- WORKOUT CRUD ---

        /** Adds or updates a workout. */
        function addOrUpdateWorkout(data) {
            if (data.id) {
                const index = state.workouts.findIndex(w => w.id === data.id);
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
            const workout = state.workouts.find(w => w.id === id);
            if (workout) {
                wIdInput.value = workout.id;
                wDateInput.value = workout.date;
                wActivityInput.value = workout.activity;
                wDurationInput.value = workout.duration;
                wCaloriesInput.value = workout.calories;
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
                    const row = document.createElement('tr');
                    row.className = 'data-row';
                    row.innerHTML = `
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-200">${workout.date}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${workout.activity}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${workout.duration}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${workout.calories}</td>
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
            const food = state.foods.find(f => f.id === id);
            if (food) {
                fIdInput.value = food.id;
                fDateInput.value = food.date;
                fItemInput.value = food.item;
                fCaloriesInput.value = food.calories;
                fProteinInput.value = food.protein;
                fFatInput.value = food.fat;
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
                    const row = document.createElement('tr');
                    row.className = 'data-row';
                    row.innerHTML = `
                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-200">${food.date}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.item}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.calories}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.protein}</td>
                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-300">${food.fat}</td>
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
            const data = {
                id: wIdInput.value,
                date: wDateInput.value,
                activity: wActivityInput.value.trim(),
                duration: parseInt(wDurationInput.value, 10),
                calories: parseInt(wCaloriesInput.value, 10),
            };
            if (data.duration <= 0 || data.calories <= 0) {
                 showCustomModal('Input Error', 'Duration and calories burned must be positive numbers.');
                 return;
            }
            addOrUpdateWorkout(data);
        });

        foodForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const data = {
                id: fIdInput.value,
                date: fDateInput.value,
                item: fItemInput.value.trim(),
                calories: parseInt(fCaloriesInput.value, 10),
                protein: parseInt(fProteinInput.value, 10),
                fat: parseInt(fFatInput.value, 10),
            };
            if (data.calories <= 0 || data.protein < 0 || data.fat < 0) {
                 showCustomModal('Input Error', 'Calories must be positive. Protein and Fat must be zero or positive.');
                 return;
            }
            addOrUpdateFood(data);
        });

        // --- INITIALIZATION ---
        window.onload = () => {
            loadData();
            renderWorkouts(); // Renders stats as well
            renderFoods();
            // Set default date for both forms
            const today = new Date().toISOString().substring(0, 10);
            wDateInput.value = today;
            fDateInput.value = today;
            switchView('workout'); // Start on the workout tab
        };
    </script>
</body>
</html>
