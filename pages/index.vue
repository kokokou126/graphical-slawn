<template>
  <div class="container">
    <div class="stack-area">
      <div>
        <ul class="stack-list">
          <li v-for="(item, index) in stack" :key="index" :class="item.class" class="stack-item">
            {{ item.value }}
          </li>
        </ul>
      </div>
    </div>
    <div class="input-area">
      <div>
        <input
          id="input-expression"
          v-model="expression"
          autocomplete="off"
          autofocus
          type="text"
          @keyup.enter="eval"
          @keyup.up="previewHistory"
          @keyup.down="nextHistory"
        >
      </div>
    </div>
  </div>
</template>

<script>
import Slawn from 'slawn'
import operators from '@/node_modules/slawn/dist/operators.json'

const operatorAliases = operators.map(v => v.alias).flat()

const slawn = new Slawn()
slawn.eval(['hello', '!print'])

export default {
  components: {},
  data () {
    return {
      expression: '',
      expressionNow: '',
      history: [],
      historyReviewAt: -1,
      stack: []
    }
  },
  watch: {
    expression () {
      if (operatorAliases.includes(this.expression)) {
        const arg = operators.filter(v => v.alias.includes(this.expression))[0].arg
        Array(arg).fill(0).forEach((_, i) => {
          const item = this.stack[this.stack.length - 1 - i]
          item.class['auto-highlight'] = true
        })
      } else {
        this.stack.forEach((_, i) => {
          const item = this.stack[i]
          item.class['auto-highlight'] = false
        })
      }
    }
  },
  methods: {
    eval () {
      slawn.eval([parseInt(this.expression) || this.expression])
      this.stack = [...slawn.stack].map(v => ({ class: {}, value: v })).reverse()
      this.history.push(this.expression)
      this.historyReviewAt = -1
      this.expression = ''
      this.expressionNow = ''
    },
    previewHistory () {
      if (this.historyReviewAt === -1) {
        this.expressionNow = this.expression
      }
      if (this.historyReviewAt + 1 <= this.history.length - 1) {
        this.historyReviewAt++
        this.expression = this.history[this.history.length - 1 - this.historyReviewAt]
        this.caretToEnd()
      }
    },
    nextHistory () {
      if (this.historyReviewAt - 1 >= 0) {
        this.historyReviewAt--
        this.expression = this.history[this.history.length - 1 - this.historyReviewAt]
        this.caretToEnd()
      } else if (this.historyReviewAt === 0) {
        this.expression = this.expressionNow
        this.historyReviewAt = -1
      }
    },
    caretToEnd () {
      const inputExpression = document.getElementById('input-expression')
      inputExpression.setSelectionRange(
        ...Array(2).fill(inputExpression.textContent.length)
      )
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

@keyframes fadeIn
  from
    opacity 0
  to
    opacity 1

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
  height 50vh
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

#input-expression
  border none
  border-bottom 1px solid
  font-family Inconsolata, monospace
  font-size 1.8rem
  outline none

.auto-highlight
  animation-name fadeIn
  background-color palegreen
</style>
