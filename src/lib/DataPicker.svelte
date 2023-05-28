<script>
  import { createEventDispatcher, onMount } from 'svelte'
  import dayjs from 'dayjs'
  import 'dayjs/locale/fr'

  const dispatch = createEventDispatcher()
  let elModal 
  let inputTxt 
  let isOpenCalendar = false 
  const arrDays = ['Lu', 'Ma', 'Me', 'Je', 'Ve', 'Sa', 'Di']
	 let arrDaysStuff = ['Mon', 'Tue', 'Wed', 'Thr', 'Fri', 'Sat', 'Sun']
  const currentDay = +dayjs().format('D') 
  const currentMonth = +dayjs().format('M') 
  const currentYear = +dayjs().format('YYYY') 
  let selectedMonth = +dayjs().format('M') 
  let selectedYear = +dayjs().format('YYYY') 
  let rows = initRows()

  export let customclass = ''

  $: dispatch('datepicked', {
    datepicked: inputTxt
  })

  onMount(() => {
    dayjs.locale('fr') 
    inputTxt = dayjs().format('YYYY-MM-DD')
    affecteRows()
  })

  // functions
  /**
   * Click outside of the modal will close it
   * @param e
   */
  function handleClickModal(e) {
    if (e.target === elModal) {
      isOpenCalendar = false
    }
  }

  function initRows() {
    return [
      [0, 0, 0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0, 0, 0],
      [0, 0, 0, 0, 0, 0, 0]
    ]
  }

  function affecteRows() {
    rows = initRows()
    const firstDayOfCurrentMonth = ucFirst(
      dayjs(selectedYear + '-' + selectedMonth)
        .startOf('month')
        .format('dd')
    ) // 'Lu'
    const lastDayOfCurrentMonth = +dayjs(selectedYear + '-' + selectedMonth)
      .endOf('month')
      .format('D') // 31
    let iRow = 0
    let iCol = 0
    let start = false
    let cpt = 0
    for (iRow = 0; iRow < 6; iRow++) {
      arrDays.forEach((daystr) => {
        if (cpt > lastDayOfCurrentMonth) {
          return
        }
        if (!start && daystr === firstDayOfCurrentMonth) {
          cpt++
          start = true
        }
        rows[iRow][iCol] = cpt
        iCol++
        if (start) {
          cpt++
        }
      })
      iCol = 0
    }
  }

  function ucFirst(str) {
    return str.charAt(0).toUpperCase() + str.slice(1)
  }

  function previousMonth() {
    selectedMonth--
    if (selectedMonth <= 0) {
      selectedMonth = 12
      selectedYear--
    }
    affecteRows()
  }

  function nextMonth() {
    selectedMonth++
    if (selectedMonth > 12) {
      selectedMonth = 1
      selectedYear++
    }
    affecteRows()
  }

  function selectDate(y, m, d) {
    inputTxt = dayjs(y + '-' + m + '-' + d).format('YYYY-MM-DD')
    isOpenCalendar = false
  }
	let monthsStuff={
		mai:"May",
		juin:"June",
		juillet:"July",
		août:"August",
		septembre:"September",
		octobre:"October",
		novembre:"November",
		décembre:"December",
		janvier:"January",
		février:"February",
		mars:"March",
		avril:"April"		
	}
</script>

{#if isOpenCalendar}
  <div
    class="fixed z-40 left-0 top-0 w-full h-full overflow-auto bg-black bg-opacity-40"
    bind:this={elModal}
    on:click={handleClickModal}
  >
    <div class="flex items-center justify-center py-8 px-4">
      <div class="max-w-sm w-full shadow-lg">
        <div class="md:p-8 p-5 dark:bg-gray-800 bg-white rounded-t">
          <div class="px-4 flex items-center justify-between">
            <!-- Month year -->
            <span class="focus:outline-none text-base font-bold text-gray-800">{monthsStuff[dayjs(selectedYear + '-' + selectedMonth).format('MMMM')]} {ucFirst(dayjs(selectedYear + '-' + selectedMonth).format('YYYY'))}</span>
				
            <div class="flex items-center">
              <!-- bnt previous -->
              <button on:click={previousMonth} aria-label="calendar backward" class="text-gray-800">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon icon-tabler icon-tabler-chevron-left"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  stroke-width="1.5"
                  stroke="currentColor"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                  <polyline points="15 6 9 12 15 18" />
                </svg>
              </button>
              <!-- bnt next -->
              <button on:click={nextMonth} aria-label="calendar forward" class="ml-3 text-gray-800">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="icon icon-tabler  icon-tabler-chevron-right"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  stroke-width="1.5"
                  stroke="currentColor"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                  <polyline points="9 6 15 12 9 18" />
                </svg>
              </button>
            </div>
          </div>
          <div class="flex items-center justify-between pt-12 overflow-x-auto">
            <table class="w-full">
              <thead>
                <tr>
                  {#each arrDaysStuff as day}
                    <th>
                      <div class="w-full flex justify-center">
                        <p class="text-base font-medium text-center text-gray-800">
                          {day}
                        </p>
                      </div>
                    </th>
                  {/each}
                </tr>
              </thead>
              <tbody>
                {#each rows as col}
                  <tr>
                    {#each col as i}
                      <td class="pt-4">
                        <div class="px-2 py-2 cursor-pointer flex w-full justify-center">
                          {#if i > 0}
                            {#if i === currentDay && selectedMonth === currentMonth && selectedYear === currentYear}
                              <button
                                on:click={() => {
                                  selectDate(selectedYear, selectedMonth, i)
                                }}
                                class="rounded focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-700 focus:bg-indigo-500 hover:bg-indigo-500 text-base w-8 h-8 flex items-center justify-center font-medium text-white bg-indigo-700"
                                >{i}</button
                              >
                            {:else}
                              <p class="text-base text-gray-500 font-medium">
                                <button class="border-none"
                                  on:click={() => {
                                    selectDate(selectedYear, selectedMonth, i)
                                  }}
                                >
                                  {i}
                                </button>
                              </p>
                            {/if}
                          {/if}
                        </div>
                      </td>
                    {/each}
                  </tr>
                {/each}
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
{/if}

<input
  type="text"
  bind:value={inputTxt}
  class={customclass}
  on:click={() => {
    isOpenCalendar = true
  }}
/>
