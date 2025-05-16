<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SafeHer - Women's Safety App</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
        .pulse-animation {
            animation: pulse 2s infinite;
        }
        .emergency-btn {
            box-shadow: 0 0 20px rgba(255, 0, 0, 0.5);
        }
        .slide-in {
            animation: slideIn 0.3s ease-out forwards;
        }
        .slide-out {
            animation: slideOut 0.3s ease-out forwards;
        }
        @keyframes slideIn {
            from { transform: translateY(100%); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        @keyframes slideOut {
            from { transform: translateY(0); opacity: 1; }
            to { transform: translateY(100%); opacity: 0; }
        }
        .map-container {
            height: 300px;
            background-color: #e5e7eb;
            border-radius: 1rem;
            position: relative;
            overflow: hidden;
        }
        .map-placeholder {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            color: #6b7280;
        }
        .auth-screen {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: white;
            z-index: 100;
            display: flex;
            flex-direction: column;
            justify-content: center;
            padding: 2rem;
        }
        .profile-pic {
            width: 100px;
            height: 100px;
            border: 3px solid #8b5cf6;
        }
        .contact-card:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .contact-card {
            transition: all 0.2s ease;
        }
    </style>
</head>
<body class="bg-gray-50 font-sans">
    <!-- Login/Signup Screen (Initially shown) -->
    <div id="authScreen" class="auth-screen">
        <div class="text-center mb-8">
            <h1 class="text-3xl font-bold text-purple-600 mb-2">SafeHer</h1>
            <p class="text-gray-600">Your personal safety companion</p>
        </div>
        
        <div id="loginForm" class="mb-6">
            <h2 class="text-xl font-semibold mb-4 text-center">Welcome Back</h2>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-medium mb-1" for="loginEmail">Email</label>
                <input type="email" id="loginEmail" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-medium mb-1" for="loginPassword">Password</label>
                <input type="password" id="loginPassword" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
            </div>
            <button id="loginBtn" class="w-full bg-purple-600 text-white py-3 rounded-lg font-medium mb-4 hover:bg-purple-700 transition">Log In</button>
            <p class="text-center text-sm text-gray-600">Don't have an account? <button id="showSignupBtn" class="text-purple-600 font-medium">Sign up</button></p>
        </div>
        
        <div id="signupForm" class="mb-6 hidden">
            <h2 class="text-xl font-semibold mb-4 text-center">Create Account</h2>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-medium mb-1" for="signupName">Full Name</label>
                <input type="text" id="signupName" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
            </div>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-medium mb-1" for="signupEmail">Email</label>
                <input type="email" id="signupEmail" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
            </div>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-medium mb-1" for="signupPhone">Phone Number</label>
                <input type="tel" id="signupPhone" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
            </div>
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-medium mb-1" for="signupPassword">Password</label>
                <input type="password" id="signupPassword" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-medium mb-1" for="signupConfirmPassword">Confirm Password</label>
                <input type="password" id="signupConfirmPassword" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
            </div>
            <button id="signupBtn" class="w-full bg-purple-600 text-white py-3 rounded-lg font-medium mb-4 hover:bg-purple-700 transition">Sign Up</button>
            <p class="text-center text-sm text-gray-600">Already have an account? <button id="showLoginBtn" class="text-purple-600 font-medium">Log in</button></p>
        </div>
    </div>

    <!-- Main App (Initially hidden) -->
    <div id="appContainer" class="max-w-md mx-auto bg-white min-h-screen shadow-lg overflow-hidden relative hidden">
        <!-- Header -->
        <header class="bg-purple-600 text-white p-4 flex justify-between items-center">
            <div>
                <h1 class="text-xl font-bold">SafeHer</h1>
                <p class="text-xs opacity-80">Your personal safety companion</p>
            </div>
            <div class="flex items-center space-x-3">
                <button id="profileBtn" class="w-10 h-10 rounded-full bg-purple-500 flex items-center justify-center">
                    <i class="fas fa-user"></i>
                </button>
            </div>
        </header>

        <!-- Status Bar -->
        <div class="bg-purple-50 p-3 flex items-center justify-between border-b">
            <div class="flex items-center space-x-2">
                <div class="w-3 h-3 rounded-full bg-green-500"></div>
                <span class="text-sm font-medium">Active Protection</span>
            </div>
            <div class="text-sm">
                <span id="currentTime" class="font-medium">12:34 PM</span>
                <span class="mx-1">•</span>
                <span id="currentDate" class="text-gray-500">Jun 15</span>
            </div>
        </div>

        <!-- Main Content -->
        <main class="p-4 pb-20">
            <!-- Emergency Button -->
            <div class="relative mb-8">
                <button id="emergencyBtn" class="emergency-btn w-full h-16 rounded-full bg-red-600 text-white font-bold text-lg flex items-center justify-center mx-auto pulse-animation">
                    <i class="fas fa-bell mr-2"></i> EMERGENCY ALERT
                </button>
                <div class="absolute -bottom-5 left-0 right-0 text-center">
                    <p class="text-xs text-gray-500">Hold for 3 seconds to activate</p>
                </div>
            </div>

            <!-- Quick Actions -->
            <div class="grid grid-cols-4 gap-3 mb-6">
                <button class="quick-action-btn bg-blue-100 text-blue-600 p-3 rounded-lg flex flex-col items-center">
                    <i class="fas fa-phone-alt text-xl mb-1"></i>
                    <span class="text-xs">Police</span>
                </button>
                <button class="quick-action-btn bg-green-100 text-green-600 p-3 rounded-lg flex flex-col items-center">
                    <i class="fas fa-hospital text-xl mb-1"></i>
                    <span class="text-xs">Ambulance</span>
                </button>
                <button class="quick-action-btn bg-yellow-100 text-yellow-600 p-3 rounded-lg flex flex-col items-center">
                    <i class="fas fa-user-shield text-xl mb-1"></i>
                    <span class="text-xs">Guardian</span>
                </button>
                <button class="quick-action-btn bg-purple-100 text-purple-600 p-3 rounded-lg flex flex-col items-center">
                    <i class="fas fa-map-marker-alt text-xl mb-1"></i>
                    <span class="text-xs">Track Me</span>
                </button>
            </div>

            <!-- Live Location -->
            <div class="mb-6">
                <h2 class="text-lg font-semibold mb-2 flex items-center">
                    <i class="fas fa-map-marked-alt text-purple-600 mr-2"></i>
                    Live Location
                </h2>
                <div class="map-container">
                    <div class="map-placeholder">
                        <i class="fas fa-map-marker-alt text-4xl mb-2 text-red-500"></i>
                        <p>Loading your current location...</p>
                    </div>
                </div>
                <div class="mt-2 flex justify-between text-sm">
                    <span class="text-gray-500">Last updated: <span id="locationTime">Just now</span></span>
                    <button id="refreshLocationBtn" class="text-purple-600 font-medium">Refresh</button>
                </div>
            </div>

            <!-- Safety Network -->
            <div class="mb-6">
                <div class="flex justify-between items-center mb-2">
                    <h2 class="text-lg font-semibold flex items-center">
                        <i class="fas fa-users text-purple-600 mr-2"></i>
                        Safety Network
                    </h2>
                    <button id="manageContactsBtn" class="text-purple-600 text-sm font-medium">Manage</button>
                </div>
                <div class="grid grid-cols-3 gap-3" id="safetyNetworkGrid">
                    <div class="contact-card bg-gray-50 p-3 rounded-lg flex flex-col items-center cursor-pointer" data-name="Mom" data-phone="+91 8406088695">
                        <div class="w-12 h-12 rounded-full bg-purple-200 flex items-center justify-center mb-2">
                            <i class="fas fa-user text-purple-600"></i>
                        </div>
                        <span class="text-sm font-medium">Mom</span>
                        <span class="text-xs text-gray-500">Last active: 5m ago</span>
                    </div>
                    <div class="contact-card bg-gray-50 p-3 rounded-lg flex flex-col items-center cursor-pointer" data-name="Dad" data-phone="+91 8406088695">
                        <div class="w-12 h-12 rounded-full bg-blue-200 flex items-center justify-center mb-2">
                            <i class="fas fa-user text-blue-600"></i>
                        </div>
                        <span class="text-sm font-medium">Dad</span>
                        <span class="text-xs text-gray-500">Last active: 1h ago</span>
                    </div>
                    <div class="contact-card bg-gray-50 p-3 rounded-lg flex flex-col items-center cursor-pointer" id="addContactBtn">
                        <div class="w-12 h-12 rounded-full bg-green-200 flex items-center justify-center mb-2">
                            <i class="fas fa-user-plus text-green-600"></i>
                        </div>
                        <span class="text-xs text-gray-500">Add Contact</span>
                    </div>
                </div>
            </div>

            <!-- Safety Tips -->
            <div>
                <h2 class="text-lg font-semibold mb-2 flex items-center">
                    <i class="fas fa-lightbulb text-purple-600 mr-2"></i>
                    Safety Tips
                </h2>
                <div class="bg-purple-50 rounded-lg p-3">
                    <div class="flex items-start mb-3">
                        <div class="bg-purple-100 p-2 rounded-full mr-3">
                            <i class="fas fa-walking text-purple-600"></i>
                        </div>
                        <div>
                            <h3 class="font-medium">Walking Alone</h3>
                            <p class="text-sm text-gray-600">Stay in well-lit areas and avoid shortcuts through alleys.</p>
                        </div>
                    </div>
                    <div class="flex items-start">
                        <div class="bg-purple-100 p-2 rounded-full mr-3">
                            <i class="fas fa-taxi text-purple-600"></i>
                        </div>
                        <div>
                            <h3 class="font-medium">Ride Safety</h3>
                            <p class="text-sm text-gray-600">Always verify the driver and vehicle details before boarding.</p>
                        </div>
                    </div>
                </div>
            </div>
        </main>

        <!-- Bottom Navigation -->
        <nav class="bg-white border-t fixed bottom-0 w-full max-w-md flex justify-around py-3">
            <button class="nav-btn active text-purple-600 flex flex-col items-center">
                <i class="fas fa-home text-xl"></i>
                <span class="text-xs mt-1">Home</span>
            </button>
            <button class="nav-btn text-gray-500 flex flex-col items-center">
                <i class="fas fa-map text-xl"></i>
                <span class="text-xs mt-1">Map</span>
            </button>
            <button class="nav-btn text-gray-500 flex flex-col items-center">
                <i class="fas fa-history text-xl"></i>
                <span class="text-xs mt-1">History</span>
            </button>
            <button class="nav-btn text-gray-500 flex flex-col items-center">
                <i class="fas fa-cog text-xl"></i>
                <span class="text-xs mt-1">Settings</span>
            </button>
        </nav>

        <!-- Emergency Modal -->
        <div id="emergencyModal" class="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
            <div class="bg-white rounded-xl w-full max-w-sm overflow-hidden slide-in">
                <div class="bg-red-600 text-white p-4 text-center">
                    <i class="fas fa-exclamation-triangle text-3xl mb-2"></i>
                    <h2 class="text-xl font-bold">EMERGENCY ALERT</h2>
                    <p class="text-sm">Help is on the way!</p>
                </div>
                <div class="p-4">
                    <div class="flex justify-between mb-4">
                        <div class="text-center">
                            <div class="w-12 h-12 bg-red-100 rounded-full flex items-center justify-center mx-auto mb-2">
                                <i class="fas fa-phone-alt text-red-600"></i>
                            </div>
                            <span class="text-xs">Calling Police</span>
                        </div>
                        <div class="text-center">
                            <div class="w-12 h-12 bg-purple-100 rounded-full flex items-center justify-center mx-auto mb-2">
                                <i class="fas fa-map-marker-alt text-purple-600"></i>
                            </div>
                            <span class="text-xs">Sharing Location</span>
                        </div>
                        <div class="text-center">
                            <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center mx-auto mb-2">
                                <i class="fas fa-users text-blue-600"></i>
                            </div>
                            <span class="text-xs">Alerting Network</span>
                        </div>
                    </div>
                    <div class="bg-gray-100 p-3 rounded-lg mb-4">
                        <p class="text-sm text-center">Your emergency contacts have been notified with your live location.</p>
                    </div>
                    <div class="flex space-x-3">
                        <button id="cancelEmergency" class="flex-1 bg-gray-200 text-gray-800 py-2 rounded-lg font-medium">
                            Cancel
                        </button>
                        <button class="flex-1 bg-red-600 text-white py-2 rounded-lg font-medium">
                            <i class="fas fa-volume-up mr-1"></i> Scream
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Profile Modal -->
        <div id="profileModal" class="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
            <div class="bg-white rounded-xl w-full max-w-sm overflow-hidden slide-in">
                <div class="bg-purple-600 text-white p-4 text-center relative">
                    <button id="closeProfileBtn" class="absolute left-4 top-4 text-white">
                        <i class="fas fa-times"></i>
                    </button>
                    <div class="flex flex-col items-center">
                        <div class="profile-pic rounded-full bg-purple-200 flex items-center justify-center mb-3">
                            <i class="fas fa-user text-purple-600 text-4xl" id="profileIcon"></i>
                        </div>
                        <h2 class="text-xl font-bold" id="profileName">User Name</h2>
                        <p class="text-sm opacity-80" id="profileEmail">user@example.com</p>
                    </div>
                </div>
                <div class="p-4">
                    <div class="mb-6">
                        <h3 class="font-medium text-gray-700 mb-2">Personal Information</h3>
                        <div class="bg-gray-50 rounded-lg p-3">
                            <div class="flex justify-between mb-3">
                                <span class="text-gray-600">Phone:</span>
                                <span class="font-medium" id="profilePhone">+1 234 567 890</span>
                            </div>
                            <div class="flex justify-between">
                                <span class="text-gray-600">Member since:</span>
                                <span class="font-medium" id="profileJoinDate">June 2023</span>
                            </div>
                        </div>
                    </div>
                    
                    <div class="mb-6">
                        <h3 class="font-medium text-gray-700 mb-2">Emergency Settings</h3>
                        <div class="bg-gray-50 rounded-lg p-3">
                            <div class="flex justify-between items-center mb-3">
                                <span class="text-gray-600">Auto-share location</span>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" class="sr-only peer" checked>
                                    <div class="w-11 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-purple-300 rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-purple-600"></div>
                                </label>
                            </div>
                            <div class="flex justify-between items-center">
                                <span class="text-gray-600">Emergency SMS</span>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" class="sr-only peer" checked>
                                    <div class="w-11 h-6 bg-gray-200 peer-focus:outline-none peer-focus:ring-4 peer-focus:ring-purple-300 rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-purple-600"></div>
                                </label>
                            </div>
                        </div>
                    </div>
                    
                    <button id="logoutBtn" class="w-full bg-red-100 text-red-600 py-3 rounded-lg font-medium hover:bg-red-200 transition">
                        <i class="fas fa-sign-out-alt mr-2"></i> Log Out
                    </button>
                </div>
            </div>
        </div>

        <!-- Manage Contacts Modal -->
        <div id="manageContactsModal" class="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
            <div class="bg-white rounded-xl w-full max-w-sm overflow-hidden slide-in">
                <div class="bg-purple-600 text-white p-4 text-center relative">
                    <button id="closeManageContactsBtn" class="absolute left-4 top-4 text-white">
                        <i class="fas fa-times"></i>
                    </button>
                    <h2 class="text-xl font-bold">Manage Safety Network</h2>
                </div>
                <div class="p-4 max-h-96 overflow-y-auto">
                    <div class="mb-4">
                        <h3 class="font-medium text-gray-700 mb-2">Your Emergency Contacts</h3>
                        <div id="contactsList" class="space-y-3">
                            <!-- Contacts will be added here dynamically -->
                        </div>
                    </div>
                    
                    <div class="mb-6">
                        <h3 class="font-medium text-gray-700 mb-2">Add New Contact</h3>
                        <div class="bg-gray-50 rounded-lg p-3">
                            <div class="mb-3">
                                <label class="block text-gray-700 text-sm font-medium mb-1" for="contactName">Name</label>
                                <input type="text" id="contactName" class="w-full p-2 border border-gray-300 rounded-lg">
                            </div>
                            <div class="mb-3">
                                <label class="block text-gray-700 text-sm font-medium mb-1" for="contactPhone">Phone Number</label>
                                <input type="tel" id="contactPhone" class="w-full p-2 border border-gray-300 rounded-lg">
                            </div>
                            <div class="mb-3">
                                <label class="block text-gray-700 text-sm font-medium mb-1" for="contactRelation">Relation</label>
                                <select id="contactRelation" class="w-full p-2 border border-gray-300 rounded-lg">
                                    <option value="Family">Family</option>
                                    <option value="Friend">Friend</option>
                                    <option value="Guardian">Guardian</option>
                                    <option value="Other">Other</option>
                                </select>
                            </div>
                            <button id="addNewContactBtn" class="w-full bg-purple-600 text-white py-2 rounded-lg font-medium">
                                Add Contact
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // User data storage
        let currentUser = null;
        const users = [
            // Demo user (in a real app, this would be server-side)
            {
                name: "Jane Doe",
                email: "jane@example.com",
                phone: "+1 234 567 890",
                password: "password123",
                joinDate: "June 2023",
                contacts: [
                    { name: "Mom", phone: "+9100000000000", relation: "Family", lastActive: "5m ago" },
                    { name: "Dad", phone: "+911111111111111", relation: "Family", lastActive: "1h ago" }
                ]
            }
        ];

        // DOM Elements
        const authScreen = document.getElementById('authScreen');
        const appContainer = document.getElementById('appContainer');
        const loginForm = document.getElementById('loginForm');
        const signupForm = document.getElementById('signupForm');
        const showSignupBtn = document.getElementById('showSignupBtn');
        const showLoginBtn = document.getElementById('showLoginBtn');
        const loginBtn = document.getElementById('loginBtn');
        const signupBtn = document.getElementById('signupBtn');
        const profileBtn = document.getElementById('profileBtn');
        const profileModal = document.getElementById('profileModal');
        const closeProfileBtn = document.getElementById('closeProfileBtn');
        const logoutBtn = document.getElementById('logoutBtn');
        const manageContactsBtn = document.getElementById('manageContactsBtn');
        const manageContactsModal = document.getElementById('manageContactsModal');
        const closeManageContactsBtn = document.getElementById('closeManageContactsBtn');
        const contactsList = document.getElementById('contactsList');
        const addNewContactBtn = document.getElementById('addNewContactBtn');
        const refreshLocationBtn = document.getElementById('refreshLocationBtn');
        const safetyNetworkGrid = document.getElementById('safetyNetworkGrid');

        // Toggle between login and signup forms
        showSignupBtn.addEventListener('click', () => {
            loginForm.classList.add('hidden');
            signupForm.classList.remove('hidden');
        });

        showLoginBtn.addEventListener('click', () => {
            signupForm.classList.add('hidden');
            loginForm.classList.remove('hidden');
        });

        // Login functionality
        loginBtn.addEventListener('click', () => {
            const email = document.getElementById('loginEmail').value;
            const password = document.getElementById('loginPassword').value;
            
            // Simple validation
            if (!email || !password) {
                alert('Please enter both email and password');
                return;
            }
            
            // Check user credentials (in a real app, this would be a server call)
            const user = users.find(u => u.email === email && u.password === password);
            
            if (user) {
                currentUser = user;
                authScreen.classList.add('slide-out');
                setTimeout(() => {
                    authScreen.classList.add('hidden');
                    appContainer.classList.remove('hidden');
                    updateProfileInfo();
                    renderContacts();
                }, 300);
            } else {
                alert('Invalid email or password');
            }
        });

        // Signup functionality
        signupBtn.addEventListener('click', () => {
            const name = document.getElementById('signupName').value;
            const email = document.getElementById('signupEmail').value;
            const phone = document.getElementById('signupPhone').value;
            const password = document.getElementById('signupPassword').value;
            const confirmPassword = document.getElementById('signupConfirmPassword').value;
            
            // Simple validation
            if (!name || !email || !phone || !password || !confirmPassword) {
                alert('Please fill in all fields');
                return;
            }
            
            if (password !== confirmPassword) {
                alert('Passwords do not match');
                return;
            }
            
            // Check if user already exists (in a real app, this would be a server call)
            if (users.some(u => u.email === email)) {
                alert('An account with this email already exists');
                return;
            }
            
            // Create new user (in a real app, this would be a server call)
            const newUser = {
                name,
                email,
                phone,
                password,
                joinDate: new Date().toLocaleDateString('en-US', { month: 'long', year: 'numeric' }),
                contacts: [
                    { name: "Mom", phone: "+91 8406088695", relation: "Family", lastActive: "5m ago" },
                    { name: "Dad", phone: "+191 9128156864", relation: "Family", lastActive: "1h ago" }
                ]
            };
            
            users.push(newUser);
            currentUser = newUser;
            
            authScreen.classList.add('slide-out');
            setTimeout(() => {
                authScreen.classList.add('hidden');
                appContainer.classList.remove('hidden');
                updateProfileInfo();
                renderContacts();
            }, 300);
        });

        // Update profile information in the modal
        function updateProfileInfo() {
            if (!currentUser) return;
            
            document.getElementById('profileName').textContent = currentUser.name;
            document.getElementById('profileEmail').textContent = currentUser.email;
            document.getElementById('profilePhone').textContent = currentUser.phone;
            document.getElementById('profileJoinDate').textContent = currentUser.joinDate;
            
            // Set profile icon initials
            const initials = currentUser.name.split(' ').map(n => n[0]).join('').toUpperCase();
            document.getElementById('profileIcon').textContent = initials;
        }

        // Render contacts in safety network
        function renderContacts() {
            if (!currentUser) return;
            
            // Clear existing contacts
            safetyNetworkGrid.innerHTML = '';
            
            // Add each contact
            currentUser.contacts.forEach(contact => {
                const contactCard = document.createElement('div');
                contactCard.className = 'contact-card bg-gray-50 p-3 rounded-lg flex flex-col items-center cursor-pointer';
                contactCard.setAttribute('data-name', contact.name);
                contactCard.setAttribute('data-phone', contact.phone);
                
                // Random color for contact icon
                const colors = ['purple', 'blue', 'green', 'yellow', 'pink', 'indigo'];
                const randomColor = colors[Math.floor(Math.random() * colors.length)];
                
                contactCard.innerHTML = `
                    <div class="w-12 h-12 rounded-full bg-${randomColor}-200 flex items-center justify-center mb-2">
                        <i class="fas fa-user text-${randomColor}-600"></i>
                    </div>
                    <span class="text-sm font-medium">${contact.name}</span>
                    <span class="text-xs text-gray-500">Last active: ${contact.lastActive}</span>
                `;
                
                // Add click event to call the contact
                contactCard.addEventListener('click', () => {
                    alert(`Calling ${contact.name} at ${contact.phone}...`);
                });
                
                safetyNetworkGrid.appendChild(contactCard);
            });
            
            // Add the "Add Contact" button at the end
            const addContactBtn = document.createElement('div');
            addContactBtn.className = 'contact-card bg-gray-50 p-3 rounded-lg flex flex-col items-center cursor-pointer';
            addContactBtn.id = 'addContactBtn';
            addContactBtn.innerHTML = `
                <div class="w-12 h-12 rounded-full bg-green-200 flex items-center justify-center mb-2">
                    <i class="fas fa-user-plus text-green-600"></i>
                </div>
                <span class="text-xs text-gray-500">Add Contact</span>
            `;
            
            addContactBtn.addEventListener('click', () => {
                manageContactsModal.classList.remove('hidden');
                renderContactsList();
            });
            
            safetyNetworkGrid.appendChild(addContactBtn);
        }

        // Render contacts list in manage modal
        function renderContactsList() {
            if (!currentUser) return;
            
            contactsList.innerHTML = '';
            
            currentUser.contacts.forEach((contact, index) => {
                const contactItem = document.createElement('div');
                contactItem.className = 'bg-gray-50 p-3 rounded-lg flex justify-between items-center';
                contactItem.innerHTML = `
                    <div>
                        <h4 class="font-medium">${contact.name}</h4>
                        <p class="text-sm text-gray-600">${contact.phone} • ${contact.relation}</p>
                    </div>
                    <button class="delete-contact-btn text-red-500" data-index="${index}">
                        <i class="fas fa-trash"></i>
                    </button>
                `;
                
                contactsList.appendChild(contactItem);
            });
            
            // Add event listeners to delete buttons
            document.querySelectorAll('.delete-contact-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const index = parseInt(this.getAttribute('data-index'));
                    currentUser.contacts.splice(index, 1);
                    renderContactsList();
                    renderContacts();
                });
            });
        }

        // Profile modal controls
        profileBtn.addEventListener('click', () => {
            profileModal.classList.remove('hidden');
        });

        closeProfileBtn.addEventListener('click', () => {
            profileModal.classList.add('hidden');
        });

        // Manage contacts modal controls
        manageContactsBtn.addEventListener('click', () => {
            manageContactsModal.classList.remove('hidden');
            renderContactsList();
        });

        closeManageContactsBtn.addEventListener('click', () => {
            manageContactsModal.classList.add('hidden');
        });

        // Add new contact
        addNewContactBtn.addEventListener('click', () => {
            const name = document.getElementById('contactName').value;
            const phone = document.getElementById('contactPhone').value;
            const relation = document.getElementById('contactRelation').value;
            
            if (!name || !phone) {
                alert('Please enter name and phone number');
                return;
            }
            
            // Add new contact (in a real app, this would be a server call)
            currentUser.contacts.push({
                name,
                phone,
                relation,
                lastActive: "Just now"
            });
            
            // Clear form
            document.getElementById('contactName').value = '';
            document.getElementById('contactPhone').value = '';
            
            // Update UI
            renderContactsList();
            renderContacts();
        });

        // Refresh location button
        refreshLocationBtn.addEventListener('click', () => {
            refreshLocationBtn.classList.add('animate-spin');
            document.getElementById('locationTime').textContent = "Updating...";
            
            setTimeout(() => {
                refreshLocationBtn.classList.remove('animate-spin');
                document.getElementById('locationTime').textContent = "Just now";
                alert('Location refreshed successfully!');
            }, 1500);
        });

        // Logout functionality
        logoutBtn.addEventListener('click', () => {
            currentUser = null;
            appContainer.classList.add('hidden');
            authScreen.classList.remove('hidden', 'slide-out');
            loginForm.classList.remove('hidden');
            signupForm.classList.add('hidden');
            profileModal.classList.add('hidden');
            manageContactsModal.classList.add('hidden');
            
            // Clear form fields
            document.getElementById('loginEmail').value = '';
            document.getElementById('loginPassword').value = '';
            document.getElementById('signupName').value = '';
            document.getElementById('signupEmail').value = '';
            document.getElementById('signupPhone').value = '';
            document.getElementById('signupPassword').value = '';
            document.getElementById('signupConfirmPassword').value = '';
        });

        // Update time and date
        function updateDateTime() {
            const now = new Date();
            const timeOptions = { hour: '2-digit', minute: '2-digit' };
            const dateOptions = { month: 'short', day: 'numeric' };
            
            document.getElementById('currentTime').textContent = now.toLocaleTimeString([], timeOptions);
            document.getElementById('currentDate').textContent = now.toLocaleDateString([], dateOptions);
        }
        
        setInterval(updateDateTime, 1000);
        updateDateTime();

        // Simulate location update
        function updateLocationTime() {
            const times = ['Just now', '1 min ago', '2 mins ago', '5 mins ago'];
            let index = 0;
            
            setInterval(() => {
                document.getElementById('locationTime').textContent = times[index];
                index = (index + 1) % times.length;
            }, 60000);
        }
        updateLocationTime();

        // Emergency button functionality
        const emergencyBtn = document.getElementById('emergencyBtn');
        const emergencyModal = document.getElementById('emergencyModal');
        const cancelEmergency = document.getElementById('cancelEmergency');
        
        let emergencyTimeout;
        
        emergencyBtn.addEventListener('mousedown', function() {
            emergencyTimeout = setTimeout(() => {
                emergencyModal.classList.remove('hidden');
            }, 3000);
        });
        
        emergencyBtn.addEventListener('mouseup', function() {
            clearTimeout(emergencyTimeout);
        });
        
        emergencyBtn.addEventListener('touchstart', function() {
            emergencyTimeout = setTimeout(() => {
                emergencyModal.classList.remove('hidden');
            }, 3000);
        });
        
        emergencyBtn.addEventListener('touchend', function() {
            clearTimeout(emergencyTimeout);
        });
        
        cancelEmergency.addEventListener('click', function() {
            emergencyModal.classList.add('hidden');
        });

        // Quick action buttons
        const quickActionBtns = document.querySelectorAll('.quick-action-btn');
        quickActionBtns.forEach(btn => {
            btn.addEventListener('click', function() {
                // Simulate button click with animation
                this.classList.add('transform', 'scale-95');
                setTimeout(() => {
                    this.classList.remove('transform', 'scale-95');
                }, 150);
                
                // Show different actions based on button
                const icon = this.querySelector('i');
                if (icon.classList.contains('fa-phone-alt')) {
                    alert('Calling local police...');
                } else if (icon.classList.contains('fa-hospital')) {
                    alert('Calling ambulance...');
                } else if (icon.classList.contains('fa-user-shield')) {
                    alert('Alerting your safety guardians...');
                } else if (icon.classList.contains('fa-map-marker-alt')) {
                    alert('Sharing your live location with safety network...');
                }
            });
        });

        // Navigation buttons
        const navBtns = document.querySelectorAll('.nav-btn');
        navBtns.forEach(btn => {
            btn.addEventListener('click', function() {
                navBtns.forEach(b => {
                    b.classList.remove('text-purple-600', 'active');
                    b.classList.add('text-gray-500');
                });
                this.classList.add('text-purple-600', 'active');
                this.classList.remove('text-gray-500');
                
                // In a real app, this would load different content
                console.log('Navigating to:', this.querySelector('span').textContent);
            });
        });
    </script>
</body>
</html>
