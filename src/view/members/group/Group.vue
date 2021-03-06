<template>
  <GlobalContainer>
    <template #left-column>
      <GlobalCard color="blue" top>
        <div class="flex col fullwidth">
          <SideNavEntries section="members" />
        </div>
      </GlobalCard>
      <br />
      <GlobalRules bot />
    </template>
    <GlobalCard
      header="group.webp"
      :height="70"
      color="blue"
      v-if="data"
      justified
    >
      <div class="group-header">
        <blazon
          :shape="data.blazon.shape"
          :top="data.blazon.top"
          :bot="data.blazon.bot"
          :primary="data.blazon.primary"
          :secondary="data.blazon.secondary"
        />
        <div class="flex col">
          <StrokeText class="group-name">{{ this.data.name }}</StrokeText>
          <div class="motto">"{{ this.data.motto }}"</div>
        </div>
        <img
          v-if="this.data.official"
          src="@/asset/img/group/official.svg"
          style="float: right"
        />
      </div>
      <div class="markdown-body description" v-html="formatDescription"></div>
      <br />
      <GlobalCard v-if="data" class="justified">
        {{ $t(`group.leader.${data.type}`) }}:
        <UserLink :user="data.leader" />
        <br /><br />
        Occupation du groupe:
        <b>{{ (((data.members.length + 1) / data.size) * 100).toFixed(0) }}%</b>
        (<b>{{ data.members.length + 1 }}</b
        >/<b>{{ data.size }}</b
        >)<br /><br />
        Membres du groupe:
        <UserLink
          v-for="(user, index) of this.data.members"
          :user="user"
          :key="user.id"
          :separator="index < this.data.members.length - 1"
        /><br /><br />
        Localisation : <b>{{ data.localisation }}</b
        ><br /><br />
        <div class="icon flex col centered">
          <div style="line-height: 10px">Niveau moyen</div>
          <div>
            <img
              draggable="false"
              @contextmenu.prevent
              :alt="digit"
              v-for="digit in data.level.toString(10)"
              :key="digit.index"
              width="19"
              height="21"
              :src="require(`@/asset/img/number/${digit}.svg`)"
            />
          </div>
        </div>
        &nbsp;<img
          :src="require(`@/asset/img/group/${this.data.status}.png`)"
        />
      </GlobalCard>
      <br />
      Groupe no. <b>{{ this.$route.params.id }}</b> créé le
      <b>{{ formatDate }} ({{ formatDistance }} jours)</b><br />
      <br />
      <GlobalCard v-if="data" class="justified"
        ><img src="@/asset/img/group/bacteria.gif" style="float: left" /><b
          >Bacteria</b
        ><br /><br />
        Classé : <b>{{ this.data.bacteria.rank }}</b
        >/<b>{{ this.data.bacteria.total }}</b> avec
        <b>{{ this.data.bacteria.points }}</b> points.</GlobalCard
      ><br />
      <GlobalCard v-if="data" class="justified"
        ><img src="@/asset/img/group/patojdur.gif" style="float: left" /><b
          >Patojdur</b
        ><br /><br />
        Classé : <b>{{ this.data.patojdur.rank }}</b
        >/<b>{{ this.data.patojdur.total }}</b> avec
        <b>{{ this.data.patojdur.points }}</b> points.</GlobalCard
      ><br />
      <GlobalCard v-if="data" class="justified"
        ><img src="@/asset/img/group/popularity.gif" style="float: left" /><b
          >Popularity</b
        ><br /><br />
        Classé : <b>{{ this.data.popularity.rank }}</b
        >/<b>{{ this.data.popularity.total }}</b> avec
        <b>{{ this.data.popularity.points }}</b> points.</GlobalCard
      ><br />
      <GlobalCard v-if="data" class="justified">
        Classement général : <b>{{ this.data.global.rank }}</b
        >/<b>{{ this.data.global.total }}</b> avec
        <b>{{ this.data.global.points }}</b> points.</GlobalCard
      >
    </GlobalCard>
    <template #right-column
      ><GlobalCard color="blue" v-if="data">
        <template #header> Inscription pour rejoindre ce groupe </template>
        <div class="justified">
          <img
            :src="require(`@/asset/img/group/${this.data.status}.png`)"
            style="float: left; margin-right: 4px"
          />
          {{ $t(`group.${this.data.status}`) }}
          <div v-if="authenticated">
            <br />
            <a @click.prevent="join" style="cursor: var(--pointer)"
              >Rejoindre ce groupe</a
            >
          </div>
        </div>
      </GlobalCard></template
    >
  </GlobalContainer>
</template>
<script>
import Blazon from "@/component/blazon/Blazon.vue";
import StrokeText from "@/component/core/StrokeText.vue";
import messageRender from "@/module/messageRender.js";
import { format, differenceInCalendarDays } from "date-fns";
import { fr, enGB } from "date-fns/locale";
import { mapGetters } from "vuex";
const locales = { fr, enGB };

// @vuese
// @group View/Members/Group
// Group view page
export default {
  name: "GroupView",
  components: {
    Blazon,
    StrokeText,
  },
  data() {
    return {
      data: null,
    };
  },
  computed: {
    ...mapGetters("auth", ["authenticated"]),
    formatDescription() {
      return messageRender(this.data.description);
    },
    formatDate() {
      return format(new Date(this.data.date), "PPp", {
        locale: locales[navigator.language.split("-")[0]],
      });
    },
    formatDistance() {
      return differenceInCalendarDays(new Date(), new Date(this.data.date), {
        locale: locales[navigator.language.split("-")[0]],
      });
    },
  },
  methods: {
    join() {
      console.log("Rejoins " + this.$route.params.id);
    },
  },
  async beforeRouteEnter(to, from, next) {
    next((vm) =>
      vm.api.get("/api/group.json").then((res) => (vm.data = res.data))
    );
  },
  async beforeRouteUpdate(to, from, next) {
    const req = await this.api.get("/api/group.json");
    this.data = req.data;
    next();
  },
  metaInfo: {
    title: "section.group",
  },
};
</script>
<style src="@/asset/css/bbs/markdown.css"></style>
<style src="katex/dist/katex.min.css"></style>
<style src="@/asset/css/bbs/code.css"></style>
<style lang="scss" scoped>
.blazon {
  float: left;
}
.motto {
  text-align: left;
  padding: 0 6px;
}

.group-name {
  height: 28px;
  font-size: 28px;
  fill: var(--text-button);
  stroke: #f39;
  stroke-width: 4;
}

.description {
  text-align: left;
}

.group-header {
  font-family: "Chimboz Heavy";
  color: #3c556f;
  font-size: 16px;
  min-height: 90px;
}

.icon {
  display: inline-flex;
  font-family: "Pixelade";
  font-size: 13px;
  justify-content: center;
  width: 50px;
  height: 50px;
  border: 1px solid var(--main-bg);
  background: linear-gradient(to bottom, #deeaf5, #a7c6e4);
  border-radius: 4px;
  vertical-align: middle;
}
</style>
