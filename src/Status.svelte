<template lang="pug">
+if li
  +else
    WAIT

</template>

<script lang="coffee">
> @~3/wait:WAIT

+ li

onMount =>
  console.log 123
  return
</script>

<style lang="stylus"></style>
