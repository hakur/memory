* On executing the DELETE statement in MySQL ，I keep getting the error about ‘foreign key constraint failing’,What do I do （简答题）
(在MySQL中执行DELETE语句时，我不断得到“外键约束失败”的错误，怎么办？)
    1.禁用外键约束SET FOREIGN_KEY_CHECKS=0;
    2.然后再删除数据
    3.启动外键约束SET FOREIGN_KEY_CHECKS=1;