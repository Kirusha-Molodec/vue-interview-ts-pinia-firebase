<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { getFirestore, setDoc, doc } from 'firebase/firestore'
import type { IInterview } from '@/interfaces'
import { v4 as uuidv4 } from 'uuid'
import { useUserStore } from '@/stores/user'

const userStore = useUserStore()
const db = getFirestore()
const router = useRouter()

const company = ref<string>('')
const vacancyLink = ref<string>('')
const hrName = ref<string>('')
const contactTelegram = ref<string>('')
const contactWhatsApp = ref<string>('')
const contactPhone = ref<string>('')

const loading = ref<boolean>(false)

const addNewInterview = async (): Promise<void> => {
  loading.value = true
  const payload: IInterview = {
    id: uuidv4(),
    company: company.value,
    vacancyLink: vacancyLink.value,
    hrName: hrName.value,
    contactTelegram: contactTelegram.value,
    contactWhatsApp: contactWhatsApp.value,
    contactPhone: contactPhone.value,
    createdAt: new Date()
  }

  if (userStore.userId) {
    await setDoc(doc(db, `users/${userStore.userId}/interviews`, payload.id), payload).then(() => {
      router.push('/list')
    })
  }
}

const disabledSaveButton = computed<boolean>(() => {
  return !(company.value && vacancyLink.value && hrName.value)
})
</script>

<template>
  <div class="content-interview">
    <app-card>
      <template #title>New Interview</template>
      <template #content>
        <app-input-text class="input mb-3" placeholder="Company" v-model="company" />
        <app-input-text v-model="vacancyLink" class="input mb-3" placeholder="Job vacancy (link)" />
        <app-input-text v-model="hrName" class="input mb-3" placeholder="Contact (name)" />
        <app-input-text
          v-model="contactTelegram"
          class="input mb-3"
          placeholder="HR Telegram username"
        />
        <app-input-text
          v-model="contactWhatsApp"
          class="input mb-3"
          placeholder="HR WhatsApp phone number"
        />
        <app-input-text v-model="contactPhone" class="input mb-3" placeholder="Phone Number HR" />
        <app-button
          @click="addNewInterview"
          label="Create interview"
          :disabled="disabledSaveButton"
          :loading="loading"
        ></app-button>
      </template>
    </app-card>
  </div>
</template>

<style scoped>
.input {
  width: 100%;
}
.content-interview {
  max-width: 600px;
  margin: auto;
}
</style>
