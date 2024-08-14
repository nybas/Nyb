// Track user activity time on warpcast.com
let startTime;
let totalTime = 0;

// Function to start the session and track the time
function startSession() {
    startTime = Date.now();
}

// Function to end the session and calculate the total time spent
function endSession() {
    const endTime = Date.now();
    const sessionTime = endTime - startTime;
    totalTime += sessionTime;

    // Save total time to local storage
    saveTotalTime(totalTime);

    // Update user rank based on total time
    updateRank(totalTime);
}

// Function to save the total time to local storage
function saveTotalTime(time) {
    const storedTime = localStorage.getItem('totalTime');
    const newTime = storedTime ? parseInt(storedTime) + time : time;
    localStorage.setItem('totalTime', newTime);
}

// Function to get the total time from local storage
function getTotalTime() {
    const storedTime = localStorage.getItem('totalTime');
    return storedTime ? parseInt(storedTime) : 0;
}

// Function to update the user rank based on total time
function updateRank(totalTime) {
    let rank;

    // Determine rank based on total time spent
    if (totalTime < 3600 * 1000) { // Less than 1 hour
        rank = 'Bronze';
    } else if (totalTime < 7200 * 1000) { // 1 to 2 hours
        rank = 'Silver';
    } else if (totalTime < 14400 * 1000) { // 2 to 4 hours
        rank = 'Gold';
    } else {
        rank = 'Platinum';
    }

    // Display the user's rank
    displayRank(rank);
}

// Function to display the user's rank in the UI
function displayRank(rank) {
    // You can customize this to update your UI with the user's rank
    console.log("Your activity rank: " + rank);
}

// Function to handle user activity monitoring
function trackActivity() {
    let idleTime = 0;
    const idleInterval = setInterval(timerIncrement, 60000); // 1 minute

    function timerIncrement() {
        idleTime++;
        if (idleTime > 5) { // 5 minutes of inactivity
            endSession(); // End session if inactive
            clearInterval(idleInterval);
        }
    }

    // Reset the idle timer when user interacts with the page
    window.addEventListener('mousemove', () => idleTime = 0);
    window.addEventListener('keypress', () => idleTime = 0);
}

// Initialize the script
function init() {
    // Get the total time from local storage on page load
    totalTime = getTotalTime();

    // Start tracking session time and activity
    startSession();
    trackActivity();

    // End session when the user leaves the page
    window.addEventListener('beforeunload', endSession);
}

// Start the tracking when the page loads
window.addEventListener('load', init);
