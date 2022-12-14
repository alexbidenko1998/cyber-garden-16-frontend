<script lang="ts" setup>
import {combineFullName, computed, ref, request, useAsyncData, useRouter} from "#imports";
import {DepartmentType, UserType} from "~/types/base";
import UserAvatar from "~/components/ui/UserAvatar.vue";

const {data: departments, pending} = useAsyncData<DepartmentType[]>('department', () => request.get('department/get_departments/', {params: {members: true}}));

const router = useRouter();

const three = computed(() => ({
  key: 'boss',
  data: {label: 'Oggetto'},
  selectable: false,
  styleClass: 'department-cfo',
  children: departments.value?.filter((f) => !f.headDepartment).map((el1) => ({
    key: `${el1.chief.uuid}_user`,
    type: 'person',
    styleClass: 'p-person',
    data: {label: el1.chief.specialization, name: combineFullName(el1.chief), avatar: el1.chief.avatar, uuid: el1.chief.uuid},
    children: [
      {
        key: `${el1.id}_d`,
        data: {label: el1.title},
        selectable: false,
        styleClass: 'department-cfo',
        children: el1.members?.filter((el2) => el2.uuid !== el1.chief.uuid).map((el2) => ({
          key: `${el2.uuid}_user`,
          type: 'person',
          styleClass: 'p-person',
          data: {label: el2.specialization, name: combineFullName(el2), avatar: el2.avatar, uuid: el2.uuid},
          children: departments.value?.filter((f) => f.headDepartment && f.chief.uuid == el2.uuid).map((m) => (
            {
              key: `${m.id}_d`,
              data: {label: m.title},
              selectable: false,
              styleClass: 'department-cfo',
              children: m.members?.filter((m2) => m2.uuid !== m.chief.uuid).map((m2) => ({
                key: `${m2.uuid}_user`,
                type: 'person',
                styleClass: 'p-person',
                data: {label: m2.specialization, name: combineFullName(m2), avatar: m2.avatar, uuid: m2.uuid},
              })),
            }
          )),
        })),
      }
    ],
  }))
}));

const clickPerson = (node: {data: UserType}) => {
  router.push(`/dashboard/users/${node.data.uuid}`);
};
</script>

<template>
  <div class="companyPage md:py-8">
    <client-only>
      <OrganizationChart :value="three" class="companyPage__three" @nodeSelect="clickPerson" selectionMode="single">
        <template #person="slotProps">
          <div class="node-header ui-corner-top">{{slotProps.node.data.label}}</div>
          <div class="node-content">
            <UserAvatar :user="slotProps.node.data" class="companyPage__image" />
            <div>{{slotProps.node.data.name}}</div>
          </div>
        </template>
        <template #default="slotProps">
          <span>{{slotProps.node.data.label}}</span>
        </template>
      </OrganizationChart>
    </client-only>
    <i v-if="pending" class="pi pi-spin pi-spinner absolute text-2xl text-black-alpha-90" style="top: 50%; left: 50%; transform: translate(-50%, -50%)" />
  </div>
</template>

<style lang="scss">
.companyPage {
  height: 100%;
  width: 100%;
  overflow: auto;

  &__image {
    width: 3rem;
    height: 3rem;
    margin: 0.5rem 0;
    object-fit: cover;
  }

  @media (max-width: 800px) {
    &__three {
      transform-origin: 0 50%;
      transform: scale(0.4);
    }
  }
}
</style>
