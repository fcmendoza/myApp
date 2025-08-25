<template>
    <ion-page>
        <ion-header>
            <ion-toolbar>
                <ion-title>Meditation</ion-title>
            </ion-toolbar>
        </ion-header>
        <ion-content :fullscreen="true">
            <ion-header collapse="condense">
                <ion-toolbar>
                    <ion-title size="large">Meditation</ion-title>
                </ion-toolbar>
            </ion-header>
            <!-- <p>Hello to today's meditation</p> -->
            <ion-item>
                <ion-label>Duration d4849</ion-label>
                <ion-range v-model="selectedTimer" min="0" max="20" step="1" snaps="true" ticks="true">
                    <ion-label slot="start">0</ion-label>
                    <ion-label slot="end">20</ion-label>
                </ion-range>
                <ion-note slot="helper">{{ selectedTimer }} minutes</ion-note>
            </ion-item>
            <ion-item>
                <ion-label>Meditation duration: {{ selectedTimer }} minute(s)</ion-label>
            </ion-item>
            <ion-item>
                <ion-label>Bell Interval</ion-label>
                <ion-range v-model="selectedBellInterval" min="0.25" max="4" step="0.25" snaps="true" ticks="true">
                    <ion-label slot="start">15 sec</ion-label>
                    <ion-label slot="end">4 min</ion-label>
                </ion-range>
                <ion-note slot="helper">{{ formattedBellInterval }}</ion-note>
            </ion-item>
            <ion-item>
                <ion-label>Selected Bell Interval: {{ formattedBellInterval }}</ion-label>
            </ion-item>
            <ion-item>
                <ion-label>Bell Sound</ion-label>
                <ion-segment v-model="selectedBellSound" @ionChange="playBellSound">
                    <ion-segment-button value="bell-hit-heart-chakra-4.mp3" @click="previewBellSound('bell-hit-heart-chakra-4.mp3')">
                        <ion-label>Heart Chakra Bell</ion-label>
                    </ion-segment-button>
                    <ion-segment-button value="copper-bell-ding-22.mp3" @click="previewBellSound('copper-bell-ding-22.mp3')">
                        <ion-label>Copper Bell Ding</ion-label>
                    </ion-segment-button>
                </ion-segment>
            </ion-item>
            <ion-item-divider></ion-item-divider>
            <ion-item>
                <ion-label>Music: </ion-label>
                <ion-segment v-model="selectedSound" @ionChange="changeBackgroundMusic">
                    <ion-segment-button value="therapy">
                        <ion-label>Therapy</ion-label>
                    </ion-segment-button>
                    <ion-segment-button value="deep">
                        <ion-label>Deep</ion-label></ion-segment-button>
                    <ion-segment-button value="nature">
                        <ion-label>Peaceful</ion-label></ion-segment-button>
                </ion-segment>
            </ion-item>
            <ion-item-divider></ion-item-divider>
            <div style="text-align: center;">
                <ion-button @click="startMeditation" :disabled="isMeditationActive">
                    <ion-icon name="play"></ion-icon>
                    Start
                </ion-button>
                <ion-button @click="stopMeditation" color="danger" :disabled="!isMeditationActive">
                    <ion-icon name="stop"></ion-icon>
                    Stop
                </ion-button>
            </div>
            <div v-if="remainingTime !== null">
                <p class="countdown">{{ formattedTime }}</p>
            </div>
        </ion-content>
    </ion-page>
</template>

<style>
.countdown {
    font-size: 3em;
    text-align: center;
    margin-top: 20px;
}
</style>

<script setup lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonItem, IonLabel, IonSelect, IonSelectOption, IonCheckbox, IonButton, IonRange, IonRadio, IonItemDivider, IonBadge, IonIcon, IonSegment, IonSegmentButton, IonNote } from '@ionic/vue';
import { play, stop } from 'ionicons/icons';
import { ref, computed, onMounted } from 'vue';
import { addIcons } from 'ionicons';

// Register icons
addIcons({
  'play': play,
  'stop': stop
});

const selectedTimer = ref<number>(4);
const selectedSound = ref<string>('deep');
const selectedBellSound = ref<string>('bell-hit-heart-chakra-4.mp3');
const selectedBellInterval = ref<number>(1);
const remainingTime = ref<number | null>(null);
let intervalId: number | null = null;
let bellIntervalId: number | null = null;
let currentAudio: HTMLAudioElement | null = null;
let previewAudio: HTMLAudioElement | null = null;
let previewBellAudio: HTMLAudioElement | null = null;
const isMeditationActive = ref<boolean>(false);

const formattedTime = computed(() => {
    if (remainingTime.value === null) return '';
    const minutes = Math.floor(remainingTime.value / 60);
    const seconds = remainingTime.value % 60;
    return `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
});

const formattedBellInterval = computed(() => {
    const minutes = Math.floor(selectedBellInterval.value);
    const seconds = (selectedBellInterval.value % 1) * 60;
    return `${minutes > 0 ? minutes + ' min ' : ''}${seconds > 0 ? seconds + ' sec' : ''}`.trim();
});

const startMeditation = () => {
    if (selectedTimer.value === null || selectedSound.value === null) return;
    remainingTime.value = (selectedTimer.value * 60);
    playSound(selectedSound.value);
    intervalId = setInterval(() => {
        if (remainingTime.value !== null) {
            remainingTime.value--;
            if (remainingTime.value <= 0) {
                clearInterval(intervalId as number);
                clearInterval(bellIntervalId as number);
                stopMeditation();
            }
        }
    }, 1000) as unknown as number;
    bellIntervalId = setInterval(playBellSound, selectedBellInterval.value * 60000) as unknown as number;
    isMeditationActive.value = true;
};

const stopMeditation = () => {
    if (intervalId !== null) {
        clearInterval(intervalId);
        intervalId = null;
    }
    if (bellIntervalId !== null) {
        clearInterval(bellIntervalId);
        bellIntervalId = null;
    }
    if (currentAudio !== null) {
        currentAudio.pause();
        currentAudio.currentTime = 0;
        currentAudio = null;
    }
    remainingTime.value = null;
    isMeditationActive.value = false;
};

const playSound = (sound: string) => {
    let audioSrc = '';
    switch (sound) {
        case 'deepx':
            audioSrc = './assets/sounds/meditation-deep.mp3';
            break;
        case 'forest':
            audioSrc = './assets/sounds/meditation-music-256141.mp3';
            break;
        case 'deep':
            audioSrc = './assets/sounds/meditation-music-289149.mp3';
            break;
        case 'nature':
            audioSrc = './assets/sounds/meditation-music-without-nature-sound-256142.mp3';
            break;
        case 'relaxing':
            audioSrc = './assets/sounds/meditation-relaxing-music-293922.mp3';
            break;
        case 'therapy':
            audioSrc = './assets/sounds/session_sound_20_minutes.mp3';
            break;
    }
    currentAudio = new Audio(audioSrc);
    currentAudio.loop = true;
    currentAudio.play();
};

const playBellSound = () => {
    if (selectedBellSound.value) {
        const audio = new Audio(`./assets/sounds/${selectedBellSound.value}`);
        audio.play();
    }
};

const previewBellSound = (sound: string) => {
    if (previewBellAudio !== null) {
        previewBellAudio.pause();
        previewBellAudio.currentTime = 0;
    }
    previewBellAudio = new Audio(`./assets/sounds/${sound}`);
    previewBellAudio.play();
    setTimeout(() => {
        if (previewBellAudio !== null) {
            previewBellAudio.pause();
            previewBellAudio.currentTime = 0;
        }
    }, 10000);
};

const changeBackgroundMusic = () => {
    if (currentAudio !== null) {
        currentAudio.pause();
        currentAudio.currentTime = 0;
    }
    if (selectedSound.value) {
        playSound(selectedSound.value);
    }
};

onMounted(() => {
    selectedSound.value = 'deep';
    selectedBellSound.value = 'bell-hit-heart-chakra-4.mp3';
    selectedBellInterval.value = 0.25; // Set Bell Interval to 15 seconds (0.25 min) by default
});
</script>
