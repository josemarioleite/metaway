<template>
<div class="form" transition="scroll-x-transition">
  <aside class="form__aside">
    <span class="form__aside--subtitle">Dados</span>
    <v-form>
      <v-container>
        <v-row>
          <v-col cols="12" md="3">
            <div style="width: 100%; display: flex;">
              <v-img
                v-if="Type !== 'add'"
                :aspect-ratio="1"
                :src="photo"
                cover
                width="200"
                class="bg-white"
                style="border-radius: 10px;"
              />
              <div v-else class="file-input">
                <v-file-input
                  v-model="fileInput"
                  accept="image/*"
                  label="Sua foto"
                  :rules="[true]"
                  @update:model-value="setFileInput"
                />
              </div>
            </div>
          </v-col>
          <v-col cols="12" md="9">
            <v-row class="justify-center" style="margin-top: 10px">
              <v-col cols="12" md="2" v-if="Type !== 'add'">
                <c-text v-model="itemPeople.id" label="ID" readonly :clearable="false" />
              </v-col>
              <v-col cols="12" md="4">
                <c-text v-model="itemPeople.nome" label="Nome" />
              </v-col>
              <v-col cols="12" md="4">
                <c-text v-model="itemPeople.cpf" label="CPF" v-maska:[maskCPF] />
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-container>
    </v-form>

    <span class="form__aside--subtitle">Localização</span>
    <v-form>
      <v-container>
        <v-row class="justify-center" style="margin-top: 10px">
          <v-col cols="12" md="5">
            <c-text v-model="itemPeople.endereco.logradouro" label="Logradouro" />
          </v-col>
          <v-col cols="12" md="3">
            <c-text v-model="itemPeople.endereco.bairro" label="Bairro" />
          </v-col>
          <v-col cols="12" md="2">
            <c-text v-model="itemPeople.endereco.numero" label="Número" />
          </v-col>
          <v-col cols="12" md="2">
            <c-text v-model="itemPeople.endereco.cep" label="CEP" />
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="4">
            <c-text v-model="itemPeople.endereco.cidade" label="Cidade" />
          </v-col>
          <v-col cols="12" md="4">
            <c-text v-model="itemPeople.endereco.estado" label="Estado" />
          </v-col>
          <v-col cols="12" md="4">
            <c-text v-model="itemPeople.endereco.pais" label="País" />
          </v-col>
        </v-row>
      </v-container>
    </v-form>
  </aside>
  <footer class="form__btn">
    <v-btn @click="closeForm" color="warning" density="comfortable" flat> fechar </v-btn>
    <v-btn @click="save" color="success" density="comfortable" flat> salvar </v-btn>
  </footer>
</div>
</template>

<script lang="ts" setup>
import { watch, computed, ref, reactive } from 'vue'
import { ActionType } from './types'
import { PeopleStore } from '../../stores/people.store'
import { People, CreateEmptyPeople } from '../../models/people.model'
import { generateUUID } from '../../services/utils'

interface Props {
  ItemPeople: People
  IsVisible: boolean
  Type: ActionType
}

interface FileInfo {
  lastModified: number
  lastModifiedDate: Date
  name: string
  size: number
  type: string
  webkitRelativePath: string
}

let itemPeople = ref<People>(CreateEmptyPeople())
const fileInput = ref(null)
const maskCPF = reactive({ mask: '###.###.###-##', eager: true })
const photo = computed(() => peopleStore.photoPeople)
const peopleStore = PeopleStore()
const props = withDefaults(defineProps<Props>(), { Type: 'add' })
const isVisible = computed(() => props.IsVisible)

const emit = defineEmits(['show'])

async function save () {
  console.log(fileInput.value)
}

async function closeForm () {
  itemPeople.value = CreateEmptyPeople()
  emit('show', false)
}

function setFileInput (value: FileInfo) {
  if (value) {
    itemPeople.value.foto.name = value.name
    itemPeople.value.foto.type = value.type
  } else {
    itemPeople.value.foto.name = ''
    itemPeople.value.foto.type = ''
  }

  console.log(itemPeople.value)
}

watch(isVisible, async function (value: any) {
  if (value && props.Type !== 'add') {
    itemPeople.value = props.ItemPeople
  } else {
    itemPeople.value.foto.id = generateUUID()
  }
})
</script>

<style lang="scss" scoped>
.form {
  width: 95%;
  background: #fdfdfd;
  margin-bottom: 30px;
  border: 1px solid #ccc;
  border-radius: 10px;
  padding: 10px;

  &__aside {
    width: 100%;

    &--subtitle {
      font-size: 1.2rem;
    }
  }

  &__btn {
    display: flex;
    flex-direction: row;
    justify-content: flex-end;
    width: 100%;
    gap: 10px;
  }
}

.file-input {
  padding-top: 15px;
  width: 100%;
}

@media only screen and (max-width: 499px) {
  .form__btn {
    justify-content: center;
  }

  .file-input {
    padding-top: 20px;
  }
}
</style>