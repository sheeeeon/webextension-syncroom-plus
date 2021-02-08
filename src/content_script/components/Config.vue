<template lang="pug">
.modal-card
  .modal-card-head
    .modal-card-title
      b-icon(icon="cog")
      |
      | 설정
  .modal-card-body
    .section
      .subtitle
        b-icon(icon="cog")
        |
        | 설정

      b-switch(v-model="configAutoReload", type="is-info", @input="onInputAutoReload")
        | 자동 업데이트

      b-switch(v-model="configAnimation", type="is-info", @input="onInputAnimation")
        | 애니메이션

      hr

      .subtitle
        b-icon(icon="star")
        |
        | 즐겨찾기 관리（{{favoriteMembers.length}}）
      b-message(type="is-info", v-if="favoriteMembers.length === 0")
        | 즐겨찾기에 추가된 멤버가 한 명도 없습니다.
        | 멤버 이름 옆에 있는 즐겨찾기 등록 버튼（
        b-icon(icon="star")
        | ）을 클릭하여 즐겨찾기를 추가/삭제할 수 있습니다.
      b-table(:data="favoriteMembers", v-else, narrowed, bordered)

        b-table-column(label="이름", v-slot="props")
          | {{ props.row.memberName }}

        b-table-column(label="추가한 날짜", v-slot="props")
          | {{ props.row.createdAt | moment('llll') }}

        b-table-column(label="조작", v-slot="props")
          b-button(type="is-danger", size="is-small", icon-left="trash", @click="confirmRemoveFavorite(props.row.memberName)")
            | 삭제

      hr

      .subtitle
        b-icon(icon="bell")
        |
        | 온라인 알림 관리（{{notificationOnlineMembers.length}}）
      b-message(type="is-info", v-if="notificationOnlineMembers.length === 0")
        | 등록되어 있는 온라인 알림이 없습니다.
        | メンバー名の横にある通知ボタン（
        b-icon(icon="bell")
        | /
        b-icon(icon="bell-slash")
        | ）をクリックすると通知の登録/解除が行えます。
      b-table(:data="notificationOnlineMembers", v-else, narrowed, bordered)

        b-table-column(label="名前", v-slot="props")
          | {{ props.row.memberName }}

        b-table-column(label="追加日時", v-slot="props")
          | {{ props.row.createdAt | moment('llll') }}

        b-table-column(label="操作", v-slot="props")
          b-button(type="is-danger", size="is-small", icon-left="trash", @click="confirmRemoveNotification(props.row.memberName)")
            | 削除
  .modal-card-foot
    b-button(@click="$emit('close')", icon-left="times") 閉じる
</template>

<script>
export default {
  data() {
    return {
      configAutoReload: this.$store.getters['config/autoReload'],
      configAnimation: this.$store.getters['config/animation'],
    };
  },
  computed: {
    favoriteMembers() {
      return this.$store.getters['favoriteMembers/members'];
    },
    notificationOnlineMembers() {
      return this.$store.getters['notificationOnlineMembers/members'];
    },
  },
  methods: {
    onInputAutoReload(value) {
      this.$store.dispatch('config/setAutoReload', value);
    },
    onInputAnimation(value) {
      this.$store.dispatch('config/setAnimation', value);
    },
    confirmRemoveFavorite(memberName) {
      this.$buefy.dialog.confirm({
        title: 'お気に入りから削除しますか？',
        message: `「<b>${memberName}</b>」さんをお気に入りから削除します。<br />この作業は<b>取り消せません</b>。`,
        confirmText: '削除する',
        cancelText: '閉じる',
        type: 'is-danger',
        hasIcon: true,
        onConfirm: async () => {
          await this.$store.dispatch('favoriteMembers/removeFavorite', memberName);
          this.$buefy.toast.open({
            message: 'お気に入りから削除しました',
            type: 'is-success',
          });
        },
      });
    },
    confirmRemoveNotification(memberName) {
      this.$buefy.dialog.confirm({
        title: 'オンライン通知を解除しますか？',
        message: `「<b>${memberName}</b>」さんのオンライン通知を解除します。<br />この作業は<b>取り消せません</b>。`,
        confirmText: '解除する',
        cancelText: '閉じる',
        type: 'is-danger',
        hasIcon: true,
        onConfirm: async () => {
          await this.$store.dispatch('notificationOnlineMembers/removeNotification', memberName);
          this.$buefy.toast.open({
            message: 'オンライン通知を解除しました',
            type: 'is-success',
          });
        },
      });
    },
  },
};
</script>
