<script>
  export let title = "Journal Entry";

  let userInput = "";
  let rating = 0;
  let musicQuery = "";
  let musicResults = [];
  let albumCoverUrl = "";
  let isSortedByRating = false;
  let isSortedByRecent = false;
  let mood = "";
  let activityType = "";

  let moodOptions = ["Happy", "Sad", "Excited", "Tired", "Anxious", "Relaxed"];
  let activityOptions = ["Work", "Exercise", "Socializing", "Relaxing", "Other"];
  let entries = [
    {
      text: "September by Earth, Wind & Fire",
      rating: 5,
      albumCoverUrl: "./images/September.webp",
      timestamp: new Date,
      mood: "Excited",
      activityType: "Socializing",
      isEditing: false
    },
    {
      text: "Sunday Best by Surfaces",
      rating: 4,
      albumCoverUrl: "./images/Surfaces.jpg",
      timestamp: new Date,
      mood: "Happy",
      activityType: "Other",
      isEditing: false
    },
    {
      text: "WILDFLOWER by Billie Eilish",
      rating: 5,
      albumCoverUrl: "./images/Wildflower.webp",
      timestamp: new Date,
      mood: "Sad",
      activityType: "Relaxing",
      isEditing: false
    }
  ];

  // Function to submit new entry with validation
  function submitEntry() {
    if (userInput.trim() !== "" && rating > 0 && mood !== "" && activityType !== "") {
      const timestamp = new Date();
      entries = [...entries, {
        text: userInput,
        rating,
        albumCoverUrl,
        timestamp,
        mood,
        activityType,
        isEditing: false
      }];
      userInput = "";
      rating = 0;
      albumCoverUrl = "";
      mood = "";
      activityType = "";
      musicQuery = "";
    } else {
      alert("Please fill out all fields before submitting.");
    }
  }

  // Function to set rating
  function setRating(stars) {
    rating = stars;
  }

  // Music search via iTunes API
  async function searchMusic() {
    if (musicQuery.trim() === "") {
      musicResults = [];
      return;
    }

    const response = await fetch(
      `https://itunes.apple.com/search?term=${encodeURIComponent(musicQuery)}&entity=musicTrack&limit=5`
    );
    const data = await response.json();
    musicResults = data.results.map(track => ({
      artist: track.artistName,
      song: track.trackName,
      albumCover: track.artworkUrl100
    }));
  }

  // Autofill music data
  function autofillMusic(song, artist, albumCover) {
    userInput += `${song} by ${artist}\n`;
    albumCoverUrl = albumCover;
    musicQuery = ""; 
    musicResults = [];
  }

  // Sort by rating
  function sortEntriesByRating() {
    isSortedByRating = !isSortedByRating;
    entries = [...entries].sort((a, b) => isSortedByRating ? b.rating - a.rating : a.rating - b.rating);
  }

  // Sort by recent
  function sortEntriesByRecent() {
    isSortedByRecent = !isSortedByRecent;
    entries = [...entries].sort((a, b) => isSortedByRecent ? a.timestamp - b.timestamp : b.timestamp - a.timestamp);
  }

  // Enable editing for a specific entry
  function enableEdit(index) {
    entries[index].isEditing = true;
  }

  // Save the edited entry
  function saveEdit(index, newText, newRating) {
    entries[index].text = newText;
    entries[index].rating = newRating;
    entries[index].isEditing = false;
  }

  // Cancel editing
  function cancelEdit(index) {
    entries[index].isEditing = false;
  }

  // Set rating for an entry in editing mode
  function setEntryRating(index, stars) {
    entries[index].rating = stars;
  }
</script>

<style>
  .container {
    display: flex;
  }
  .input-section, .entry-section {
    flex: 1;
    padding: 1rem;
    margin: 5px;
    border-radius: 10px;
  }
  .input-section {
    background-color: #212121;
    border-left: 1px solid #000000;
    position: relative;
  }
  .entry-section {
    background-color: #212121;
    border-left: 1px solid #000000;
  }
  .entry {
    display: flex;
    align-items: flex-start;
    margin-bottom: 1rem;
    padding: 0.5rem;
    cursor: pointer;
    background-color: #535353;
    transition: background-color 0.3s ease;
    border-radius: 5px;
  }
  .entry:hover {
    background-color: #979797;
  }
  .album-cover {
    max-width: 100px;
    margin-right: 1rem;
  }
  .edit-controls {
    margin-top: 0.5rem;
  }
  .stars {
    cursor: pointer;
    color: rgb(255, 0, 234);
    font-size: 1.5rem;
    display: inline-block;
  }
  .music-results {
    background-color: #343434;
    border: 1px solid #555;
    border-radius: 5px;
    max-height: 200px;
    overflow-y: auto;
    padding: 0.5rem;
    position: absolute;
    z-index: 10;
    width: calc(100% - 1rem);
  }
  .music-results div {
    padding: 0.5rem;
    cursor: pointer;
    color: #dddbdb;
    transition: background-color 0.3s ease;
  }
  .music-results div:hover {
    background-color: #666;
  }
</style>

<div class="container">
  <div class="input-section" style="color: #dddbdb;">
    <h2>{title}</h2>
    
    <h3>How are you feeling?</h3>
    <div>
      {#each moodOptions as moodOption}
        <label>
          <input type="radio" bind:group={mood} value={moodOption} />
          {moodOption}
        </label>
      {/each}
    </div>
     
    
    <h3>What type of activity did you do?</h3>
    <select bind:value={activityType}>
      <option value="" disabled>Select activity type</option>
      {#each activityOptions as activityOption}
        <option value={activityOption}>{activityOption}</option>
      {/each}
    </select>

    <h3>Select a song/artist:</h3>
    <input
      type="text"
      bind:value={musicQuery}
      placeholder="Search for music..."
      on:input={searchMusic}
    />
    {#if musicResults.length > 0}
      <div class="music-results">
        {#each musicResults as result}
          <div on:click={() => autofillMusic(result.song, result.artist, result.albumCover)}>
            {result.song} by {result.artist}
          </div>
        {/each}
      </div>
    {/if}
    <br />

    <h3>What is your star rating?</h3>
    <div class="stars-container">
      {#each [1, 2, 3, 4, 5] as star}
        <span class="stars" on:click={() => setRating(star)}>
          {star <= rating ? "★" : "☆"}
        </span>
      {/each}
    </div>
    
    <button on:click={submitEntry}>Submit</button>
  </div>

  <div class="entry-section" style="color: #dddbdb;">
    <h2>Your Entries</h2>
    <button on:click={sortEntriesByRating}>
      {isSortedByRating ? "Sort by Highest Rating" : "Sort by Lowest Rating"}
    </button>
    <button on:click={sortEntriesByRecent}>
      {isSortedByRecent ? "Sort by Oldest Entry" : "Sort by Recent Entry"}
    </button>

    <ul>
      {#each entries as entry, i}
        <li class="entry">
          {#if entry.albumCoverUrl}
            <img src={entry.albumCoverUrl} alt="Album Cover" class="album-cover" />
          {/if}

          {#if entry.isEditing}
            <div>
              <textarea bind:value={entry.text}></textarea>
              <div class="stars-container">
                {#each [1, 2, 3, 4, 5] as star}
                  <span class="stars" on:click={() => setEntryRating(i, star)}>
                    {star <= entry.rating ? "★" : "☆"}
                  </span>
                {/each}
              </div>
              <div class="edit-controls">
                <button on:click={() => saveEdit(i, entry.text, entry.rating)}>Save</button>
                <button on:click={() => cancelEdit(i)}>Cancel</button>
              </div>
              </div>
                {:else}
              <div class="entry-text" on:click={() => enableEdit(i)}>
                {entry.text}
                <div>Mood: {entry.mood}</div>
                <div>Activity: {entry.activityType}</div>
                <div>
                  {#each [1, 2, 3, 4, 5] as star}
                    <span class="stars">{star <= entry.rating ? "★" : "☆"}</span>
                  {/each}
                </div>
              </div>
            {/if}
          </li>
        {/each}
      </ul>
    </div>
  </div>