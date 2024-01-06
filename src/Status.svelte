<template lang="pug">
+if check
  main
    h1 距离上次检查 {last} 秒
      b 当前进程累计 {count} 次
        +if count
          | / 每次耗时 {cost} 秒
    - var n = 0;
    while ++n < 10
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

+ ok, err, check, last, count, cost, begin

get =  =>
  [
    kind
    host
    _ok
    _err
    check
  ] = await Li()

  now = Math.round new Date/1000
  [begin, count, total_cost] = check
  last = now - begin
  if count
    cost = Math.round(total_cost / count) / 1000

  kind = new Map kind
  host = new Map host

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

timer = setInterval(
  =>
    if begin
      last += 1
      if last - begin > 60
        begin = 0
        try
         await get()
        finally
          begin = last
    return
  1000
)
onMount =>
  get()
  =>
    clearInterval timer
    return

</script>

<style lang="stylus">
main
  display flex
  flex-direction row
  flex-wrap wrap
  overflow auto

:global(table>tr>td, table>tr>th)
  border 1px dashed #ccc
  font-weight 400
  padding 16px
  text-align center

h1
  display flex
  flex-direction column
  font-size 30px
  margin 20px 20px 0
  padding 0

  b
    font-size 16px
    margin-top 14px

table
  border 1px solid #ccc
  border-collapse collapse
  margin 20px

  &.e
    color red
    margin-bottom 0

    th
      background #fee

  th
    background #efe

    &>b
      display block
      font-size 12px
</style>
