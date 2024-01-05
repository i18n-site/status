<script lang="coffee">
< i
</script>

<template lang="pug">
+each i as [kind, hli]
  h3 { kind }
  +each hli as [host, li]
    h4 { host }
    +each li as [ip, err, ts]
      p {ip} {err} {ts}
</template>

<style lang="stylus"></style>
