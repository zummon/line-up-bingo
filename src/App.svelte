<script>
	let rows = $state([
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""]
	]);
	let players = $state(['yellow','red'])
	let count = $state(0)

	function drop (colindex) {
		let placerow = 5;
		let rowindex = 0
		for (let row of rows) {
			if (row[colindex] == "") {
				placerow = rowindex;
			}
			rowindex++
		}
		rows[placerow][colindex] = players[count % players.length];
		count++
	}

	function restart () {
		rows = rows.map((row) => { 
			return row.map(() => '')
		})
	}

</script>


<div class="p-2">
	<button class="text-orange-300 cursor-pointer hover:text-orange-500" title="Reset" onclick={() => { restart() }}>
		<!-- heroicons micro arrow-path -->
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="w-12 h-12">
			<path fill-rule="evenodd" d="M13.836 2.477a.75.75 0 0 1 .75.75v3.182a.75.75 0 0 1-.75.75h-3.182a.75.75 0 0 1 0-1.5h1.37l-.84-.841a4.5 4.5 0 0 0-7.08.932.75.75 0 0 1-1.3-.75 6 6 0 0 1 9.44-1.242l.842.84V3.227a.75.75 0 0 1 .75-.75Zm-.911 7.5A.75.75 0 0 1 13.199 11a6 6 0 0 1-9.44 1.241l-.84-.84v1.371a.75.75 0 0 1-1.5 0V9.591a.75.75 0 0 1 .75-.75H5.35a.75.75 0 0 1 0 1.5H3.98l.841.841a4.5 4.5 0 0 0 7.08-.932.75.75 0 0 1 1.025-.273Z" clip-rule="evenodd" />
		</svg>
	</button>
</div>

<div class="grid grid-cols-7 gap-2 p-2 min-w-fit">
	{#each rows as row, rowindex (rowindex)}
		{#each row as col, colindex (`${rowindex}-${colindex}`)}
			<button class="rounded-full w-16 h-16 cursor-pointer hover:border-orange-300 {col == 'red' ? 'bg-red-500' : col ? 'bg-yellow-300' : 'border-2'}" 
				onclick={() => { drop(colindex) }}>
			</button>
		{/each}
	{/each}
</div>

