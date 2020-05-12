<template>
  <div class="container">
    <div class="stack-area">
      <ul class="stack-list">
        <li v-for="item in stack" :key="item" class="stack-item">
          {{ item }}
        </li>
      </ul>
    </div>
    <div class="input-area">
      <div>
        <input v-model="expression" autofocus class="input-expression" type="text" @keyup.enter="eval">
      </div>
    </div>
  </div>
</template>

<script>
import Slawn from 'slawn'

const slawn = new Slawn()
slawn.eval(['hello', '!print'])

export default {
  components: {},
  data () {
    return {
      expression: '',
      stack: []
    }
  },
  methods: {
    eval () {
      slawn.eval([parseInt(this.expression) || this.expression])
      this.stack = [...slawn.stack].reverse()
      this.expression = ''
    }
  }
}
</script>

<style lang="stylus">
@import url('https://fonts.googleapis.com/css2?family=Inconsolata&display=swap')

@keyframes dropdown
  from
    opacity 0
    transform translateY(-100%)
  to
    opacity 1
    transform translateY(0)

body
  font-size: 1.6rem
  margin 0
  padding 0

html
  font-size 62.5%

.container
  display grid
  grid-template-columns 1fr max-content 1fr
  grid-template-rows 50% 50%
  grid-template-areas \
    ". stack ." \
    ". input ."
  height 100vh
  margin 0
  width 100vw

.input-area
  bottom 0
  grid-area input
  height max-content
  left 0
  margin auto
  right 0
  top 0
  width 100%

.stack-area
  bottom 0
  grid-area stack
  height max-content
  left 0
  margin auto
  overflow auto
  right 0
  top 0
  width 100%

.stack-item
  animation-duration .5s
  animation-name dropdown
  border 1px solid #dddddd
  padding 10px

.stack-list
  font-family Inconsolata, monospace
  font-size 1.8rem
  list-style none
  padding-left 0

.input-expression
  border none
  border-bottom 1px solid
  font-family Inconsolata, monospace
  font-size 1.8rem
  outline none
</style>
