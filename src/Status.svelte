<template lang="pug">
+if check
  i
    h1 距离上次检查 {last} 秒
      b 当前进程累计 {count} 次
        +if count
          | / 每次耗时 {cost} 秒
    i
      +if err.length
        +each err as [kind, hli]
          table.e
            tr
              th(colspan=2) { kind }
              th 出错次数
              th 持续分钟
            +each hli as [host, li]
              +each li as [dns_type, err, ts], p
                tr
                  +if 0 == dns_type
                    td(colspan=2) {host}
                    +else
                      +if p == 0
                        td(rowspan:li.length) { host }
                      td.s IPV{dns_type}
                  td.s {err}
                  td.s {-ts}
      +if ok.length
        +each ok as [kind, hli]
          table
            tr
              th(colspan=2) { kind }
              th
                b 下次检查
                b 单位分钟
            +each hli as [host, li]
              +each li as [dns_type, err, ts], p
                tr
                  +if 0 == dns_type
                    td(colspan=2) {host}
                    +else
                      +if p == 0
                        td(rowspan:li.length) { host }
                      td.s IPV{dns_type}
                  td.s {ts}
  +else
    WAIT
</template>

<script lang="coffee">
> @~3/wait:WAIT
  @5-/alive/S.js > Li

+ ok, err, check, last, count, begin, ipname

cost = 0

get =  =>
  [
    kind
    host
    ipname
    _ok
    _err
    check
  ] = await Li()

  # console.log {
  #   kind
  #   host
  #   ipname
  #   _ok
  #   _err
  #   check
  # }
  now = Math.round new Date/1000
  [ts, count, total_cost] = check
  begin = 1
  last = now - ts

  if count
    cost = Math.round(total_cost / count) / 1000

  kind = new Map kind
  host = new Map host
  ipname = new Map ipname

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
            if [4,6].includes(l[0][0]) then host.get(host_id) else ipname.get(host_id)
            l.map ([dns_type, err, ts])=>
              diff = ts-now
              if err == 0 and diff < 0
                diff += 60

              [
                dns_type
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
      if (last - begin) > (60 + cost)
        begin = 0
        try
         await get()
        finally
          begin = last
    last += 1
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
i
  font-style normal
  overflow auto

  &>i
    display flex
    flex-flow row wrap
    justify-content space-around
    margin 0 20px 20px 0
    overflow hidden

:global(table>tr>td, table>tr>th)
  border 1px dashed #ccc
  font-weight 400
  padding 16px
  text-align center

h1
  display flex
  flex-direction column
  font-size 30px
  margin 20px 20px 10px
  padding 0

  b
    font-size 16px
    margin-top 5px

table
  border 1px solid #ccc
  border-collapse collapse
  margin 20px 0 0 20px

  &.e
    color red
    margin-bottom 0

    th
      background #fee

  td
    font-size 14px

    &.s
      font-size 12px

  th
    background #efe
    height 38px

    &>b
      display block
      font-size 12px

@media (max-width 600px)
  table
    width 100%
</style>
