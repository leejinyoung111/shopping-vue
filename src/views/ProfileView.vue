<script setup>
import { onMounted, ref } from "vue";
import { useAuthStore } from "@/stores/auth";
import { useRouter } from "vue-router";
import ContainerLayout from "@/components/layout/ContainerLayout.vue";
import { useModal } from "vue-final-modal";
import BlueButton from "@/components/button/BlueButton.vue";
import EditProfileModal from "@/components/modal/edit/EditProfileModal.vue";
import ProfileThItem from "@/components/table/profile/ProfileThItem.vue";
import ProfileTdItem from "@/components/table/profile/ProfileTdItem.vue";

// storage
const authStore = useAuthStore();

// 변수
const getToken = ref(JSON.parse(localStorage.getItem("accessToken")));
const getUser = ref();
const router = useRouter();

// 유저 정보 가져오기
const getUserInfo = async () => {
  if (getToken.value != null) {
    // 로그인 한 경우

    // 토큰으로 유저 정보 가져오기
    const user = await authStore.getUserInfo(getToken.value);

    getUser.value = user.userInfo.user;

    // 관리자 여부
    if (getUser.value.role == "admin") {
      router.replace("/");
    }
  } else {
    // 로그인 하지 않은 경우
    router.replace("/");
  }
};

// 회원 정보 수정 모달창
const changeProfileModal = () => {
  const { open, close } = useModal({
    component: EditProfileModal,
    attrs: {
      title: "회원 정보 수정",
      item: getUser.value,
      buttonOk: "수정",
      async onOk(user) {
        getUser.value = user.userInfo.user;
        close();
      },
      onClose() {
        close();
      },
    },
  });
  open();
};

onMounted(() => {
  getUserInfo();
});
</script>

<template>
  <ContainerLayout v-if="getUser != null">
    <div
      class="mx-auto my-10 rounded-lg p-5 w-full md:w-2/3 lg:w-2/4 max-w-2/4"
    >
      <!-- 이미지 -->
      <img
        class="w-32 h-32 rounded-full mx-auto"
        src="https://picsum.photos/200"
        alt="Profile-picture"
      />

      <div class="w-full">
        <!-- 회원 정보 -->
        <div
          class="relative overflow-x-auto my-5 border border-gray-400 rounded-md"
        >
          <table
            class="w-full text-left rtl:text-right text-gray-700 dark:text-gray-400"
          >
            <tbody>
              <tr
                class="border-b border-gray-300 dark:bg-gray-800 dark:border-gray-700"
              >
                <ProfileThItem>이메일</ProfileThItem>
                <ProfileTdItem>
                  {{ getUser.email }}
                </ProfileTdItem>
              </tr>
              <tr
                class="border-b border-gray-300 dark:bg-gray-800 dark:border-gray-700"
              >
                <ProfileThItem>이름</ProfileThItem>
                <ProfileTdItem>
                  {{ getUser.name }}
                </ProfileTdItem>
              </tr>
              <tr
                class="border-b border-gray-300 dark:bg-gray-800 dark:border-gray-700"
              >
                <ProfileThItem>우편번호</ProfileThItem>
                <ProfileTdItem>
                  {{ getUser.address.post_code }}
                </ProfileTdItem>
              </tr>
              <tr
                class="border-b border-gray-300 dark:bg-gray-800 dark:border-gray-700"
              >
                <ProfileThItem>주소</ProfileThItem>
                <ProfileTdItem>
                  {{ getUser.address.address }}
                </ProfileTdItem>
              </tr>
              <tr
                class="border-b border-gray-300 dark:bg-gray-800 dark:border-gray-700"
              >
                <ProfileThItem>상세주소</ProfileThItem>
                <ProfileTdItem>
                  {{ getUser.address.detail_address }}
                </ProfileTdItem>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- 회원 정보 수정 -->
        <div class="flex flex-col gap-5 justify-center items-center">
          <BlueButton
            type="button"
            text="정보 수정"
            @click="changeProfileModal()"
          />
        </div>
      </div>
    </div>
  </ContainerLayout>
</template>
