<template>
  <header>

    <h1>Task Tracker</h1>
    <Button
        @btn-click="$emit('show-form')"
        :text="isFormShow ? 'Close form':'Open form'"
        :color="isFormShow ? 'red':'green'" />
  </header>

</template>

<script>


import Button from './Button';

export default {
  name: 'Header',
  components: {
    Button,
  },
  props: {
    isFormShow: Boolean
  }
}
</script>
<style scoped>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}
</style>
