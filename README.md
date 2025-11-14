<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Firebase Config Fix</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'primary-blue': '#4c51bf',
                        'secondary-gray': '#f3f4f6',
                    },
                }
            }
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
        }
        .card {
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
        }
    </style>
</head>
<body class="p-4 sm:p-8 min-h-screen">

    <div class="max-w-xl mx-auto">
        <h1 class="text-3xl font-bold text-primary-blue mb-2">Secure Firebase Initialization & Notes</h1>
        <p class="text-sm text-gray-600 mb-6">Demonstrates the correct way to load config, authenticate, and perform CRUD operations (Save/Delete).</p>

        <!-- Status and Config Info -->
        <div id="status-card" class="card bg-white p-4 rounded-lg mb-6">
            <h2 class="text-lg font-semibold mb-2">Status</h2>
            <div class="text-sm space-y-1">
                <p>App ID: <span id="app-id" class="font-mono text-xs bg-secondary-gray p-1 rounded">Loading...</span></p>
                <!-- Showing full user ID is mandatory for multi-user apps -->
                <p>User ID: <span id="user-id" class="font-mono text-xs bg-secondary-gray p-1 rounded break-all">Loading...</span></p>
                <p>Auth Status: <span id="auth-status" class="font-semibold text-orange-500">Initializing...</span></p>
            </div>
        </div>
        
        <!-- Note Creation Form -->
        <div class="card bg-white p-6 rounded-lg mb-6">
            <h2 class="text-xl font-semibold text-primary-blue mb-4">Add a New Note</h2>
            <textarea id="note-input" class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-primary-blue mb-4" rows="3" placeholder="Write your note here..."></textarea>
            <button id="save-button" class="w-full bg-primary-blue text-white font-semibold py-2 rounded-lg hover:bg-opacity-90 transition duration-150 disabled:opacity-50" disabled>
                Save Note
            </button>
        </div>

        <!-- Notes List -->
        <div class="card bg-white p-6 rounded-lg">
            <h2 class="text-xl font-semibold text-primary-blue mb-4">Your Notes (Realtime)</h2>
            <div id="notes-list" class="space-y-3">
                <p class="text-gray-500" id="loading-notes">Loading notes...</p>
            </div>
        </div>

    </div>

    <script type="module">
        // Import necessary Firebase modules, including doc and deleteDoc for removal
        import { 
            initializeApp 
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { 
            getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged 
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { 
            getFirestore, collection, addDoc, onSnapshot, query, serverTimestamp, 
            setLogLevel, doc, deleteDoc // Added for delete functionality
        } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables for Firebase services and user ID
        let app;
        let db;
        let auth;
        let userId = null;
        
        const noteInput = document.getElementById('note-input');
        const saveButton = document.getElementById('save-button');
        const notesList = document.getElementById('notes-list');
        const statusMessage = document.getElementById('auth-status');
        const appIdDisplay = document.getElementById('app-id');
        const userIdDisplay = document.getElementById('user-id');
        const loadingNotes = document.getElementById('loading-notes');

        // --- MANDATORY FIREBASE INITIALIZATION AND AUTH SETUP ---
        
        const initializeFirebase = async () => {
            // 1. Safely retrieve the global variables (THE FIX)
            const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
            const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
            
            let firebaseConfig = null;
            try {
                // IMPORTANT: Parse the config string. This is protected by the try/catch.
                firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
            } catch (e) {
                statusMessage.textContent = 'ERROR: Failed to parse Firebase config JSON.';
                console.error("Firebase config parsing error:", e);
                return;
            }

            appIdDisplay.textContent = appId;

            if (!firebaseConfig) {
                statusMessage.textContent = 'ERROR: Firebase configuration object is missing.';
                console.error("Critical Error: Firebase config is null or undefined.");
                return;
            }

            // 2. Initialize Firebase services
            try {
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);
                setLogLevel('debug'); // Enable debug logs for troubleshooting
                statusMessage.textContent = 'Config loaded. Authenticating...';

                // 3. Set up Auth State Listener (Crucial for waiting for sign-in)
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        // Successfully authenticated or state changed to signed in
                        userId = user.uid;
                        userIdDisplay.textContent = userId;
                        statusMessage.textContent = 'Authenticated successfully!';
                        saveButton.disabled = false;
                        
                        // Start real-time data listener only after successful authentication
                        setupRealtimeListener(userId, appId);
                    } else {
                        // User is signed out or initial state check is running.
                        userId = null;
                        userIdDisplay.textContent = 'N/A';
                        saveButton.disabled = true;
                        
                        // Attempt to sign in if not already done
                        if (initialAuthToken) {
                            try {
                                await signInWithCustomToken(auth, initialAuthToken);
                                statusMessage.textContent = 'Signing in with token...';
                            } catch (error) {
                                console.error("Error signing in with custom token, falling back to anonymous:", error);
                                statusMessage.textContent = 'Token failed. Signing in anonymously...';
                                await signInAnonymously(auth);
                            }
                        } else {
                            // Fallback to anonymous sign-in if no token is available
                            await signInAnonymously(auth);
                        }
                    }
                });

            } catch (error) {
                statusMessage.textContent = 'ERROR: Failed to initialize Firebase app.';
                console.error("Firebase Initialization Error:", error);
            }
        };

        // --- FIRESTORE LOGIC ---
        
        // Helper function to get the private user notes collection path
        const getNotesCollectionRef = (currentUserId, currentAppId) => {
            // Path: /artifacts/{appId}/users/{userId}/notes
            return collection(db, `artifacts/${currentAppId}/users/${currentUserId}/notes`);
        };

        const saveNote = async () => {
            const noteText = noteInput.value.trim();
            if (!noteText || !userId || !db) return;

            saveButton.disabled = true;
            saveButton.textContent = 'Saving...';
            
            try {
                const notesCollection = getNotesCollectionRef(userId, appIdDisplay.textContent);
                await addDoc(notesCollection, {
                    content: noteText,
                    timestamp: serverTimestamp(),
                });
                noteInput.value = ''; // Clear input on success
            } catch (error) {
                console.error("Error writing document: ", error);
                statusMessage.textContent = `Error saving: ${error.message.substring(0, 50)}...`;
            } finally {
                saveButton.textContent = 'Save Note';
                saveButton.disabled = false;
            }
        };

        const deleteNote = async (noteId, currentUserId, currentAppId) => {
            if (!db) return;

            try {
                // Get the document reference using doc()
                const docRef = doc(db, `artifacts/${currentAppId}/users/${currentUserId}/notes`, noteId);
                // Delete the document
                await deleteDoc(docRef);
                // The onSnapshot listener will automatically update the UI
            } catch (error) {
                console.error("Error removing document: ", noteId, error);
                statusMessage.textContent = `Error deleting: ${error.message.substring(0, 50)}...`;
            }
        };

        const setupRealtimeListener = (currentUserId, currentAppId) => {
            // Remove the initial loading placeholder message if it exists
            const loadingMsg = document.getElementById('loading-notes');
            if (loadingMsg) loadingMsg.remove(); 
            
            const notesCollection = getNotesCollectionRef(currentUserId, currentAppId);
            const q = query(notesCollection); 

            // Set up real-time listener (onSnapshot)
            onSnapshot(q, (snapshot) => {
                const notes = [];
                snapshot.forEach((doc) => {
                    // Extract data and add the document ID (crucial for deletion)
                    const data = doc.data();
                    notes.push({ id: doc.id, ...data });
                });

                // Sort the notes by timestamp (newest first) in memory
                notes.sort((a, b) => {
                    const timeA = a.timestamp?.toMillis() || 0;
                    const timeB = b.timestamp?.toMillis() || 0;
                    return timeB - timeA;
                });
                
                renderNotes(notes);
            }, (error) => {
                console.error("Error listening to notes:", error);
                statusMessage.textContent = `Error listening to data: ${error.message.substring(0, 50)}...`;
            });
        };

        const renderNotes = (notes) => {
            notesList.innerHTML = '';
            if (notes.length === 0) {
                notesList.innerHTML = '<p class="text-gray-500">You have no notes yet. Start writing above!</p>';
                return;
            }

            notes.forEach(note => {
                const noteElement = document.createElement('div');
                const date = note.timestamp ? note.timestamp.toDate().toLocaleString() : 'Saving...';
                
                noteElement.className = 'p-3 bg-secondary-gray rounded-lg border border-gray-300 flex justify-between items-start';
                noteElement.innerHTML = `
                    <div class="flex-1 min-w-0 pr-4">
                        <p class="text-gray-800 break-words">${note.content}</p>
                        <p class="text-xs text-gray-500 mt-1">Saved: ${date}</p>
                    </div>
                    <!-- Delete Button using inline SVG -->
                    <button data-id="${note.id}" class="delete-btn flex-shrink-0 text-red-500 hover:text-red-700 ml-4 p-1 rounded-full hover:bg-red-100 transition duration-150" title="Delete Note">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                            <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm4 0a1 1 0 10-2 0v6a1 1 0 102 0V8z" clip-rule="evenodd" />
                        </svg>
                    </button>
                `;
                notesList.appendChild(noteElement);
            });

            // Add event listeners for the new delete buttons
            document.querySelectorAll('.delete-btn').forEach(button => {
                button.addEventListener('click', (e) => {
                    const noteId = e.currentTarget.getAttribute('data-id');
                    if (noteId && userId && appIdDisplay.textContent) {
                        deleteNote(noteId, userId, appIdDisplay.textContent);
                    }
                });
            });
        };

        // --- EVENT LISTENERS AND START ---
        saveButton.addEventListener('click', saveNote);
        
        // Start the entire initialization process on window load
        window.onload = initializeFirebase;

    </script>
</body>
</html>
