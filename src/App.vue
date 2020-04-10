<template>
	<div id="app">
		<div id="content">
			<canvas
				id="canvas"
				ref="canvasEl"
				@mousemove="onMouseMoveFn"
				@mousedown="onMouseDownFn"
				@mouseup="onMouseUpFn"
			></canvas>
			<div class="handle-container">
				<div class="brush">
					<p>브러쉬 조절</p>
					<input
						type="range"
						class="js-range"
						:value="lineWidth"
						min="1"
						max="10"
						step="1"
						@change="onChangeBrush"
					/>
				</div>
				<div class="btn-group">
					<button
						type="button"
						class="btn btn-fill"
						:class="{ active: isFill }"
						@click="onFillActive"
					>
						Fill
					</button>
					<a href="javascript:void(0);" class="btn btn-download">Download</a>
					<button type="button" class="btn btn-clear" @click="onResetFn">
						Reset
					</button>
				</div>
				<ul class="color-list">
					<li
						class="color"
						v-for="(item, index) in colors"
						:key="index"
						:style="{ 'background-color': item }"
						:class="{ active: colorActive == index }"
						@click="colorChange(item, index)"
					>
						<i class="icon"></i>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			ctx: null,
			isDrawing: false,
			isFill: false,
			colorActive: 0,
			canvasStyle: {
				width: 700,
				height: 700,
				color: '#222',
			},
			lineWidth: 2,
			colors: ['#000', '#ff0000', '#000cff', '#00ff2a', '#fff000', '#ff8400'],
		};
	},
	methods: {
		canvasInit(el) {
			el.strokeStyle = this.canvasStyle.color;
			el.fillStyle = this.canvasStyle.color;
			el.lineWidth = this.lineWidth;
		},
		onMouseMoveFn(event) {
			const x = event.offsetX;
			const y = event.offsetY;
			if (!this.isDrawing) {
				this.ctx.beginPath();
				this.ctx.moveTo(x, y);
			} else {
				this.ctx.lineTo(x, y);
				this.ctx.stroke();
			}
		},
		onMouseDownFn() {
			this.isDrawing = true;
			if (this.isFill) {
				this.ctx.fillRect(
					0,
					0,
					this.canvasStyle.width,
					this.canvasStyle.height,
				);
			}
		},
		onMouseUpFn() {
			this.isDrawing = false;
		},
		onChangeBrush(e) {
			const value = e.target.value;
			this.ctx.lineWidth = value;
		},
		colorChange(color, index) {
			this.colorActive = index;
			this.ctx.strokeStyle = color;
			this.ctx.fillStyle = color;
		},
		onFillActive() {
			this.isFill = !this.isFill;
		},
		onResetFn() {
			this.ctx.clearRect(0, 0, this.canvasStyle.width, this.canvasStyle.height);
		},
	},
	mounted() {
		// 캔버스 초기화
		const canvas = this.$refs.canvasEl;
		const ctx = canvas.getContext('2d');
		this.ctx = ctx;
		canvas.width = this.canvasStyle.width;
		canvas.height = this.canvasStyle.height;
		this.canvasInit(this.ctx);
	},
};
</script>

<style lang="scss">
@import './assets/css/reset.css';
#canvas {
	display: block;
	width: 700px;
	height: 700px;
	margin: 40px auto 0;
	border: 1px solid #000;
	background-color: #fff;
}
.handle-container {
	width: 700px;
	margin: 40px auto;
	text-align: center;
}
.color-list {
	display: flex;
	justify-content: center;
	width: 700px;
	margin: 0 auto;
	li {
		position: relative;
		width: 30px;
		height: 30px;
		border-radius: 50%;
		cursor: pointer;
		+ li {
			margin-left: 10px;
		}
		.icon {
			display: none;
			position: absolute;
			top: 50%;
			left: 50%;
			width: 8px;
			height: 8px;
			margin-left: -4px;
			margin-top: -4px;
			background-color: #fff;
			border-radius: 50%;
			border: 1px solid #ddd;
		}
		&.active {
			.icon {
				display: block;
			}
		}
	}
}
.btn-group {
	margin: 20px;
	.btn {
		display: inline-block;
		padding: 10px 20px;
		border: 1px solid #222;
		font-size: 15px;
		color: #222;
		background-color: #fff;
		cursor: pointer;
	}
	.btn-fill {
		&.active {
			color: #fff;
			background-color: #000;
		}
	}
}
</style>
