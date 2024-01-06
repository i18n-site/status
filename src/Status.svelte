<template lang="pug">
+if check
  main
    +if err.length
      +each err as [kind, hli]
        table.e
          tr
            th(colspan=2) { kind }
            th 出错次数
            th 持续分钟
          +each hli as [host, li]
            +each li as [ip, err, ts], p
              tr
                +if p == 0
                  td(rowspan:li.length) { host }
                td IPV{ip}
                td {err}
                td {-ts}
    +if ok.length
      +each ok as [kind, hli]
        table
          tr
            th(colspan=2) { kind }
            th
              b 下次检查
              b 单位分钟
          +each hli as [host, li]
            +each li as [ip, err, ts], p
              tr
                +if p == 0
                  td(rowspan:li.length) { host }
                td IPV{ip}
                td {ts}
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
  now = new Date/1000

  kindHostLi = (li)=>
    li.sort(
      (a,b)=>
        a[0]-b[0]
    )
    li.map ([kind_id, host_li])=>
      [
        kind.get(kind_id)
        host_li.map ([host_id, l])=>
          [
            host.get(host_id)
            l.map ([ip, err, ts])=>
              diff = ts-now
              if err == 0 and diff < 0
                diff += 60
              [
                ip
                err
                Math.round(diff / 6)/10
              ]
          ]
      ]

  ok = kindHostLi _ok
  err = kindHostLi _err

  return
</script>

<style lang="stylus">
table
  border-collapse collapse
  margin 32px 0 0 32px

  &.e
    color red
    margin-bottom 0

  th
    &>b
      display block
      font-size 12px

main
  display flex
  flex-flow column wrap

:global(table>tr>td, table>tr>th)
  border 1px solid #ccc
  font-weight 400
  padding 16px
  text-align center
</style>
