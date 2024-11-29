<script setup>
import {onMounted, ref} from "vue";
import Field from "@/components/Field/Field.vue";
import Button from "@/components/Button/Button.vue";

const candidate = ref('');
const vacancy = ref('');
const candidates = ref([]);
const vacancies = ref([]);
const text = ref(null);

const createID = () => {
  return `${(+new Date).toString(16)}_${Math.round(10000 * Math.random())}`
}

const handleAddCandidate = (newCandidate) => {
  if (!candidates.value.some((candidateItem) => candidateItem.name === newCandidate)) {
    candidates.value = [...candidates.value, {
      id: createID(),
      name: newCandidate,
      usedCount: 0
    }];
    localStorage.setItem("candidates", JSON.stringify(candidates.value));
    handleSelectCandidate(newCandidate);
  }
}

const handleSelectCandidate = (current) => {
  candidate.value = current;
}

const handleRemoveCandidate = (deletingCandidate) => {
  candidates.value = candidates.value.filter(candidateItem => candidateItem.id !== deletingCandidate.id);
  localStorage.setItem("candidates", JSON.stringify(candidates.value))
}

const handleAddVacancy = (newVacancy) => {
  if (!vacancies.value.some((vacancyItem) => vacancyItem.name === newVacancy)) {
    vacancies.value = [...vacancies.value, {
      id: createID(),
      name: newVacancy,
      usedCount: 0
    }];
    localStorage.setItem("vacancies", JSON.stringify(vacancies.value));
    handleSelectVacancy(newVacancy);
  }
}

const handleSelectVacancy = (current) => {
  vacancy.value = current;
}

const handleRemoveVacancy = (deletingVacancy) => {
  vacancies.value = vacancies.value.filter(vacancyItem => vacancyItem.id !== deletingVacancy.id);
  localStorage.setItem("vacancies", JSON.stringify(vacancies.value))
}

const handleCopy = () => {
  if (vacancy.value && candidate.value) {
    console.log(text.value?.innerText);
    navigator.clipboard.write(text.value?.innerText ?? '')
        .then(() => {
          vacancies.value = vacancies.value.map((vacancyItem) => {
            if (vacancyItem.name === vacancy.value) {
              return {...vacancyItem, usedCount: vacancyItem.usedCount + 1};
            }

            return vacancyItem;
          });

          candidates.value = candidates.value.map((candidateItem) => {
            if (candidateItem.name === candidate.value) {
              return {...candidateItem, usedCount: candidateItem.usedCount + 1};
            }

            return candidateItem;
          });

          localStorage.setItem("vacancies", JSON.stringify(vacancies.value));
          localStorage.setItem("candidates", JSON.stringify(candidates.value));

          alert('Успешно скопировано!');
        })
        .catch((err) => {
          console.log(err)
          alert('Ошибка');
        })
  } else {
    alert('Не заполнены данные')
  }
}

onMounted(() => {
  candidates.value = (JSON.parse(localStorage.getItem('candidates')) ?? []).sort((a,b) => {
    return b.usedCount - a.usedCount;
  });
  vacancies.value = (JSON.parse(localStorage.getItem('vacancies')) ?? []).sort((a,b) => {
    return b.usedCount - a.usedCount;
  });

  if (candidates.value.length) {
    candidate.value = candidates.value[0].name
  }

  if (vacancies.value.length) {
    vacancy.value = vacancies.value[0].name
  }
})
</script>

<template>
  <div class="container">
    <div class="row">
      <Field
          :list="candidates"
          :current="candidate"
          placeholder="Кандидат"
          @add="handleAddCandidate"
          @delete="handleRemoveCandidate"
          @select="handleSelectCandidate"
      />
      <Field
          :list="vacancies"
          :current="vacancy"
          placeholder="Вакансия"
          @add="handleAddVacancy"
          @delete="handleRemoveVacancy"
          @select="handleSelectVacancy"
      />
    </div>
    <div class="text" ref="text">
      Здравствуйте, <span class="value" :class="{'empty': !candidate}">{{ candidate || 'ИМЯ КАНДИДАТА' }}</span>, меня зовут
      Алина, общались с Вами сегодня по вакансии
      <span class="value" :class="{'empty': !vacancy}">
        <template v-if="vacancy">
          “{{vacancy}}”
        </template>
        <template v-else>
          ВАКАНСИЯ
        </template>
      </span> и отправили анкету соискателя,
      подскажите, получили её? Если у Вас возникнут вопросы - я буду на связи, Вы можете написать или позвонить, будем
      рады помочь, хорошего дня 🙂 С уважением, команда найма Яндекс
    </div>
    <Button @click="handleCopy">Скопировать</Button>
  </div>
</template>

<style lang="scss" scoped>
.container {
  max-width: 1170px;
  margin: 0 auto;
}

.row {
  display: flex;
  gap: 30px;
}

.value {
  color: #fff;
}

.empty {
  font-weight: 700;
}

.text {
  margin-top: 40px;
  margin-bottom: 20px;
  font-size: 18px;
  line-height: 27px;
}
</style>
