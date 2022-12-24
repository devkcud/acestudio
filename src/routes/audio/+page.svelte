<script lang="ts">
    import { onMount } from 'svelte';

    let playButton: HTMLInputElement;
    let audioElement: HTMLAudioElement;

    /* Utils */
    function pauseSong() {
        audioElement.pause();
        playButton.value = 'Play';
    }

    /* Controls */
    export function playSong() {
        if (audioElement.paused) {
            audioElement.play();
            playButton.value = 'Pause';
        } else pauseSong();
    }

    export function stopSong() {
        pauseSong();
        audioElement.currentTime = 0; // Reset the audio time
    }

    export function changeVolume() {
        const element = document.getElementById('volume-slider') as HTMLInputElement;
        audioElement.volume = element.valueAsNumber / 100;
    }

    /* After render */
    onMount(() => {
        playButton = document.getElementById('play-button') as HTMLInputElement;
        audioElement = document.getElementById('audio') as HTMLAudioElement;
    });
</script>

<svelte:head>
    <title>Hello, world!</title>
</svelte:head>

<audio id="audio" src="/src/assets/daftpunk_song.mp3" />

<!-- Audio Controls -->
<input id="play-button" type="button" on:click={playSong} value="Play" />
<input id="stop-button" type="button" on:click={stopSong} value="Stop" />

<!-- TODO: make a slider to control playback -->
<input id="volume-slider" type="range" max="100" value="100" step="1" on:input={changeVolume} />
