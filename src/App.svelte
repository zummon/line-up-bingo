<script>
	import { fly } from 'svelte/transition';
	import { bounceOut } from 'svelte/easing';
	
	let rows = $state([
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
		["", "", "", "", "", "", ""],
	]);
	
	let players = ["yellow", "red"];
	let count = $state(0);
	let winner = $state(null);
	let hoveredCol = $state(null);
	
	let currentPlayer = $derived(players[count % players.length]);

	function checkWin() {
		const R = rows.length;
		const C = rows[0].length;
		for (let r = 0; r < R; r++) {
			for (let c = 0; c < C; c++) {
				const p = rows[r][c];
				if (p === "") continue;
				if (c + 3 < C && p === rows[r][c+1] && p === rows[r][c+2] && p === rows[r][c+3]) return p;
				if (r + 3 < R && p === rows[r+1][c] && p === rows[r+2][c] && p === rows[r+3][c]) return p;
				if (r + 3 < R && c + 3 < C && p === rows[r+1][c+1] && p === rows[r+2][c+2] && p === rows[r+3][c+3]) return p;
				if (r + 3 < R && c - 3 >= 0 && p === rows[r+1][c-1] && p === rows[r+2][c-2] && p === rows[r+3][c-3]) return p;
			}
		}
		return null;
	}

	function drop(colindex) {
		if (winner) return;
		let placerow = -1;
		for (let rowindex = 0; rowindex < rows.length; rowindex++) {
			if (rows[rowindex][colindex] === "") {
				placerow = rowindex;
			}
		}
		
		if (placerow !== -1) {
			rows[placerow][colindex] = currentPlayer;
			count++;
			winner = checkWin();
		}
	}

	function restart() {
		rows = rows.map((row) => row.map(() => ""));
		count = 0;
		winner = null;
		hoveredCol = null;
	}
</script>

<div class="w-full flex-grow bg-stone-50 flex flex-col items-center py-8 sm:py-12 font-sans selection:bg-orange-200">
	
	<!-- Header -->
	<div class="text-center mb-6">
		<h1 class="text-4xl sm:text-5xl font-extrabold text-stone-700 tracking-tight drop-shadow-sm mb-3">Line-Up Bingo</h1>
		<div class="text-xl font-medium h-10 flex items-center justify-center">
			{#if winner}
				<span class="text-2xl font-bold {winner === 'red' ? 'text-rose-500' : 'text-amber-500'} animate-bounce inline-block">
					{winner === 'red' ? 'Red' : 'Yellow'} Wins! 🎉
				</span>
			{:else}
				<div class="flex items-center justify-center gap-3 bg-white px-5 py-2 rounded-full shadow-sm border border-stone-200">
					<span class="text-stone-500">Current Turn</span>
					<div class="w-6 h-6 rounded-full shadow-inner border border-black/10 {currentPlayer === 'red' ? 'bg-rose-400' : 'bg-amber-300'}"></div>
				</div>
			{/if}
		</div>
	</div>

	<!-- Reset Button -->
	<button
		class="mb-8 px-6 py-2.5 bg-white hover:bg-orange-50 text-orange-600 font-bold rounded-full shadow-sm border border-orange-200 transition-all hover:scale-105 active:scale-95 flex items-center gap-2 cursor-pointer"
		onclick={restart}
	>
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
			<path fill-rule="evenodd" d="M15.312 11.424a5.5 5.5 0 01-9.201 2.466l-.312-.311h2.433a.75.75 0 000-1.5H3.989a.75.75 0 00-.75.75v4.242a.75.75 0 001.5 0v-2.43l.31.31a7 7 0 0011.712-3.138.75.75 0 00-1.449-.39zm1.23-3.723a.75.75 0 00.219-.53V2.929a.75.75 0 00-1.5 0V5.36l-.31-.31A7 7 0 003.239 8.188a.75.75 0 101.448.389A5.5 5.5 0 0113.89 6.11l.311.31h-2.432a.75.75 0 000 1.5h4.243a.75.75 0 00.53-.219z" clip-rule="evenodd" />
		</svg>
		Restart Game
	</button>

	<!-- Game Area -->
	<div class="inline-flex flex-col relative">
		
		<!-- Ghost Indicator Row -->
		<div class="grid grid-cols-7 gap-2 sm:gap-3 mb-2 px-3 sm:px-4">
			{#each {length: 7} as _, c}
				<div class="w-12 h-12 sm:w-16 sm:h-16 flex items-center justify-center">
					{#if !winner && hoveredCol === c && rows[0][c] === ""}
						<div 
							in:fly={{ y: -20, duration: 200 }}
							class="w-11 h-11 sm:w-14 sm:h-14 rounded-full shadow-lg border-b-4 {currentPlayer === 'red' ? 'bg-rose-400 border-rose-500' : 'bg-amber-300 border-amber-400'} relative animate-bounce"
						>
							<div class="absolute top-1.5 left-2 w-3 h-2 sm:w-4 sm:h-3 bg-white/60 rounded-full rotate-[-45deg]"></div>
						</div>
					{/if}
				</div>
			{/each}
		</div>

		<!-- Board Wrapper -->
		<div class="relative p-3 sm:p-4 bg-orange-300 rounded-[2rem] shadow-2xl border-b-8 border-orange-400 overflow-hidden">
			
			<!-- Container for layers to ensure identical layout -->
			<div class="relative grid grid-rows-6 gap-2 sm:gap-3">
				
				<!-- Pieces Layer (Background) -->
				<div class="absolute inset-0 z-0 grid grid-rows-6 gap-2 sm:gap-3 pointer-events-none">
					{#each rows as row, r}
						<div class="grid grid-cols-7 gap-2 sm:gap-3">
							{#each row as cell, c}
								<div class="w-12 h-12 sm:w-16 sm:h-16 bg-stone-200 rounded-full flex items-center justify-center shadow-[inset_0_3px_6px_rgba(0,0,0,0.15)] relative">
									{#if cell !== ""}
										<div 
											in:fly={{ y: -(r + 1) * 80 - 100, duration: 600, easing: bounceOut }}
											class="absolute w-11 h-11 sm:w-14 sm:h-14 rounded-full shadow-sm border-b-4 {cell === 'red' ? 'bg-rose-400 border-rose-500' : 'bg-amber-300 border-amber-400'}"
										>
											<div class="absolute top-1.5 left-2 w-3 h-2 sm:w-4 sm:h-3 bg-white/60 rounded-full rotate-[-45deg]"></div>
										</div>
									{/if}
								</div>
							{/each}
						</div>
					{/each}
				</div>

				<!-- Board Mask Layer (Foreground with holes) -->
				{#each rows as row, r}
					<div class="grid grid-cols-7 gap-2 sm:gap-3 relative z-10 pointer-events-none">
						{#each row as cell, c}
							<button 
								class="w-12 h-12 sm:w-16 sm:h-16 relative overflow-hidden pointer-events-auto cursor-pointer rounded-full focus:outline-none"
								onclick={() => drop(c)}
								onmouseenter={() => hoveredCol = c}
								onmouseleave={() => hoveredCol = null}
								aria-label="Drop in column {c + 1}"
							>
								<!-- The mask itself (draws the board color outside the circle) -->
								<div class="absolute inset-0 rounded-full shadow-[0_0_0_100px_#fdba74] transition-all duration-200 {hoveredCol === c && !winner ? 'shadow-[0_0_0_100px_#fb923c]' : ''}"></div>
								<!-- Inner shadow to make the hole look deep -->
								<div class="absolute inset-0 rounded-full shadow-[inset_0_4px_8px_rgba(0,0,0,0.2)] pointer-events-none"></div>
							</button>
						{/each}
					</div>
				{/each}
				
			</div>
		</div>
	</div>

</div>
