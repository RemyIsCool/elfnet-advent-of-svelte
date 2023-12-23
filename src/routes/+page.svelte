<script lang="ts">
	import { onMount } from 'svelte';
	import { slide } from 'svelte/transition';

	type ElfPost = {
		id: string;
		author: string;
		timestamp: string;
		content: string;
		likes: number;
		comments: Comment[];
		expanded?: boolean;
	};

	type Comment = {
		id: string;
		author: string;
		timestamp: string;
		content: string;
		likes: number;
	};

	let elfPosts: ElfPost[] = [];

	onMount(async () => {
		elfPosts = await (
			await fetch('https://advent.sveltesociety.dev/data/2023/day-twenty-three.json')
		).json();
	});
</script>

<h1 class="mb-4 mt-20 text-center text-6xl font-extrabold">ElfNet 🧝🖥️</h1>
<p class="mb-20 text-center text-xl font-light italic">"The elf's online paradise"</p>

<ul class="m-4 grid gap-4">
	{#each elfPosts as elfPost (elfPost.id)}
		{@const elfPostDate = new Date(elfPost.timestamp)}
		{@const randomOptions = ['🎄', '🧝‍♀️', '🧝‍♂️', '🧝', '🎅', '❄️', '⛄️', '🍪']}

		<li class="rounded-lg border-2 border-slate-800 p-3" transition:slide|global>
			<div class="flex gap-2 text-xl">
				<p>{randomOptions[Math.floor(Math.random() * randomOptions.length)]}</p>
				<p class="font-bold">{elfPost.author}</p>
				<p class="font-light">
					{elfPostDate.getDate()}/{elfPostDate.getMonth()}/{elfPostDate.getFullYear()}
				</p>
			</div>

			<p class="my-4 text-lg">{elfPost.content}</p>

			<div class="flex gap-2">
				<p>👍 {elfPost.likes}</p>

				<button
					disabled={elfPost.comments.length <= 0}
					on:click={() => (elfPost.expanded = !elfPost.expanded)}
					>{elfPost.expanded ? '🚫' : `💬 ${elfPost.comments.length}`}</button
				>
			</div>
			{#if elfPost.expanded}
				<div class="mt-4 grid gap-4" transition:slide>
					<p class="text-lg font-semibold">Comments</p>
					<ul class="grid max-w-max gap-3">
						{#each elfPost.comments as comment (comment.id)}
							{@const commentDate = new Date(comment.timestamp)}

							<li class="rounded-lg border-2 border-slate-800 p-4">
								<div class="flex gap-2">
									<p class="font-bold">{comment.author}</p>
									<p class="font-light">
										{commentDate.getDate()}/{commentDate.getMonth()}/{commentDate.getFullYear()}
									</p>
								</div>

								<p>{comment.content}</p>

								<p>👍 {comment.likes}</p>
							</li>
						{/each}
					</ul>
				</div>
			{/if}
		</li>
	{/each}
</ul>

<style>
	:global(body) {
		@apply bg-slate-900 text-slate-50;
	}
</style>
