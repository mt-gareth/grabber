<template>
	<div class="card-parse-video px-3 py-3">
		<div v-show="!videoLoaded" class="upload-wrapper">
			<label for="video-upload">Drag and Drop File<br>Or<br>Click To Load Video</label>
			<input id="video-upload" type="file" @change="processFile($event)">
		</div>
		<div v-show="videoLoaded" class="video-and-controls">
			<div class="video-wrapper">
				<video id="parse-video" :src="videoSrc" controls muted v-on:timeupdate="videoTimeUpdate" v-on:loadedmetadata="setCanvasSize" v-on:play="syncCanvas" v-on:seeked="syncCanvas(false)"></video>
				<div class="video-controls">
					<a id="playpause" class="control-button control-play" @click="togglePlay"></a>
					<vue-slider v-model="videoProgress" v-bind="sliderOptions"/>
					<div class="jump-frames-controls">
						<a class="control-button control-double-fast-back" v-on:click="moveTime(-frameTime * 100) "></a>
						<a class="control-button control-fast-back" v-on:click="moveTime(-frameTime * 10)"></a>
						<a class="control-button control-back" v-on:click="moveTime(-frameTime)"></a>
						<a class="control-button control-forward" v-on:click="moveTime(frameTime)"></a>
						<a class="control-button control-fast-forward" v-on:click="moveTime(frameTime * 10)"></a>
						<a class="control-button control-double-fast-forward" v-on:click="moveTime(frameTime * 100)"></a>
					</div>
				</div>
				<canvas id="video-canvas"></canvas>
			</div>
			<div class="controls-wrapper">
				<div class="controls">
					<a class="btn" id="image-download" download="screen-grab.jpg" v-on:click="downloadCanvas">Download Image</a>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
    import VueSlider from 'vue-slider-component'

    export default {
        components: {
            VueSlider
        },

        data() {
            return {
                video: null,
                canvas: null,
                download: null,
                frameTime: 1 / 60,
                videoLoaded: false,
                videoSrc: '',
                videoProgress_: 0,
                sliderOptions: {
                    width: '50%',
                    max: 100,
                    interval: 1 / 60,
                    duration: 0,
                    tooltip: 'none',
                }
            }
        },

        methods: {
            processFile(event) {
                this.videoLoaded = true;
                this.videoSrc = URL.createObjectURL(event.target.files[0]);
                this.video.load();
            },

            setCanvasSize() {
                this.canvas.width = this.video.videoWidth;
                this.canvas.height = this.video.videoHeight;
                this.sliderOptions.max = Math.floor(this.video.duration * 60) / 60;
                console.log(this.video.duration);
            },

            syncCanvas() {
                this.canvas.getContext('2d').drawImage(this.video, 0, 0);
            },

            downloadCanvas() {
                this.download.href = this.canvas.toDataURL('image/jpeg');
            },

            togglePlay() {
                if (this.video.paused) {
                    this.video.play();
                } else {
                    this.video.pause();
                }

            },

            moveTime(amount) {
                this.videoProgress += amount;
            },

            videoTimeUpdate($event) {
                this.videoProgress_ = $event.target.currentTime;
                this.syncCanvas();
            }

        },

        computed: {
            videoProgress: {
                get: ({videoProgress_}) => videoProgress_,
                set(time) {
                    this.video.currentTime = time;
                    this.syncCanvas();
                }
            }
        },

        mounted() {
            this.video = document.getElementById('parse-video');
            this.canvas = document.getElementById('video-canvas');
            this.download = document.getElementById('image-download');
        },
    }
</script>

<style lang="scss">
	$themeColor: #88A297;

	/* import theme style */
	@import '~vue-slider-component/lib/theme/default.scss';

	.card-parse-video {
		display: flex;
		height: calc(100vh - 140px);
		max-height: 600px;
		min-height: 200px;
		width: 90%;

		.upload-wrapper {
			display: flex;
			justify-content: center;
			align-items: center;
			width: 100%;

			input {
				display: none;
			}

			label {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				display: flex;
				align-items: center;
				justify-content: center;
				text-align: center;
				font-size: 36px;
				cursor: pointer;
			}
		}

		.video-and-controls {
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			width: 100%;
			max-width: 125vh;
			margin: 0 auto;

			.controls-wrapper {
				width: 100%;

				.controls {
					margin-top: 20px;
					text-align: center;
				}
			}
		}


		.video-wrapper {
			position: relative;
			width: 100%;
			max-width: 1024px;

			video {
				width: 100%;

				&::-webkit-media-controls-enclosure {
					display: none !important;
				}
			}

			.video-controls {
				position: absolute;
				bottom: 0;
				width: calc(100% - 40px);
				display: flex;
				align-items: center;
				padding: 20px;
				background: rgba(0, 0, 0, .4);

				.vue-slider {
					margin: 0 20px;
					flex-grow: 1;
				}

				.jump-frames-controls {
					display: flex;
					justify-content: space-between;
					max-width: 200px;
				}
			}

		}


		canvas {
			display: none;
		}

		.control-button {
			display: block;
			cursor: pointer;
			color: white;
			width: 16px;
			height: 15px;
			background-position: center;
			background-repeat: no-repeat;
			background-size: contain;
			margin: 4px 5px;

			&.control-play {
				background-image: url(../assets/play.svg);
			}

			&.control-double-fast-back {
				background-image: url(../assets/double-fast-back.svg);
			}

			&.control-fast-back {
				background-image: url(../assets/fast-back.svg);
			}

			&.control-back {
				background-image: url(../assets/back.svg);
			}

			&.control-forward {
				background-image: url(../assets/forward.svg);
			}

			&.control-fast-forward {
				background-image: url(../assets/fast-forward.svg);
			}

			&.control-double-fast-forward {
				background-image: url(../assets/double-fast-forward.svg);
			}
		}

		.btn {
			cursor: pointer;
			position: relative;
			display: inline-block;
			padding: .4em 2em;
			text-decoration: none;
			border: 3px solid #2d3242;
			color: #2d3242;
			font-size: 1.1em;
			transition: .2s;
			background-color: transparent;

			&:hover {
				background-color: #2d3242;
				color: #fff;
			}
		}
	}
</style>