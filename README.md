<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Complete Macro & TDEE Tracker</title>

    <!-- Tailwind CDN -->
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Fonts + Complete Reset -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        html, body {
            width: 100%;
            height: 100%;
            background: #f1f5f9; 
            overflow-x: hidden;
            font-family: 'Inter', sans-serif;
        }
    </style>
</head>

<body>

    <!-- HEADER -->
    <header class="w-full bg-blue-600 text-white py-5 shadow">
        <h1 class="text-center text-3xl font-bold">Macro & TDEE Calculator</h1>
    </header>

    <!-- MAIN CONTENT -->
    <main class="w-full flex justify-center mt-6 px-4">
        <div class="w-full max-w-3xl bg-white rounded-xl shadow p-6">

            <h2 class="text-xl font-semibold mb-4 text-gray-800 text-center">
                Enter Your Information
            </h2>

            <!-- INPUT GRID -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">

                <div>
                    <label class="block mb-1 font-medium">Age</label>
                    <input type="number" id="age" class="w-full p-2 border rounded-lg" placeholder="Years">
                </div>

                <div>
                    <label class="block mb-1 font-medium">Weight (kg)</label>
                    <input type="number" id="weight" class="w-full p-2 border rounded-lg" placeholder="Weight in kg">
                </div>

                <div>
                    <label class="block mb-1 font-medium">Height (cm)</label>
                    <input type="number" id="height" class="w-full p-2 border rounded-lg" placeholder="Height in cm">
                </div>

                <div>
                    <label class="block mb-1 font-medium">Activity Level</label>
                    <select id="activity" class="w-full p-2 border rounded-lg">
                        <option value="1.2">Sedentary (No exercise)</option>
                        <option value="1.375">Light (1–3 days/week)</option>
                        <option value="1.55">Moderate (3–5 days/week)</option>
                        <option value="1.725">Active (6–7 days/week)</option>
                    </select>
                </div>

            </div>

            <!-- CALCULATE BUTTON -->
            <button 
                onclick="calculate()"
                class="mt-6 w-full bg-blue-600 hover:bg-blue-700 text-white p-3 rounded-lg font-semibold transition">
                Calculate TDEE
            </button>

            <!-- RESULT BOX -->
            <div id="result" class="mt-6 hidden bg-gray-100 p-4 rounded-lg border">
                <h3 class="text-lg font-bold mb-2">Your Results</h3>
                <p class="text-gray-800"><strong>TDEE:</strong> <span id="tdee"></span> kcal/day</p>
                <p class="text-gray-800 mt-2"><strong>Recommended Macros:</strong></p>
                <ul class="list-disc ml-6 text-gray-700">
                    <li>Protein: <span id="protein"></span> g</li>
                    <li>Carbs: <span id="carbs"></span> g</li>
                    <li>Fats: <span id="fats"></span> g</li>
                </ul>
            </div>

        </div>
    </main>

    <!-- JS -->
    <script>
        function calculate() {
            const age = parseInt(document.getElementById("age").value);
            const weight = parseFloat(document.getElementById("weight").value);
            const height = parseFloat(document.getElementById("height").value);
            const activity = parseFloat(document.getElementById("activity").value);

            if (!age || !weight || !height) {
                alert("Please fill all fields.");
                return;
            }

            // Mifflin-St Jeor Formula (Male)
            let bmr = 10 * weight + 6.25 * height - 5 * age + 5;
            let tdee = Math.round(bmr * activity);

            // Macro Breakdown
            let protein = Math.round(weight * 1.8);      // grams
            let fats = Math.round((0.25 * tdee) / 9);    // grams
            let carbs = Math.round((tdee - (protein * 4) - (fats * 9)) / 4);

            // Update UI
            document.getElementById("tdee").textContent = tdee;
            document.getElementById("protein").textContent = protein;
            document.getElementById("carbs").textContent = carbs;
            document.getElementById("fats").textContent = fats;

            document.getElementById("result").classList.remove("hidden");
        }
    </script>

</body>
</html>
