<template>
    <div id="app">
        <Clock/>
    </div>
</template>

<script>
import Clock from './components/clock.vue'

export default {
	components: {
		Clock
	}    

}
</script>

<style lang="scss">
*, *::before, *::after {
	box-sizing: border-box;
}

#app {
	//max-width: 400px;
	margin: 0 auto;
	line-height: 1.4;
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

</style>