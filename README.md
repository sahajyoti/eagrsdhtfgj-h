# eagrsdhtfgj-h
<!DOCTYPE html>
<html lang="en">
<head>
<title>Simple Music Player</title>
<!-- Load FontAwesome icons -->
<link rel="stylesheet"
		href=
"https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.13.0/css/all.min.css">


<!-- Load the custom CSS style file -->

<body>
<div class="player">

	<!-- Define the section for displaying details -->
	<div class="details">
	<div class="now-playing">PLAYING x OF y</div>
	<div class="track-art"></div>
	<div class="track-name">Track Name</div>
	<div class="track-artist">Track Artist</div>
	</div>

	<!-- Define the section for displaying track buttons -->
	<div class="buttons">
	<div class="prev-track" onclick="prevTrack()">
		<i class="fa fa-step-backward fa-2x"></i>
	</div>
	<div class="playpause-track" onclick="playpauseTrack()">
		<i class="fa fa-play-circle fa-5x" ></i>
	</div>
	<div class="next-track" onclick="nextTrack()">
		<i class="fa fa-step-forward fa-2x"></i>
	</div>
	</div>

	<!-- Define the section for displaying the seek slider-->
	<div class="slider_container">
	<div class="current-time">00:00</div>
	<input type="range" min="1" max="100"
		value="0" class="seek_slider" onchange="seekTo()">
	<div class="total-duration">00:00</div>
	</div>

	<!-- Define the section for displaying the volume slider-->
	<div class="slider_container">
	<i class="fa fa-volume-down"></i>
	<input type="range" min="1" max="100"
		value="99" class="volume_slider" onchange="setVolume()">
	<i class="fa fa-volume-up"></i>
	</div>
</div>

<!-- Load the main script for the player -->
<script >
	// Select all the elements in the HTML page
// and assign them to a variable
let now_playing = document.querySelector(".now-playing");
let track_art = document.querySelector(".track-art");
let track_name = document.querySelector(".track-name");
let track_artist = document.querySelector(".track-artist");

let playpause_btn = document.querySelector(".playpause-track");
let next_btn = document.querySelector(".next-track");
let prev_btn = document.querySelector(".prev-track");

let seek_slider = document.querySelector(".seek_slider");
let volume_slider = document.querySelector(".volume_slider");
let curr_time = document.querySelector(".current-time");
let total_duration = document.querySelector(".total-duration");

// Specify globally used values
let track_index = 0;
let isPlaying = false;
let updateTimer;

// Create the audio element for the player
let curr_track = document.createElement('audio');


// Define the list of tracks that have to be played

let track_list = [
{
	name: "Jawl Phoring",
	artist: "Anupam Roy",
	image: "data:image/jpeg;base64,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",
    path: "",

	name: "Boba Tunnel",
	artist: "Anupam Roy",
    image: "anupam roy.jpg",
	path: "Boba Tunnel.mp3"
},
{
	name: "Shipping Lanes",
	artist: "Chad Crouch",
	image: "anupam roy.jpg",
	path: "Shipping_Lanes.mp3",
},
];

function loadTrack(track_index) {
    // Clear the previous seek timer
    clearInterval(updateTimer);
    resetValues();
    
    // Load a new track
    curr_track.src = track_list[track_index].path;
    curr_track.load();
    
    // Update details of the track
    track_art.style.backgroundImage =
        "url(" + track_list[track_index].image + ")";
    track_name.textContent = track_list[track_index].name;
    track_artist.textContent = track_list[track_index].artist;
    now_playing.textContent =
        "PLAYING " + (track_index + 1) + " OF " + track_list.length;
    
    // Set an interval of 1000 milliseconds
    // for updating the seek slider
    updateTimer = setInterval(seekUpdate, 1000);
    
    // Move to the next track if the current finishes playing
    // using the 'ended' event
    curr_track.addEventListener("ended", nextTrack);
    
    // Apply a random background color
    random_bg_color();
    }
    
    function random_bg_color() {
    // Get a random number between 64 to 256
    // (for getting lighter colors)
    let red = Math.floor(Math.random() * 256) + 64;
    let green = Math.floor(Math.random() * 256) + 64;
    let blue = Math.floor(Math.random() * 256) + 64;
    
    // Construct a color with the given values
    let bgColor = "rgb(" + red + ", " + green + ", " + blue + ")";
    
    // Set the background to the new color
    document.body.style.background = bgColor;
    }
    
    // Function to reset all values to their default
    function resetValues() {
    curr_time.textContent = "00:00";
    total_duration.textContent = "00:00";
    seek_slider.value = 0;
    }

    function playpauseTrack() {
        // Switch between playing and pausing
        // depending on the current state
        if (!isPlaying) playTrack();
        else pauseTrack();
        }
        
        function playTrack() {
        // Play the loaded track
        curr_track.play();
        isPlaying = true;
        
        // Replace icon with the pause icon
        playpause_btn.innerHTML = '<i class="fa fa-pause-circle fa-5x"></i>';
        }
        
        function pauseTrack() {
        // Pause the loaded track
        curr_track.pause();
        isPlaying = false;
        
        // Replace icon with the play icon
        playpause_btn.innerHTML = '<i class="fa fa-play-circle fa-5x"></i>';
        }
        
        function nextTrack() {
        // Go back to the first track if the
        // current one is the last in the track list
        if (track_index < track_list.length - 1)
            track_index += 1;
        else track_index = 0;
        
        // Load and play the new track
        loadTrack(track_index);
        playTrack();
        }
        
        function prevTrack() {
        // Go back to the last track if the
        // current one is the first in the track list
        if (track_index > 0)
            track_index -= 1;
        else track_index = track_list.length - 1;
        
        // Load and play the new track
        loadTrack(track_index);
        playTrack();
        }

        function seekTo() {
            // Calculate the seek position by the
            // percentage of the seek slider
            // and get the relative duration to the track
            seekto = curr_track.duration * (seek_slider.value / 100);
            
            // Set the current track position to the calculated seek position
            curr_track.currentTime = seekto;
            }
            
            function setVolume() {
            // Set the volume according to the
            // percentage of the volume slider set
            curr_track.volume = volume_slider.value / 100;
            }
            
            function seekUpdate() {
            let seekPosition = 0;
            
            // Check if the current track duration is a legible number
            if (!isNaN(curr_track.duration)) {
                seekPosition = curr_track.currentTime * (100 / curr_track.duration);
                seek_slider.value = seekPosition;
            
                // Calculate the time left and the total duration
                let currentMinutes = Math.floor(curr_track.currentTime / 60);
                let currentSeconds = Math.floor(curr_track.currentTime - currentMinutes * 60);
                let durationMinutes = Math.floor(curr_track.duration / 60);
                let durationSeconds = Math.floor(curr_track.duration - durationMinutes * 60);
            
                // Add a zero to the single digit time values
                if (currentSeconds < 10) { currentSeconds = "0" + currentSeconds; }
                if (durationSeconds < 10) { durationSeconds = "0" + durationSeconds; }
                if (currentMinutes < 10) { currentMinutes = "0" + currentMinutes; }
                if (durationMinutes < 10) { durationMinutes = "0" + durationMinutes; }
            
                // Display the updated duration
                curr_time.textContent = currentMinutes + ":" + currentSeconds;
                total_duration.textContent = durationMinutes + ":" + durationSeconds;
            }
            }

            // Load the first track in the tracklist
loadTrack(track_index);

</script>
<style>
	body {
		background-color: lightgreen;
		
		/* Smoothly transition the background color */
		transition: background-color .2s;
		}
		
		/* Using flex with the column direction to
		align items in a vertical direction */
		.player {
		height: 95vh;
		display: flex;
		align-items: center;
		flex-direction: column;
		justify-content: center;
		}
		
		.details {
		display: flex;
		align-items: center;
		flex-direction: column;
		justify-content: center;
		margin-top: 25px;
		}
		
		.track-art {
		margin: 25px;
		height: 250px;
		width: 250px;
		background-image:(
			"../Music Player/anupam roy.jpg");
		background-size: cover;
		background-position: center;
		border-radius: 15%;
		}
		
		/* Changing the font sizes to suitable ones */
		.now-playing {
		font-size: 1rem;
		}
		
		.track-name {
		font-size: 3rem;
		}
		
		.track-artist {
		font-size: 1.5rem;
		}
		
		/* Using flex with the row direction to
		align items in a horizontal direction */
		.buttons {
		display: flex;
		flex-direction: row;
		align-items: center;
		}
		
		.playpause-track,
		.prev-track,
		.next-track {
		padding: 25px;
		opacity: 0.8;
		
		/* Smoothly transition the opacity */
		transition: opacity .2s;
		}
		
		/* Change the opacity when mouse is hovered */
		.playpause-track:hover,
		.prev-track:hover,
		.next-track:hover {
		opacity: 1.0;
		}
		
		/* Define the slider width so that it scales properly */
		.slider_container {
		width: 75%;
		max-width: 400px;
		display: flex;
		justify-content: center;
		align-items: center;
		}
		
		/* Modify the appearance of the slider */
		.seek_slider, .volume_slider {
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;
		height: 5px;
		background: black;
		opacity: 0.7;
		-webkit-transition: .2s;
		transition: opacity .2s;
		}
		
		/* Modify the appearance of the slider thumb */
		.seek_slider::-webkit-slider-thumb,
		.volume_slider::-webkit-slider-thumb {
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;
		width: 15px;
		height: 15px;
		background: white;
		cursor: pointer;
		border-radius: 50%;
		}
		
		/* Change the opacity when mouse is hovered */
		.seek_slider:hover,
		.volume_slider:hover {
		opacity: 1.0;
		}
		
		.seek_slider {
		width: 60%;
		}
		
		.volume_slider {
		width: 30%;
		}
		
		.current-time,
		.total-duration {
		padding: 10px;
		}
		
		i.fa-volume-down,
		i.fa-volume-up {
		padding: 10px;
		}
		
		/* Change the mouse cursor to a pointer
		when hovered over */
		i.fa-play-circle,
		i.fa-pause-circle,
		i.fa-step-forward,
		i.fa-step-backward {
		cursor: pointer;
		}
		
</style>
</body>
</html>
