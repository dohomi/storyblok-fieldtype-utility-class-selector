<template>
    <div>
        <div class="uk-form-row">
            <div v-if="model.values && model.values.length">
                <div v-for="(item,key) in model.values"
                     :key="'values_'+item"
                     class="uk-badge uk-badge-notification">
                    {{item}}
                    <a @click.stop="unselectItem(key)" class="uk-icon-hover uk-icon-remove color-danger"></a>
                </div>
            </div>
            <div style="text-align: right" v-if="model.values && model.values.length">
                <a @click="unselect">
                    <small>(<i class="uk-icon color-danger uk-icon-remove"></i> remove all)</small>
                </a>
            </div>
            <div class="select select--inline" :class="{'select--open':openSelect}">
                <a class="select__btn ellipsis" @click.stop="onSelectionSearch">
                    Please select...
                </a>

                <div class="select__dropdown" :style="{'display':openSelect?'block':'none'}">
                    <div class="select__type-search">
                        <input type="search" class="uk-width-1-1 js-term-input" v-model="search" placeholder="Search">
                    </div>
                    <div style="max-height: 200px; overflow-y: auto; ">
                        <div style="padding: 5px;">

                            <div v-for="(items,key) in listItems"
                                 :key="key">
                                <template v-if="items && items.length">
                                    <h4 style="text-transform: capitalize;background-color: whitesmoke"
                                        class="padding-sm margin-0">
                                        {{key}}</h4>
                                    <div class="padding-sm">
                                        <a class="text-block"
                                           v-for="(value,keyValues) in items"
                                           :key="keyValues"
                                           @click="selectItem(value)">
                                            {{value}}
                                        </a>
                                    </div>
                                </template>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>


  const breakpoints = ['sm', 'md', 'lg', 'xl']
  const sizes = ['2', '3', '4', '5']
  const variants = ['primary', 'secondary', 'success', 'danger', 'warning', 'info', 'light', 'dark', 'white']
  const options = {
    display: ['d-block', 'd-flex', 'd-none', 'd-inline-block', 'd-inline-flex', 'd-table', 'd-table-cell'], // with breakpoints
    sizing: ['w-100', 'h-100', 'mw-100', 'mh-100'],
    spacing: ['m-1', 'mt-1', 'mb-1', 'mr-1', 'ml-1', 'mx-1', 'my-1', 'p-1', 'pt-1', 'pb-1', 'pr-1', 'pl-1', 'px-1', 'py-1'],
    addons: ['d-print-none'],
    border: ['border', 'rounded', 'rounded-sm', 'rounded-lg', 'rounded-circle', 'rounded-0'],
    color: [],
    text: ['text-left', 'text-right', 'text-center', 'text-lowercase', 'text-uppercase', 'text-capitalize', 'text-truncate', 'text-black-50', 'text-white-50', 'text-muted', 'text-hide', 'text-monospace', 'font-weight-light', 'font-weight-bold']
  }

  addResponsiveBreakpoints('display')
  addResponsiveBreakpoints('spacing')
  addSizes('spacing')
  addVariants('border', 'border')
  addVariants('color', 'bg')
  addVariants('color', 'text')
  addResponsiveBreakpointsIntoArray('text', 'text-left')
  addResponsiveBreakpointsIntoArray('text', 'text-right')
  addResponsiveBreakpointsIntoArray('text', 'text-center')


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
        options[location].push(item.replace('-1', `-${size}`))
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

  export default {
    mixins: [window.Storyblok.plugin],
    data () {
      return {
        search: '',
        openSelect: false
      }
    },
    methods: {
      unselectItem (position) {
        this.model.values.splice(position, 1)
      },
      onSelectionSearch () {
        this.search = ''
        this.openSelect = !this.openSelect
      },
      unselect () {
        this.search = ''
        this.$set(this.model, 'values', [])
      },
      selectItem (item) {
        const oldValues = this.model.values && this.model.values.slice(0)
        const newValues = Array.isArray(oldValues) ? oldValues.concat(item) : [item]
        this.$set(this.model, 'values', newValues)
      },
      initWith () {
        return {
          // needs to be equal to your storyblok plugin name
          plugin: 'bootstrap-utility-class-selector',
          title: '',
          description: ''
        }
      },
      pluginCreated () {
        // eslint-disable-next-line
//        console.log('View source and customize: https://github.com/storyblok/storyblok-fieldtype')
      }
    },
    computed: {
      listItems () {
        const res = {}
        let modelValues = this.model.values || []
        Object.keys(options).forEach(key => {
          const values = options[key]
          res[key] = values
            .filter(item => item.includes(this.search))
            .filter(item => !modelValues.includes(item))
        })
        return res
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
    .text-block {
        display: block;
    }

    .text-block:hover {
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
