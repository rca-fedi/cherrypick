<template>
<div class="_formRoot">
	<FormSelect v-model="lang" class="_formBlock">
		<template #label>{{ i18n.ts.uiLanguage }}</template>
		<option v-for="x in langs" :key="x[0]" :value="x[0]">{{ x[1] }}</option>
		<template #caption>
			<I18n :src="i18n.ts.i18nInfo" tag="span">
				<template #link>
					<MkLink url="https://crowdin.com/project/misskey">Crowdin</MkLink>
				</template>
			</I18n>
		</template>
	</FormSelect>

	<FormRadios v-model="overridedDeviceKind" class="_formBlock">
		<template #label>{{ i18n.ts.overridedDeviceKind }}</template>
		<option :value="null">{{ i18n.ts.auto }}</option>
		<option value="smartphone"><i class="fas fa-mobile-alt"/> {{ i18n.ts.smartphone }}</option>
		<option value="tablet"><i class="fas fa-tablet-alt"/> {{ i18n.ts.tablet }}</option>
		<option value="desktop"><i class="fas fa-desktop"/> {{ i18n.ts.desktop }}</option>
	</FormRadios>

	<FormSwitch v-model="showFixedPostForm" class="_formBlock">{{ i18n.ts.showFixedPostForm }}</FormSwitch>

	<FormSection>
		<template #label>{{ i18n.ts.behavior }}</template>
		<FormSwitch v-model="imageNewTab" class="_formBlock">{{ i18n.ts.openImageInNewTab }}</FormSwitch>
		<FormSwitch v-model="enableInfiniteScroll" class="_formBlock">{{ i18n.ts.enableInfiniteScroll }}</FormSwitch>
		<FormSwitch v-model="useReactionPickerForContextMenu" class="_formBlock">{{ i18n.ts.useReactionPickerForContextMenu }}</FormSwitch>
		<FormSwitch v-model="disablePagesScript" class="_formBlock">{{ i18n.ts.disablePagesScript }}</FormSwitch>
		<FormSwitch v-model="useEnterToSend" class="_formBlock">
			<template #label>{{ i18n.ts.useEnterToSend }}</template>
			<template #caption>{{ i18n.ts.useEnterToSendDescription }}</template>
		</FormSwitch>

		<FormSelect v-model="serverDisconnectedBehavior" class="_formBlock">
			<template #label>{{ i18n.ts.whenServerDisconnected }}</template>
			<option value="reload">{{ i18n.ts._serverDisconnectedBehavior.reload }}</option>
			<option value="dialog">{{ i18n.ts._serverDisconnectedBehavior.dialog }}</option>
			<option value="quiet">{{ i18n.ts._serverDisconnectedBehavior.quiet }}</option>
			<option value="none">{{ i18n.ts._serverDisconnectedBehavior.none }}</option>
		</FormSelect>
	</FormSection>

	<FormSection>
		<template #label>{{ i18n.ts.appearance }}</template>
		<FormSwitch v-model="disableAnimatedMfm" class="_formBlock">{{ i18n.ts.disableAnimatedMfm }}</FormSwitch>
		<FormSwitch v-model="reduceAnimation" class="_formBlock">{{ i18n.ts.reduceUiAnimation }}</FormSwitch>
		<FormSwitch v-model="useBlurEffect" class="_formBlock">{{ i18n.ts.useBlurEffect }}</FormSwitch>
		<FormSwitch v-model="useBlurEffectForModal" class="_formBlock">{{ i18n.ts.useBlurEffectForModal }}</FormSwitch>
		<FormSwitch v-model="showGapBetweenNotesInTimeline" class="_formBlock">{{ i18n.ts.showGapBetweenNotesInTimeline }}</FormSwitch>
		<FormSwitch v-model="loadRawImages" class="_formBlock">{{ i18n.ts.loadRawImages }}</FormSwitch>
		<FormSwitch v-model="disableShowingAnimatedImages" class="_formBlock">{{ i18n.ts.disableShowingAnimatedImages }}</FormSwitch>
		<FormSwitch v-model="squareAvatars" class="_formBlock">{{ i18n.ts.squareAvatars }}</FormSwitch>
		<FormSwitch v-model="useSystemFont" class="_formBlock">{{ i18n.ts.useSystemFont }}</FormSwitch>
		<FormSwitch v-model="useOsNativeEmojis" class="_formBlock">
			{{ i18n.ts.useOsNativeEmojis }}
			<div><Mfm :key="useOsNativeEmojis" text="🍮🍦🍭🍩🍰🍫🍬🥞🍪"/></div>
		</FormSwitch>
		<FormSwitch v-model="disableDrawer" class="_formBlock">{{ i18n.ts.disableDrawer }}</FormSwitch>

		<FormSelect v-model="newNoteRecivedNotificationBehavior" class="_formBlock">
			<template #label>{{ $ts.newNoteRecivedNotification }}</template>
			<option value="default">{{ $ts._newNoteRecivedNotificationBehavior.default }}</option>
			<option value="count">{{ $ts._newNoteRecivedNotificationBehavior.count }}</option>
			<option value="none">{{ $ts._newNoteRecivedNotificationBehavior.none }}</option>
		</FormSelect>

		<FormRadios v-model="fontSize" class="_formBlock">
			<template #label>{{ i18n.ts.fontSize }}</template>
			<option value="1"><span style="font-size: 12px;">Aa</span></option>
			<option value="2"><span style="font-size: 13px;">Aa</span></option>
			<option :value="null"><span style="font-size: 14px;">Aa</span></option>
			<option value="3"><span style="font-size: 15px;">Aa</span></option>
			<option value="4"><span style="font-size: 16px;">Aa</span></option>
			<option value="5"><span style="font-size: 17px;">Aa</span></option>
		</FormRadios>
	</FormSection>

	<FormSection>
		<FormSwitch v-model="aiChanMode">{{ i18n.ts.aiChanMode }}</FormSwitch>
	</FormSection>

	<FormSelect v-model="instanceTicker" class="_formBlock">
		<template #label>{{ i18n.ts.instanceTicker }}</template>
		<option value="none">{{ i18n.ts._instanceTicker.none }}</option>
		<option value="remote">{{ i18n.ts._instanceTicker.remote }}</option>
		<option value="always">{{ i18n.ts._instanceTicker.always }}</option>
	</FormSelect>

	<FormSelect v-model="nsfw" class="_formBlock">
		<template #label>{{ i18n.ts.nsfw }}</template>
		<option value="respect">{{ i18n.ts._nsfw.respect }}</option>
		<option value="ignore">{{ i18n.ts._nsfw.ignore }}</option>
		<option value="force">{{ i18n.ts._nsfw.force }}</option>
	</FormSelect>

	<FormRange v-model="numberOfPageCache" :min="1" :max="10" :step="1" easing class="_formBlock">
		<template #label>{{ i18n.ts.numberOfPageCache }}</template>
		<template #caption>{{ i18n.ts.numberOfPageCacheDescription }}</template>
	</FormRange>

	<FormLink to="/settings/deck" class="_formBlock">{{ i18n.ts.deck }}</FormLink>

	<FormLink to="/settings/custom-css" class="_formBlock"><template #icon><i class="fas fa-code"></i></template>{{ i18n.ts.customCss }}</FormLink>
</div>
</template>

<script lang="ts" setup>
import { computed, ref, watch } from 'vue';
import FormSwitch from '@/components/form/switch.vue';
import FormSelect from '@/components/form/select.vue';
import FormRadios from '@/components/form/radios.vue';
import FormRange from '@/components/form/range.vue';
import FormSection from '@/components/form/section.vue';
import FormLink from '@/components/form/link.vue';
import MkLink from '@/components/MkLink.vue';
import { langs } from '@/config';
import { defaultStore } from '@/store';
import * as os from '@/os';
import { unisonReload } from '@/scripts/unison-reload';
import { i18n } from '@/i18n';
import { definePageMetadata } from '@/scripts/page-metadata';

const lang = ref(localStorage.getItem('lang'));
const fontSize = ref(localStorage.getItem('fontSize'));
const useSystemFont = ref(localStorage.getItem('useSystemFont') != null);

async function reloadAsk() {
	const { canceled } = await os.confirm({
		type: 'info',
		text: i18n.ts.reloadToApplySetting,
	});
	if (canceled) return;

	unisonReload();
}

const overridedDeviceKind = computed(defaultStore.makeGetterSetter('overridedDeviceKind'));
const serverDisconnectedBehavior = computed(defaultStore.makeGetterSetter('serverDisconnectedBehavior'));
const reduceAnimation = computed(defaultStore.makeGetterSetter('animation', v => !v, v => !v));
const useBlurEffectForModal = computed(defaultStore.makeGetterSetter('useBlurEffectForModal'));
const useBlurEffect = computed(defaultStore.makeGetterSetter('useBlurEffect'));
const showGapBetweenNotesInTimeline = computed(defaultStore.makeGetterSetter('showGapBetweenNotesInTimeline'));
const disableAnimatedMfm = computed(defaultStore.makeGetterSetter('animatedMfm', v => !v, v => !v));
const useOsNativeEmojis = computed(defaultStore.makeGetterSetter('useOsNativeEmojis'));
const disableDrawer = computed(defaultStore.makeGetterSetter('disableDrawer'));
const disableShowingAnimatedImages = computed(defaultStore.makeGetterSetter('disableShowingAnimatedImages'));
const loadRawImages = computed(defaultStore.makeGetterSetter('loadRawImages'));
const imageNewTab = computed(defaultStore.makeGetterSetter('imageNewTab'));
const nsfw = computed(defaultStore.makeGetterSetter('nsfw'));
const disablePagesScript = computed(defaultStore.makeGetterSetter('disablePagesScript'));
const showFixedPostForm = computed(defaultStore.makeGetterSetter('showFixedPostForm'));
const numberOfPageCache = computed(defaultStore.makeGetterSetter('numberOfPageCache'));
const instanceTicker = computed(defaultStore.makeGetterSetter('instanceTicker'));
const enableInfiniteScroll = computed(defaultStore.makeGetterSetter('enableInfiniteScroll'));
const useReactionPickerForContextMenu = computed(defaultStore.makeGetterSetter('useReactionPickerForContextMenu'));
const squareAvatars = computed(defaultStore.makeGetterSetter('squareAvatars'));
const aiChanMode = computed(defaultStore.makeGetterSetter('aiChanMode'));
const useEnterToSend = computed(defaultStore.makeGetterSetter('useEnterToSend'));
const newNoteRecivedNotificationBehavior = computed(defaultStore.makeGetterSetter('newNoteRecivedNotificationBehavior'));

watch(lang, () => {
	localStorage.setItem('lang', lang.value as string);
	localStorage.removeItem('locale');
});

watch(fontSize, () => {
	if (fontSize.value == null) {
		localStorage.removeItem('fontSize');
	} else {
		localStorage.setItem('fontSize', fontSize.value);
	}
});

watch(useSystemFont, () => {
	if (useSystemFont.value) {
		localStorage.setItem('useSystemFont', 't');
	} else {
		localStorage.removeItem('useSystemFont');
	}
});

watch([
	lang,
	fontSize,
	useSystemFont,
	enableInfiniteScroll,
	squareAvatars,
	aiChanMode,
	showGapBetweenNotesInTimeline,
	instanceTicker,
	overridedDeviceKind,
], async () => {
	await reloadAsk();
});

const headerActions = $computed(() => []);

const headerTabs = $computed(() => []);

definePageMetadata({
	title: i18n.ts.general,
	icon: 'fas fa-cogs',
});
</script>
