<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex
      class="page-wrapper"
      xs12
      sm8
      md6
    >
      <draggable
        v-model="list"
        v-bind="dragOptions"
        @start="drag = true"
        @end="drag = false"
        class="list-group"
        tag="ul"
      >
        <transition-group name="list" type="transition">
          <li
            v-for="element in list"
            :key="element.order"
            class="list-group-item"
          >
            <v-img
              v-if="element.type === 'image'"
              src="https://picsum.photos/510/300?random"
              aspect-ratio="1.7"
              lazy-src="https://picsum.photos/id/11/10/6"
            />
            <v-text-field
              v-if="element.type === 'text'"
              v-model="element.value"
              color="pink"
              outlined
              placeholder="Введите текст"
            />
          </li>
        </transition-group>
      </draggable>

      <div v-if="list.length === 0" class="empty-element">
        Нажмите на "+" чтобы добавить элементы
      </div>

      <v-bottom-sheet v-model="sheet">
        <template v-slot:activator="{ on }">
          <v-btn
            v-on="on"
            class="add-btn"
            dark
            fixed
            bottom
            right
            fab
            color="pink"
          >
            <v-icon dark>
              mdi-plus
            </v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-subheader>Добавить новый блок</v-subheader>
          <v-list-item
            v-for="tile in tiles"
            :key="tile.title"
            @click.stop="addItem(tile.type)"
          >
            <v-list-item-icon>
              <v-icon>{{ tile.icon }}</v-icon>
            </v-list-item-icon>
            <v-list-item-title>{{ tile.title }}</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-bottom-sheet>
    </v-flex>
  </v-layout>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  components: {
    draggable
  },
  data: () => ({
    drag: false,
    sheet: false,
    tiles: [
      { icon: 'mdi-image', title: 'Изображение', type: 'image' },
      { icon: 'mdi-text', title: 'Текст', type: 'text' }
    ],
    list: []
  }),
  computed: {
    dragOptions () {
      return {
        animation: 200,
        group: 'description',
        disabled: false,
        ghostClass: 'ghost'
      }
    }
  },
  mounted () {
    // just to show API usage :-)
    this.$axios.$get('http://icanhazip.com')
      .then((response) => {
        // eslint-disable-next-line no-console
        console.log(response)
      })
  },
  methods: {
    sort () {
      this.list = this.list.sort((a, b) => a.order - b.order)
    },
    addItem (type) {
      this.list.push({
        value: '',
        order: this.list.length + 1,
        type
      })
      this.sheet = false
    }
  }
}
</script>
<style lang="scss">
  .add-btn.v-btn--bottom {
    bottom: 45px;
  }
  ul.list-group {
    list-style-type: none;
    padding-left: 0;
    .list-group-item {
      margin: 10px;
    }
  }
  .list-enter-active, .list-leave-active {
    transition: all 1s;
  }
  .list-enter, .list-leave-to {
    opacity: 0;
    transform: translateY(30px);
  }
  .page-wrapper {
    width: 100%;
  }
  .empty-element {
    position: absolute;
    top: 44%;
    text-align: center;
    color: #ccc;
    font-size: 22px;
    font-weight: 300;
  }
  .v-input {
    .v-text-field__details {
      display: none;
    }
  }
</style>
