<script>
export default {
  props: ["value", "renderTemp","item"],
  data() {
    return {};
  },
  created() {
    // this.$options.components[this.vueName] = require('@/components/' + this.vueName + '.vue')
  },
  render: function (createElement) {
    //   console.log(this.value);
    //   console.log(this.renderTemp);
    return this.renderTemp == null
      ? createElement("span", {}, this.value)
      : this.renderTemp(createElement,this.item);
  },
};
</script>
