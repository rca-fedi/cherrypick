<template>
<MkStickyContainer>
	<template #header>
		<CPPageHeader v-if="isFriendly && !isDesktop" v-model:tab="src" :actions="headerActions" :tabs="headerTabs"/>
		<MkPageHeader v-else v-model:tab="src" :actions="headerActions" :tabs="headerTabs" :display-my-avatar="true"/>
	</template>

	<MkSpacer :content-max="800">
		<div ref="rootEl" v-hotkey.global="keymap" class="cmuxhskf">
			<XTutorial v-if="$store.reactiveState.tutorial.value != -1" class="tutorial _block"/>
			<XPostForm v-if="$store.reactiveState.showFixedPostForm.value" class="post-form _block" fixed/>

			<div v-if="queue > 0 && $store.state.newNoteRecivedNotificationBehavior === 'default'" class="new"><button class="_buttonPrimary" @click="top()"><i class="fas fa-arrow-up"></i>{{ i18n.ts.newNoteRecived }}</button></div>
			<div v-if="queue > 0 && $store.state.newNoteRecivedNotificationBehavior === 'count'" class="new"><button class="_buttonPrimary" @click="top()"><i class="fas fa-arrow-up"></i><I18n :src="i18n.ts.newNoteRecivedCount" text-tag="span"><template #n>{{ queue }}</template></I18n></button></div>
			<div class="tl _block">
				<XTimeline
					ref="tl" :key="src"
					class="tl"
					:src="src"
					:sound="true"
					@queue="queueUpdated"
				/>
			</div>
		</div>
	</MkSpacer>
</MkStickyContainer>
</template>

<script lang="ts" setup>
import { defineAsyncComponent, computed, watch, ref } from 'vue';
import XTimeline from '@/components/MkTimeline.vue';
import XPostForm from '@/components/MkPostForm.vue';
import { scroll } from '@/scripts/scroll';
import * as os from '@/os';
import { defaultStore } from '@/store';
import { i18n } from '@/i18n';
import { instance } from '@/instance';
import { $i } from '@/account';
import { definePageMetadata } from '@/scripts/page-metadata';

const isFriendly = $ref(localStorage.getItem('ui') === 'friendly');
const DESKTOP_THRESHOLD = 1100;
const isDesktop = ref(window.innerWidth >= DESKTOP_THRESHOLD);

let includeTypes = $ref<string[] | null>(null);

const XTutorial = defineAsyncComponent(() => import('./timeline.tutorial.vue'));

const isLocalTimelineAvailable = !instance.disableLocalTimeline || ($i != null && ($i.isModerator || $i.isAdmin));
const isGlobalTimelineAvailable = !instance.disableGlobalTimeline || ($i != null && ($i.isModerator || $i.isAdmin));
const keymap = {
	't': focus,
};

const tlComponent = $ref<InstanceType<typeof XTimeline>>();
const rootEl = $ref<HTMLElement>();

let queue = $ref(0);
const src = $computed({ get: () => defaultStore.reactiveState.tl.value.src, set: (x) => saveSrc(x) });

watch ($$(src), () => queue = 0);

function queueUpdated(q: number): void {
	queue = q;
}

function top(): void {
	scroll(rootEl, { top: 0 });
}

async function chooseList(ev: MouseEvent): Promise<void> {
	const lists = await os.api('users/lists/list');
	const items = lists.map(list => ({
		type: 'link' as const,
		text: list.name,
		to: `/timeline/list/${list.id}`,
	}));
	os.popupMenu(items, ev.currentTarget ?? ev.target);
}

async function chooseAntenna(ev: MouseEvent): Promise<void> {
	const antennas = await os.api('antennas/list');
	const items = antennas.map(antenna => ({
		type: 'link' as const,
		text: antenna.name,
		indicate: antenna.hasUnreadNote,
		to: `/timeline/antenna/${antenna.id}`,
	}));
	os.popupMenu(items, ev.currentTarget ?? ev.target);
}

async function chooseChannel(ev: MouseEvent): Promise<void> {
	const channels = await os.api('channels/followed');
	const items = channels.map(channel => ({
		type: 'link' as const,
		text: channel.name,
		indicate: channel.hasUnreadNote,
		to: `/channels/${channel.id}`,
	}));
	os.popupMenu(items, ev.currentTarget ?? ev.target);
}

function saveSrc(newSrc: 'home' | 'local' | 'social' | 'global'): void {
	defaultStore.set('tl', {
		...defaultStore.state.tl,
		src: newSrc,
	});
}

async function timetravel(): Promise<void> {
	const { canceled, result: date } = await os.inputDate({
		title: i18n.ts.date,
	});
	if (canceled) return;

	tlComponent.timetravel(date);
}

function focus(): void {
	tlComponent.focus();
}

const headerActions = $computed(() => []);

const headerTabs = $computed(() => [{
	key: 'home',
	title: i18n.ts._timelines.home,
	icon: 'fas fa-home',
	iconOnly: true,
	onClick: top,
}, ...(isLocalTimelineAvailable ? [{
	key: 'local',
	title: i18n.ts._timelines.local,
	icon: 'fas fa-comments',
	iconOnly: true,
	onClick: top,
}, {
	key: 'social',
	title: i18n.ts._timelines.social,
	icon: 'fas fa-share-alt',
	iconOnly: true,
	onClick: top,
}] : []), ...(isGlobalTimelineAvailable ? [{
	key: 'global',
	title: i18n.ts._timelines.global,
	icon: 'fas fa-globe',
	iconOnly: true,
	onClick: top,
}] : []), {
	icon: 'fas fa-list-ul',
	title: i18n.ts.lists,
	iconOnly: true,
	onClick: chooseList,
}, {
	icon: 'fas fa-satellite',
	title: i18n.ts.antennas,
	iconOnly: true,
	onClick: chooseAntenna,
}, {
	icon: 'fas fa-satellite-dish',
	title: i18n.ts.channel,
	iconOnly: true,
	onClick: chooseChannel,
}]);

definePageMetadata(computed(() => ({
	title: i18n.ts.timeline,
	icon: src === 'local' ? 'fas fa-comments' : src === 'social' ? 'fas fa-share-alt' : src === 'global' ? 'fas fa-globe' : 'fas fa-home',
})));
</script>

<style lang="scss" scoped>
.cmuxhskf {
	> .new {
		position: sticky;
		top: calc(var(--stickyTop, 0px) + 16px);
		z-index: 1000;
		width: 100%;

		> button {
			display: block;
			margin: var(--margin) auto 0 auto;
			padding: 8px 16px;
			border-radius: 32px;

			> i {
				margin-right: 5px;
			}
		}
	}

	> .post-form {
		border-radius: var(--radius);
	}

	> .tl {
		background: var(--bg);
		border-radius: var(--radius);
		overflow: clip;
	}
}
</style>
