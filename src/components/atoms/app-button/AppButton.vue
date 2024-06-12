<template>
  <button
    :class="btnClass"
    :disabled="disabled"
    class="button"
    v-bind="$attrs"
    @click="handleClick($event)"
  >
    <slot name="default">
      {{ props.text }}
    </slot>
  </button>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  disabled: {
    type: Boolean,
    required: false,
    default: false,
  },

  fz: {
    type: [String, Number],
    default: 13,
    required: false,
  },

  fw: {
    type: [String, Number],
    default: 400,
    required: false,
    validator(value) {
      const converted = Number.parseInt(`${value}`);
      return (
        converted === 100 ||
        converted === 200 ||
        converted === 300 ||
        converted === 400 ||
        converted === 500 ||
        converted === 600 ||
        converted === 700 ||
        converted === 800 ||
        converted === 900
      );
    },
  },

  widthFull: {
    type: Boolean,
    required: false,
    default: false,
  },

  icon: {
    type: String,
    required: false,
    default: "",
    validator: (v) => {
      //s 30
      //m 40
      return ["", "s", "m"].includes(v);
    },
  },

  round: {
    type: Boolean,
    required: false,
    default: false,
  },

  width: {
    type: String,
    required: false,
    default: "m",
    validator: (v) => {
      //xs 80
      //s 120
      //m 180
      //l 240
      //xl 300
      return ["xs", "s", "m", "l", "xl", "xxl"].includes(v);
    },
  },

  height: {
    type: String,
    required: false,
    default: "l",
    validator: (v) => {
      //m 30
      //l 40
      //xl 50
      return ["m", "l", "xl", "xxl"].includes(v);
    },
  },

  shadow: {
    type: Boolean,
    required: false,
    default: false,
  },

  view: {
    type: String,
    required: false,
    default: "primary",
    validator: (v) => {
      return (
        v === "primary" ||
        v === "secondary" ||
        v === "error" ||
        v === "success" ||
        v === "warning" ||
        v === "link" ||
        v === "light" ||
        v === "transparent"
      );
    },
  },

  text: {
    type: String,
    required: false,
    default: "",
  },

  outlined: {
    type: Boolean,
    required: false,
    default: false,
  },
});

const emit = defineEmits(["click"]);

const handleClick = (e) => {
  emit("click", e);
};

const classWidth = computed(() => {
  if (props.icon) {
    switch (props.icon) {
      // s 30
      // m 40
      case "s": {
        return "button_icon_s";
      }
      case "m": {
        return "button_icon_m";
      }
      default: {
        return "";
      }
    }
  }

  if (props.widthFull) {
    return "w-100";
  }

  switch (props.width) {
    case "xs": {
      return "button_width_xs";
    }
    case "s": {
      return "button_width_s";
    }
    case "m": {
      return "button_width_m";
    }
    case "l": {
      return "button_width_l";
    }
    case "xl": {
      return "button_width_xl";
    }
    case "xxl": {
      return "button_width_xxl";
    }
    default: {
      return "";
    }
  }
});

const classObjHeight = computed(() => {
  switch (props.height) {
    case "xs": {
      return "button_height_xs";
    }
    case "m": {
      return "button_height_m";
    }
    case "l": {
      return "button_height_l";
    }
    case "xl": {
      return "button_height_xl";
    }
    case "xxl": {
      return "button_height_xxl";
    }
    default: {
      return "";
    }
  }
});

const classView = computed(() => {
  switch (props.view) {
    case "secondary": {
      return "button_view_secondary";
    }
    case "warning": {
      return "button_view_warning";
    }
    case "success": {
      return "button_view_success";
    }
    case "error": {
      return "button_view_error";
    }
    case "primary": {
      return "button_view_primary";
    }
    case "transparent": {
      return "button_view_transparent";
    }
    case "link": {
      return "button_view_link";
    }
    case "light": {
      return "button_view_light";
    }

    default: {
      return "";
    }
  }
});


const classObj = computed(() => {
  const obj = {};
  obj[`text_fz_${props.fz}`] = true;
  obj[`text_fw_${props.fw}`] = true;

  return obj;
});

const btnClass = computed(() => {
  let el = {
    ...classObj.value,
  };

  el["button_shadow"] = props.shadow;
  el["button_round"] = props.round;
  el["button_outlined"] = props.outlined;
  el[classWidth.value] = true;
  el[classView.value] = true;
  el[classObjHeight.value] = true;

  return el;
});
</script>

<style scoped>
.button {
  cursor: pointer;
  user-select: none;
  position: relative;
  font-size: 15px;
  background-color: transparent;
  padding: 0 20px;
  transition: background-color 0.2s, border 0.2s, color 0.2s;
  font-family: Open Sans, Arial, "sans-serif";
  border-radius: 4px;
  line-height: 20px;
  outline: none;
  border: none;
  font-weight: 400;
}
.button:disabled {
  opacity: 0.35;
}
.button_width_xs {
  width: 80px;
  min-width: 80px;
}
.button_width_s {
  width: 120px;
  min-width: 120px;
}
.button_width_m {
  width: 180px;
  min-width: 180px;
}
.button_width_l {
  width: 240px;
  min-width: 240px;
}
.button_width_xl {
  width: 300px;
  min-width: 300px;
}
.button_width_xxl {
  width: 450px;
  min-width: 450px;
}
.button_icon_m {
  width: 30px;
  min-width: 30px;
  max-width: 30px;
  height: 30px;
  min-height: 30px;
  max-height: 30px;
}
.button_icon_l {
  width: 40px;
  max-width: 40px;
  min-width: 40px;
  height: 40px;
  max-height: 40px;
  min-height: 40px;
}
.button_height_m {
  height: 32px;
  max-height: 32px;
  min-height: 32px;
}
.button_height_l {
  height: 40px;
  max-height: 40px;
  min-height: 40px;
}
.button_height_l {
  height: 50px;
  max-height: 50px;
  min-height: 50px;
}
.button_height_xl {
  height: 60px;
  max-height: 60px;
  min-height: 60px;
}
.button_height_xxl {
  height: 72px;
  max-height: 72px;
  min-height: 72px;
}
.button_view_primary {
  background-color: #5471d0;
  color: #f2f6fb;
}
.button_view_primary:hover {
  background-color: #2b4cba;
}
.button_view_primary:active {
  background-color: #30489a;
}
.button_view_link {
  color: #5471d0;
}
.button_view_link:hover {
  color: #2b4cba;
}
.button_view_transparent {
  background-color: transparent;
}
.button_view_light {
  background-color: #fff;
  color: #272a2e !important;
}
.button_view_light:hover {
  background-color: #f5f5f7 !important;
}
.button_round {
  border-radius: 25px;
}
.button.button_shadow {
  box-shadow: 0 3px 4px rgba(0, 0, 0, .06);
}
.button_outlined.button_view_primary {
  background-color: #f2f6fb;
  color: #5471d0;
  border: 1px solid #5471d0;
}
.button_outlined.button_view_primary:hover {
  color: #2b4cba;
  border: 1px solid #2b4cba;
}

</style>
