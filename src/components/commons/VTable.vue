<template>
  <v-data-table
    :headers="Headers"
    :items="Items"
    :items-per-page="itemsPerPage"
    class="table"
    item-value="name"
    items-per-page-text="Itens por página:"
    no-data-text="Sem dados no momento..."
  >
    <template v-slot:headers="{ columns, isSorted, getSortIcon, toggleSort }">
      <tr class="table__header">
        <template v-for="column in columns" :key="column.key">
          <td>
            <span class="table__header--title" @click="() => toggleSort(column)"> {{ column.title }} </span>
            <template v-if="isSorted(column)">
              <v-icon :icon="getSortIcon(column)"></v-icon>
            </template>
          </td>
        </template>
      </tr>
    </template>

    <template v-slot:item.action="{ item }">
      <div class="table__action">
        <v-tooltip text="Editar">
          <template v-slot:activator="{ props }">
            <v-btn @click="editRow(item)" v-bind="props" density="compact" icon="mdi-pencil" color="primary" />
          </template>
        </v-tooltip>

        <v-tooltip text="Deletar">
          <template v-slot:activator="{ props }">
            <v-btn v-bind="props" density="compact" icon="mdi-delete" color="red" />
          </template>
        </v-tooltip>
      </div>
    </template>
  </v-data-table>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

interface Props {
  Headers: []
  Items: []
  TotalItems: 0
  IsLoading: false
}

defineProps<Props>()
const itemsPerPage = ref(6)

const emit = defineEmits(['editRow'])

function editRow<T> (data: T) {
  emit('editRow', data)
}
</script>

<style lang="scss" scoped>
.table {
  width: 95%;
  height: 450px;
  border: 1px solid #ccc;
  border-radius: 10px;

  &__action {
    display: flex;
    flex-direction: row;
    justify-content: center;
    gap: 15px;
  }

  &__header {
    background: #ccc;

    &--title {
      cursor: pointer;
      font-size: 1rem;
      font-weight: 600;
    }
  }

}
</style>