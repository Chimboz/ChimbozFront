<template>
  <span class="pink justified" style="text-align: left">
    <router-link to="/bbs" class="pink">BBS</router-link> »
    <router-link v-if="topic" :to="'/bbs/' + topic.forum.id" class="pink">{{
      topic.forum.name
    }}</router-link></span
  >
  <router-link :to="'/topic/' + $route.params.topic" class="pink"
    ><h1 class="justified ellipsis" v-if="topic.messages[0]">
      {{ topic.messages[0].title }}
    </h1></router-link
  >
  <br />
  <table class="bbs">
    <colgroup>
      <col class="info" width="100" />
      <col width="100%" />
    </colgroup>
    <thead>
      <tr>
        <th valign="top" colspan="2" height="25" nowrap="nowrap">
          <pagination
            :current="this.$route.params.page ? +this.$route.params.page : 1"
            :total="topic.pages"
            :callback="(page) => '/topic/' + $route.params.topic + '/' + page"
          />
        </th>
      </tr>
    </thead>
    <tbody>
      <Message
        v-for="(message, index) in topic.messages"
        :key="message.id"
        :message="message"
        :separator="index < topic.messages.length - 1"
      />
    </tbody>
    <tr>
      <th valign="bottom" colspan="2" height="25" nowrap="nowrap">
        <pagination
          :current="this.$route.params.page ? +this.$route.params.page : 1"
          :total="topic.pages"
          :callback="(page) => '/topic/' + $route.params.topic + '/' + page"
        />
      </th>
    </tr> </table
  ><br />
  <div style="text-align: end" v-if="authenticated">
    <button class="btn-action" v-if="user.role > 50" @click.prevent="move"
      ><img
        src="@/asset/img/bbs/icon/arrow.svg"
        width="10"
        height="12"
        draggable="false"
        alt="Arrow"
        @contextmenu.prevent
      />&nbsp;Déplacer</button
    >&nbsp;
    <button class="btn-action" v-if="user.role > 50" @click.prevent="lock"
      ><img
        src="@/asset/img/bbs/icon/lock.svg"
        width="9"
        height="12"
        draggable="false"
        alt="Lock"
        @contextmenu.prevent
      />&nbsp;Verrouiller</button
    >&nbsp;
    <button
      class="btn-action"
      v-if="user.role > 50"
      @click.prevent="this.delete"
      >x&nbsp;Supprimer</button
    >
  </div>
</template>

<script>
import Pagination from "../../core/Pagination.vue";
import Message from "../row/Message.vue";
import { mapGetters, mapState } from "vuex";

// @vuese
// @group BBS/List
export default {
  name: "TopicList",
  components: {
    Message,
    Pagination
  },
  props: {
    topic: {
      required: true,
      type: Object
    }
  },
  computed: {
    ...mapGetters("auth", ["authenticated"]),
    ...mapState("auth", ["user"])
  },
  methods: {
    lock() {
      console.log("Lock " + this.$route.params.topic);
    },
    delete() {
      console.log("Delete " + this.$route.params.topic);
    },
    move() {
      console.log("Move " + this.$route.params.topic);
    }
  }
};
</script>
<style lang="scss" scoped>
.info {
  display: none !important;
}

@media (min-width: 800px) {
  .info {
    display: table-column !important;
  }
}
</style>
