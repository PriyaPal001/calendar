<script>
	import DatePicker from './lib/DataPicker.svelte'
	let cmpDatePicker
	
	function datepicked (e) {
		console.log(e.detail.datepicked)
	}
</script>

<DatePicker bind:this={cmpDatePicker} on:datepicked={datepicked} />

<svelte:head>
	<link href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css" rel="stylesheet">
</svelte:head>