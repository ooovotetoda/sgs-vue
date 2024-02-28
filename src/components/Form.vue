<script setup>
import { ref, watch, computed } from 'vue';

const isWarned = ref(false)

const cities = ref([
  { id: 1, name: 'Екатеринбург' },
  { id: 2, name: 'Казань' },
  { id: 3, name: 'Нижний Новгород' },
  { id: 4, name: 'Самара' },
  { id: 5, name: 'Омск' },
]);

const factories = ref([
  { id: 1, cityId: 1, name: 'Машиностроительный завод' },
  { id: 2, cityId: 1, name: 'Химический комбинат' },
  { id: 3, cityId: 2, name: 'Авиационный завод' },
  { id: 4, cityId: 3, name: 'Электротехнический завод' },
  { id: 5, cityId: 4, name: 'Завод по производству стройматериалов' },
  { id: 6, cityId: 5, name: 'Пищевой комбинат' },
]);

const employees = ref([
  { id: 1, factoryId: 1, name: 'Алексеев А.А.' },
  { id: 2, factoryId: 1, name: 'Борисова Б.Б.' },
  { id: 3, factoryId: 2, name: 'Васильев В.В.' },
  { id: 4, factoryId: 3, name: 'Григорьева Г.Г.' },
  { id: 5, factoryId: 4, name: 'Дмитриев Д.Д.' },
  { id: 6, factoryId: 5, name: 'Ефимова Е.Е.' },
  { id: 7, factoryId: 6, name: 'Жуков Ж.Ж.' },
]);

const brigades = ref([
  { id: 1, name: 'Бригада Производство' },
  { id: 2, name: 'Бригада Сборка' },
  { id: 3, name: 'Бригада Распределение' },
  { id: 4, name: 'Бригада Контроль' },
]);

const shifts = ref([
  { id: 1, name: 'Дневная смена' },
  { id: 2, name: 'Ночная смена' },
  { id: 3, name: 'Смена выходного дня' },
]);


const selectedCity = ref('');
const selectedFactory = ref('');
const selectedEmployee = ref('');
const selectedBrigade = ref('');
const selectedShift = ref('');

const filteredFactories = computed(() => factories.value.filter(f => f.cityId == selectedCity.value));
const filteredEmployees = computed(() => employees.value.filter(e => e.factoryId == selectedFactory.value));

const saveSelection = () => {
  if (!selectedCity.value
    || !selectedFactory.value
    || !selectedEmployee.value
    || !selectedBrigade.value
    || !selectedShift.value) {
      isWarned.value = true;
      return
  }

  const selection = {
    city: selectedCity.value,
    factory: selectedFactory.value,
    employee: selectedEmployee.value,
    brigade: selectedBrigade.value,
    shift: selectedShift.value,
  };

  document.cookie = `selection=${JSON.stringify(selection)};path=/`;

  isWarned.value = false;

  alert('Выбор сохранён в cookies');
};

const onCityChange = () => {
  selectedFactory.value = '';
  selectedEmployee.value = '';
};

const onFactoryChange = () => {
  selectedEmployee.value = '';
};

watch(selectedCity, onCityChange);
watch(selectedFactory, onFactoryChange);
</script>

<template>
  <form class="form" @submit.prevent="">
    <div class="form__item">
      <label class="form__label" for="city">Город</label>
      <select
        class="form__select"
        id="city"
        name="city"
        v-model="selectedCity"
        @change="onCityChange"
      >
        <option disabled value="">Выберите город</option>
        <option v-for="city in cities" :key="city.id" :value="city.id">{{ city.name }}</option>
      </select>
    </div>

    <div class="form__item">
      <label class="form__label" for="factory">Цех</label>
      <select
        class="form__select"
        id="factory"
        name="factory"
        v-model="selectedFactory"
        @change="onFactoryChange"
      >
        <option disabled value="">Выберите цех</option>
        <option v-for="factory in filteredFactories" :key="factory.id" :value="factory.id">{{ factory.name }}</option>
      </select>
    </div>

    <div class="form__item">
      <label class="form__label" for="employee">Сотрудник</label>
      <select
        class="form__select"
        id="employee"
        name="employee"
        v-model="selectedEmployee"
      >
        <option disabled value="">Выберите сотрудника</option>
        <option v-for="employee in filteredEmployees" :key="employee.id" :value="employee.id">{{ employee.name }}</option>
      </select>
    </div>

    <div class="form__item">
      <label class="form__label" for="brigade">Бригада</label>
      <select
        class="form__select"
        id="brigade"
        name="brigade"
        v-model="selectedBrigade"
      >
        <option disabled value="">Выберите бригаду</option>
        <option v-for="brigade in brigades" :key="brigade.id" :value="brigade.id">{{ brigade.name }}</option>
      </select>
    </div>

    <div class="form__item">
      <label class="form__label" for="shift">Смена</label>
      <select
        class="form__select"
        id="shift"
        name="shift"
        v-model="selectedShift"
      >
        <option disabled value="">Выберите смену</option>
        <option v-for="shift in shifts" :key="shift.id" :value="shift.id">{{ shift.name }}</option>
      </select>
    </div>
  </form>

  <div v-if="isWarned" class="warning">Пожалуйста, выберите все пункты.</div>

  <button class="form__button" @click="saveSelection">Сохранить</button>
</template>

<style scoped lang="scss">
.form {
  display: flex;
  flex-direction: column;
  gap: 20px;

  &__item {
    display: flex;
    flex-direction: column;
  }

  &__label {
    margin-bottom: 8px;
    font-weight: bold;
  }

  &__select {
    width: 100%;
    height: 56px;
    padding: 0 16px;
    border-radius: 7px;
    font-size: 16px;
    -webkit-appearance: none;
    background: url("data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxZW0iIGhlaWdodD0iMWVtIiB2aWV3Qm94PSIwIDAgMjQgMjQiPjxwYXRoIGZpbGw9ImN1cnJlbnRDb2xvciIgZD0ibTEyIDE1LjRsLTYtNkw3LjQgOGw0LjYgNC42TDE2LjYgOEwxOCA5LjR6Ii8+PC9zdmc+") no-repeat calc(100% - 16px) center;
    outline: none;
  }

  &__button {
    width: 100%;
    height: 56px;
    margin-top: 28px;
    background: #FFDD2D;
    border: none;
    border-radius: 7px;
    font-size: 16px;
    cursor: pointer;
  }
}

.warning {
  margin-top: 14px;
  color: red;
  font-weight: bold;
  font-size: 14px;
}
</style>


