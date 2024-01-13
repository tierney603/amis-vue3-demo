<template>
  <div style="padding: 20px" class="content">
    <amisRender
      v-on:ready="ready"
      :schema="schema"
      :locals="locals"
      :props="props"
    />
  </div>
</template>
<script setup>
import amisRender from "@/components/amisRender.vue";
import { onMounted, reactive, ref } from "vue";

let scoped = ref(null);
let schema = ref({});
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

onMounted(() => {
  schema.value = {
    type: "crud",
    draggable: true,
    api: "/amis/api/sample?waitSeconds=1",
    perPage: 15,
    keepItemSelectionOnPageChange: true,
    maxKeepItemSelectionLength: 11,
    labelTpl: "${id} ${engine}",
    orderBy: "id",
    orderDir: "asc",
    bulkActions: [
      {
        label: "批量删除",
        actionType: "ajax",
        api: "delete:/amis/api/sample/${ids|raw}",
        confirmText: "确定要批量删除?",
      },
      {
        label: "批量修改",
        actionType: "dialog",
        dialog: {
          title: "批量编辑",
          name: "sample-bulk-edit",
          body: {
            type: "form",
            api: "/amis/api/sample/bulkUpdate2",
            body: [
              {
                type: "hidden",
                name: "ids",
              },
              {
                type: "input-text",
                name: "engine",
                label: "Engine",
              },
            ],
          },
        },
      },
    ],
    quickSaveApi: "/amis/api/sample/bulkUpdate",
    quickSaveItemApi: "/amis/api/sample/$id",
    filterTogglable: true,
    headerToolbar: [
      "filter-toggler",
      "bulkActions",
      {
        type: "tpl",
        // "tpl": "定制内容示例：当前有 ${count} 条数据。",
        className: "v-middle",
      },
      {
        type: "link",
        href: "https://www.baidu.com",
        body: "百度一下",
        htmlTarget: "_parent",
        className: "v-middle",
      },
      {
        type: "columns-toggler",
        align: "right",
      },
      {
        type: "drag-toggler",
        align: "right",
      },
      {
        type: "pagination",
        align: "right",
      },
    ],
    footerToolbar: ["statistics", "switch-per-page", "pagination"],
    columns: [
      {
        name: "id",
        label: "ID",
        sortable: true,
        type: "text",
        toggled: true,
        remark: "Bla bla Bla",
      },
      {
        name: "engine",
        label: "Rendering engine",
        sortable: true,
        searchable: true,
        popOver: {
          body: "Popover 内容：${platform}",
          trigger: "hover",
        },
        popOverEnableOn: "this.id === 1",
        type: "text",
        toggled: true,
      },
      {
        name: "browser",
        label: "Browser",
        sortable: true,
        type: "text",
        toggled: false,
      },
      {
        name: "platform",
        label: "Platform(s)",
        popOver: {
          body: "Popover 内容：${platform}",
        },
        sortable: true,
        type: "text",
        toggled: true,
      },
      {
        name: "version",
        label: "Engine version",
        quickEdit: true,
        type: "text",
        toggled: true,
        filterable: {
          options: [
            {
              label: "4",
              value: "4",
            },
            {
              label: "5",
              value: "5",
            },
            {
              label: "6",
              value: "6",
            },
          ],
        },
        classNameExpr: "<%= data.version < 5 ? 'bg-danger' : '' %>",
      },
      {
        type: "text",
        name: "grade",
        label: "CSS grade",
        quickEdit: {
          saveImmediately: true,
          mode: "inline",
          type: "select",
          options: ["A", "B", "C", "D", "X"],
        },
      },
      {
        type: "operation",
        label: "操作",
        width: 100,
        buttons: [
          {
            type: "button",
            icon: "fa fa-eye",
            actionType: "dialog",
            tooltip: "查看",
            dialog: {
              title: "查看",
              body: {
                type: "form",
                body: [
                  {
                    type: "static",
                    name: "engine",
                    label: "Engine",
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "static",
                    name: "browser",
                    label: "Browser",
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "static",
                    name: "platform",
                    label: "Platform(s)",
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "static",
                    name: "version",
                    label: "Engine version",
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "static",
                    name: "grade",
                    label: "CSS grade",
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "html",
                    html: "<p>添加其他 <span>Html 片段</span> 需要支持变量替换（todo）.</p>",
                  },
                ],
              },
            },
          },
          {
            type: "button",
            icon: "fa fa-pencil",
            tooltip: "编辑",
            actionType: "drawer",
            drawer: {
              position: "left",
              size: "lg",
              title: "编辑",
              body: {
                type: "form",
                name: "sample-edit-form",
                api: "/amis/api/sample/$id",
                body: [
                  {
                    type: "input-text",
                    name: "engine",
                    label: "Engine",
                    required: true,
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "input-text",
                    name: "browser",
                    label: "Browser",
                    required: true,
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "input-text",
                    name: "platform",
                    label: "Platform(s)",
                    required: true,
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "input-text",
                    name: "version",
                    label: "Engine version",
                  },
                  {
                    type: "divider",
                  },
                  {
                    type: "select",
                    name: "grade",
                    label: "CSS grade",
                    options: ["A", "B", "C", "D", "X"],
                  },
                ],
              },
            },
          },
          {
            type: "button",
            icon: "fa fa-times text-danger",
            actionType: "ajax",
            tooltip: "删除",
            confirmText: "您确认要删除?",
            api: "delete:/amis/api/sample/$id",
          },
        ],
        toggled: true,
      },
    ],
  };
});
</script>