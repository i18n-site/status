<template lang="pug">
+if check
  +if err.length
  +if ok.length
    +each ok as [kind, hli]
      h3 { kind }
      +each hli as [host, li]
        h4 { host }
        +each li as [ip, err, ts]
          p {ip} {err} {ts}


  +else
    WAIT

</template>

<script lang="coffee">
> @~3/wait:WAIT
  @5-/alive/S.js > Li

+ ok, err, check

onMount =>
  [
    kind
    host
    _ok
    _err
    check
  ] = await Li()

  kind = new Map kind
  host = new Map host

  kindHostLi = (li)=>
    li.map ([kind_id, host_li])=>
      [
        kind.get(kind_id)
        host_li.map ([host_id, l])=>
          [host.get(host_id), l]
      ]

  ok = kindHostLi _ok
  err = kindHostLi _err
  console.log ok
  return
</script>

<style lang="stylus"></style>
