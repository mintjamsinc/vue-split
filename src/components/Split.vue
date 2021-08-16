<!-- Copyright (c) 2021 MintJams Inc. Licensed under MIT License. -->

<template>
	<div style="position: absolute; top: 0; bottom: 0; left: 0; right: 0;" v-on:mousemove="onMouseMove" v-on:mouseup="doResizingOff" v-on:mouseleave="doResizingOff">
		<div style="position: absolute; top: 0; bottom: 0; left: 0;" :style="{'width': pane1Width, 'display': (show[0] ? 'block' : 'none')}">
			<slot name="pane1"></slot>
		</div>
		<div v-if="show[1]" style="position: absolute; top: 0; bottom: 0; right: 0;" :style="{'left': pane2Left, 'display': (show[1] ? 'block' : 'none')}">
			<slot name="pane2"></slot>
		</div>
		<div v-if="needsCover" style="position: absolute; top: 0; bottom: 0; left: 0; right: 0; z-index: 1;"></div>
		<div v-if="showAll" style="position: absolute; top: 0; bottom: 0; width: 4px; z-index: 2; cursor: col-resize;" :style="{'left': dividerLeft}" v-on:mousedown="doResizingOn"><!-- divider --></div>
	</div>
</template>

<script>
export default {
	props: {
		'size': {
			'type': String,
			default() {
				return '20%';
			}
		},
		'min': {
			'type': String,
			default() {
				return '20%';
			}
		},
		'max': {
			'type': String,
			default() {
				return '80%';
			}
		},
		'initial': {
			'type': String
		},
		'onResize': {
			'type': Function
		},
		'show': {
			'type': Array,
			default() {
				return [true, true];
			}
		}
	},
	data() {
		return {
			'resize': null
		};
	},
	mounted() {
		let vm = this;
		vm.size = vm.initialPercent + '%';
	},
	computed: {
		dividerLeft() {
			let vm = this;
			return 'calc(' + vm.size + ' - 2px)';
		},
		needsCover() {
			let vm = this;
			return !!vm.resize;
		},
		minPercent() {
			let vm = this;
			let p = 20;
			if (vm.min.endsWith('%')) {
				try {
					let v = Number(vm.min.substring(0, vm.min.length - 1));
					if (isNaN(v)) {
						throw 'Invalid min value.';
					}
					p = v;
				} catch (ex) {
					// invalid
				}
			}
			return p;
		},
		maxPercent() {
			let vm = this;
			let p = 80;
			if (vm.max.endsWith('%')) {
				try {
					let v = Number(vm.max.substring(0, vm.max.length - 1));
					if (isNaN(v)) {
						throw 'Invalid max value.';
					}
					p = v;
				} catch (ex) {
					// invalid
				}
			}
			return p;
		},
		initialPercent() {
			let vm = this;
			let p = vm.minPercent;
			if (!vm.initial) {
				return p;
			}
			if (vm.initial.endsWith('%')) {
				try {
					let v = Number(vm.initial.substring(0, vm.initial.length - 1));
					if (isNaN(v)) {
						throw 'Invalid initial value.';
					}
					if (vm.minPercent <= v && v <= vm.maxPercent) {
						p = v;
					}
				} catch (ex) {
					// invalid
				}
			}
			return p;
		},
		showAll() {
			let vm = this;
			for (let e of vm.show) {
				if (!e) {
					return false;
				}
			}
			return true;
		},
		pane1Width() {
			let vm = this;
			return vm.size;
		},
		pane2Left() {
			let vm = this;
			if (!vm.show[0]) {
				return '0';
			}
			return vm.size;
		}
	},
	methods: {
		doResizingOn(event) {
			let vm = this;
			vm.resize = {
				'x': event.clientX,
				'y': event.clientY
			};
		},
		doResizingOff() {
			let vm = this;
			vm.resize = null;
		},
		onMouseMove(event) {
			let vm = this;
			if (!vm.resize) {
				return;
			}

			let rects = vm.$el.getClientRects();
			let percent = (event.clientX - rects[0].x) / vm.$el.clientWidth * 100;
			if (percent < vm.minPercent) {
				percent = vm.minPercent;
			}
			if (percent > vm.maxPercent) {
				percent = vm.maxPercent;
			}
			vm.size = '' + percent + '%';
			if (typeof vm.onResize == 'function') {
				vm.onResize(percent);
			}
		}
	},
}
</script>
