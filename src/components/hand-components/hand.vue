<template>
	<div :class="{'hand-looser': isLooser}" class="hand">
		<template v-if="isGameRunning">
			<div class="lds-ellipsis"><div></div><div></div><div></div><div></div></div>
		</template>
		<template v-else>
			<img class="handImg" :class="'handImg-' + which" :src="createImageSrc" alt="">
		</template>
	</div>
</template>

<script>
	export default {
		name: 'hand',
		props: {
			'bus': {
				type: Object,
				required: true,
			},
			'which': {
				type: String,
				required: true,
			},
			'isGameRunning': {
				type: Boolean,
				required: true,
			},
		},
		methods: {
			newHand() {
				const randomNumber = Math.floor(Math.random() * 3) + 1;
				this.shape = this.shapes[randomNumber - 1];
			},
			onRoundFinished(looser) {
				if (looser.length === 0) {
					this.isLooser = false;
				}
				else {
					if (looser === this.which) {
						this.isLooser = true;
					} else {
						this.isLooser = false;
					}
				}
			},
			resetHand() {
				this.isLooser = false;
				this.shape = '';
			}
		},
		computed: {
			createImageSrc() {
				if (this.shape.length === 0) {
					return `${process.env.VUE_APP_IMAGES_PATH}/racing-flag.png`;
				} else {
					return `${process.env.VUE_APP_IMAGES_PATH}/${this.shape}.png`;
				}
			}
		},
		watch: {
			isGameRunning(newVal, oldVal) {
				if (newVal) {
					this.newHand();
				} else {
					this.$emit('handChoosed', [this.which, this.shape]);
				}
			},
		},
		mounted() {
			this.bus.$on('roundFinished', this.onRoundFinished);
			this.bus.$on('roundStarted', this.resetHand);
			this.bus.$on('resetHand', this.resetHand);
		},
		data() {
			return {
				shape: '',
				shapes: [
					'rock',
					'paper',
					'scissors',
				],
				isLooser: false,
			};
		}
	}
</script>