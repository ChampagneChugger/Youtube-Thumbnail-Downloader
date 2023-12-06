<script lang="ts">
	import type { HTMLImgAttributes } from "svelte/elements"

	type State = "Idle" | "Fetching" | "Completed"

	let state: State = "Idle"

	let videoTitle: string
	let thumbnailS: string
	let thumbnailL: string

	async function getVideoDetails(event: ClipboardEvent) {
		state = "Fetching"
		const data = await fetch(
			`https://www.youtube.com/oembed?format=json&url=${event.clipboardData?.getData("Text")}`
		)

		const { thumbnail_url, title } = await data.json()

		videoTitle = title
		thumbnailS = thumbnail_url

		let thumbnailArray: string[] = thumbnailS.split("/")
		thumbnailArray[thumbnailArray.length - 1] = "maxresdefault.jpg"
		thumbnailL = thumbnailArray.join("/")

		state = "Completed"
	}

	async function download(event: Event) {
		const url: string = (event.target as HTMLImageElement).src

		const response = await fetch(url, {
			mode: "cors"
		})

		const blob = await response.blob()

		const href = URL.createObjectURL(blob)

		console.log(blob)
	}
</script>

<h1>YouTube Thumbnail Downloader</h1>
<input on:paste={getVideoDetails} type="text" placeholder="Enter YouTube video url here..." />

{#if state != "Idle"}
	{#if state == "Fetching"}
		<p>Fetching data...</p>
	{:else}
		<div class="images">
			<img src={thumbnailL} alt={videoTitle} on:click={download} />
			<img src={thumbnailS} alt={videoTitle} />
		</div>
	{/if}
{/if}
