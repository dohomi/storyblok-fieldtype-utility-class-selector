<template>
    <div>
        <div class="uk-form-row" :style="{'min-height': openSelect ? '400px' : 'auto'}">
            <multiselect multiple
                         :taggable="true"
                         v-model="model.values"
                         @tag="addTag"
                         class="uk-width-1-1"
                         :options="optionValues"
                         placeholder="Select class names"
                         :hide-selected="true"
                         :clear-on-select="false"
                         @open="onMultiOpen"
                         @close="onMultiClose">
            </multiselect>
        </div>
    </div>
</template>

<script>
  import Multiselect from 'vue-multiselect'
  import 'vue-multiselect/dist/vue-multiselect.min.css'
  import utilityClassNames from '@lumen/mwc/utilityClassNames'

  const breakpoints = ['sm', 'md', 'lg', 'xl']
  const sizes = ['1', '2', '3', '4', '5']
  const variants = ['primary', 'secondary', 'success', 'danger', 'warning', 'info', 'light', 'dark', 'white', 'transparent']
  const options = {
    display: ['d-block', 'd-flex', 'd-none', 'd-inline-block', 'd-inline-flex', 'd-table', 'd-table-cell', 'flex-column', 'flex-row', 'flex-column-reverse', 'flex-nowrap', 'flex-wrap',
      'flex-wrap-reverse', 'flex-grow-1', 'flex-grow-0', 'flex-shrink-1', 'flex-shrink-0'], // with breakpoints
    sizing: ['w-100', 'h-100', 'mw-100', 'mh-100'],
    spacing: ['m-0', 'mt-0', 'mb-0', 'mr-0', 'ml-0', 'mx-0', 'my-0', 'p-0', 'pt-0', 'pb-0', 'pr-0', 'pl-0', 'px-0', 'py-0'],
    addons: ['d-print-none'],
    border: ['border', 'rounded', 'rounded-sm', 'rounded-lg', 'rounded-circle', 'rounded-0'],
    color: [],
    text: ['text-left', 'text-right', 'text-center', 'text-lowercase', 'text-uppercase', 'text-capitalize', 'text-truncate', 'text-black-50',
      'text-white-50', 'text-muted',
      'text-hide', 'text-monospace',
      'font-weight-light', 'font-weight-bold', 'font-weight-normal'],
    badge: ['badge', 'badge-pill'],
    flex: ['justify-content-start', 'justify-content-end', 'justify-content-center', 'justify-content-between', 'justify-content-around', 'align-content-start',
      'align-content-end', 'align-content-center', 'align-content-around', 'align-content-stretch', 'align-items-start', 'align-items-end', 'align-items-center',
      'align-items-baseline', 'align-items-stretch', 'align-self-start', 'align-self-end', 'align-self-center', 'align-self-baseline', 'align-self-stretch']
  }

  addResponsiveBreakpoints('display')
  addResponsiveBreakpoints('spacing')
  addSizes('spacing')
  addVariants('border', 'border')
  addVariants('color', 'bg')
  addVariants('color', 'text')
  addVariants('badge', 'badge')
  addResponsiveBreakpointsIntoArray('text', 'text-left')
  addResponsiveBreakpointsIntoArray('text', 'text-right')
  addResponsiveBreakpointsIntoArray('text', 'text-center')
  addResponsiveBreakpointsEnd('flex')


  function addResponsiveBreakpointsIntoArray (location, key) {
    const splitted = key.split('-')
    breakpoints.forEach(breakpoint => {
      const copied = splitted.slice(0)
      copied.splice(1, 0, breakpoint)
      options[location].push(copied.join('-'))
    })
  }

  function addVariants (location, key) {
    variants.forEach(v => {
      options[location].push(`${key}-${v}`)
    })
  }

  function addSizes (location) {
    const values = options[location]
    values.forEach(item => {
      sizes.forEach(size => {
        options[location].push(item.replace('-0', `-${size}`))
      })
    })
  }

  function addResponsiveBreakpoints (location) {
    const values = options[location]
    values.forEach(item => {
      const splitted = item.split('-')
      breakpoints.forEach(breakpoint => {
        const copied = splitted.slice(0)
        copied.splice(1, 0, breakpoint)
        options[location].push(copied.join('-'))
      })
    })
  }

  function addResponsiveBreakpointsEnd (location) {
    const values = options[location]
    values.forEach(item => {
      const splitted = item.split('-')
      // const last = splitted[splitted.length - 1]
      // const newArray = splitted.splice(-1, 1)
      breakpoints.forEach(breakpoint => {
        const copied = splitted.slice(0)
        copied.splice(copied.length - 1, 0, breakpoint)
        options[location].push(copied.join('-'))
      })
    })
  }


  let allOptions = []
  Object.keys(options).forEach(key => {
    allOptions = allOptions.concat(options[key])
  })
  export default {
    components: {
      Multiselect
    },
    mixins: [window.Storyblok.plugin],
    data () {
      return {
        search: '',
        selected: 0,
        openSelect: false,
        optionValues: []
      }
    },
    mounted () {
      if (this.options && this.options.mui) {
        this.optionValues = utilityClassNames
      } else {
        this.optionValues = allOptions.slice(0)
      }
    },
    methods: {
      addTag (newTag) {
        this.optionValues.push(newTag)
        const oldValues = this.model.values || []
        oldValues.push(newTag)
        this.$set(this.model, 'values', oldValues)
      },
      onMultiOpen () {
        this.openSelect = true
      },
      onMultiClose () {
        this.openSelect = false
      },
      initWith () {
        return {
          // needs to be equal to your storyblok plugin name
          plugin: 'bootstrap-utility-class-selector'
        }
      },
      pluginCreated () {
        // eslint-disable-next-line
//        console.log('View source and customize: https://github.com/storyblok/storyblok-fieldtype')
      }
    },
    watch: {
      'model': {
        handler: function (value) {
          this.$emit('changed-model', value)
        },
        deep: true
      }
    }
  }
</script>


<style>

    .multiselect__input {
        border: none !important;
    }

    .text-block {
        display: block;
    }

    .text-block:hover {
        background-color: floralwhite;
    }

    .text-block.active {
        background-color: floralwhite;
    }

    .padding-sm {
        padding: 5px;
    }

    .margin-0 {
        margin: 0;
    }

    .color-danger {
        color: lightcoral;
    }

    .color-danger:hover {
        color: darkred;
    }
</style>
