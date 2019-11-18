<template>
  <div class="base-comp">
    <div class="center">
      <slot></slot>
    </div>
  </div>
</template>

<script>
export default {

}
</script>

<style>
.base-comp {
  position: relative;
  width: 600px;
  height: 520px;
  padding: 0;

  overflow: scroll;
}

.center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>