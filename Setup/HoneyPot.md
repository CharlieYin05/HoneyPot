# 部署的服务

## OCI放行
### Redis蜜罐
- Stateless:关闭
- Source Type: CIDR
- Source CIRD: 0.0.0.0/0        ← 所有人都能进来
- Source Port Range:留空
- Destination Port:6379
- Description: HFish Redis HoneyPot
