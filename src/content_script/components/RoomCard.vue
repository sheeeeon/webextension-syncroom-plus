<template lang="pug">
.card(:class="{ 'card--no_vacancy': isNoVacancy, 'card--need_passwd': needPasswd }")
  .card__header
    .card__header__item
      RemainingTime(:create-time="createTime")
  .card__body
    h3.room_name {{ roomName }}

    p.room_tags
      b-taglist
        b-tag(v-for="tag in roomTags", :key="tag")
          | {{tag}}

    p.room_desc(v-html="linkedRoomDesc")

    Members(:num-members="numMembers", :members="members", :iconlist="iconlist", :room-create-time="createTime")

    div(v-if="isNoVacancy")
      template(v-if="isNotificationVacancyRoom")
        b-button(type="is-link is-light", expanded, @click="onRemoveNotificationVacancyRoom", icon-left="bell")
          | 알림을 해제
      template(v-else)
        b-button(type="is-link is-light", expanded, @click="onSetNotificationVacancyRoom", icon-left="bell-slash")
          | 자리가 비면 알림을 받기

    .buttons(v-else)
      b-button.card__body__buttons__entry-button-e(type="is-light" @click="onOpenTentativeSyncroom")
        | 임시 입장

      b-button.card__body__buttons__entry-button(v-if="needPasswd", type="is-dark" @click="onOpenSyncroom", icon-left="lock")
        | 방에 입장
      b-button.card__body__buttons__entry-button(v-else, type="is-link" @click="onOpenSyncroom")
        | 방에 입장
</template>

<script>
import RemainingTime from './RemainingTime';
import Members from './Members';
import makeJoinUri from '../../lib/make_join_uri';

export default {
  props: {
    iconlist: {
      type: Array,
      required: true,
    },
    createTime: {
      type: String,
      required: true,
    },
    numMembers: {
      type: Number,
      required: true,
    },
    roomDesc: {
      type: String,
      required: true,
    },
    roomName: {
      type: String,
      required: true,
    },
    members: {
      type: Array,
      required: true,
    },
    needPasswd: {
      type: Boolean,
      required: true,
    },
    roomTags: {
      type: Array,
      required: true,
    },
  },
  components: {
    RemainingTime,
    Members,
  },
  methods: {
    onSetNotificationVacancyRoom() {
      this.$store.dispatch('notificationVacancyRooms/setNotificationByUID', `${this.createTime}||${this.roomName}`);
    },

    onRemoveNotificationVacancyRoom() {
      this.$store.dispatch('notificationVacancyRooms/removeNotificationByUID', `${this.createTime}||${this.roomName}`);
    },

    onOpenSyncroom() {
      if (this.needPasswd) {
        const pwPrompt = window.prompt('방 비밀번호를 입력하세요.', '');

        if (pwPrompt) {
          location.href = makeJoinUri(this.roomName, pwPrompt, 4, 2);
        }
      } else {
        location.href = makeJoinUri(this.roomName, '', 4, 2);
      }
    },

    onOpenTentativeSyncroom() {
      if (this.needPasswd) {
        const pwPrompt = window.prompt('방 비밀번호를 입력하세요.', '');

        if (pwPrompt) {
          location.href = makeJoinUri(this.roomName, pwPrompt, 4, 3);
        }
      } else {
        location.href = makeJoinUri(this.roomName, '', 4, 3);
      }
    },
  },

  computed: {
    linkedRoomDesc() {
      return (
        this.roomDesc
          .replace(/&/g, '&amp;')
          .replace(/</g, '&lt;')
          .replace(/>/g, '&gt;')
          .replace(/"/g, '&quot;')
          .replace(/'/g, '&#039;')
          .replace(/\n/g, '<br />')
          /* eslint-disable no-useless-escape */
          .replace(/(\b(https|http):\/\/[-A-Z0-9+&@#\/%?=~_|!:,.;]*[-A-Z0-9+&@#\/%=~_|])/gi, (link) => {
            return `<a href='${link}' target='_blank' rel='noopener noreferrer'>${link}</a>`;
          })
          .replace(/((@|＠)[0-9a-zA-Z_]{1,15})/g, (twitterID) => {
            const noAtTwitterID = twitterID.replace(/@|＠/g, '');
            return `<a href='https://twitter.com/${noAtTwitterID}' target='_blank' rel='noopener noreferrer'>${twitterID}</a>`;
          })
      );
    },
    isNoVacancy() {
      return this.numMembers === 5;
    },
    isNotificationVacancyRoom() {
      return this.$store.getters['notificationVacancyRooms/rooms'].find((r) => r.uid === `${this.createTime}||${this.roomName}`);
    },
  },
};
</script>

<style lang="sass" scoped>
.card
  margin: 5px 5px 15px 5px
  width: 300px
  background: #D9E2FE
  border-radius: 5px
  box-shadow: 0px 1px 8px -5px #20293a

  &__header
    border-radius: 5px 5px 0 0
    background: #5073f5
    color: #fff
    font-weight: normal
    font-size: 15px
    line-height: 30px
    text-align: center

  &__body

    padding: 10px
    font-size: 12px

    .room_name
      font-size: 15px
      font-weight: bold
      margin-bottom: 0.5em

      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis

    .room_tags
      margin-bottom: 10px
      overflow: hidden
      height: 24px

    .room_desc
      font-size: 12px
      background: #F9FCFF
      padding: 0em 0.5em
      margin-bottom: 1em

      overflow-y: auto
      word-break: break-word

      height: calc( 1.8em * 4 )
      line-height: 1.8em
      border-radius: 5px

    &__buttons
      &__entry-button
        width: 178px
      &__entry-button-e
        width: 94px

.card.card--need_passwd
  background: #eaeaea

  .card__header
    background: #272727

.card.card--no_vacancy
  background: #E6E6E6

  .card__header
    background: #A3A3A3

  .card__body
    color: #808080
</style>
