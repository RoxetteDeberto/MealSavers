<script setup>
import { supabase, formActionDefault } from '@/utils/supabase'
import { useAuthUserStore } from '@/stores/authUser'
import { useBranchesStore } from '@/stores/branches'
import { useProductsStore } from '@/stores/products'
import { getAvatarText } from '@/utils/helpers'
import { useRouter } from 'vue-router'
import { ref } from 'vue'

const router = useRouter()
const authStore = useAuthUserStore()
const productsStore = useProductsStore()
const branchesStore = useBranchesStore()

const formAction = ref({
  ...formActionDefault,
})

const onLogout = async () => {
  formAction.value = { ...formActionDefault, formProcess: true }

  await supabase.auth.signOut()

  formAction.value.formProcess = false
  setTimeout(() => {
    authStore.$reset()
    branchesStore.$reset()
    productsStore.$reset()
  }, 2500)
  router.replace('/')
}
</script>

<template>
  <v-menu min-width="200px" rounded>
    <template #activator="{ props }">
      <v-btn icon v-bind="props">
        <v-avatar
          v-if="authStore.userData.image_url"
          :image="authStore.userData.image_url"
          color="orange-darken-3"
          size="large"
        ></v-avatar>

        <v-avatar v-else color="orange-darken-3" size="large">
          <span class="text-h5">
            {{ getAvatarText(authStore.userData.firstname + ' ' + authStore.userData.lastname) }}
          </span>
        </v-avatar>
      </v-btn>
    </template>

    <v-card class="mt-1">
      <v-card-text>
        <v-list>
          <v-list-item
            :subtitle="authStore.userData.email"
            :title="authStore.userData.firstname + ' ' + authStore.userData.lastname"
          >
            <template #prepend>
              <v-avatar
                v-if="authStore.userData.image_url"
                :image="authStore.userData.image_url"
                color="orange-darken-3"
                size="large"
              ></v-avatar>

              <v-avatar v-else color="orange-darken-3" size="large">
                <span class="text-h5">
                  {{
                    getAvatarText(authStore.userData.firstname + ' ' + authStore.userData.lastname)
                  }}
                </span>
              </v-avatar>
            </template>
          </v-list-item>
        </v-list>

        <v-divider class="my-3"></v-divider>

        <v-btn prepend-icon="mdi-wrench" variant="plain" to="/accountsettings">
          Account Settings
        </v-btn>

        <v-divider class="my-3"></v-divider>

        <v-btn
          prepend-icon="mdi-logout"
          variant="plain"
          @click="onLogout"
          :loading="formAction.formProcess"
          :disabled="formAction.formProcess"
        >
          Logout
        </v-btn>
      </v-card-text>
    </v-card>
  </v-menu>
</template>
