<template>
	<div id="app">
		<div id="content">
			<canvas
				id="canvas"
				ref="canvasEl"
				@mousemove="onMouseMoveFn"
				@mousedown="onMouseDownFn"
				@mouseup="onMouseUpFn"
				@contextmenu.prevent
			></canvas>
			<div class="handle-container">
				<div class="brush">
					<h3>Brush size {{ lineWidth }}</h3>
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
				<ul class="color-list" v-if="colors.length">
					<li
						class="color"
						v-for="(item, index) in colors"
						:key="index"
						:style="{ 'background-color': item || '#000' }"
						:class="{
							active: colorActive === index,
							white: item === '#ffffff',
						}"
						@click="colorChange(item, index)"
					>
						<i class="icon"></i>
					</li>
				</ul>
				<div class="color-edit">
					<h3>Edit Color</h3>
					<form @submit.prevent="addColorFn">
						<input type="text" placeholder="ex) #f1f1f1" v-model="addColor" />
						<button type="submit">컬러추가</button>
						<button type="button" @click="onColorDelete">
							선택된 컬러삭제
						</button>
					</form>
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
					<a
						href="javascript:void(0);"
						class="btn btn-download"
						@click="onSaveBtn"
						>Download</a
					>
					<button type="button" class="btn btn-clear" @click="onResetFn">
						Reset
					</button>
				</div>
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
			colorIdx: 0,
			canvasStyle: {
				width: 1000,
				height: 700,
				color: '#222',
			},
			lineWidth: 2,
			colors: [
				'#222222',
				'#ffffff',
				'#ff0000',
				'#000cff',
				'#00ff2a',
				'#fff000',
				'#ff8400',
			],
			addColor: '',
		};
	},
	methods: {
		canvasInit(el) {
			el.strokeStyle = this.canvasStyle.color;
			el.fillStyle = '#ffffff';
			this.ctx.fillRect(0, 0, this.canvasStyle.width, this.canvasStyle.height);
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
			this.lineWidth = value;
			this.ctx.lineWidth = value;
		},
		colorChange(color, index) {
			this.colorActive = index;
			this.ctx.strokeStyle = color;
			this.ctx.fillStyle = color;
			this.colorIdx = index;
		},
		onFillActive() {
			this.isFill = !this.isFill;
		},
		onResetFn() {
			this.ctx.clearRect(0, 0, this.canvasStyle.width, this.canvasStyle.height);
		},
		addColorFn() {
			this.colors.push(this.addColor);
			this.addColor = '';
		},
		onColorDelete() {
			this.colors.splice(this.colorIdx, 1);
			if (this.colorIdx !== 0) {
				this.colorChange(this.colors[this.colorIdx - 1], this.colorIdx - 1);
			} else {
				this.colorChange(this.colors[this.colorIdx], this.colorIdx);
			}
		},
		onSaveBtn() {
			const canvas = this.$refs.canvasEl;
			const imageUrl = canvas.toDataURL('image/png');
			const link = document.createElement('a');
			link.href = imageUrl;
			link.download = 'paint';
			link.click();
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
	width: 1000px;
	height: 700px;
	margin: 40px auto 0;
	box-shadow: 0px 0px 7px 1px #999;
	background-color: #fff;
}
.handle-container {
	width: 700px;
	margin: 20px auto;
	text-align: center;
	h3 {
		margin-bottom: 7px;
	}
}
.color-list {
	display: flex;
	justify-content: center;
	flex-wrap: wrap;
	width: 700px;
	margin: 20px auto;
	li {
		position: relative;
		width: 30px;
		height: 30px;
		margin-right: 10px;
		margin-top: 5px;
		border-radius: 50%;
		cursor: pointer;
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
		&.white {
			border: 1px solid #999;
		}
	}
}
.color-edit {
	input {
		display: inline-block;
		vertical-align: middle;
		height: 30px;
		padding: 0 10px;
		border: 1px solid #ddd;
		box-sizing: border-box;
	}
	button {
		display: inline-block;
		vertical-align: middle;
		min-width: 50px;
		height: 30px;
		margin-left: 5px;
		line-height: 28px;
		border: 0;
		color: #fff;
		background-color: #222;
	}
}
.btn-group {
	margin-top: 10px;
	.btn {
		display: inline-block;
		padding: 10px 20px;
		border: 1px solid #ddd;
		border-radius: 8px;
		font-size: 15px;
		color: #222;
		background-color: #fff;
		cursor: pointer;
		+ .btn {
			margin-left: 10px;
		}
	}
	.btn-fill {
		&.active {
			color: #fff;
			background-color: #000;
		}
	}
}
</style>
