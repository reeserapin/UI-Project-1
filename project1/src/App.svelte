<script>
  import JournalEntry from './lib/hi.svelte'; // Import the JournalEntry component

  // User's name
  let userName = "Reese Rapin"; 

  // Get current date and time
  let currentTime = new Date();

  // Function to update time every second
  setInterval(() => {
    currentTime = new Date();
  }, 1000);
</script>

<!-- HTML section: Website layout and structure -->

<!-- Top bar with user name, company name, time/date, and Spotify logo -->
<header>
  <div class="top-bar">
    <div class="user-info">
      <span>{userName}</span>
      <span>{currentTime.toLocaleDateString()} {currentTime.toLocaleTimeString()}</span>
    </div>

    <!-- Company name centered on the page -->
    <div class="company-name">
      <h1>Trackify</h1>
    </div>

    <!-- Spotify logo on the right side -->
    <img src="/images/Spotify_Primary_Logo_Pink.png" alt="Spotify Logo" class="spotify-logo" />
  </div>
</header>

<main>
  <!-- Add JournalEntry component -->
  <JournalEntry title="Review A Song" />
</main>

<!-- CSS Styles specific to this component -->
<style>
  header {
    background-color: #121212;
    color: #dddbdb; 
    padding: 1rem;
    position: relative; 
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-family: Arial, sans-serif;
  }

  .top-bar {
    display: flex;
    justify-content: space-between;
    width: 100%;
    align-items: center;
  }

  .user-info {
    display: flex;
    justify-content: space-between;
    width: 200px;
  }

  .company-name {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
  }

  .spotify-logo {
    height: 40px; 
    width: auto;
    margin-left: auto; 
  }

  main {
    font-family: Arial, sans-serif;
    max-width: 1500px;
    margin: auto;
    padding: 1rem;
    border-radius: 10px;
  }
</style>
