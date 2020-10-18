## Seata 
### Seata 是什么？

> Seata is an easy-to-use, high-performance, open source distributed transaction solution.
>
> Seata 是一个简单易用的，高性能，开源的分布式事务解决方案。
>

### AT模式
`AT` 模式是一种无侵入的分布式事务解决方案。在 `AT` 模式下，用户只需关注自己的“业务 `SQL`”，
用户的 “业务 `SQL`” 就是全局事务一阶段，`Seata` 框架会自动生成事务的二阶段提交和回滚操作。

`AT` 模式如何做到对业务的无侵入 ：

- 一阶段：
  在一阶段，`Seata` 会拦截“业务 `SQL`”，首先解析 `SQL` 语义，找到“业务 `SQL`”要更新的业务数据，
  在业务数据被更新前，将其保存成`before image`，然后执行“业务 `SQL`”更新业务数据，在业务数据更新之后，
  再将其保存成`after image`，最后生成行锁。以上操作全部在一个数据库事务内完成，这样保证了一阶段操作的原子性。

- 二阶段提交：
  二阶段如果是提交的话，因为“业务 `SQL`”在一阶段已经提交至数据库， 所以 `Seata` 框架只需将一阶段保存的快照数据和行锁删掉，完成数据清理即可。
  
- 二阶段回滚：
  二阶段如果是回滚的话，`Seata` 就需要回滚一阶段已经执行的“业务 `SQL`”，还原业务数据。
  回滚方式便是用`before image`还原业务数据；但在还原前要首先要校验脏写，对比“数据库当前业务数据”和 `after image`，
  如果两份数据完全一致就说明没有脏写，可以还原业务数据，如果不一致就说明有脏写，出现脏写就需要转人工处理。


`AT` 模式的一阶段、二阶段提交和回滚均由 `Seata` 框架自动生成，用户只需编写“业务 `SQL`”，便能轻松接入分布式事务，`AT` 模式是一种对业务无任何侵入的分布式事务解决方案。
(以上选自知乎[分布式事务的4种模式](https://zhuanlan.zhihu.com/p/78599954))

### `nacos-dubbo`
`nacos-dubbo`是一个简单的`Seata AT`模式的入门项目，使用`dubbo`去实现服务与服务之间的调用。


### `nacos-http`(TBD)
`nacos-http`是一个使用`Spring Cloud Alibaba` 实现`Seata AT`模式的入门项目。