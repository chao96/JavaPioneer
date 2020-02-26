<details> 
	<summary>展开查看</summary> 
    ```mysql
CREATE TABLE if not exists item_kill_success (
  `code` varchar(50) NOT NULL COMMENT '秒杀成功生成的订单编号',
  `item_id` int(11) DEFAULT NULL COMMENT '商品id',
  `kill_id` int(11) DEFAULT NULL COMMENT '秒杀id',
  `user_id` varchar(20) DEFAULT NULL COMMENT '用户id',
  `status` tinyint(4) DEFAULT '-1' COMMENT '秒杀结果: -1无效  0成功(未付款)  1已付款  2已取消',
  `create_time` timestamp NULL DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`code`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='秒杀成功订单表';
```
</details>
