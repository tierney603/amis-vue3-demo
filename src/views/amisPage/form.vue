<template>
  <div style="padding: 20px">
    <h3>AMIS部分</h3>
    <amisRender
      v-on:ready="ready"
      :schema="schema"
      :locals="locals"
      :props="props"
    />
    <h3>VUE部分</h3>
    <button v-on:click="reset">重置数据</button>
    <button v-on:click="submit">提交表单</button>
    <button v-on:click="getData">getData</button>
  </div>
</template>
<script setup>
import amisRender from "@/components/amisRender.vue";
import { onMounted, reactive, ref } from "vue";

let scoped = ref(null);
let schema = ref({
  type: "form",
  name: "theform",
  mode: "horizontal",
  title: "${siteName} Mixed Demo", // siteName 是 AMISRenderer 中 context 定义的
  body: [
    {
      label: "Name",
      type: "input-text",
      name: "name",
      id: "name",
    },
    {
      label: "Email",
      type: "input-email",
      required: true,
      placeholder: "请输入邮箱地址",
      name: "email",
    },
  ],
  actions: [
    {
      type: "button",
      label: "自定义事件",
      onEvent: {
        click: {
          actions: [
            {
              ignoreError: false,
              actionType: "custom",
              script: () => {},
            },
          ],
        },
      },
      id: "u:367fb5ab6801",
    },
    {
      type: "button",
      label: "弹窗",
      onEvent: {
        click: {
          actions: [
            {
              args: {},
              dialog: {
                type: "dialog",
                title: "弹框标题",
                body: [
                  {
                    type: "tpl",
                    tpl: "对，你刚刚点击了",
                    wrapperComponent: "",
                    inline: false,
                    id: "u:600051e78f88",
                  },
                ],
                showCloseButton: true,
                showErrorMsg: true,
                showLoading: true,
                id: "u:daf65abf8b79",
              },
              actionType: "dialog",
            },
          ],
        },
      },
      id: "u:367fb5ab6801",
    },
    {
      type: "submit",
      label: "Sumibt",
      primary: true,
    },
    {
      type: "button",
      label: "页面跳转",
      actionType: "link",
      url: "/amisPage-table?",
    },
    {
      type: "button",
      label: "调用外部",
      onEvent: {
        click: {
          actions: [
            {
              actionType: "broadcast",
              args: {
                eventName: "event-1",
              },
            },
          ],
        },
      },
    },
  ],
});
let locals = ref({
  // 传递初始值
  name: "Your Name",
  sourceCode: "123",
});

let props = reactive({
  onBroadcast: (type, rawEvent, data) => {
    alert(type);
  },
  onFinished: (values) => {
    alert("提交成功" + JSON.stringify(values, null, 2));
  },
});

const ready = (data) => {
  scoped.value = data.scoped;
};

const customEvent = (context, doAction, event) => {
  // 不能访问vue组件
  alert("customEvent");
};

const reset = () => {
  locals.value = {
    name: "tierney",
  };
};

const submit = () => {
  scoped.value?.getComponentByName("theform").doAction({
    actionType: "submit",
  });
};

const getData = () => {
  let data = scoped.value?.getComponentByName("theform").props.data;
  alert(JSON.stringify(data));
};

onMounted(() => {
  // schema.value={}
});
</script>
  