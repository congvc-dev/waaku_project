<template>
	<div class="min-h-screen bg-gradient-to-br from-green-50 via-blue-50 to-purple-50">
		<!-- Header -->
		<div class="bg-white shadow-lg border-b border-gray-200 sticky top-0 z-30">
			<div class="max-w-7xl mx-auto px-6 py-4">
				<div class="flex flex-col gap-4 md:flex-row md:items-center md:justify-between">
					<div class="flex items-center space-x-4">
						<div class="w-12 h-12 bg-gradient-to-r from-green-500 to-blue-500 rounded-full flex items-center justify-center">
							<svg class="w-8 h-8 text-white" fill="currentColor" viewBox="0 0 24 24">
								<path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.62-.01-.248 0-.654.037-.953.371-.298.334-1.037 1.016-1.037 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.890-5.335 11.893-11.893A11.821 11.821 0 0020.465 3.516"/>
							</svg>
						</div>
						<div>
							<h1 class="text-2xl font-bold text-gray-900">Waaku</h1>
							<p class="text-gray-600 text-sm">WhatsApp sessions, inbox, and sending</p>
						</div>
					</div>
					<div class="flex flex-wrap items-center gap-3">
						<button @click="fetchAllData" class="px-4 py-2 bg-blue-500 hover:bg-blue-600 text-white rounded-lg transition-colors flex items-center space-x-2">
							<span>Refresh</span>
						</button>
						<button @click="openModal" class="px-4 py-2 bg-gradient-to-r from-green-500 to-blue-500 hover:from-green-600 hover:to-blue-600 text-white rounded-lg transition-colors font-semibold">
							Create Session
						</button>
					</div>
				</div>
			</div>
		</div>

		<!-- Main Content -->
		<div class="max-w-7xl mx-auto px-6 py-8 space-y-8">
			<!-- Dashboard Stats -->
			<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6" v-if="healthData">
				<div class="bg-white rounded-2xl shadow-xl p-6 border-l-4 border-blue-500 transform hover:scale-105 transition-all duration-200">
					<dt class="text-sm font-medium text-gray-500 truncate">Total Sessions</dt>
					<dd class="text-2xl font-bold text-gray-900 mt-2">{{ healthData.summary?.total || 0 }}</dd>
				</div>
				<div class="bg-white rounded-2xl shadow-xl p-6 border-l-4 border-green-500 transform hover:scale-105 transition-all duration-200">
					<dt class="text-sm font-medium text-gray-500 truncate">Ready</dt>
					<dd class="text-2xl font-bold text-gray-900 mt-2">{{ healthData.summary?.ready || 0 }}</dd>
				</div>
				<div class="bg-white rounded-2xl shadow-xl p-6 border-l-4 border-yellow-500 transform hover:scale-105 transition-all duration-200">
					<dt class="text-sm font-medium text-gray-500 truncate">Healthy</dt>
					<dd class="text-2xl font-bold text-gray-900 mt-2">{{ healthData.summary?.healthy || 0 }}</dd>
				</div>
				<div class="bg-white rounded-2xl shadow-xl p-6 border-l-4 border-red-500 transform hover:scale-105 transition-all duration-200">
					<dt class="text-sm font-medium text-gray-500 truncate">Unhealthy</dt>
					<dd class="text-2xl font-bold text-gray-900 mt-2">{{ healthData.summary?.unhealthy || 0 }}</dd>
				</div>
			</div>

			<!-- Messaging Inbox -->
			<section class="bg-white rounded-2xl shadow-xl border border-gray-100 overflow-hidden">
				<div class="p-6 border-b border-gray-100 flex flex-col gap-4 lg:flex-row lg:items-center lg:justify-between">
					<div>
						<h2 class="text-2xl font-bold text-gray-900">Inbox & Messages</h2>
						<p class="text-sm text-gray-500 mt-1">Browse chats, fetch the latest 50 messages, validate numbers, and send replies.</p>
					</div>
					<div class="flex flex-col sm:flex-row gap-3 sm:items-center">
						<select v-model="activeSessionId" @change="handleSessionChange" class="px-4 py-2 border border-gray-300 rounded-xl focus:ring-2 focus:ring-green-500 focus:border-green-500 min-w-56">
							<option value="">Select ready session</option>
							<option v-for="s in readySessions" :key="s.id" :value="s.id">{{ s.id }}</option>
						</select>
						<button :disabled="!activeSessionId || chatsLoading" @click="loadChats" class="px-4 py-2 bg-green-500 hover:bg-green-600 disabled:bg-gray-300 text-white rounded-xl transition-colors">
							{{ chatsLoading ? 'Loading...' : 'Refresh chats' }}
						</button>
					</div>
				</div>

				<div v-if="readySessions.length === 0" class="p-8 text-center text-gray-500">
					No ready WhatsApp sessions yet. Create/connect a session first, then the inbox will appear here.
				</div>

				<div v-else class="grid grid-cols-1 lg:grid-cols-[360px_1fr] min-h-[620px]">
					<!-- Chats column -->
					<aside class="border-r border-gray-100 bg-gray-50/80 flex flex-col min-h-[620px]">
						<div class="p-4 border-b border-gray-100 space-y-3 bg-white">
							<input v-model="chatSearch" type="search" placeholder="Search chats..." class="w-full px-4 py-2 border border-gray-300 rounded-xl focus:ring-2 focus:ring-green-500 focus:border-green-500" />
							<div class="text-xs text-gray-500 flex items-center justify-between">
								<span>{{ filteredChats.length }} chats</span>
								<span v-if="selectedChat">Selected: {{ selectedChat.name || selectedChat.id }}</span>
							</div>
						</div>

						<div v-if="chatsError" class="m-4 p-3 bg-red-50 text-red-700 rounded-xl text-sm">
							{{ chatsError }}
						</div>

						<div v-if="chatsLoading" class="p-6 text-center text-gray-500">Loading chats...</div>
						<div v-else-if="activeSessionId && chats.length === 0" class="p-6 text-center text-gray-500">
							No chats loaded yet. Click <b>Refresh chats</b>.
						</div>
						<div v-else class="overflow-y-auto flex-1">
							<button
								v-for="chat in filteredChats"
								:key="chat.id"
								@click="selectChat(chat)"
								:class="selectedChatId === chat.id ? 'bg-green-100 border-green-400' : 'bg-white hover:bg-gray-50 border-transparent'"
								class="w-full text-left p-4 border-l-4 border-b border-gray-100 transition-colors"
							>
								<div class="flex items-start justify-between gap-3">
									<div class="min-w-0">
										<div class="flex items-center gap-2">
											<h3 class="font-semibold text-gray-900 truncate">{{ chat.name || chat.id }}</h3>
											<span v-if="chat.isGroup" class="text-[10px] uppercase tracking-wide bg-blue-100 text-blue-700 px-2 py-0.5 rounded-full">group</span>
											<span v-if="chat.pinned" class="text-[10px] uppercase tracking-wide bg-yellow-100 text-yellow-700 px-2 py-0.5 rounded-full">pinned</span>
										</div>
										<p class="text-xs text-gray-500 truncate mt-1">{{ chat.id }}</p>
									</div>
									<div class="text-right flex-shrink-0">
										<div class="text-xs text-gray-400">{{ formatTimestamp(chat.timestamp) }}</div>
										<div v-if="chat.unreadCount" class="mt-2 inline-flex items-center justify-center min-w-6 h-6 rounded-full bg-green-500 text-white text-xs font-bold px-2">
											{{ chat.unreadCount }}
										</div>
									</div>
								</div>
							</button>
						</div>
					</aside>

					<!-- Messages column -->
					<section class="flex flex-col min-h-[620px] bg-gradient-to-b from-white to-green-50/30">
						<div class="p-4 border-b border-gray-100 bg-white flex flex-col gap-3 md:flex-row md:items-center md:justify-between">
							<div class="min-w-0">
								<h3 class="text-lg font-bold text-gray-900 truncate">{{ selectedChat ? (selectedChat.name || selectedChat.id) : 'Select a chat' }}</h3>
								<p class="text-xs text-gray-500 truncate">{{ selectedChat?.id || 'Or type a phone/JID below to send a new message' }}</p>
							</div>
							<button :disabled="!selectedChat || messagesLoading" @click="loadMessages" class="px-4 py-2 bg-blue-500 hover:bg-blue-600 disabled:bg-gray-300 text-white rounded-xl transition-colors">
								{{ messagesLoading ? 'Loading...' : 'Refresh messages' }}
							</button>
						</div>

						<div v-if="messagesError" class="m-4 p-3 bg-red-50 text-red-700 rounded-xl text-sm">
							{{ messagesError }}
						</div>

						<div ref="messagesPane" class="flex-1 overflow-y-auto p-4 space-y-3">
							<div v-if="!activeSessionId" class="h-full flex items-center justify-center text-gray-500 text-center">
								Choose a ready session to browse chats.
							</div>
							<div v-else-if="messagesLoading" class="h-full flex items-center justify-center text-gray-500 text-center">
								Loading messages...
							</div>
							<div v-else-if="selectedChat && messages.length === 0" class="h-full flex items-center justify-center text-gray-500 text-center">
								No messages returned for this chat.
							</div>
							<div v-else-if="!selectedChat" class="h-full flex items-center justify-center text-gray-500 text-center">
								Select a chat from the left, or use the composer below for direct send.
							</div>

							<div
								v-for="message in messages"
								:key="message.id"
								:class="message.fromMe ? 'justify-end' : 'justify-start'"
								class="flex"
							>
								<div :class="message.fromMe ? 'bg-green-500 text-white rounded-br-sm' : 'bg-white text-gray-900 border border-gray-100 rounded-bl-sm'" class="max-w-[82%] rounded-2xl px-4 py-3 shadow-sm">
									<div v-if="message.mediaData && message.mimeType?.startsWith('image/')" class="mb-2">
										<img :src="`data:${message.mimeType};base64,${message.mediaData}`" class="max-h-72 rounded-xl" alt="media" />
									</div>
									<div v-else-if="message.hasMedia" class="mb-2 text-xs opacity-80">📎 {{ message.type || 'media' }}</div>
									<p class="whitespace-pre-wrap break-words">{{ message.body || '(empty message)' }}</p>
									<div :class="message.fromMe ? 'text-green-100' : 'text-gray-400'" class="text-[11px] mt-2 text-right">
										{{ formatTimestamp(message.timestamp) }} · {{ message.type || 'chat' }}
									</div>
								</div>
							</div>
						</div>

						<!-- Composer -->
						<div class="border-t border-gray-100 bg-white p-4 space-y-3">
							<div class="grid grid-cols-1 md:grid-cols-[1fr_auto] gap-3">
								<input
									v-model="messageTarget"
									type="text"
									:placeholder="selectedChat ? 'Using selected chat ID; edit to override' : 'Phone number or WhatsApp JID, e.g. 628xxx or ...@c.us'"
									class="w-full px-4 py-2 border border-gray-300 rounded-xl focus:ring-2 focus:ring-green-500 focus:border-green-500"
								/>
								<button :disabled="!activeSessionId || !messageTarget.trim() || validationLoading" @click="validateNumber" class="px-4 py-2 bg-gray-700 hover:bg-gray-800 disabled:bg-gray-300 text-white rounded-xl transition-colors">
									{{ validationLoading ? 'Checking...' : 'Validate' }}
								</button>
							</div>
							<div v-if="validationResult" :class="validationResult.exists ? 'bg-green-50 text-green-700' : 'bg-red-50 text-red-700'" class="px-3 py-2 rounded-xl text-sm">
								{{ validationResult.exists ? `Valid WhatsApp number: ${validationResult.chatId || validationResult.number}` : 'Number not found on WhatsApp' }}
							</div>
							<div class="grid grid-cols-1 md:grid-cols-[1fr_auto] gap-3">
								<textarea v-model="composerText" rows="2" placeholder="Type a message..." class="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-green-500 focus:border-green-500 resize-none" @keydown.ctrl.enter.prevent="sendComposerMessage" @keydown.meta.enter.prevent="sendComposerMessage"></textarea>
								<button :disabled="!canSend" @click="sendComposerMessage" class="px-6 py-3 bg-green-500 hover:bg-green-600 disabled:bg-gray-300 text-white font-semibold rounded-xl transition-colors">
									{{ sending ? 'Sending...' : 'Send' }}
								</button>
							</div>
							<p class="text-xs text-gray-500">Tip: select a chat to reply there, or type a phone/JID manually. Ctrl/⌘ + Enter sends.</p>
						</div>
					</section>
				</div>
			</section>

			<!-- Sessions List -->
			<section class="space-y-6">
				<div class="flex items-center justify-between">
					<h2 class="text-2xl font-bold text-gray-900">Active Sessions ({{ sessions.length }})</h2>
				</div>

				<div v-if="sessions.length === 0" class="bg-white rounded-2xl shadow-xl p-12 text-center">
					<h3 class="text-xl font-semibold text-gray-900 mb-2">No sessions yet</h3>
					<p class="text-gray-500 mb-6">Create your first WhatsApp session to get started.</p>
					<button @click="openModal" class="inline-flex items-center px-6 py-3 bg-gradient-to-r from-green-500 to-blue-500 hover:from-green-600 hover:to-blue-600 text-white font-semibold rounded-xl transition-all shadow-lg">
						Create Your First Session
					</button>
				</div>

				<div v-for="s in sessions" :key="s.id" class="bg-white rounded-2xl shadow-xl border border-gray-100 overflow-hidden hover:shadow-2xl transition-all duration-300">
					<div class="p-6 border-b border-gray-100">
						<div class="flex flex-col gap-4 lg:flex-row lg:items-center lg:justify-between">
							<div class="flex items-center space-x-4 min-w-0">
								<div class="relative flex-shrink-0">
									<div :class="getStatusBadgeClass(s)" class="w-12 h-12 rounded-full flex items-center justify-center">
										<svg class="w-8 h-8 text-white" fill="currentColor" viewBox="0 0 24 24">
											<path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.62-.01-.248 0-.654.037-.953.371-.298.334-1.037 1.016-1.037 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347"/>
										</svg>
									</div>
									<div :class="getStatusIndicatorClass(s)" class="absolute -bottom-1 -right-1 w-4 h-4 rounded-full border-4 border-white"></div>
								</div>
								<div class="min-w-0">
									<h3 class="text-md font-bold text-gray-900 truncate">{{ s.id }}</h3>
									<div class="flex items-center flex-wrap gap-2 mt-1">
										<span :class="getStatusTextClass(s)" class="px-3 py-1 rounded-full text-sm font-medium">{{ getStatusText(s) }}</span>
										<span v-if="s.uptime" class="text-sm text-gray-500">⏱️ {{ formatUptime(s.uptime) }}</span>
									</div>
								</div>
							</div>

							<div class="flex items-center flex-wrap gap-2">
								<button v-if="s.ready" @click="useSession(s.id)" class="px-3 py-2 text-green-700 bg-green-50 hover:bg-green-100 rounded-lg transition-colors" title="Use in inbox">Use Inbox</button>
								<button v-if="!qrLoading[s.id] && (qr[s.id] && qr[s.id] !== '')" @click="openQrModal(s.id)" class="px-3 py-2 text-green-600 hover:bg-green-50 rounded-lg transition-colors" title="Show QR Code">Show QR</button>
								<button @click="checkSessionHealth(s.id)" class="px-3 py-2 text-blue-600 hover:bg-blue-50 rounded-lg transition-colors" title="Check Health">Health</button>
								<button @click="restartSession(s.id)" class="px-3 py-2 text-orange-600 hover:bg-orange-50 rounded-lg transition-colors" title="Restart Session">Restart</button>
								<button @click="deleteSession(s.id)" class="px-3 py-2 text-red-600 hover:bg-red-50 rounded-lg transition-colors" title="Delete Session">Delete</button>
							</div>
						</div>
					</div>

					<div v-if="s.error" class="p-6 bg-red-50">
						<h4 class="text-lg font-semibold text-red-800">Connection Error</h4>
						<p class="text-red-700 mt-1">{{ s.error }}</p>
						<button @click="restartSession(s.id)" class="mt-3 px-4 py-2 bg-red-600 hover:bg-red-700 text-white font-medium rounded-lg transition-colors">Try Restart</button>
					</div>
				</div>
			</section>
		</div>

		<!-- Loading Overlay -->
		<div v-if="loading" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
			<div class="bg-white rounded-2xl p-8 shadow-2xl max-w-sm w-full mx-4">
				<div class="text-center">
					<div class="animate-spin rounded-full h-16 w-16 border-b-4 border-green-500 mx-auto mb-4"></div>
					<h3 class="text-lg font-semibold text-gray-900 mb-2">Processing...</h3>
					<p class="text-gray-600">{{ loadingMessage || 'Please wait...' }}</p>
				</div>
			</div>
		</div>

		<!-- Toast Notifications -->
		<div v-if="notification" class="fixed top-4 right-4 z-50 transform transition-all duration-300 max-w-md">
			<div :class="notification.type === 'success' ? 'bg-green-500' : 'bg-red-500'" class="text-white px-6 py-4 rounded-lg shadow-lg flex items-center space-x-3">
				<span class="font-medium">{{ notification.message }}</span>
			</div>
		</div>

		<CreateSessionModal :is-visible="showModal" :is-loading="loading" @close="closeModal" @submit="handleCreateSession" />

		<!-- QR Modal -->
		<div v-if="qrModal.visible" class="fixed inset-0 bg-black/60 flex items-center justify-center z-50">
			<div class="bg-white rounded-2xl p-6 shadow-2xl w-[90vw] max-w-md mx-4">
				<div class="flex items-center justify-between mb-4">
					<h3 class="text-lg font-semibold text-gray-900">Scan QR — {{ qrModal.sessionId }}</h3>
					<button @click="closeQrModal" class="p-2 rounded-lg hover:bg-gray-100">✕</button>
				</div>
				<div class="flex flex-col items-center">
					<div v-if="qrLoading[qrModal.sessionId]" class="flex items-center justify-center h-72 w-72">
						<div class="animate-spin rounded-full h-10 w-10 border-b-4 border-green-500"></div>
					</div>
					<div v-else-if="qr[qrModal.sessionId]" class="bg-white p-3 rounded-xl shadow border">
						<img :src="qr[qrModal.sessionId]" alt="WhatsApp QR Code" class="w-72 h-72"/>
					</div>
					<p class="mt-4 text-sm text-gray-600 text-center">Open WhatsApp → Settings → Linked Devices → Link a Device → Scan this QR code</p>
				</div>
			</div>
		</div>
	</div>
</template>

<script setup>
import { ref, reactive, computed, nextTick, onMounted } from 'vue'
import api, { getErrorMessage, isNetworkError } from './lib/api.js'
import CreateSessionModal from './components/CreateSessionModal.vue'
import { connectSocket, on as onSocket, off as offSocket } from './lib/socket.js'

const sessions = ref([])
const qr = reactive({})
const qrLoading = reactive({})
const healthData = ref(null)
const loading = ref(false)
const loadingMessage = ref('')
const notification = ref(null)
const showModal = ref(false)
const qrModal = reactive({ visible: false, sessionId: '' })

const activeSessionId = ref('')
const chats = ref([])
const chatSearch = ref('')
const selectedChatId = ref('')
const messages = ref([])
const messagesPane = ref(null)
const messageTarget = ref('')
const composerText = ref('')
const validationResult = ref(null)
const chatsLoading = ref(false)
const messagesLoading = ref(false)
const validationLoading = ref(false)
const sending = ref(false)
const chatsError = ref('')
const messagesError = ref('')

const readySessions = computed(() => sessions.value.filter(s => s.ready))
const selectedChat = computed(() => chats.value.find(chat => chat.id === selectedChatId.value) || null)
const filteredChats = computed(() => {
	const q = chatSearch.value.trim().toLowerCase()
	if (!q) return chats.value
	return chats.value.filter(chat => `${chat.name || ''} ${chat.id}`.toLowerCase().includes(q))
})
const canSend = computed(() => Boolean(activeSessionId.value && messageTarget.value.trim() && composerText.value.trim() && !sending.value))

function showNotification(message, type = 'success') {
	notification.value = { message, type }
	setTimeout(() => {
		notification.value = null
	}, 4000)
}

function showLoading(message = '') {
	loading.value = true
	loadingMessage.value = message
}

function hideLoading() {
	loading.value = false
	loadingMessage.value = ''
}

function openModal() {
	showModal.value = true
}

function closeModal() {
	showModal.value = false
}

function openQrModal(id) {
	qrModal.sessionId = id
	qrModal.visible = true
	if (!qr[id] && !qrLoading[id]) loadQr(id)
}

function closeQrModal() {
	qrModal.visible = false
}

function handleCreateSession(sessionName) {
	addSession(sessionName)
}

function getStatusBadgeClass(session) {
	if (session.error) return 'bg-gradient-to-r from-red-500 to-red-600'
	if (session.ready) return 'bg-gradient-to-r from-green-500 to-green-600'
	return 'bg-gradient-to-r from-yellow-500 to-orange-500'
}

function getStatusIndicatorClass(session) {
	if (session.error) return 'bg-red-500'
	if (session.ready) return 'bg-green-500'
	return 'bg-yellow-500'
}

function getStatusTextClass(session) {
	if (session.error) return 'bg-red-100 text-red-800'
	if (session.ready) return 'bg-green-100 text-green-800'
	return 'bg-yellow-100 text-yellow-800'
}

function getStatusText(session) {
	if (session.error) return '❌ Error'
	if (session.ready) return '✅ Ready'
	return '⏳ Connecting...'
}

function formatUptime(seconds) {
	if (seconds < 60) return `${seconds}s`
	if (seconds < 3600) return `${Math.floor(seconds / 60)}m`
	return `${Math.floor(seconds / 3600)}h ${Math.floor((seconds % 3600) / 60)}m`
}

function formatTimestamp(timestamp) {
	if (!timestamp) return ''
	const ms = Number(timestamp) < 10000000000 ? Number(timestamp) * 1000 : Number(timestamp)
	const date = new Date(ms)
	if (Number.isNaN(date.getTime())) return ''
	return date.toLocaleString([], { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' })
}

function scrollMessagesToBottom() {
	nextTick(() => {
		if (messagesPane.value) messagesPane.value.scrollTop = messagesPane.value.scrollHeight
	})
}

function resetInboxForSession() {
	chats.value = []
	selectedChatId.value = ''
	messages.value = []
	messageTarget.value = ''
	validationResult.value = null
	chatsError.value = ''
	messagesError.value = ''
}

async function handleSessionChange() {
	resetInboxForSession()
	if (activeSessionId.value) await loadChats()
}

async function useSession(id) {
	if (activeSessionId.value !== id) {
		activeSessionId.value = id
		await handleSessionChange()
	} else if (chats.value.length === 0) {
		await loadChats()
	}
}

async function loadChats() {
	if (!activeSessionId.value) return
	chatsLoading.value = true
	chatsError.value = ''
	try {
		const data = await api.getChats(activeSessionId.value)
		chats.value = [...data].sort((a, b) => (b.pinned === a.pinned ? (b.timestamp || 0) - (a.timestamp || 0) : Number(b.pinned) - Number(a.pinned)))
		if (selectedChatId.value && !chats.value.some(chat => chat.id === selectedChatId.value)) {
			selectedChatId.value = ''
			messages.value = []
		}
	} catch (err) {
		chatsError.value = getErrorMessage(err)
		showNotification(`Failed to load chats: ${chatsError.value}`, 'error')
	} finally {
		chatsLoading.value = false
	}
}

async function selectChat(chat) {
	selectedChatId.value = chat.id
	messageTarget.value = chat.id
	validationResult.value = null
	await loadMessages()
}

async function loadMessages() {
	if (!activeSessionId.value || !selectedChatId.value) return
	messagesLoading.value = true
	messagesError.value = ''
	try {
		messages.value = await api.getChatMessages(activeSessionId.value, selectedChatId.value)
		scrollMessagesToBottom()
		await loadChats()
	} catch (err) {
		messagesError.value = getErrorMessage(err)
		showNotification(`Failed to load messages: ${messagesError.value}`, 'error')
	} finally {
		messagesLoading.value = false
	}
}

async function validateNumber() {
	if (!activeSessionId.value || !messageTarget.value.trim()) {
		showNotification('Select a session and enter a phone number/JID first.', 'error')
		return
	}
	validationLoading.value = true
	validationResult.value = null
	try {
		const data = await api.validatePhoneNumber(activeSessionId.value, messageTarget.value.trim())
		validationResult.value = data
		showNotification(data.exists ? 'Phone number is valid!' : 'Phone number not found on WhatsApp', data.exists ? 'success' : 'error')
	} catch (err) {
		showNotification(`Failed to validate number: ${getErrorMessage(err)}`, 'error')
	} finally {
		validationLoading.value = false
	}
}

async function sendComposerMessage() {
	if (!canSend.value) return
	sending.value = true
	try {
		const target = messageTarget.value.trim()
		const text = composerText.value.trim()
		const data = await api.sendMessage(activeSessionId.value, target, text)
		if (data.success) {
			showNotification('Message sent successfully! 🚀')
			composerText.value = ''
			if (selectedChatId.value && (target === selectedChatId.value || data.to === selectedChatId.value)) {
				await loadMessages()
			} else {
				await loadChats()
			}
		} else {
			showNotification(`Failed to send message: ${data.error || 'unknown error'}`, 'error')
		}
	} catch (err) {
		showNotification(`Failed to send message: ${getErrorMessage(err)}`, 'error')
	} finally {
		sending.value = false
	}
}

function normalizeSocketPayload(payload) {
	const data = payload?.data || payload || {}
	return {
		id: data.messageId || `${data.sessionId || activeSessionId.value}-${data.timestamp || Date.now()}-${data.from || ''}`,
		body: data.body || '',
		timestamp: data.timestamp || Math.floor(Date.now() / 1000),
		from: data.from,
		to: data.to,
		fromMe: false,
		hasMedia: false,
		type: 'chat',
		chatId: data.from || data.chat?.id,
		chatName: data.chat?.name || data.contact?.name || data.contact?.number
	}
}

async function applyIncomingMessage(payload) {
	const msg = normalizeSocketPayload(payload)
	const sessionId = payload?.sessionId || payload?.data?.sessionId
	if (sessionId && activeSessionId.value && sessionId !== activeSessionId.value) return

	if (msg.chatId && selectedChatId.value === msg.chatId) {
		if (!messages.value.some(existing => existing.id === msg.id)) {
			messages.value = [...messages.value, msg]
			scrollMessagesToBottom()
		}
	}

	if (msg.chatId) {
		const existing = chats.value.find(chat => chat.id === msg.chatId)
		if (existing) {
			existing.timestamp = msg.timestamp
			existing.unreadCount = selectedChatId.value === msg.chatId ? 0 : (existing.unreadCount || 0) + 1
		} else if (activeSessionId.value) {
			chats.value = [{ id: msg.chatId, name: msg.chatName || msg.chatId, unreadCount: 1, timestamp: msg.timestamp, pinned: false, isGroup: String(msg.chatId).endsWith('@g.us') }, ...chats.value]
		}
	}
}

let detachSockets = null

function initSockets() {
	if (detachSockets) {
		detachSockets()
		detachSockets = null
	}

	fetchAllData()
	connectSocket()

	const updateSessions = (list) => {
		sessions.value = list
		if (!activeSessionId.value && readySessions.value.length > 0) {
			activeSessionId.value = readySessions.value[0].id
			loadChats()
		}
		if (activeSessionId.value && !list.some(s => s.id === activeSessionId.value && s.ready)) {
			resetInboxForSession()
			activeSessionId.value = ''
		}
		if (qrModal.visible && qrModal.sessionId) {
			const s = list.find(x => x.id === qrModal.sessionId)
			if (!s || s.ready || s.status === 'disconnected') closeQrModal()
		}
		for (const s of list) {
			if (s.ready || s.status === 'disconnected') {
				if (qr[s.id]) qr[s.id] = ''
				qrLoading[s.id] = false
			}
		}
	}
	const onSessionUpdate = (list) => updateSessions(list)
	const onSessionReady = (payload) => {
		if (payload?.id && qrModal.visible && qrModal.sessionId === payload.id) closeQrModal()
		if (payload?.id) {
			qr[payload.id] = ''
			qrLoading[payload.id] = false
		}
		fetchAllData()
	}
	const onSessionError = () => fetchAllData()
	const onSessionDisconnected = ({ id, reason }) => {
		if (qrModal.visible && qrModal.sessionId === id) closeQrModal()
		if (id) {
			qr[id] = ''
			qrLoading[id] = false
		}
		if (activeSessionId.value === id) {
			resetInboxForSession()
			activeSessionId.value = ''
		}
		showNotification(`Session ${id} disconnected${reason ? `: ${reason}` : ''}`, 'error')
	}
	const onQR = ({ id, qr: qrStr }) => {
		if (qrStr) qr[id] = qrStr
		if (id) qrLoading[id] = false
	}
	const onHealthUpdate = (payload) => {
		healthData.value = payload
	}
	const onMessageReply = (payload) => {
		const data = payload?.data || payload
		applyIncomingMessage(payload)
		showNotification(`💬 Reply from ${data.contact?.name || data.contact?.number || data.from}: "${(data.body || '').substring(0, 50)}"`)
	}
	const onMessageReceived = (payload) => {
		const data = payload?.data || payload
		applyIncomingMessage(payload)
		if (!data.isReply) {
			showNotification(`📨 New message from ${data.contact?.name || data.contact?.number || data.from}: "${(data.body || '').substring(0, 50)}${(data.body || '').length > 50 ? '...' : ''}"`)
		}
	}

	onSocket('sessions:update', onSessionUpdate)
	onSocket('session:ready', onSessionReady)
	onSocket('session:error', onSessionError)
	onSocket('session:disconnected', onSessionDisconnected)
	onSocket('session:qr', onQR)
	onSocket('health:update', onHealthUpdate)
	onSocket('message:reply', onMessageReply)
	onSocket('message:received', onMessageReceived)

	detachSockets = () => {
		offSocket('sessions:update', onSessionUpdate)
		offSocket('session:ready', onSessionReady)
		offSocket('session:error', onSessionError)
		offSocket('session:disconnected', onSessionDisconnected)
		offSocket('session:qr', onQR)
		offSocket('health:update', onHealthUpdate)
		offSocket('message:reply', onMessageReply)
		offSocket('message:received', onMessageReceived)
	}

	if (import.meta.hot) {
		import.meta.hot.dispose(() => {
			if (detachSockets) detachSockets()
		})
	}
}

onMounted(() => {
	initSockets()
})

async function fetchSessions() {
	try {
		sessions.value = await api.getSessions()
	} catch (err) {
		console.error('Failed to fetch sessions:', err)
		if (isNetworkError(err)) showNotification('Network error: Please check your connection', 'error')
	}
}

async function fetchHealth() {
	try {
		healthData.value = await api.getSessionsHealth()
	} catch (err) {
		console.error('Failed to fetch health:', err)
		if (isNetworkError(err)) showNotification('Network error: Please check your connection', 'error')
	}
}

async function addSession(sessionName) {
	if (!sessionName?.trim()) return
	showLoading('Creating new session...')
	try {
		await api.createSession(sessionName.trim())
		await fetchAllData()
		closeModal()
		showNotification(`Session "${sessionName}" created successfully!`)
	} catch (err) {
		showNotification(`Failed to create session: ${getErrorMessage(err)}`, 'error')
	} finally {
		hideLoading()
	}
}

async function loadQr(id) {
	if (qrLoading[id] || qr[id]) return
	qrLoading[id] = true
	showLoading('Generating QR code...')
	try {
		const data = await api.generateQRCode(id)
		if (data.qr) {
			qr[id] = data.qr
			showNotification('QR code generated successfully!')
		}
	} catch (err) {
		showNotification(`Failed to generate QR code: ${getErrorMessage(err)}`, 'error')
	} finally {
		hideLoading()
		qrLoading[id] = false
	}
}

async function checkSessionHealth(id) {
	showLoading('Checking session health...')
	try {
		const health = await api.getSessionHealth(id)
		const statusText = health.healthy ? '✅ Healthy' : '❌ Unhealthy'
		showNotification(`${statusText} - ${health.status}`)
	} catch (err) {
		showNotification(`Failed to check health: ${getErrorMessage(err)}`, 'error')
	} finally {
		hideLoading()
	}
}

async function restartSession(id) {
	if (!confirm(`Restart session ${id}? This will disconnect the current session.`)) return
	showLoading('Restarting session...')
	try {
		const data = await api.restartSession(id)
		if (data.success) {
			showNotification('Session restarted successfully!')
			await fetchAllData()
		} else {
			showNotification(`Failed to restart: ${data.error}`, 'error')
		}
	} catch (err) {
		showNotification(`Failed to restart: ${getErrorMessage(err)}`, 'error')
	} finally {
		hideLoading()
	}
}

async function deleteSession(id) {
	if (!confirm(`Delete session "${id}"? This action cannot be undone.`)) return
	showLoading('Deleting session...')
	try {
		await api.deleteSession(id)
		delete qr[id]
		showNotification(`Session "${id}" deleted successfully!`)
		await fetchAllData()
	} catch (err) {
		showNotification(`Failed to delete: ${getErrorMessage(err)}`, 'error')
	} finally {
		hideLoading()
	}
}

async function fetchAllData() {
	try {
		const { sessions: sessionsData, health: healthPayload } = await api.getSessionsWithHealth()
		sessions.value = sessionsData
		healthData.value = healthPayload
		if (!activeSessionId.value) {
			const firstReady = sessionsData.find(s => s.ready)
			if (firstReady) {
				activeSessionId.value = firstReady.id
				loadChats()
			}
		}
	} catch (err) {
		console.warn('Batch fetch failed, falling back to individual calls:', err)
		await fetchSessions()
		await fetchHealth()
	}
}
</script>
