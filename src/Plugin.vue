<template>
    <div>
        <div class="uk-form-row" :style="{'min-height': openSelect ? '400px' : 'auto'}">
            <multiselect multiple
                         :taggable="true"
                         v-model="model.values"
                         class="uk-width-1-1"
                         :options="optionValues"
                         placeholder="Select class names"
                         :hide-selected="true"
                         :clear-on-select="false"
                         @open="onMultiOpen"
                         @close="onMultiClose">
            </multiselect>
        </div>
        <div class="uk-form-row">


            <template v-if="false">
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
                            <input type="search" class="uk-width-1-1 js-term-input" v-model="search"
                                   placeholder="Search">
                        </div>
                        <div style="max-height: 200px; overflow-y: auto; ">
                            <div style="padding: 5px;"
                                 ref="wrap_list">

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
            </template>
        </div>
    </div>
</template>

<script>
  import Multiselect from 'vue-multiselect'
  import 'vue-multiselect/dist/vue-multiselect.min.css'

  const breakpoints = ['sm', 'md', 'lg', 'xl']
  const sizes = ['1', '2', '3', '4', '5']
  const variants = ['primary', 'secondary', 'success', 'danger', 'warning', 'info', 'light', 'dark', 'white', 'transparent']
  const options = {
    display: ['d-block', 'd-flex', 'd-none', 'd-inline-block', 'd-inline-flex', 'd-table', 'd-table-cell'], // with breakpoints
    sizing: ['w-100', 'h-100', 'mw-100', 'mh-100'],
    spacing: ['m-0', 'mt-0', 'mb-0', 'mr-0', 'ml-0', 'mx-0', 'my-0', 'p-0', 'pt-0', 'pb-0', 'pr-0', 'pl-0', 'px-0', 'py-0'],
    addons: ['d-print-none'],
    border: ['border', 'rounded', 'rounded-sm', 'rounded-lg', 'rounded-circle', 'rounded-0'],
    color: [],
    text: ['text-left', 'text-right', 'text-center', 'text-lowercase', 'text-uppercase', 'text-capitalize', 'text-truncate', 'text-black-50',
      'text-white-50', 'text-muted',
      'text-hide', 'text-monospace',
      'font-weight-light', 'font-weight-bold', 'font-weight-normal'],
    badge: ['badge','badge-pill']
  }

  addResponsiveBreakpoints('display')
  addResponsiveBreakpoints('spacing')
  addSizes('spacing')
  addVariants('border', 'border')
  addVariants('color', 'bg')
  addVariants('color', 'text')
  addVariants('badge','badge')
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

  export default {
    components: {
      Multiselect
    },
    mixins: [window.Storyblok.plugin],
    data () {
      return {
        search: '',
        selected: 0,
        openSelect: false
      }
    },
    // mounted () {
    //   document.addEventListener('keyup', this.keyboardNav)
    // },
    // destroyed () {
    //
    //   document.removeEventListener('keyup', this.keyboardNav)
    // },
    methods: {
      // keyboardNav (e) {
      //   if (this.openSelect) {
      //     const allItems = document.getElementsByClassName('text-block')
      //     console.log(allItems)
      //     if (e.key === 'ArrowUp') {
      //       // key up
      //       this.selected > 0 && this.selected--
      //     } else if (e.key === 'ArrowDown') {
      //         console.log(this.selected)
      //       if (this.selected === 0) {
      //         allItems[this.selected].classList.add('active')
      //         this.selected++
      //       } else if (this.selected < allItems.length - 1) {
      //         allItems[this.selected].classList.remove('active')
      //         allItems[this.selected + 1].classList.add('active')
      //         this.selected++
      //       }
      //       this.selected++
      //     } else if (e.key === 'Enter') {
      //       console.log('enter')
      //     }
      //     console.log(e)
      //   }
      // },
      onMultiOpen () {
        this.openSelect = true
      },
      onMultiClose () {
        this.openSelect = false
      },
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
        this.search = ''
        const oldValues = this.model.values && this.model.values.slice(0)
        const newValues = Array.isArray(oldValues) ? oldValues.concat(item) : [item]
        this.$set(this.model, 'values', newValues)
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
    computed: {
      optionValues () {
        let res = []
        Object.keys(options).forEach(key => {
          // res.push({
          //   selector: key,
          //   values: options[key]
          // })
          res = res.concat(options[key])
          // const values = options[key]

          // res[key] = values
        })
        return res
      },
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
