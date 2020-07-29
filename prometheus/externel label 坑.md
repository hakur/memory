1.远程读取会使用external_labels作为label匹配条件
prometheus2.14版本鉴于 /storage/remote/read.go#70 插入fmt.Println(query.String())
打印一下查询条件就能看到