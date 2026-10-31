<!--
  - SPDX-FileCopyrightText: 2024 Nextcloud GmbH and Nextcloud contributors
  - SPDX-License-Identifier: AGPL-3.0-or-later
  -->

<script setup lang="ts">
import { provide, ref } from 'vue'
import AppWindow from '../../shared/components/AppWindow.vue'
import TalkWrapper from './TalkWrapper/TalkWrapper.vue'
import TitleBar from './TitleBar/TitleBar.vue'
import { useNotificationsStore } from './notifications/notifications.store.js'
import { createViewer } from './Viewer/Viewer.js'

const isTalkInitialized = ref(false)
provide('talk:isInitialized', isTalkInitialized)

const isPipMode = ref(false)

async function restoreFromPip() {
	if (!isPipMode.value) {
		return
	}

	isPipMode.value = await window.TALK_DESKTOP.togglePip()
}

if (process.env.NODE_ENV === 'development') {
	Object.assign(window, {
		testTogglePip: async () => {
			isPipMode.value = await window.TALK_DESKTOP.togglePip()
			return isPipMode.value
		},
	})
}

useNotificationsStore()

window.OCA.Viewer = createViewer()
</script>

<template>
	<AppWindow :class="{ 'talk-desktop--pip': isPipMode }">
		<div id="skip-actions" />
		<TitleBar id="header" />
		<button
			v-if="isPipMode"
			class="pip-restore-button"
			type="button"
			aria-label="Restore Nextcloud Talk window"
			title="Restore Nextcloud Talk window"
			@click="restoreFromPip">
			⛶
		</button>
		<TalkWrapper @ready="isTalkInitialized = true" />
	</AppWindow>
</template>
