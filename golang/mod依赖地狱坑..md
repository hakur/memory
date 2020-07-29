../../pkg/mod/github.com/coreos/etcd@v3.3.18+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:114:78: undefined: resolver.BuildOption
../../pkg/mod/github.com/coreos/etcd@v3.3.18+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:182:31: undefined: resolver.ResolveNowOption

解决办法：强行切换 replace ( google.golang.org/grpc v1.27.0 => google.golang.org/grpc v1.26.0 ) 
