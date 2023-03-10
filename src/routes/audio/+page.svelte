<script lang="ts">
    import { onMount } from 'svelte';

    let playButton: HTMLInputElement;
    let audioElement: HTMLAudioElement;
    let seekSlider: HTMLInputElement;
    let audioCurrentTimeText: HTMLSpanElement;
    let audioDurationText: HTMLSpanElement;

    /* Utils */
    export function pauseSong(): void {
        audioElement.pause();
        playButton.value = 'Play';
    }

    export function getDuration(totalInSeconds: number): string {
        const minutes = Math.floor(totalInSeconds / 60);
        const seconds = Math.floor(totalInSeconds % 60);

        return `${minutes}:${seconds < 10 ? `0${seconds}` : seconds}`;
    }

    export function updateCurrentTime() {
        const currentTime = Math.floor(audioElement.currentTime).toString();

        audioCurrentTimeText.textContent = getDuration(parseInt(currentTime));
        seekSlider.value = currentTime;
    }

    export function updateDuration() {
        audioDurationText.textContent = getDuration(audioElement.duration);
    }

    /* Controls */
    export function playSong(): void {
        if (audioElement.paused) {
            audioElement.play();
            playButton.value = 'Pause';
        } else pauseSong();
    }

    export function stopSong(): void {
        pauseSong();
        audioElement.currentTime = 0; // Reset the audio time
    }

    export function changeVolume(): void {
        const element = document.getElementById('volume-slider') as HTMLInputElement;
        audioElement.volume = element.valueAsNumber / 100;
    }

    export function changeCurrentTime(): void {
        audioElement.currentTime = seekSlider.valueAsNumber;
    }

    /* After render */
    onMount(() => {
        playButton = document.getElementById('play-button') as HTMLInputElement;
        audioElement = document.getElementById('audio') as HTMLAudioElement;

        audioCurrentTimeText = document.getElementById('audio-current-time')!;
        audioDurationText = document.getElementById('audio-duration')!;

        seekSlider = document.getElementById('seek-slider') as HTMLInputElement;
        seekSlider.max = Math.floor(audioElement.duration).toString();

        // Initial audio duration update
        updateDuration();
    });
</script>

<svelte:head>
    <title>Hello, world!</title>
</svelte:head>

<audio
    id="audio"
    src="/src/assets/daftpunk_song.mp3"
    on:ended={stopSong}
    on:timeupdate={updateCurrentTime}
    on:loadedmetadata={updateDuration}
/>

<!-- Audio Controls -->
<input id="play-button" type="button" on:click={playSong} value="Play" />
<input id="stop-button" type="button" on:click={stopSong} value="Stop" />

<span id="audio-current-time">0:00</span>
<input id="seek-slider" type="range" max="100" value="0" on:input={changeCurrentTime} />
<span id="audio-duration">0:00</span>

<input id="volume-slider" type="range" max="100" value="100" step="1" on:input={changeVolume} />
