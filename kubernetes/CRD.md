[文章引用1](https://blog.csdn.net/github_35614077/article/details/98749285)
[文章引用2](https://segmentfault.com/a/1190000023097945)
[文章引用3](https://book-v1.book.kubebuilder.io/quick_start.html)
[文章引用4](https://tangxusc.github.io/2019/05/code-generator%E4%BD%BF%E7%94%A8/)
[文章引用5](https://www.cnblogs.com/alisystemsoftware/p/11580202.html)
> ## 依赖工具
* linux 或者 wsl
* make
* kustomize v3.8.7 [下载地址](https://github.com/kubernetes-sigs/kustomize)
* kubebuilder v2.3.1 [下载地址](https://github.com/kubernetes-sigs/kubebuilder)
* kubectl 确保配置好了证书 make install 安装crd的时候会用到
* controller-tools 下的子命令controller-gen v0.4.1 [下载地址](https://github.com/kubernetes-sigs/controller-tools)

> ## 创建多组模式
* ### 初始化kubebuilder
```sh
#!/bin/bash
# 以下代码产生 crd  infra.hakurei.cn/Redis:v1
go mod init github.com/hakur/operator
kubebuilder init hakurei.cn
kubebuilder edit --multigroup=true
kubebuilder create api --group infra --version v1 --kind Redis
# 在出现的框里面自己决定是否创建controller，我是Resource和controller都选择Yes的
make manifests
# 如果你修改了redis_types.go的结构，执行make && make manifests 来更新yaml
mkdir hack
cd hack 
# 创建代码生成脚本
cat <<EOF >update-codegen.sh
#!/usr/bin/env bash
set -o errexit
set -o nounset
set -o pipefail
rm -rf ../clientset
rm -rf ../listers
rm -rf ../informers
../../code-generator/generate-groups.sh "all" "github.com/hakur/operator" "github.com/hakur/operator/apis" "infra:v1" --output-base "" --go-header-file "./boilerplate.go.txt"

mv github.com/hakur/operator/clientset ..
mv github.com/hakur/operator/informers ..
mv github.com/hakur/operator/listers ..

rm -rf github.com
EOF
```
* ### 追加注释 "// +genclient" 到apis/infra/v1/redis_types.go,如
```go
// +genclient
// +kubebuilder:object:root=true
// +kubebuilder:subresource:status

// Redis is the Schema for the redis API
type Redis struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata,omitempty"`

	Spec   RedisSpec   `json:"spec,omitempty"`
	Status RedisStatus `json:"status,omitempty"`
}
```

* ### 新建文件/apis/infra/v1/register.go
```go
package v1

import (
	"k8s.io/apimachinery/pkg/runtime/schema"
)

// SchemeGroupVersion is group version used to register these objects.
var SchemeGroupVersion = GroupVersion

func Resource(resource string) schema.GroupResource {
	return SchemeGroupVersion.WithResource(resource).GroupResource()
}
```

* ### 新建文件/apis/infra/v1/doc.go
```go
// +groupName=infra.hakurei.cn

package v1

```


* ### 执行code-generator
    下载kubernetes code-generator,然后放项目的同级目录，因为上面的update-codegen.sh的脚本引用路径写死了../..路径

    最后执行 update-codegen.sh 即可产生crd的listers,informers,clientset代码

> ## 创建单组
* ### 创建一个名为redis.hakurei.cn 的crd
```sh
go mod init github.com/hakur/single
kubebuilder init --domain hakurei.cn --license apache2 --owner "hakurei.cn"
kubebuilder create api --version v1alpha1 --kind Redis  --group infra # Resource和Controller都选yes

make manifests
# 如果你修改了redis_types.go的结构，执行make && make manifests 来更新yaml
mkdir hack
cd hack 
# 创建代码生成脚本
cat <<EOF >update-codegen.sh
#!/usr/bin/env bash
set -o errexit
set -o nounset
set -o pipefail
rm -rf ../clientset
rm -rf ../listers
rm -rf ../informers
../../code-generator/generate-groups.sh "all" "github.com/hakur/operator" "github.com/hakur/operator/api" "infra:v1" --output-base "" --go-header-file "./boilerplate.go.txt"

mv github.com/hakur/operator/clientset ..
mv github.com/hakur/operator/informers ..
mv github.com/hakur/operator/listers ..

rm -rf github.com
EOF
```
* ### 追加注释 "// +genclient" 到apis/infra/v1/redis_types.go,如
```go
// +genclient
// +kubebuilder:object:root=true
// +kubebuilder:subresource:status

// Redis is the Schema for the redis API
type Redis struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata,omitempty"`

	Spec   RedisSpec   `json:"spec,omitempty"`
	Status RedisStatus `json:"status,omitempty"`
}
```

* ### 新建文件/api/v1alpha1/register.go
```go
package v1alpha1

import (
	"k8s.io/apimachinery/pkg/runtime/schema"
)

// SchemeGroupVersion is group version used to register these objects.
var SchemeGroupVersion = GroupVersion

func Resource(resource string) schema.GroupResource {
	return SchemeGroupVersion.WithResource(resource).GroupResource()
}
```

* ### 新建文件/api/v1alpha1/doc.go
```go
// +groupName=infra.hakurei.cn

package v1alpha1

```


* ### 执行code-generator
    下载kubernetes code-generator,然后放项目的同级目录，因为上面的update-codegen.sh的脚本引用路径写死了../..路径

    最后执行 update-codegen.sh 即可产生crd的listers,informers,clientset代码