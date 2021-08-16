<img src="https://www.mintjams.jp/img/cr.svg" alt ="" width="64">

# vue-split
A reusable Split component for Vue.js 2.x used by webtop applications.

## Installation

```sh
npm install --save-dev @mintjamsinc/vue-split
```

## Usage

```vue
<Split :onResize="onResize" min="20%" :initial="splitPercent" :show="[showFilter, true]">
  <template v-slot:pane1>
    <!-- Defines the pane1 content of your application -->
  </template>
  <template v-slot:pane2>
    <!-- Defines the pane2 content of your application -->
  </template>
</Split>
```

```js
import Split from '@mintjamsinc/vue-split';

export default {
  components: {
    Split: Split,
  },
  data() {
    return {
      splitPercent: '28%',
      showFilter: true,
    }
  },
  methods: {
    onResize(percent) {
      window.Webtop.local.set('splitPercent', percent + '%');
    },
  },
}
```

## License

[MIT](https://opensource.org/licenses/MIT)

Copyright (c) 2021 MintJams Inc.