<template>
  <h1>{{ $t("message.hello") }}</h1>
  <h2>{{ search }}</h2>

  <p>
    Recommended IDE setup:
    <a href="https://code.visualstudio.com/" target="_blank">VSCode</a>
    +
    <a
      href="https://marketplace.visualstudio.com/items?itemName=octref.vetur"
      target="_blank"
    >
      Vetur
    </a>
    or
    <a href="https://github.com/johnsoncodehk/volar" target="_blank">Volar</a>
    (if using
    <code>&lt;script setup&gt;</code>)
  </p>

  <p>See <code>README.md</code> for more information.</p>

  <p>
    <a href="https://vitejs.dev/guide/features.html" target="_blank">
      Vite Docs
    </a>
    |
    <a href="https://v3.vuejs.org/" target="_blank">Vue 3 Docs</a>
  </p>

  <button class="m-4 p-4 first" type="button" @click="increment">
    count is: {{ count }}
  </button>
  <button class="m-4 p-4" type="button" @click="asyncIncrement">
    count is: {{ count }}
  </button>
  <button class="m-4 p-4 last" type="button" @click="emitIncrement">
    count is: {{ count }}
  </button>
  <input ref="input" v-model="search" type="text" />
  <p>
    Edit
    <code>components/HelloWorld.vue</code> to test hot module replacement.
  </p>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from "vue"
import { useRouteQuery } from "@vueuse/router"
import { useStore } from "@/store"
import { emitter } from "@/store/PubSub"
import { useTitle, onStartTyping } from "@vueuse/core"

export default defineComponent({
  name: "HelloWorld",
  props: {
    msg: {
      type: String,
      required: true,
    },
  },
  setup: () => {
    const input = ref(null)

    onStartTyping(() => {
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      // @ts-ignore
      if (input?.value && !input?.value.active) input.value.focus()
    })

    const store = useStore()

    const search = useRouteQuery("search")

    const title = useTitle()

    return {
      // typed as number
      count: computed(() => store.state.count),

      // access a mutation
      increment: () => store.commit("increment", 1),

      // access an action
      asyncIncrement: () => store.dispatch("asyncIncrement", 1),

      // emit global event
      emitIncrement: () => emitter.emit("globalIncrement", 1),

      // reactive search text
      search: computed({
        get: () => search?.value,
        set: (val) => {
          search.value = val
          title.value = search.value as string
        },
      }),
      // the DOM element will be assigned to the ref after initial render
      // 그러니 프로퍼티로 지정되어야 null 에러가 나지 않음
      input,
    }
  },
  methods: {},
})
</script>
<style lang="scss" scoped>
button {
  color: red;
  background: blue;
  border: 0.1em solid gray;
  text-shadow: beige 0.07em 0.25em 0.25em, beige -0.07em -0.07em 0.25em,
    beige 0.07em -0.07em 0.25em, beige -0.07em 0.07em 0.25em;

  &.first {
    background: green;
  }

  &.last {
    background: yellow;
  }
}
</style>
