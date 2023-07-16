<script lang="ts">
	import clone from 'clone';
	import Card from '$lib/components/Card.svelte';
	import type Note from '$lib/shared/models/note';
	import { onMount } from 'svelte';

	let notes: Note[] = [];

	onMount(async function () {
		const response = await fetch('http://localhost:8080/api/note/all', {
			method: 'GET'
		});

		notes = await response.json();
	});

	async function closeModal(event: MouseEvent) {
		const modal = document.querySelector<HTMLDivElement>('#modal');
		if (modal?.classList.contains('block')) modal?.classList.remove('block');

		modal?.classList.add('hidden');
	}

	async function createNote(event: MouseEvent) {
		const title = document.querySelector<HTMLInputElement>('#title');
		const text = document.querySelector<HTMLInputElement>('#text');

		const note: Note = {
			_id: '',
			index: notes.length,
			title: title?.value ?? '',
			text: text?.value ?? ''
		};

		const response = await fetch('http://localhost:8080/api/note/create', {
			method: 'PUT',
			body: JSON.stringify(note)
		});
		notes = await response.json();

		if (title !== null) title.value = '';
		if (text !== null) text.value = '';
		await closeModal(event);
	}

	async function openModal(event: MouseEvent) {
		const modal = document.querySelector<HTMLDivElement>('#modal');
		if (modal?.classList.contains('hidden')) modal?.classList.remove('hidden');

		modal?.classList.add('block');
	}
</script>

<div class="grid grid-cols-4 gap-8 w-screen h-screen p-12 overflow-x-hidden overflow-y-scroll">
	<button
		type="button"
		on:click={openModal}
		class="flex flex-row justify-between items-center absolute w-[6.75rem] h-8 py-1 px-2 bg-green-500 hover:bg-green-400 top-2 right-12 z-10 rounded-lg text-sm font-medium text-stone-100/95 hover:text-stone-100"
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="1.5"
			stroke="currentColor"
			class="w-6 h-6"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M12 9v6m3-3H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z"
			/>
		</svg>
		New Note
	</button>

	<div
		id="modal"
		class="hidden relative z-10"
		aria-labelledby="modal-title"
		role="dialog"
		aria-modal="true"
	>
		<div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" />

		<div class="fixed inset-0 z-10 overflow-y-auto">
			<div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
				<div
					class="relative transform overflow-hidden rounded-lg bg-white text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg"
				>
					<div class="bg-white px-4 pb-4 pt-5 sm:p-6 sm:pb-4">
						<div class="sm:flex sm:items-start">
							<div class="mt-3 text-center sm:ml-4 sm:mt-0 sm:text-left">
								<h3 class="text-base font-semibold leading-6 text-stone-900" id="modal-title">
									New Note
								</h3>
								<div class="mt-2">
									<p class="text-sm text-stone-500">Enter a title and your note, please.</p>
								</div>
								<form class="flex flex-col justify-start items-start gap-3 mt-4 text-stone-800">
									<label for="title"> Title </label>
									<input
										class="px-2 py-1 rounded-lg border border-transparent bg-stone-100"
										id="title"
										type="text"
									/>
									<label for="text"> Note </label>
									<input
										class="px-2 py-1 rounded-lg border border-transparent bg-stone-100"
										id="text"
										type="text"
									/>
								</form>
							</div>
						</div>
					</div>
					<div class="bg-stone-50 px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6">
						<button
							type="button"
							on:click={createNote}
							class="inline-flex w-full justify-center rounded-md bg-blue-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-blue-500 sm:ml-3 sm:w-auto"
							>Create</button
						>
						<button
							type="button"
							on:click={closeModal}
							class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-stone-300 hover:bg-stone-50 sm:mt-0 sm:w-auto"
							>Cancel</button
						>
					</div>
				</div>
			</div>
		</div>
	</div>

	{#each notes as note}
		<Card {note} />
	{/each}
</div>
