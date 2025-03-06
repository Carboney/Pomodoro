<script lang="ts">
	import TimerIcon from '$lib/components/timerIcon.svelte';
	import Tooltip from '$lib/components/tooltip.svelte';
	import { onMount } from 'svelte';

	const pomodoroTime = 1500;
	const shortBreakTime = 300;
	const longBreakTime = 900;

	let message: string = $state("It's time to focus");
	let time: number = pomodoroTime;
	let timer: number = $state(pomodoroTime);
	let percentage: number = $derived.by(() => {
		let elapsedTime = time - timer;
		let percentageLeft = (elapsedTime / time) * 100;
		return percentageLeft;
	});
	let isPause: boolean = $state(true);
	let selectedMode: number = $state(0);

	let minutes = $derived(Math.floor(timer / 60));
	let seconds = $derived(Math.floor(timer - minutes * 60));

	let pomodorosCompleted: number = $state(0);

	let endDate: Date = $state(new Date());
	let endHours: number = $state(0);
	let endMinutes: number = $state(0);

	function Countdown() {
		isPause = !isPause;

		if (!isPause) {
			endDate = new Date();
			endDate.setMinutes(endDate.getMinutes() + minutes);
			endHours = endDate.getHours();
			endMinutes = endDate.getMinutes();
		}
	}

	let bell: HTMLAudioElement;

	onMount(() => {
		bell = new Audio('src/lib/bell.wav');
	});

	function RingBell() {
		bell.play();
	}

	setInterval(() => {
		if (timer > 0 && !isPause) timer -= 1;
		else {
			if (timer == 0 && !isPause) {
				RingBell();
				isPause = true;
				resetClock();
				if (selectedMode === 1) pomodorosCompleted += 1;
				if (selectedMode === 3 && pomodorosCompleted >= 4) pomodorosCompleted = 0;
			}
		}
	}, 1000);

	function resetClock() {
		timer = time;
	}
</script>

<div class="bg-base-200 flex h-screen w-screen flex-col place-items-center p-12 transition-colors duration-300">
	<!--Radial progress-->
	<div class="radial-progress text-secondary z-10" style="--value:100; --size:30rem; --thickness: 8px" aria-valuenow={percentage} role="progressbar">
		<div class="radial-progress text-primary z-20" style="--value:{percentage}; --size:30rem; --thickness: 8px" aria-valuenow={percentage} role="progressbar">
			<!--Clock-->
			<div class="flex h-fit w-fit flex-col items-center gap-2 border-0 p-6">
				<div class="join z-10">
					<input
						class="join-item btn btn-ghost"
						type="radio"
						name="options"
						aria-label="Pomodoro"
						onchange={() => {
							time = pomodoroTime;
							timer = pomodoroTime;
							message = "It's time to focus";
							selectedMode = 1;
							isPause = true;
						}}
						checked={true}
					/>
					<input
						class="join-item btn btn-ghost"
						type="radio"
						name="options"
						aria-label="Short Break"
						onchange={() => {
							time = shortBreakTime;
							timer = shortBreakTime;
							message = 'Take a break';
							selectedMode = 2;
							isPause = true;
						}}
					/>
					<input
						class="join-item btn btn-ghost"
						type="radio"
						name="options"
						aria-label="Long break"
						onchange={() => {
							time = longBreakTime;
							timer = longBreakTime;
							message = 'Take a break';
							selectedMode = 3;
							isPause = true;
						}}
					/>
				</div>

				<span class="text-9xl">
					<span aria-live="polite" style="--value:{String(minutes).padStart(2, '0')};">{String(minutes).padStart(2, '0')}</span>:<span aria-live="polite" style="--value:{String(seconds).padStart(2, '0')};">{String(seconds).padStart(2, '0')}</span>
				</span>

				<div class="relative flex w-full flex-row place-content-between">
					<Tooltip actionName="Reset" key="R">
						<button
							class="btn btn-secondary btn-circle p-8"
							onclick={() => {
								timer = time;
								isPause = true;
							}}>Reset</button
						>
					</Tooltip>

					<div class="flex flex-row place-items-center">
						<TimerIcon active={pomodorosCompleted >= 1 ? true : false}></TimerIcon>
						<TimerIcon active={pomodorosCompleted >= 2 ? true : false}></TimerIcon>
						<TimerIcon active={pomodorosCompleted >= 3 ? true : false}></TimerIcon>
						<TimerIcon active={pomodorosCompleted >= 4 ? true : false}></TimerIcon>
					</div>

					<Tooltip actionName={isPause ? 'Start' : 'Pause'} key="space">
						<button class="btn btn-circle btn-primary p-8" onclick={() => Countdown()}>{isPause ? 'Start' : 'Pause'}</button>
					</Tooltip>
				</div>
			</div>
		</div>
	</div>
	<!--End hour & message-->
	<div class="relative mt-8 flex h-fit w-full place-content-center">
		<div class="transition-300 absolute bottom-[22px] flex gap-1 transition-opacity ease-out {!isPause ? 'opacity-100' : 'opacity-0'}">
			<span class="text-base-300/60 text-center">
				{endHours > 12 ? endHours - 12 : endHours}:{endMinutes}
				{endHours < 12 ? 'AM' : 'PM'}
			</span>

			<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" stroke-width={1.5} class="fill-base-300/60 size-4 place-self-center">
				<path stroke-linecap="round" stroke-linejoin="round" d="M14.857 17.082a23.848 23.848 0 0 0 5.454-1.31A8.967 8.967 0 0 1 18 9.75V9A6 6 0 0 0 6 9v.75a8.967 8.967 0 0 1-2.312 6.022c1.733.64 3.56 1.085 5.455 1.31m5.714 0a24.255 24.255 0 0 1-5.714 0m5.714 0a3 3 0 1 1-5.714 0" />
			</svg>
		</div>
		<p>{message}</p>
	</div>
</div>
