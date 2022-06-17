# 数据结构

## g/m/p之间的关系
m -> p: m.p, m.nextp, m.mcache
p -> m: p.m, p.status

m -> g: m.curg, m.lockedg, m.g0
g -> m: g.m, g.atomicstatus

p -> g: p.runnext, p.runq

# 基本命令

## vendor
build: 
* set build flags: ```go env -w GOFLAGS="-mod=vendor"```
* verify build flags: ```go env GOFLAGS```
