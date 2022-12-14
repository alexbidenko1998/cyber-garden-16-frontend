<script lang="ts" setup>
import {computed, onMounted, ref} from "#imports";

const deferredPrompt = ref<any>();
const notAvailable = ref(true);

const dialog = ref(false);
const isPWA = computed(() => {
  if (process.server) return false;
  // @ts-ignore
  return navigator.standalone || window.matchMedia('(display-mode: standalone)').matches;
});

const onClick = async () => {
  if (deferredPrompt.value) {
    try {
      deferredPrompt.value.prompt();
      const {outcome} = await deferredPrompt.value.userChoice;
      if (outcome === 'accepted') {
        deferredPrompt.value = null;
      }
    } catch {
      deferredPrompt.value = null;
    }
  } else dialog.value = true;
};

onMounted(() => {
  window.addEventListener('beforeinstallprompt', (e) => {
    deferredPrompt.value = e;
    notAvailable.value = !e;
  });
});
</script>

<template>
  <div class="dashboardPage py-4 px-3 flex flex-column gap-4 max-w-30rem mx-auto">
    <div class="flex flex-wrap dashboardPage__grid">
      <NuxtLink to="/dashboard/games" class="dashboardPage__gridItem">
        <Card class="hover:shadow-2 shadow-6 transition-all transition-duration-300 dashboardPage__gridCard">
          <template #content>
            <img class="dashboardPage__gridIcon" src="/icons/dashboard-icon-1.png" />
            Начать знакомство
          </template>
        </Card>
      </NuxtLink>
      <NuxtLink to="/dashboard/cards" class="dashboardPage__gridItem">
        <Card class="hover:shadow-2 shadow-6 transition-all transition-duration-300 dashboardPage__gridCard">
          <template #content>
            <img class="dashboardPage__gridIcon" src="/icons/games-icon-6.svg" />
            Моя коллекция
          </template>
        </Card>
      </NuxtLink>
      <NuxtLink to="/dashboard/relationship" class="dashboardPage__gridItem">
        <Card class="hover:shadow-2 shadow-6 transition-all transition-duration-300 dashboardPage__gridCard">
          <template #content>
            <img class="dashboardPage__gridIcon" src="/icons/dashboard-icon-3.png" />
            Отметить знакомых
          </template>
        </Card>
      </NuxtLink>
      <NuxtLink to="/dashboard/company" class="dashboardPage__gridItem">
        <Card class="hover:shadow-2 shadow-6 transition-all transition-duration-300 dashboardPage__gridCard">
          <template #content>
            <img class="dashboardPage__gridIcon" src="/icons/dashboard-icon-4.png" />
            Карта компании
          </template>
        </Card>
      </NuxtLink>
    </div>

    <NuxtLink to="/dashboard/users">
      <Card class="dashboardPage__linkCard dashboardPage__linkCard_1 hover:shadow-2 shadow-6 transition-all transition-duration-300">
        <template #content>
          <div class="flex justify-content-between align-items-center dashboardPage__link">
            <img src="/icons/link-1.svg" />
            <span>
              Поиск сотрудников
            </span>
            <img src="/icons/link-1.svg" />
          </div>
        </template>
      </Card>
    </NuxtLink>

    <NuxtLink to="/dashboard/products">
      <Card class="dashboardPage__linkCard dashboardPage__linkCard_2 hover:shadow-2 shadow-6 transition-all transition-duration-300">
        <template #content>
          <div class="flex justify-content-between align-items-center dashboardPage__link">
            <img src="/icons/link-2.svg" />
            <span>
              Потрать свои баллы
            </span>
            <img src="/icons/link-2.svg" />
          </div>
        </template>
      </Card>
    </NuxtLink>

    <NuxtLink to="/dashboard/barter">
      <Card class="dashboardPage__linkCard dashboardPage__linkCard_3 hover:shadow-2 shadow-6 transition-all transition-duration-300">
        <template #content>
          <div class="flex justify-content-between align-items-center dashboardPage__link">
            <img src="/icons/dashboard-icon-5.svg" />
            <span>
              Предложения обмена
            </span>
            <img src="/icons/dashboard-icon-5.svg" />
          </div>
        </template>
      </Card>
    </NuxtLink>

    <Card class="dashboardPage__linkCard dashboardPage__linkCard_5 hover:shadow-2 shadow-6 transition-all transition-duration-300 cursor-pointer" @click="onClick" v-if="!isPWA">
      <template #content>
        <div class="flex justify-content-between align-items-center dashboardPage__link">
          <img src="/icons/d-i.svg" />
          <span>
            Скачать приложение
          </span>
          <img src="/icons/d-i.svg" />
        </div>
      </template>
    </Card>

    <Dialog v-model:visible="dialog" header="Установка приложения" modal style="width: 420px">
      <p class="text-lg line-height-3 my-1">
        Сайт реализован как PWA и подразумевает установку через браузер.
        Если вы видите это окно, значит у вас не работает автоматическая установка, однако вы можете сделать это сами через настройки браузера.
      </p>
    </Dialog>
  </div>
</template>

<style lang="scss">
.dashboardPage {
  &__grid {
    border-radius: 12px;
  }

  &__linkCard {
    .p-card-body {
      padding: 0.5rem 1rem;
      color: black;
      font-size: 16px;
    }

    &_1 {
      background-color: #8DD0FF;
    }

    &_2 {
      background-color: #B7A2E0;
    }

    &_3 {
      background-color: #FFD24B;
    }

    &_5 {
      background-color: #7FD8E6;
    }
  }

  &__link {
    img {
      width: 42px;
      height: 42px;
    }
    > *:nth-child(3) {
      transform: scaleX(-100%);
    }
  }

  &__gridItem {
    width: 50%;
    aspect-ratio: 1 / 1;
    transition: transform 0.3s ease;

    --delta: 7px;
    --negative-delta: -7px;

    .p-card {
      border-radius: 0 !important;
      box-shadow: none !important;
    }

    .p-card-body {
      height: 100%;
    }

    .p-card-content {
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 18px;
      text-align: center;
      color: black;
      font-weight: 600;
    }

    &:nth-child(1) .p-card {
      border-top-left-radius: 12px !important;
      background-color: #9FDAA8;
    }

    &:nth-child(2) .p-card {
      border-top-right-radius: 12px !important;
      background-color: #8DD0FF;
    }

    &:nth-child(3) .p-card {
      border-bottom-left-radius: 12px !important;
      background-color: #FFE082;
    }

    &:nth-child(4) .p-card {
      border-bottom-right-radius: 12px !important;
      background-color: #B7A2E0;
    }

    &:hover {
      &:nth-child(1) {
        transform: translate(var(--negative-delta), var(--negative-delta));
      }

      &:nth-child(2) {
        transform: translate(var(--delta), var(--negative-delta));
      }

      &:nth-child(3) {
        transform: translate(var(--negative-delta), var(--delta));
      }

      &:nth-child(4) {
        transform: translate(var(--delta), var(--delta));
      }
    }
  }

  &__gridIcon {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    max-width: 100%;
    min-height: 130px;
    min-width: 130px;
    object-fit: contain;
  }

  &__gridCard {
    height: 100%;
    position: relative;
  }

  .p-card {
    border-radius: 12px;
  }

  .p-card-content {
    padding: 0 !important;
  }
}
</style>
