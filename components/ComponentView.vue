<template lang="pug">
  div.view(v-bind:id="`${doc.title.toLowerCase().replace(' ', '-')}-view`")
    v-layout(row wrap)
      v-flex(xs12 sm8 md12)
        section-def(v-bind:doc="doc")
          dt(slot="title" v-text="doc.title")
          dd(slot="desc" v-html="doc.desc")
          v-divider
          v-card-actions
            v-tooltip(bottom)
              v-btn(
                v-bind:href="'https://github.com/vuetifyjs/vuetify/tree/master/src/'+componentLink"
                target="_blank"
                icon
                slot="activator"
                v-if="componentLink"
                flat
                v-bind:color="currentColor"
              )
                v-icon widgets
              span {{ `View ${doc.directive ? 'Directive' : 'Component'}` }}
            v-tooltip(bottom)
              v-btn(
                tag="a"
                v-bind:href="'https://github.com/vuetifyjs/docs/tree/master/pages/'+doc.edit+'.vue'"
                target="_blank"
                icon
                slot="activator"
                v-if="doc.edit"
                flat
                v-bind:color="currentColor"
              )
                v-icon edit
              span Edit this page
            v-spacer
            v-btn(
              flat
              tag="a"
              href="#api"
              v-bind:color="currentColor"
              v-if="doc.props"
            ) Go to api
      ad

    slot(name="top")
    section-header Examples
    component-example(
      v-for="(example, i) in doc.examples"
      v-bind:key="i"
      v-bind:header="`#${i + 1} ${example.header}`"
      v-bind:new-in="example.new"
      v-bind:file="example.file"
      v-bind:id="`example-${i + 1}`"
    )
      div(slot="desc" v-html="example.desc" v-if="example.desc")
    slot
    template(v-if="doc.props")
      section-header.mt-5(id="api") API
      v-tabs(v-model="tabs" dark v-bind:scrollable="false").elevation-1
        v-tabs-bar(v-bind:class="[currentColor]")
          v-tabs-slider(v-bind:class="[currentColor]").lighten-4
          v-tabs-item(
            v-for="(p, i) in ['props', 'slots', 'events', 'functional']"
            v-show="doc[p]"
            v-bind:href="`#${p}`"
            v-bind:key="i"
          ) {{ p }}
        v-tabs-items
          v-tabs-content(
            v-for="(p, i) in ['props', 'slots', 'events', 'functional']"
            v-if="doc[p]"
            v-bind:id="p"
            v-bind:key="i"
          )
            component-parameters(
              v-bind:headers="headers[p]"
              v-bind:data="doc[p]"
              v-bind:type="p"
            )
</template>

<script>
  export default {
    data () {
      return {
        current: {
          props: null,
          slots: null,
          events: null,
          functional: null
        },
        headers: {
          props: [
            { text: 'Option', value: 'prop', align: 'left' },
            { text: 'Type(s)', value: 'type', align: 'left' },
            { text: 'Default', value: 'default', align: 'left' },
            { text: 'Description', value: 'desc', align: 'left' }
          ],
          slots: [
            { text: 'Name', value: 'name', align: 'left' },
            { text: 'Description', value: 'description', align: 'left' }
          ],
          events: [
            { text: 'Name', value: 'name', align: 'left' },
            { text: 'Description', value: 'description', align: 'left' }
          ],
          functional: [
            { text: 'Name', value: 'name', align: 'left' },
            { text: 'Description', value: 'description', align: 'left' }
          ]
        },
        tabs: null
      }
    },

    props: {
      doc: Object
    },

    computed: {
      componentLink () {
        if (this.doc.component) return `components/${this.doc.component}`
        if (this.doc.directive) return `directives/${this.doc.directive}`
        return false
      },
      currentColor () {
        return this.$store.state.currentColor
      }
    },

    mounted () {
      if (this.doc.props) {
        const props = Object.keys(this.doc.props)

        if (props.length) {
          this.current.props = props[0]
          this.currentComponentKey = props[0]
        }
      }

      if (this.doc.slots) {
        const slots = Object.keys(this.doc.slots)

        if (slots.length) this.current.slots = slots[0]
      }

      if (this.doc.events) {
        const events = Object.keys(this.doc.events)

        if (events.length) this.current.events = events[0]
      }

      if (this.doc.functional) {
        const functional = Object.keys(this.doc.functional)

        if (functional.length) this.current.functional = functional[0]
      }
    }
  }
</script>

<style lang="stylus">
  .view
    max-width: 1024px
</style>
