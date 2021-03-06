<template>
  <!-- We display the TOC only if it is not empty (v-if) and the screen is wide
  enough (class: gt-xs). We apply a medium margin: 'q-ma-md' -->
  <q-card
    v-if="tocTree.length"
    class="gt-xs float-right q-ma-md"
    flat
    bordered
  >
    <!-- We use the 'horizontal' to remove paddings -->
    <q-card-section horizontal>
      <!-- We use 'q-expansion-item' to enable toggling (hide/show) the TOC -->
      <q-expansion-item
        default-opened
        icon="toc"
        label="Table of contents"
      >
        <q-separator />
        <q-list>
          <!-- loop through items of tocTree -->
          <template v-for="item in tocTree">
            <!-- if the item does not have children we use 'q-item' -->
            <q-item
              v-if="!item.children.length"
              :key="item.id"
              :to="`#${item.id}`"
              @click="onClick"
            >
              <q-item-section>{{ item.label }}</q-item-section>
            </q-item>
            <!-- if the item has children we use 'q-expansion-item' to enable
            toggling (hiding/showing) them -->
            <q-expansion-item
              v-else
              :key="item.id"
              default-opened
              :label="item.label"
              :to="`#${item.id}`"
              @click="onClick"
            >
              <!-- children are displayed in a q-list below their parent -->
              <q-list>
                <!-- each child is indented a little (inset-level).
                Dense mode uses less space -->
                <q-item
                  v-for="childItem in item.children"
                  :key="childItem.id"
                  dense
                  :inset-level="0.2"
                  :to="`#${childItem.id}`"
                  @click="onClick"
                >
                  <q-item-section>{{ childItem.label }}</q-item-section>
                </q-item>
              </q-list>
            </q-expansion-item>
          </template>
        </q-list>
      </q-expansion-item>
    </q-card-section>
  </q-card>
</template>

<script>
import { scroll } from 'quasar'
const { setScrollPosition } = scroll

export default {
  props: {
    // 'tocTree' will be provided by the component using QmToc (vmd components)
    tocTree: {
      type: Array,
      default () { return [] }
    }
  },
  methods: {
    onClick (data) {
      debugger
      console.log(data)
      this.scrollTo(data.toElement)
    },
    scrollTo (el) {
      if (el) {
        setTimeout(() => {
          this.scrollPage(el)
        }, 200)
      }
    },
    scrollPage (el) {
      const offset = el.offsetTop + el.parentNode.offsetTop - 50
      setScrollPosition(window, offset, 500)
    }
  }
}
</script>

<style lang="sass">
// When navigating (scrolling) to an anchor and to avoid that the anchor gets
// hidden by the fixed page header we offset the scrolling by the header height.
.q-markdown [class^="q-markdown--heading-h"]
  scroll-margin-top: $toolbar-min-height
</style>
