<script lang="ts">
	import { onMount } from 'svelte';
	import type CanvasComponent from '$lib/Canvas.svelte';
	import image from '$lib/image.jpg';

	let Canvas: typeof CanvasComponent;
	let canvas: CanvasComponent;
	let imageModal: HTMLDialogElement;
	let imgDataURL: string;

	onMount(async () => {
		Canvas = (await import('$lib/Canvas.svelte')).default;
	});

	const generateImage = () => {
		imgDataURL = canvas.saveImage();

		imageModal.showModal();
	};
</script>

<section class="flex h-full flex-col items-center justify-center gap-4">
	<svelte:component this={Canvas} bind:this={canvas} bgUrl={image} text={'Meme, I embrace.'} />

	<button on:click={generateImage} class="btn">Save</button>

	<dialog class="modal" bind:this={imageModal}>
		<div class="modal-box space-y-4">
			<p class="text-center">Generated image</p>
			<h3 class="text-lg font-bold">
				<img src={imgDataURL} alt="" />
			</h3>
			<form method="dialog" class="modal-form">
				<div class="flex justify-center">
					<button class="btn">Close</button>
				</div>
			</form>
		</div>
		<form method="dialog" class="modal-backdrop">
			<button>close</button>
		</form>
	</dialog>
</section>
