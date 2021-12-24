<template>
  <div
    :class="[
      'ayou-day-cell',
      customCellClass,
      isSelected && ['ayou-day-cell-selected', cellSeletedClass],
      { passive: day.isPassive },
    ]"
    @click.stop.prevent="handleDayClick()"
    :title="showLunar && lunarText"
  >
    <div class="day-wrapper">
      <div class="top">
        <template v-if="showLunar">
          <div class="lunar">
            {{ lunarText }}
          </div>
        </template>

        <div class="groupName">
          <div v-if="cellGroupName != ' '" class="note-red"></div>
        </div>
      </div>
      <div
        class="solar"
        :class="{
          'solar-selected': isSelected,
          'without-lunar': !showLunar,
        }"
      >
        {{ day.dayMoment.date() }}
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
import Transformer from "./lunar";
import Translation from "./lang";

export default {
  props: {
    showLunar: {
      type: Boolean,
      default: false,
    },
    day: {
      type: Object,
    },
    isSelected: {
      type: Boolean,
      default: false,
    },
    lang: {
      type: String,
      default: "ko",
    },
    cellSeletedClass: {
      type: String,
    },
    customCellClass: {
      type: String,
    },
    cellGroupName: {
      type: String,
    },
  },
  mixins: [Transformer],
  data() {
    return {
      lunar: this.convertLunar(this.day),
      lan: this.lang,
      lunarTxt: this.solar2lunar(this.day.dayMoment._d),
    };
  },
  watch: {
    day(val) {
      this.lunar = this.convertLunar(val);
    },
    lang(val) {
      this.lan = val;
    },
  },
  methods: {
    handleDayClick() {
      if (this.day.isPassive) return;
      this.$emit("dayClick", this.day, this.lunarTxt.day);
    },
    convertLunar(day) {
      this.lunarTxt = this.solar2lunar(day.dayMoment._d);
      return JSON.parse(JSON.stringify(this.SolarToLunar(day.dayMoment._d)));
    },
  },
  computed: {
    lunarText() {
      return `${this.lunar.day}`;
      // return this.lunar.dayTxt;
    },
  },
  created() {
    // this.cellGroupName = this.cellGroupName.trim();
    // console.log(this.cellGroupName);
  },
};
</script>

<style>
.ayou-day-cell {
  margin: 1px;
  width: 12.86%;
  height: 49px;
  display: inline-block;
  text-align: center;
}
.ayou-day-cell-selected {
  /* background-color: #222944; */
  color: #fff;
  /* border: 2px solid #f1b2b2; */
  border-radius: 15px;
  background-color: #9e0c10 !important;
}
.ayou-day-cell:hover {
  cursor: pointer;
}
.day-wrapper {
  position: relative;
  width: 100%;
  height: 100%;
}
.solar {
  position: absolute;
  text-align: right;
  font-size: 14px;
  font-weight: 300;
  top: 32%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: #333;
  font-weight: 400;
}
.solar-selected {
  color: #ffffff;
}
.passive {
  color: #cccccc;
  opacity: 0.5;
}
.lunar {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  font-size: 11px;
  color: #979494;
  margin: 5px 3px -2px;
}
.without-lunar {
  margin-top: 1px;
}
.groupName {
  font-size: 9px;
  padding: 3px;
  color: #e9e9e9;
}
.note-red {
  position: absolute;
  width: 5px;
  height: 5px;
  background-color: red;
  border-radius: 50%;
  right: -7px;
  bottom: 12px;
}
.top {
  position: absolute;
  text-align: left;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
}
.container-event {
  padding: 18px;
}
@media (max-width: 320px) {
  .ayou-day-cell {
    width: 13.63%;
  }
}
</style>
