<template>
  <input
    class="form-control"
    required="required"
    size="16"
    type="date"
    v-bind:disabled="!enabled"
    v-bind:readonly="usedFallback"
    v-model="stringValue"
  />
</template>

<script lang="ts">
import { Vue, Component, Watch, Prop } from 'vue-property-decorator';
import T from '../lang';
import * as UI from '../ui';
import '../../../third_party/js/bootstrap-datepicker.js';

@Component
export default class DatePicker extends Vue {
  T = T;

  @Prop() value!: Date;
  @Prop({ default: true }) enabled!: boolean;
  @Prop({ default: T.datePickerFormat }) format!: string;

  private usedFallback: boolean = false;
  private stringValue: string = UI.formatDateLocal(this.value);

  mounted() {
    if ((this.$el as HTMLInputElement).type === 'text') {
      // Even though we declared the input as having date type,
      // browsers that don't support it will silently change the type to text.
      // In that case, use the bootstrap datepicker.
      this.mountedFallback();
    }
  }

  private mountedFallback() {
    let self = this;
    self.usedFallback = true;
    $(self.$el)
      .datepicker({
        weekStart: 1,
        format: self.format,
      })
      .on('changeDate', ev => {
        self.$emit('input', ev.date);
      })
      .datepicker('setValue', self.value);
  }

  @Watch('stringValue')
  onStringValueChanged(newStringValue: string) {
    if (this.usedFallback) {
      // If the fallback was used, we don't need to update anything.
      return;
    }
    this.$emit('input', UI.parseDateLocal(newStringValue));
  }

  @Watch('value')
  onPropertyChanged(newValue: Date) {
    this.stringValue = UI.formatDateLocal(newValue);
    if (this.usedFallback) {
      $(this.$el).datepicker('setValue', newValue);
    }
  }
}
</script>

<style>
@import '../../../third_party/css/bootstrap-datepicker.css';
</style>
