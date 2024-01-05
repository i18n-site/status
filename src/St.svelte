<script lang="coffee">
+each i as [kind, hli]
  h3 { kind }
  +each hli as [host, li]
    h4 { host }
    +each li as [ip, err, ts]
      p {ip} {err} {ts}
</script>

<template lang="pug">
< i
</template>

<style lang="stylus"></style>
