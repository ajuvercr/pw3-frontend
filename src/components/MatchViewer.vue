<template>
    <canvas ref="canvas"/>
    <input
        type="range"
        min="0"
        v-bind:max="sliderMax"
        step="1"
        v-model="turnNum"
    />
</template>

<script lang="ts">
import * as visualizer from '../visualizer';

function parseMatchLog(entries) {
    return entries.map(e => JSON.parse(e));
}

export default {
    name: 'MatchViewer',
    data() {
        return {
            loading: true,
            error: false,
            matchData: null,
            turnNum: 0,
            sliderMax: 0,
        }
    },
    created() {
        this.fetchData();
    },
    mounted() {
        this.visualizer = new visualizer.Visualizer(this.$refs.canvas);
    },
    updated() {
        this.renderScene();
    },
    methods: {
        fetchData() {
            this.loading = true;
            this.error = false;
            this.matchData = null;
            const matchId = this.$route.params.id

            const a = this;
            fetch(`/api/matches/${matchId}`)
                .then(resp => resp.json())
                .then(data => {
                    this.matchData = parseMatchLog(data);
                    this.sliderMax = this.matchData.length - 1;
                    this.loading = false;
                    this.renderScene();
                })
                .catch(err => { throw(err) });
        },

        renderScene() {
            const state = this.matchData[this.turnNum];
            this.visualizer.render(state);
        }
    }
}
</script>