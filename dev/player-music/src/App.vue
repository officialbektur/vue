<template>
	<div class="player px-4 py-2 h-full">
		<div
			class="player__container bg-slate-400 py-5 px-4 max-w-80 border border-sky-500 m-auto"
		>
			<div class="player__info w-full overflow-hidden">
				<div
					class="text-lg w-full overflow-hidden text-ellipsis whitespace-nowrap"
					:class="{ 'opacity-60 pointer-events-none': !current.title }"
				>
					{{ current.title || 'Выберите аудио' }}
				</div>
				<div
					class="text-sm text-neutral-600 w-full overflow-hidden text-ellipsis whitespace-nowrap"
					:class="{ 'opacity-60 pointer-events-none': !current.author }"
				>
					Автор: {{ current.author || 'Выберите аудио' }}
				</div>
			</div>
			<div
				class="player__controller py-3 player-controller flex gap-x-3 justify-center items-center leading-none font-bold text-sm text-white"
			>
				<div
					@click.prevent="prev()"
					class="player-controller__prev p-2 rounded-full bg-lime-500 border border-green-600 cursor-pointer"
					:class="{
						'opacity-30 pointer-events-none': !current.title,
						'opacity-20 pointer-events-none': !isPrevAudio,
					}"
				>
					<
				</div>
				<div
					v-if="!isPlay"
					@click.prevent="play()"
					class="player-controller__play p-2 text-lg rounded-full border border-white transition-all hover:bg-red-600 hover:border-red-600 cursor-pointer"
					:class="{ 'opacity-60 pointer-events-none': !current.title }"
				>
					@
				</div>
				<div
					v-else
					@click.prevent="pause()"
					class="player-controller__pause p-2 text-lg rounded-full bg-red-600 border border-white transition-all hover:bg-transparent hover:border-white cursor-pointer"
				>
					=
				</div>
				<div
					@click.prevent="next()"
					class="player-controller__next p-2 rounded-full border bg-lime-500 border-green-600 cursor-pointer"
					:class="{
						'opacity-30 pointer-events-none': !current.title,
						'opacity-20 pointer-events-none': !isNextAudio,
					}"
				>
					>
				</div>
			</div>
			<div
				class="player__loading player-loading w-full flex justify-between items-center gap-x-2"
			>
				<span class="player-loading__number text-xs">
					{{ current.time.loading }}
				</span>
				<input
					type="range"
					name="loading"
					max="100"
					min="0"
					:value="current.time.timer"
					@input="seek($event.target.value)"
					class="player-loading__input w-full"
					:disabled="!current.title"
				/>
				<span class="player-loading__maxtime text-xs text-red-600">
					{{ current.time.max }}
				</span>
			</div>
			<div
				class="player__volume player-volume ml-auto w-6/12 flex justify-between items-center gap-x-2"
			>
				<span class="player-volume__current text-xs">
					{{ volume.current }}
				</span>
				<input
					type="range"
					name="volume"
					max="100"
					min="0"
					v-model="volume.current"
					@input="setVolume($event.target.value)"
					class="player-volume__input w-full"
				/>
				<span class="player-volume__max text-xs text-red-600">
					{{ volume.max }}
				</span>
			</div>
			<div class="player__content mt-3">
				<div class="player__title text-white text-xl border-b border-b-white">
					Play Lists: {{ lists.length }}шт
				</div>
				<ul class="player__lists mt-1">
					<li
						v-for="(list, key) in lists"
						:key="key"
						class="player__list player-list gap-x-1 py-2 border-b border-b-white text-white leading-none"
					>
						<div
							class="flex gap-x-2 items-center"
							:class="{ 'opacity-20': key === index }"
						>
							<div class="player-list__number text-xs text-red-600">
								{{ key }}.
							</div>
							<div
								class="player-list__content w-full flex justify-between items-center overflow-hidden"
							>
								<div class="player-list__info overflow-hidden mr-1">
									<div
										class="player-list__title text-lg w-full overflow-hidden text-ellipsis whitespace-nowrap"
									>
										{{ list.title }}
									</div>
									<div
										class="player-list__author text-sm text-neutral-600 w-full overflow-hidden text-ellipsis whitespace-nowrap"
									>
										Автор: {{ list.author }}
									</div>
								</div>
								<div v-if="index === key">
									<div
										v-if="!isPlay"
										@click.prevent="play()"
										class="player-list__play p-2 rounded-full transition-all cursor-pointer border border-white hover:bg-red-600 hover:border-red-600"
									>
										@
									</div>
									<div
										v-else
										@click.prevent="pause()"
										class="player-list__play p-2 rounded-full transition-all cursor-pointer border border-white hover:bg-red-600 hover:border-red-600"
									>
										=
									</div>
								</div>
								<div
									v-else
									@click.prevent="start(key)"
									class="player-list__play p-2 rounded-full transition-all cursor-pointer border border-white hover:bg-red-600 hover:border-red-600"
								>
									@
								</div>
							</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script lang="ts">
	import { defineComponent } from 'vue';

	interface AudioInterface {
		title: string | null;
		author: string | null;
		patch: string | null;
		time: {
			max: string;
			timer: number;
			loading: string;
		};
	}

	export default defineComponent({
		name: 'App',
		data() {
			return {
				player: new Audio(),
				isPlay: false,
				isPrevAudio: false,
				isNextAudio: false,
				index: null as number | null,
				volume: {
					max: 100,
					current: 0,
				},
				timeout: null as ReturnType<typeof setTimeout> | null,
				current: {
					title: null,
					author: null,
					patch: null,
					time: {
						max: '00:00',
						timer: 0,
						loading: '00:00',
					},
				} as AudioInterface,
				lists: [
					{
						title: 'beach',
						author: 'ocean',
						patch: '/src/assets/music/beach.m4a',
					},
					{
						title: 'birds',
						author: 'animal',
						patch: '/src/assets/music/birds.m4a',
					},
					{
						title: 'cafe',
						author: 'people',
						patch: '/src/assets/music/cafe.m4a',
					},
				] as AudioInterface[],
			};
		},
		mounted() {
			this.setVolume(30);
			this.player.addEventListener('timeupdate', this.updateTime);
			this.player.addEventListener('ended', this.next);
		},
		methods: {
			start(index: number): void {
				if (this.timeout) {
					clearTimeout(this.timeout);
				}
				this.change(index);
			},
			changeIndex(index: number): void {
				this.index = index;
			},
			changeAudio(): void {
				if (this.index !== null) {
					const currentAudio = this.lists[this.index];
					if (currentAudio) {
						this.current = this.getCurrent(currentAudio);
						if (this.current.patch) {
							this.player.src = this.current.patch;
							this.player.onloadedmetadata = () => {
								if (this.player.duration) {
									this.current.time.max = this.formatTime(this.player.duration);
									this.updateTime();
								}
							};
						}
					}
				}
			},
			play(): void {
				this.player.play();
				this.isPlay = true;
			},
			pause(): void {
				this.player.pause();
				this.isPlay = false;
			},
			prev(): void {
				if (this.index !== null) {
					const prevIndex: number = this.index - 1;
					if (this.checkAudio(prevIndex)) {
						this.change(prevIndex);
					}
				}
			},
			next(): void {
				if (this.index !== null) {
					const nextIndex: number = this.index + 1;
					if (this.checkAudio(nextIndex)) {
						this.change(nextIndex);
					}
				}
			},
			checkPrevAudio(): void {
				if (this.index !== null) {
					const prevIndex: number = this.index - 1;
					this.isPrevAudio = this.checkAudio(prevIndex);
				}
			},
			checkNextAudio(): void {
				if (this.index !== null) {
					const nextIndex: number = this.index + 1;
					this.isNextAudio = this.checkAudio(nextIndex);
				}
			},
			checkAudio(index: number): boolean {
				return index >= 0 && index < this.lists.length && !!this.lists[index];
			},
			change(index: number): void {
				this.changeIndex(index);
				this.changeAudio();
				this.checkPrevAudio();
				this.checkNextAudio();
				this.play();
			},
			getCurrent(data: AudioInterface): AudioInterface {
				return {
					title: data.title,
					author: data.author,
					patch: data.patch,
					time: {
						max: '00:00',
						timer: 0,
						loading: '00:00',
					},
				};
			},
			seek(time: number): void {
				if (this.player.duration) {
					const newTime: number = (time / 100) * this.player.duration;
					this.player.currentTime = newTime;
					this.current.time.loading = this.formatTime(newTime);
					this.play();
				}
			},
			updateTime(): void {
				if (
					!isNaN(this.player.duration) &&
					this.player.currentTime < this.player.duration
				) {
					this.current.time.loading = this.formatTime(this.player.currentTime);
					this.current.time.timer =
						(this.player.currentTime / this.player.duration) * 100;
				}
			},
			setVolume(volume: number): void {
				this.volume.current = volume;
				this.player.volume = volume / 100;
			},
			formatTime(seconds: number): string {
				const minutes: number = Math.floor(seconds / 60);
				const remainderSeconds: number = Math.floor(seconds % 60);
				const formattedMinutes: string = String(minutes).padStart(2, '0');
				const formattedSeconds: string = String(remainderSeconds).padStart(
					2,
					'0'
				);
				return `${formattedMinutes}:${formattedSeconds}`;
			},
		},
	});
</script>

<style scoped></style>
