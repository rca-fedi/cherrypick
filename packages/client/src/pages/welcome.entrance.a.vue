<template>
<div v-if="meta" class="rsqzvsbo">
	<div class="top">
		<MkFeaturedPhotos class="bg"/>
		<XTimeline class="tl"/>
		<div class="shape1"></div>
		<div class="shape2"></div>
		<img src="/client-assets/misskey.svg" class="misskey"/>
		<div class="emojis">
			<MkEmoji :normal="true" :no-style="true" emoji="👍"/>
			<MkEmoji :normal="true" :no-style="true" emoji="❤"/>
			<MkEmoji :normal="true" :no-style="true" emoji="😆"/>
			<MkEmoji :normal="true" :no-style="true" emoji="🎉"/>
			<MkEmoji :normal="true" :no-style="true" emoji="🍮"/>
		</div>
		<div class="main">
			<img :src="$instance.iconUrl || $instance.faviconUrl || '/favicon.ico'" alt="" class="icon"/>
			<button class="_button _acrylic menu" @click="showMenu"><i class="fas fa-ellipsis-h"></i></button>
			<div class="fg">
				<h1>
					<!-- 背景色によってはロゴが見えなくなるのでとりあえず無効に -->
					<!-- <img class="logo" v-if="meta.logoImageUrl" :src="meta.logoImageUrl"><span v-else class="text">{{ instanceName }}</span> -->
					<span class="text">{{ instanceName }}</span>
				</h1>
				<div class="about">
					<!-- eslint-disable-next-line vue/no-v-html -->
					<div class="desc" v-html="meta.description || i18n.ts.headlineMisskey"></div>
				</div>
				<div class="action">
					<MkButton inline rounded gradate data-cy-signup class="button" @click="signup()">{{ i18n.ts.signup }}</MkButton>
					<MkButton inline rounded data-cy-signin class="button" @click="signin()">{{ i18n.ts.login }}</MkButton>
					<MkButton inline rounded class="button" @click="mainRouter.push('/explore')">{{ i18n.ts.explore }}</MkButton>
				</div>
			</div>
		</div>
		<div v-if="instances" class="federation">
			<MarqueeText :duration="40">
				<MkA v-for="instance in instances" :key="instance.id" :class="$style.federationInstance" :to="`/instance-info/${instance.host}`" behavior="window">
					<!--<MkInstanceCardMini :instance="instance"/>-->
					<img v-if="instance.iconUrl" class="icon" :src="instance.iconUrl" alt=""/>
					<span class="name _monospace">{{ instance.host }}</span>
				</MkA>
			</MarqueeText>
		</div>
	</div>
</div>
</template>

<script lang="ts" setup>
import { } from 'vue';
import { toUnicode } from 'punycode/';
import XTimeline from './welcome.timeline.vue';
import MarqueeText from '@/components/MkMarquee.vue';
import XSigninDialog from '@/components/MkSigninDialog.vue';
import XSignupDialog from '@/components/MkSignupDialog.vue';
import MkButton from '@/components/MkButton.vue';
import XNote from '@/components/MkNote.vue';
import MkFeaturedPhotos from '@/components/MkFeaturedPhotos.vue';
import { host, instanceName } from '@/config';
import * as os from '@/os';
import number from '@/filters/number';
import { i18n } from '@/i18n';
import { mainRouter } from '@/router';

let meta = $ref();
let stats = $ref();
let tags = $ref();
let onlineUsersCount = $ref();
let instances = $ref();

os.api('meta', { detail: true }).then(_meta => {
	meta = _meta;
});

os.api('stats').then(_stats => {
	stats = _stats;
});

os.api('get-online-users-count').then(res => {
	onlineUsersCount = res.count;
});

os.api('hashtags/list', {
	sort: '+mentionedLocalUsers',
	limit: 8,
}).then(_tags => {
	tags = _tags;
});

os.api('federation/instances', {
	sort: '+pubSub',
	limit: 20,
}).then(_instances => {
	instances = _instances;
});

function signin() {
	os.popup(XSigninDialog, {
		autoSet: true,
	}, {}, 'closed');
}

function signup() {
	os.popup(XSignupDialog, {
		autoSet: true,
	}, {}, 'closed');
}

function showMenu(ev) {
	os.popupMenu([{
		text: i18n.ts.instanceInfo,
		icon: 'fas fa-info-circle',
		action: () => {
			os.pageWindow('/about');
		},
	}, {
		text: i18n.ts.aboutMisskey,
		icon: 'fas fa-info-circle',
		action: () => {
			os.pageWindow('/about-misskey');
		},
	}, null, {
		text: i18n.ts.help,
		icon: 'fas fa-question-circle',
		action: () => {
			window.open('https://misskey-hub.net/help.md', '_blank');
		},
	}], ev.currentTarget ?? ev.target);
}
</script>

<style lang="scss" scoped>
.rsqzvsbo {
	> .top {
		display: flex;
		text-align: center;
		min-height: 100vh;
		box-sizing: border-box;
		padding: 16px;

		> .bg {
			position: absolute;
			top: 0;
			right: 0;
			width: 80%; // 100%からshapeの幅を引いている
			height: 100%;
		}

		> .tl {
			position: absolute;
			top: 0;
			bottom: 0;
			right: 64px;
			margin: auto;
			width: 500px;
			height: calc(100% - 128px);
			overflow: hidden;
			-webkit-mask-image: linear-gradient(0deg, rgba(0,0,0,0) 0%, rgba(0,0,0,1) 128px, rgba(0,0,0,1) calc(100% - 128px), rgba(0,0,0,0) 100%);
			mask-image: linear-gradient(0deg, rgba(0,0,0,0) 0%, rgba(0,0,0,1) 128px, rgba(0,0,0,1) calc(100% - 128px), rgba(0,0,0,0) 100%);

			@media (max-width: 1200px) {
				display: none;
			}
		}

		> .shape1 {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			background: var(--accent);
			clip-path: polygon(0% 0%, 45% 0%, 20% 100%, 0% 100%);
		}
		> .shape2 {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			background: var(--accent);
			clip-path: polygon(0% 0%, 25% 0%, 35% 100%, 0% 100%);
			opacity: 0.5;
		}

		> .misskey {
			position: absolute;
			top: 42px;
			left: 42px;
			width: 140px;

			@media (max-width: 450px) {
				width: 130px;
			}
		}

		> .emojis {
			position: absolute;
			bottom: 32px;
			left: 35px;

			> * {
				margin-right: 8px;
			}

			@media (max-width: 1200px) {
				display: none;
			}
		}

		> .main {
			position: relative;
			width: min(480px, 100%);
			margin: auto auto auto 128px;
			background: var(--panel);
			border-radius: var(--radius);
			box-shadow: 0 12px 32px rgb(0 0 0 / 25%);

			@media (max-width: 1200px) {
				margin: auto;
			}

			> .icon {
				width: 85px;
				margin-top: -47px;
				border-radius: 100%;
				vertical-align: bottom;
			}

			> .menu {
				position: absolute;
				top: 16px;
				right: 16px;
				width: 32px;
				height: 32px;
				border-radius: 8px;
				font-size: 18px;
				z-index: 11;
			}

			> .fg {
				position: relative;
				z-index: 1;

				> h1 {
					display: block;
					margin: 0;
					padding: 16px 32px 24px 32px;
					font-size: 1.4em;

					> .logo {
						vertical-align: bottom;
						max-height: 120px;
						max-width: min(100%, 300px);
					}
				}

				> .about {
					padding: 0 32px;
				}

				> .action {
					padding: 32px;

					> * {
						line-height: 28px;
					}

					@media (min-width: 501px) {
						button:not(:last-child) {
							margin-right: 12px;
						}
					}

					@media (max-width: 500px) {
						button:not(:first-child) {
							margin-top: 10px;
						}

						> .button {
							width: 90%;
							margin-right: initial;
						}
					}
				}
			}
		}

		> .federation {
			position: absolute;
			bottom: 16px;
			left: 0;
			right: 0;
			margin: auto;
			background: var(--acrylicPanel);
			-webkit-backdrop-filter: var(--blur, blur(15px));
			backdrop-filter: var(--blur, blur(15px));
			border-radius: 999px;
			overflow: clip;
			width: 800px;
			padding: 8px 0;

			@media (max-width: 900px) {
				display: none;
			}
		}
	}
}
</style>

<style lang="scss" module>
.federationInstance {
	display: inline-flex;
	align-items: center;
	vertical-align: bottom;
	padding: 6px 12px 6px 6px;
	margin: 0 10px 0 0;
	background: var(--panel);
	border-radius: 999px;

	> :global(.icon) {
		display: inline-block;
		width: 20px;
		height: 20px;
		margin-right: 5px;
		border-radius: 999px;
	}
}
</style>
