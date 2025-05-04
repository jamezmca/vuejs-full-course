<script setup>
    import { ref, computed } from 'vue'
    import Portal from '../Portal.vue'
    import { workoutProgram, exerciseDescriptions } from '../../utils'

    const workoutType = ['Push', "Pull", 'Legs']

    const { data, selectedWorkout } = defineProps({
        data: Object,
        selectedWorkout: Number,
        handleSaveWorkout: Function,
        isWorkoutComplete: Boolean
    })

    const { workout, warmup } = workoutProgram[selectedWorkout]
    // let selectedExercise = null // this wont work because we need stateful variables so that the ui knows it needs to rerender to reflect any changes in the javascript
    let selectedExercise = ref(null)
    const exerciseDescription = computed(() => exerciseDescriptions[selectedExercise.value])  
    

    function handleCloseModal() {
        selectedExercise.value = null
    }
</script>

<template>
    <Portal hello="world" :handleCloseModal="handleCloseModal"  v-if="selectedExercise">
        <div class="exercise-description">
            <h3>{{ selectedExercise }}</h3>
            <div>
                <small>Description</small>
                <p>{{ exerciseDescription }}</p>
            </div>
            <button @click="handleCloseModal">Close <i class="fa-solid fa-xmark"></i></button>
        </div>
    </Portal>
  <section id="workout-card">
    <div class="plan-card card">
        <div class="plan-card-header">
            <p>Day {{ selectedWorkout < 9 ? '0' + (selectedWorkout + 1) : (selectedWorkout + 1) }}</p>
            <i class="fa-solid fa-dumbbell"></i>
        </div>
        <h2>{{ workoutType[selectedWorkout % 3] }} Workout</h2>
    </div>
    <div class="workout-grid">
        <h4 class="grid-name">Warmup</h4>
        <h6 >Sets</h6>
        <h6>Reps</h6>
        <h6 class="grid-weights">Weights</h6>
        <div class="workout-grid-row" v-for="(w, wIdx) in warmup" :key="wIdx">
            <div class="grid-name">
                <p>{{ w.name }}</p>
                <button @click="() => {
                    selectedExercise = w.name
                }">
                    <i class="fa-regular fa-circle-question"></i>
                </button>
            </div>
            <p>{{ w.sets }}</p>
            <p>{{ w.reps }}</p>
            <input class="grid-weights" placeholder="14kg" type="text" disabled/>
        </div>
        <div class="workout-grid-line"></div>
        <h4 class="grid-name">Workout</h4>
        <h6 >Sets</h6>
        <h6>Reps</h6>
        <h6 class="grid-weights">Weights</h6>
        <div class="workout-grid-row" v-for="(w, wIdx) in workout" :key="wIdx">
            <div class="grid-name">
                <p>{{ w.name }}</p>
                <button @click="() => {
                    selectedExercise = w.name
                }">
                    <i class="fa-regular fa-circle-question"></i>
                </button>
            </div>
            <p>{{ w.sets }}</p>
            <p>{{ w.reps }}</p>
            <input v-model="data[selectedWorkout][w.name]" class="grid-weights" placeholder="14kg" type="text"/>
        </div>
    </div>
    <div class="card workout-btns">
        <button @click="handleSaveWorkout">Save & Exit <i class="fa-solid fa-save"></i></button>
        <button :disabled="!isWorkoutComplete" @click="handleSaveWorkout">Complete <i class="fa-solid fa-check"></i></button>
    </div>
  </section>
</template>

<style scoped>
    #workout-card,
    .plan-card {
        display: flex;
        flex-direction: column;
    }

    #workout-card {
        gap: 1.5rem;
    }

    .plan-card-header,
    .workout-btns {
        display: flex;
        align-items: center;
        justify-content: space-between;
        gap: 1rem;
    }

    .workout-grid,
    .workout-grid-row {
        display: grid;
        grid-template-columns: repeat(7, minmax(0, 1fr));
        gap: 1rem;
    }

    .workout-grid-row,
    .workout-grid-line {
        grid-column: span 7 / span 7;
    }

    .workout-grid-line {
        margin: 0.5rem 0;
        height: 3px;
        border-radius: 2px;
        background: var(--background-muted);
    }

    .grid-name {
        grid-column: span 3 / span 3;
        display: flex;
        align-items: center;
        gap: 1rem;
    }

    .grid-name button {
        padding: 0;
        border: none;
        box-shadow: none;
    }

    .grid-name button:hover {
        transform: none;
        box-shadow: none;
        color: var(--color-link);
    }

    .workout-grid-row .grid-name button {
        opacity: 0;
        pointer-events: none;
    }

    .workout-grid-row:hover .grid-name button {
        opacity: 1;
        pointer-events: all;
    }

    .grid-name p {
        text-transform: capitalize;
    }

    .grid-weights {
        grid-column: span 2 / span 2;
    }

    .workout-btns button {
        flex: 1;
    }

    .workout-btns button i {
        padding-left: 0.5rem;
    }

    .exercise-description {
        display: flex;
        flex-direction: column;
        gap: 1rem;
        width: 100%;
    }

    .exercise-description h3 {
        text-transform: capitalize;
    }

    .exercise-description button i {
        padding-left: 0.5rem;
    }
</style>
