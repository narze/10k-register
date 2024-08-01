<script lang="ts">
	import Konva from 'konva';
	import { onMount } from 'svelte';
	import {
		Text,
		Transformer,
		type KonvaDragTransformEvent,
		type KonvaMouseEvent,
		type KonvaTouchEvent
	} from 'svelte-konva';

	export let text = 'Hello';
	export let xPercent = 50;
	export let yPercent = 90;
	export let fontSize = 48;

	export function hideTransformer() {
		transformer.nodes([]);
	}

	export function showTransformer() {
		transformer.nodes([textEl]);
	}

	let textEl: Konva.Text;

	$: textConfig = {
		text,
		fontSize,
		fill: 'white',
		align: 'center',
		verticalAlign: 'middle',
		draggable: true,
		name: 'text1',
		origin: 'center'
	};

	let transformer: Konva.Transformer;
	let selectedShapeName = '';

	function handleStageMouseDown(e: KonvaMouseEvent | KonvaTouchEvent) {
		const konvaEvent = e.detail;
		// clicked on stage - clear selection
		if (konvaEvent.target === konvaEvent.target.getStage()) {
			selectedShapeName = '';
			updateTransformer();
			return;
		}

		// clicked on transformer - do nothing
		const clickedOnTransformer = konvaEvent.target.getParent()!.className === 'Transformer';
		if (clickedOnTransformer) {
			return;
		}

		// find clicked text by its name
		const name = konvaEvent.target.name();
		// const text = texts.find((r) => r.name === name);

		if (textConfig) {
			selectedShapeName = name;
		} else {
			selectedShapeName = '';
		}

		updateTransformer();
	}

	function handleTransformEnd() {
		// // find element in our state
		// const text = texts.find((r) => r.name === selectedShapeName)!;
		// // change fill
		// text.fill = Konva.Util.getRandomColor();
	}

	function updateTransformer() {
		if (!transformer) return;

		// here we need to manually attach or detach Transformer node
		const stage = transformer.getStage()!;

		const selectedNode = stage.findOne('.' + selectedShapeName);

		// do nothing if selected node is already attached
		// if (selectedNode === transformer.node()) {
		// 	return;
		// }

		if (selectedNode) {
			// attach to another node
			transformer.nodes([selectedNode]);
		} else {
			// remove transformer
			transformer.nodes([]);
		}
	}

	// FIXME: It breaks mobile viewport
	// function handleDblClick(e: KonvaMouseEvent | KonvaTouchEvent) {
	// 	const textNode = e.detail.target as unknown as Text;
	// 	const text = textNode;
	// 	const tr = transformer;
	// 	const textPosition = text.getAbsolutePosition();
	// 	const stageBox = text.getStage()!.container().getBoundingClientRect();

	// 	const areaPosition = {
	// 		x: stageBox.left + textPosition.x - text.width() / 2,
	// 		y: stageBox.top + textPosition.y - text.height() / 2
	// 	};

	// 	textNode.hide();
	// 	transformer.hide();

	// 	const scale = textNode.getAbsoluteScale().x;

	// 	const textarea = document.createElement('input');
	// 	document.body.appendChild(textarea);

	// 	// textarea.value = text.text();
	// 	// textarea.style.position = 'absolute';
	// 	// textarea.style.top = areaPosition.y + 'px';
	// 	// textarea.style.left = areaPosition.x + 'px';
	// 	// textarea.style.width = text.width();

	// 	// apply many styles to match text on canvas as close as possible
	// 	// remember that text rendering on canvas and on the textarea can be different
	// 	// and sometimes it is hard to make it 100% the same. But we will try...
	// 	textarea.value = textNode.text();
	// 	textarea.style.position = 'absolute';
	// 	textarea.style.top = areaPosition.y + 'px';
	// 	textarea.style.left = areaPosition.x + 'px';
	// 	textarea.style.width = textNode.width() - textNode.padding() * 2 + 'px';
	// 	textarea.style.height = textNode.height() - textNode.padding() * 2 + 5 + 'px';
	// 	textarea.style.fontSize = textNode.fontSize() * scale + 'px';
	// 	textarea.style.border = 'none';
	// 	// textarea.style.border = '1px solid red'; // debug
	// 	textarea.style.boxSizing = 'border-box';
	// 	textarea.style.padding = '0px';
	// 	textarea.style.margin = '0px';
	// 	textarea.style.overflow = 'hidden';
	// 	textarea.style.background = 'none';
	// 	textarea.style.outline = 'none';
	// 	textarea.style.resize = 'none';
	// 	textarea.style.lineHeight = `${textNode.lineHeight()}`;
	// 	textarea.style.fontFamily = textNode.fontFamily();
	// 	textarea.style.transformOrigin = 'center';
	// 	textarea.style.textAlign = textNode.align();
	// 	textarea.style.color = `${textNode.fill()}`;
	// 	const rotation = textNode.rotation();
	// 	let transform = '';
	// 	if (rotation) {
	// 		transform += 'rotateZ(' + rotation + 'deg)';
	// 	}
	// 	let px = 0;

	// 	// also we need to slightly move textarea on firefox
	// 	// because it jumps a bit
	// 	const isFirefox = navigator.userAgent.toLowerCase().indexOf('firefox') > -1;
	// 	if (isFirefox) {
	// 		px += 2 + Math.round(textNode.fontSize() / 20);
	// 	}
	// 	transform += 'translateY(-' + px + 'px)';

	// 	textarea.style.transform = transform;

	// 	// reset height
	// 	textarea.style.height = 'auto';
	// 	// after browsers resized it we can set actual value
	// 	textarea.style.height = textarea.scrollHeight + 3 + 'px';

	// 	textarea.focus();

	// 	function removeTextarea() {
	// 		textarea.parentNode?.removeChild(textarea);
	// 		window.removeEventListener('click', handleOutsideClick);
	// 		textNode.show();
	// 		tr.show();
	// 		tr.forceUpdate();
	// 	}

	// 	function setTextareaWidth(newWidth: number) {
	// 		if (!newWidth) {
	// 			// set width for placeholder
	// 			newWidth = textNode.placeholder.length * textNode.fontSize();
	// 		}
	// 		// some extra fixes on different browsers
	// 		const isSafari = /^((?!chrome|android).)*safari/i.test(navigator.userAgent);
	// 		const isFirefox = navigator.userAgent.toLowerCase().indexOf('firefox') > -1;
	// 		if (isSafari || isFirefox) {
	// 			newWidth = Math.ceil(newWidth);
	// 		}

	// 		const isEdge = /Edge/.test(navigator.userAgent);
	// 		if (isEdge) {
	// 			newWidth += 1;
	// 		}
	// 		textarea.style.width = newWidth + 'px';
	// 	}

	// 	textarea.addEventListener('keydown', function (e) {
	// 		// hide on enter
	// 		// but don't hide on shift + enter
	// 		if (e.keyCode === 13 && !e.shiftKey) {
	// 			textNode.text(textarea.value);
	// 			removeTextarea();
	// 		}
	// 		// on esc do not set value back to node
	// 		if (e.keyCode === 27) {
	// 			removeTextarea();
	// 		}
	// 	});

	// 	textarea.addEventListener('keydown', function () {
	// 		const scale = textNode.getAbsoluteScale().x;
	// 		setTextareaWidth(textNode.width() * scale);
	// 		textarea.style.height = 'auto';
	// 		textarea.style.height = textarea.scrollHeight + 3 + 'px';
	// 		// textarea.style.height = textarea.scrollHeight + textNode.fontSize() + 'px';
	// 	});

	// 	function handleOutsideClick(e: MouseEvent) {
	// 		if (e.target !== textarea) {
	// 			textNode.text(textarea.value);
	// 			removeTextarea();
	// 		}
	// 	}
	// 	setTimeout(() => {
	// 		window.addEventListener('click', handleOutsideClick);
	// 	});
	// }

	// Use prompt for now
	function handleDblClick(e: KonvaMouseEvent | KonvaTouchEvent) {
		const newText = prompt('Edit text:', text);

		if (newText) {
			text = newText;
		}
	}

	function handleTransform(e: KonvaDragTransformEvent) {
		// const textNode = e.detail.target;
		// textNode.setAttrs({
		// 	width: textNode.width() * textNode.scaleX(),
		// 	scaleX: 1,
		// 	height: textNode.height() * textNode.scaleY(),
		// 	scaleY: 1
		// });
	}

	onMount(async () => {
		textEl.offsetX(textEl.width() / 2);
		textEl.offsetY(textEl.height() / 2);

		const parent = textEl.getParent()!;
		textEl.x((parent.width() * xPercent * 0.01) / parent.scaleX());
		textEl.y((parent.height() * yPercent * 0.01) / parent.scaleY());

		showTransformer();
	});
</script>

<Transformer
	bind:handle={transformer}
	config={{
		enabledAnchors: ['top-left', 'top-right', 'bottom-left', 'bottom-right']
	}}
/>

<Text
	bind:handle={textEl}
	config={textConfig}
	on:transform={handleTransform}
	on:transformend={handleTransformEnd}
	on:mousedown={handleStageMouseDown}
	on:touchstart={handleStageMouseDown}
	on:dblclick={handleDblClick}
	on:dbltap={handleDblClick}
/>
