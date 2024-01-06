<template lang="pug">
+if check
  main
    +if err.length
      table.e
        +each err as [kind, hli]
          tr
            th(colspan=2) { kind }
            th 出错次数
            th 持续分钟
          +each hli as [host, li]
            +each li as [ip, err, ts], p
              tr
                +if p == 0
                  td(rowspan:li.length) { host }
                td IrV{ip}
                td {err}
                td {-ts}
    +if ok.length
      table
        +each ok as [kind, hli]
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
    li.map ([kind_id, host_li])=>
      [
        kind.get(kind_id)
        host_li.map ([host_id, l])=>
          [
            host.get(host_id)
            l.map ([ip, err, ts])=>
              [
                ip
                err
                Math.round((ts-now) / 6)/10
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
  margin auto

  &.e
    color red
    margin-bottom 0

  th
    &>b
      display block
      font-size 12px

:global(table>tr>td, table>tr>th)
  border 1px solid #999
  font-weight 400
  padding 16px
  text-align center
</style>
