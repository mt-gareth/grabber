<template>
	<div id="app">
		<Grabber/>
	</div>
</template>

<script>
    //import HelloWorld from './components/HelloWorld.vue'
    import Grabber from './components/Grabber.vue'
    //import 'vue-slider-component/theme/antd.css'
    import "normalize.css"

    export default {
        name: 'App',
        components: {
            //HelloWorld,
            Grabber,
        }
    }
</script>

<style>
	#app {
		font-family:Avenir, Helvetica, Arial, sans-serif;
		-webkit-font-smoothing:antialiased;
		-moz-osx-font-smoothing:grayscale;
		color:#2d3242;
		display:flex;
		justify-content:center;
		align-items:center;
		height:100%;
		width:100%;
		left:0;
		top:0;
		overflow:hidden;
		position:fixed;
	}
</style>
