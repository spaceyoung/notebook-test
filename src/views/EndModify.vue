<template>
  <v-card class="end-modify view px-0 py-10 px-sm-8 px-lg-15 py-lg-5" variant="flat">
    <BookInfo :book="myReadingEndItem" />
    <BookDesc :book="myReadingEndItem" />
    <v-form class="mb-13">
      <BookPlatform :state="state" :book="myReadingEndItem" />
      <ReadingState :state="state" :book="myReadingEndItem" />
      <ReadingDate :state="state" :book="myReadingEndItem" />
      <ReadingPage v-if="myReadingEndItem.readingState === '독서 중'" :book="myReadingEndItem" />
      <Rate v-if="myReadingEndItem.readingState === '독서 완료'" :book="myReadingEndItem" />
      <Sentence v-if="myReadingEndItem.readingState === '독서 완료'" :book="myReadingEndItem" />
      <Review v-if="myReadingEndItem.readingState === '독서 완료'" :book="myReadingEndItem" />
    </v-form>
    <v-card-actions class="book-buttons">
      <v-dialog v-model="state.deleteModal" class="modal delete">
        <template v-slot:activator="{ props }">
          <v-btn v-bind="props" class="delete-button ma-0 mr-sm-auto px-8" variant="outlined" size="large">삭제하기</v-btn>
        </template>
        <v-container>
          <v-row class="justify-center">
            <v-col cols="12" sm="8" md="6" lg="4">
              <v-card class="modal-box pa-1">
                <v-card-text class="modal-desc pa-10">정말 기록을 삭제하시겠어요?</v-card-text>
                <v-card-actions class="modal-buttons">
                  <v-spacer />
                  <v-btn class="cancel-button px-5" variant="outlined" size="large" @click="state.deleteModal = false">취소하기</v-btn>
                  <v-btn class="delete-button px-5" variant="outlined" size="large" @click="deleteRecord">삭제하기</v-btn>
                </v-card-actions>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-dialog>
      <v-btn class="cancel-button ma-0 px-8" variant="outlined" size="large" @click="cancelRecord">취소하기</v-btn>
      <v-btn class="modify-button ma-0 ml-sm-5 px-8" variant="outlined" size="large" @click="endModifyRecord">수정하기</v-btn>
    </v-card-actions>
    <v-card-actions class="close-button pa-0">
      <v-btn class="pa-0" size="large" @click="cancelRecord">
        <v-icon icon="mdi-window-close" size="x-large" />
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script setup>
import { computed } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { useNotebookStore } from '@/stores/index';

import BookInfo from '@/components/contents/BookInfo.vue';
import BookDesc from '@/components/contents/BookDesc.vue';
import BookPlatform from '@/components/contents/BookPlatform.vue';
import ReadingState from '@/components/contents/ReadingState.vue';
import ReadingDate from '@/components/contents/ReadingDate.vue';
import ReadingPage from '@/components/contents/ReadingPage.vue';
import Rate from '@/components/contents/Rate.vue';
import Sentence from '@/components/contents/Sentence.vue';
import Review from '@/components/contents/Review.vue';

const currentRoute = useRoute();
const router = useRouter();
const id = currentRoute.params.id;
const { state, addMyReading, updateMyReadingEnd, deleteMyReadingEnd } = useNotebookStore();
const myReadingEndList = computed(() => useNotebookStore().myReadingEndList);
const myReadingEndItem = myReadingEndList.value.find((myReadingEndItem) => myReadingEndItem.isbn === id);

// 파이어베이스 TimeStamp 변환
myReadingEndItem.startDate = new Date(myReadingEndItem.startDate.seconds * 1000);
myReadingEndItem.endDate = new Date(myReadingEndItem.endDate.seconds * 1000);

const deleteRecord = () => {
  state.deleteModal = false;
  deleteMyReadingEnd(myReadingEndItem.id);
  router.push({ name: 'home' });
};

const cancelRecord = () => { router.back(); };

const endModifyRecord = () => {
  if (myReadingEndItem.platform && myReadingEndItem.readingState && myReadingEndItem.startDate && myReadingEndItem.readingPage >= 0) {
    if (myReadingEndItem.readingState === '독서 중') {
      myReadingEndItem.endDate = null;
      myReadingEndItem.formattedEndDate = null;
      myReadingEndItem.rating = 0;
      addMyReading(myReadingEndItem);
      deleteMyReadingEnd(myReadingEndItem.id);
      router.push({ name: 'home' });
    } else if (myReadingEndItem.readingState === '독서 완료' && myReadingEndItem.endDate) {
      updateMyReadingEnd(myReadingEndItem.id, myReadingEndItem);
      router.push({ name: 'home' });
    } else {
      alert('기록에 필요한 정보를 입력해주세요😢');
    }
  } else {
    alert('기록에 필요한 정보를 입력해주세요😢');
  }
};
</script>

<style scoped>
.end-modify .book-buttons {
  flex-wrap: wrap;
}
</style>