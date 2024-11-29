<script setup>
import {computed, onMounted, ref, watch} from "vue";
import Button from "@/components/Button/Button.vue";

const search = ref('');
const isOpen = ref(false);

const emit = defineEmits(['add', 'delete', 'select']);

const props = defineProps({
  placeholder: {
    type: String,
  },
  list: {
    type: Array,
  },
  current: {
    type: String
  }
});

const filteredList = computed(() => {
  return props.list.filter(item => item.name.includes(search.value))
})

const handleSelect = (item) => {
  emit('select', item);
  search.value = '';
  isOpen.value = false;
}

const handleAdd = (item) => {
  if (item) {
    emit('add', item);
    search.value = '';
    isOpen.value = false;
  }
}

const handleDelete = (item) => {
  emit('delete', item);
  search.value = '';
}

if (props.current) {
  search.value = props.current;
}
</script>

<template>
  <div class="field" v-click-outside="() => {isOpen = false}">
    <input
        :placeholder="placeholder"
        type="text"
        class="input"
        v-model="search"
        @focus="isOpen = true"
    >
    <div v-if="isOpen" class="field-content">
      <template v-if="list.length">
        <ul v-if="filteredList.length" class="list">
          <li
              v-for="(item) in filteredList"
              :key="item.id"
              class="item"
              :class="{'item--active': item.name === current}"
              @click.stop="handleSelect(item.name)"
          >
            <span>{{ item.name }}</span>
            <Button :small="true" @click="handleDelete(item)">
              &times;
            </Button>
          </li>
        </ul>
        <div class="empty-field" v-else>
          Не найдено записей с именем {{ search }}
          <div v-if="search">
            <p>
              Добавить запись {{ search }}?
            </p>
            <Button @click="handleAdd(search)">
              Добавить
            </Button>
          </div>
        </div>
      </template>
      <div class="empty-field" v-else>
        <div v-if="search">
          <p>
            Добавить запись {{ search }}?
          </p>
          <Button @click="handleAdd(search)">
            Добавить
          </Button>
        </div>
        <div v-else>
          Отсутствуют записи
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.field {
  position: relative;
  width: 250px;
  background-color: #0B0D0E;
  font-size: 1rem;
}

.input {
  display: block;
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #32383E;
  border-radius: 6px;
  color: #ffffff;
  min-height: 2.5rem;
  cursor: pointer;

  &:focus {
    cursor: text;
  }

  &:hover {
    background-color: #171A1C;
  }
}

.field-content {
  border-radius: 6px;
  border: 1px solid #32383E;
  position: absolute;
  top: calc(100% + 5px);
  left: 0;
  right: 0;
  background-color: #0B0D0E;
  z-index: 2;
  min-height: 2.5rem;
  max-height: 300px;
}

.empty-field {
  display: flex;
  flex-direction: column;
  gap: 7px;
  padding: 8px 12px;

  p {
    margin-bottom: 12px;
  }
}

.item {
  cursor: pointer;
  padding: 8px 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 15px;
  transition: 0.2s;
  font-weight: 600;

  &:hover, &--active {
    background-color: #171A1C;
  }
}

.text {
  margin-top: 50px;
}
</style>