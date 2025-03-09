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
            <p>Hello to today's meditation</p>
            <ion-item>
                <ion-label>Timer (minutes)</ion-label>
                <ion-range v-model="selectedTimer" min="0" max="30" step="1" snaps="true" ticks="true">
                    <ion-label slot="start">0</ion-label>
                    <ion-label slot="end">30</ion-label>
                </ion-range>
            </ion-item>
            <div>
                <ion-item>
                    <ion-label>Heart Chakra Bell</ion-label>
                    <ion-radio v-model="selectedBellSound" value="bell-hit-heart-chakra-4.mp3"></ion-radio>
                    <ion-button @click="previewBellSound('bell-hit-heart-chakra-4.mp3')">Preview</ion-button>
                    <ion-badge v-if="selectedBellSound === 'bell-hit-heart-chakra-4.mp3'" color="primary">Selected</ion-badge>
                </ion-item>
                <ion-item>
                    <ion-label>Bell A</ion-label>
                    <ion-radio v-model="selectedBellSound" value="bell-a.mp3"></ion-radio>
                    <ion-button @click="previewBellSound('bell-a.mp3')">Preview</ion-button>
                    <ion-badge v-if="selectedBellSound === 'bell-a.mp3'" color="primary">Selected</ion-badge>
                </ion-item>
                <ion-item>
                    <ion-label>Copper Bell Ding</ion-label>
                    <ion-radio v-model="selectedBellSound" value="copper-bell-ding-22.mp3"></ion-radio>
                    <ion-button @click="previewBellSound('copper-bell-ding-22.mp3')">Preview</ion-button>
                    <ion-badge v-if="selectedBellSound === 'copper-bell-ding-22.mp3'" color="primary">Selected</ion-badge>
                </ion-item>
            </div>
            <ion-item-divider></ion-item-divider>
            <div>
                <ion-item>
                    <ion-label>Deep Meditation</ion-label>
                    <ion-radio v-model="selectedSound" value="deep"></ion-radio>
                    <ion-button @click="previewSound('deep')">Preview</ion-button>
                    <ion-badge v-if="selectedSound === 'deep'" color="primary">Selected</ion-badge>
                </ion-item>
                <ion-item>
                    <ion-label>Forest - Calming forest ambiance</ion-label>
                    <ion-radio v-model="selectedSound" value="forest"></ion-radio>
                    <ion-button @click="previewSound('forest')">Preview</ion-button>
                    <ion-badge v-if="selectedSound === 'forest'" color="primary">Selected</ion-badge>
                </ion-item>
                <ion-item>
                    <ion-label>Waves - Relaxing ocean waves</ion-label>
                    <ion-radio v-model="selectedSound" value="waves"></ion-radio>
                    <ion-button @click="previewSound('waves')">Preview</ion-button>
                    <ion-badge v-if="selectedSound === 'waves'" color="primary">Selected</ion-badge>
                </ion-item>
                <ion-item>
                    <ion-label>Nature - Peaceful nature sounds</ion-label>
                    <ion-radio v-model="selectedSound" value="nature"></ion-radio>
                    <ion-button @click="previewSound('nature')">Preview</ion-button>
                    <ion-badge v-if="selectedSound === 'nature'" color="primary">Selected</ion-badge>
                </ion-item>
                <ion-item>
                    <ion-label>Relaxing - Gentle relaxing music</ion-label>
                    <ion-radio v-model="selectedSound" value="relaxing"></ion-radio>
                    <ion-button @click="previewSound('relaxing')">Preview</ion-button>
                    <ion-badge v-if="selectedSound === 'relaxing'" color="primary">Selected</ion-badge>
                </ion-item>
            </div>
            <ion-button @click="startMeditation">Start Meditation</ion-button>
            <ion-button @click="stopMeditation" color="danger" :disabled="!isMeditationActive">Stop Meditation</ion-button>
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
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonItem, IonLabel, IonSelect, IonSelectOption, IonCheckbox, IonButton, IonRange, IonRadio, IonItemDivider, IonBadge } from '@ionic/vue';
import { ref, computed, onMounted } from 'vue';

const selectedTimer = ref<number>(4);
const selectedSound = ref<string>('deep');
const selectedBellSound = ref<string>('bell-hit-heart-chakra-4.mp3');
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

const startMeditation = () => {
    if (selectedTimer.value === null || selectedSound.value === null) return;
    remainingTime.value = selectedTimer.value * 60;
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
    bellIntervalId = setInterval(playBellSound, 60000) as unknown as number;
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
        case 'deep':
            audioSrc = '/assets/sounds/meditation-deep.mp3';
            break;
        case 'forest':
            audioSrc = '/assets/sounds/meditation-music-256141.mp3';
            break;
        case 'waves':
            audioSrc = '/assets/sounds/meditation-music-289149.mp3';
            break;
        case 'nature':
            audioSrc = '/assets/sounds/meditation-music-without-nature-sound-256142.mp3';
            break;
        case 'relaxing':
            audioSrc = '/assets/sounds/meditation-relaxing-music-293922.mp3';
            break;
    }
    currentAudio = new Audio(audioSrc);
    currentAudio.loop = true;
    currentAudio.play();
};

const playBellSound = () => {
    const audio = new Audio(`/assets/sounds/${selectedBellSound.value}`);
    audio.play();
};

const previewBellSound = (sound: string) => {
    if (previewBellAudio !== null) {
        previewBellAudio.pause();
        previewBellAudio.currentTime = 0;
    }
    previewBellAudio = new Audio(`/assets/sounds/${sound}`);
    previewBellAudio.play();
    setTimeout(() => {
        if (previewBellAudio !== null) {
            previewBellAudio.pause();
            previewBellAudio.currentTime = 0;
        }
    }, 5000);
};

const previewSound = (sound: string) => {
    if (previewAudio !== null) {
        previewAudio.pause();
        previewAudio.currentTime = 0;
    }
    let audioSrc = '';
    switch (sound) {
        case 'deep':
            audioSrc = '/assets/sounds/meditation-deep.mp3';
            break;
        case 'forest':
            audioSrc = '/assets/sounds/meditation-music-256141.mp3';
            break;
        case 'waves':
            audioSrc = '/assets/sounds/meditation-music-289149.mp3';
            break;
        case 'nature':
            audioSrc = '/assets/sounds/meditation-music-without-nature-sound-256142.mp3';
            break;
        case 'relaxing':
            audioSrc = '/assets/sounds/meditation-relaxing-music-293922.mp3';
            break;
    }
    previewAudio = new Audio(audioSrc);
    previewAudio.play();
    setTimeout(() => {
        if (previewAudio !== null) {
            previewAudio.pause();
            previewAudio.currentTime = 0;
        }
    }, 10000);
};

onMounted(() => {
    selectedSound.value = 'deep';
    selectedBellSound.value = 'bell-hit-heart-chakra-4.mp3';
});
</script>
