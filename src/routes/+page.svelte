<script lang="ts">
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
</script>

<h1>YouTube Thumbnail Downloader</h1>
<input on:paste={getVideoDetails} type="text" placeholder="Enter YouTube video url here..." />

{#if state != "Idle"}
	{#if state == "Fetching"}
		<p>Fetching data...</p>
	{:else}
		<div class="images">
			<a href={thumbnailL}>
				<img src={thumbnailL} alt={videoTitle} />
			</a>
			<a href={thumbnailS}>
				<img src={thumbnailS} alt={videoTitle} />
			</a>
		</div>
	{/if}
{/if}
