<template>
  <div>
    <button v-if="!editMode" style="float: right" @click="edit_mode_click_handler">Edit</button>
    <form @submit="form_submit_handler">
      <div v-for="({id,align,items,label,value,options,editable,name,multiple}) in details" :key="id">
        <!--      Row Align-->
        <template v-if="align ==='split'">
          <div style="display: flex;gap: 16px">
            <div v-for="{inputType,value,label,name:childName} in items" :key="value">
              <strong>{{ label }}</strong>
              <div>
                <template v-if="editMode">
                  <input
                      :value="value"
                      :type="inputType"

                      @change="input_change_handler($event,[name,childName],undefined,align)"
                  />
                </template>
                <template v-else>
                  {{ value }}
                </template>
              </div>
            </div>
          </div>
        </template>

        <!--      Column Align-->
        <template v-else>
          <strong>{{ label }}</strong>
          <div>
            <!--          Edit Mode-->
            <template v-if="editMode && editable">

              <!--            Input Type: Select-->
              <template v-if="options">
                <select
                    :multiple="multiple"

                    @change="input_change_handler($event,[name],'select')"
                >
                  <option
                      v-for="option in options"

                      :selected="is_selected(value, option)"
                      :value="option"
                      :key="option"
                  >
                    {{ option }}
                  </option>
                </select>
              </template>

              <!--            Input Type: Multiple Text Input-->
              <template v-else-if="items">
                <input
                    type="text"
                    style="display: block"

                    v-for="{value} in items"

                    :key="value"
                    :value="value"

                    @change="input_change_handler($event,[value])"
                />
              </template>

              <!--            Input Type: Single Text Input-->
              <template v-else>
                <input
                    type="text"

                    :value="value"

                    @change="input_change_handler($event,[name],undefined,undefined)"
                />
              </template>
            </template>

            <!--          View Mode-->
            <template v-else>
              <template v-if="items">
                <ul>
                  <li v-for="item in items" :key="item">
                    {{ item.value }}
                  </li>
                </ul>
              </template>
              <template v-else-if="Array.isArray(value)">
                <ul>
                  <li v-for="item in value" :key="item">
                    {{ item }}
                  </li>
                </ul>
              </template>
              <template v-else>
                {{ value }}
              </template>
            </template>
          </div>
        </template>
      </div>

      <!--    Edit Mode Buttons-->
      <template v-if="editMode">
        <hr>
        <div style="display: flex; justify-content: center; gap: 16px">
          <input type="submit" value="save"/>
          <button @click="cancel_click_handler">Cancel</button>
        </div>
      </template>
    </form>
  </div>
</template>

<script>
export default {
  name: "al-details",
  props: {
    details: Array
  },
  data: function () {
    return {
      editMode: true,
      inputs: []
    }
  },
  methods: {
    edit_mode_click_handler: function () {
      this.editMode = true
    },

    form_submit_handler: function ($e) {
      $e.preventDefault();
      this.editMode = false;
      this.$emit('submit-clicked', [...this.inputs]);
      this.inputs = []
    },

    cancel_click_handler: function () {
      this.editMode = false
    },

    input_change_handler: function ({target}, [name, childName], type, align) {
      const foundInput = this.inputs.find(input => input.name === name)
      if (foundInput) {
        if (type === 'select') {
          foundInput.value = [...target.selectedOptions].map(option => option.value)
        } else {
          if (childName) {
            foundInput.items.find(input => input.name === childName).value = target.value
          } else {
            foundInput.value = target.value;
          }
        }
      } else {
        if (align === 'split') {
          const {items, ...rest} = {...this.details.find(input => input.name === name)};
          const parent = {...rest};
          const index = items.findIndex(item => item.name === childName)

          parent.items = [...items];
          parent.items.splice(index, 1, {
            ...(items.find(item => item.name === childName)),
            value: target.value
          })
          this.inputs.push({...parent})
        } else {
          this.inputs.push(
              {
                ...(this.details.find(input => input.name === name)),
                value: target.value
              }
          )
        }
      }
    },

    // helpers
    is_selected: function (value, option) {
      return value.indexOf(option) !== -1
    }
  }
}
</script>

<style scoped>

</style>