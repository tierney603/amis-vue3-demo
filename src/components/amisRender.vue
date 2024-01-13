<template>
  <div :id="domId"></div>
</template>
    
    <script setup>
import {
  defineComponent,
  nextTick,
  defineProps,
  ref,
  onMounted,
  onUnmounted,
  watch,
  toRefs,
  getCurrentInstance,
  defineEmits,
} from "vue";
import "amis/sdk/sdk.js";
import "amis/lib/themes/default.css";
const amisEmbed = amisRequire("amis/embed");
const emit = defineEmits(["ready"]);

const propsData = defineProps(["schema", "locals", "props", "env"]);
let domId = ref("amis_key");
const { schema, locals, props, env } = toRefs(propsData);

let context = ref({
  siteName: "AMIS DEMO",
});

let $router = getCurrentInstance().appContext.config.globalProperties.$router;

let amisInstance = ref(null);

watch(locals, (newVal, oldVal) => {
  updateProps();
});

watch(props, (newVal, oldVal) => {
  updateProps();
});
watch($router, (newVal, oldVal) => {
  updateProps();
});

watch(schema, (newVal, oldVal) => {
  if (amisInstance.value) amisInstance.value.unmount();
  mountAmis(newVal);
});

onMounted(() => {
  mountAmis();
});

onUnmounted(() => {
  amisInstance.value?.unmount();
});

let mountAmis = (data = schema.value) => {
  const { normalizeLink } = amisRequire("amis");
  const router = $router;

  const scoped = amisEmbed.embed(
    document.querySelector("#amis_key"),
    schema.value,
    {
      data: {
        ...locals.value,
      },
      context: context.value,
      location: location.value,
      // todo 下发 location 对象
      ...props.value,
    },
    {
      // 覆盖 amis env
      // 参考 https://aisuda.bce.baidu.com/amis/zh-CN/docs/start/getting-started#sdk
      jumpTo: (to, action) => {
        if (to === "goBack") {
          return router.go(-1);
        }

        to = normalizeLink(to, location.value);

        if (action?.actionType === "url") {
          action.blank === false ? router.push(to) : window.open(to);
          return;
        }
        // 主要是支持 nav 中的跳转
        if (action && to && action.target) {
          window.open(to, action.target);
          return;
        }
        if (/^https?:\/\//.test(to)) {
          window.location.replace(to);
        } else {
          router.push(to);
        }
      },

      updateLocation: (location, replace) => {
        if (location === "goBack") {
          return router.go(-1);
        }

        location = normalizeLink(location, location.value);
        replace ? router.replace(location) : router.replace(location);
      },

      ...env.value,
    },
    () => {
      issueUpdates(scoped);
    }
  );
  amisInstance.value = scoped;
  return scoped;
};

let issueUpdates = (scoped) => {
  emit("ready", { scoped });
};

let updateProps = () => {
  amisInstance.value?.updateProps(
    {
      data: {
        ...locals.value,
      },
      context: context.value,
      ...props.value,
    },
    (a) => {
      console.log(a);
    }
  );
};

</script>
  
  <style lang="scss">
</style>