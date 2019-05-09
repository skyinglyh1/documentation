

1. 什么是Native合约，Native合约有哪些方法可以调用？
    
    Native合约是ONT，ONG合约及其它主链治理合约。可调用方法可在[源码](https://github.com/ontio/ontology/tree/master/smartcontract/service/native)中获得。

    Native合约的[Hash地址](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/utils/params.go)。
    
    [ONT与ONG合约方法](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/ont/utils.go)。
    
    [ONTID合约方法](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/ontid/init.go)。
    
    [Param合约方法](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/global_params/global_params.go)。
    
    [Auth合约方法](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/auth/auth.go)。
    
    [Governance合约方法](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/governance/governance.go)。

2. 什么是跨合约调用，跨合约调用时的注意点？

    跨合约调用是指在A合约调用B合约的方法。跨合约调用分为两种：
    ```
    1、普通合约之间的相互调用；
    
    2、跨Native合约的调用。
    ```

    区别在于:
    
    第一种应该使用[```ontology.interop.System.App.py```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/App.py)中的```DynamicAppCall```或是```RegisterAppCall```方法，参数构造时可使用```state```方法或是直接使用```[]```；
    
    第二种应该使用[```ontology.interop.Ontology.Native```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/Ontology/Native.py)中的```Invoke```方法，参数构造时必须使用```ontology.builtins```中的```state```方法。

    具体可参照[python-template中的模板](https://github.com/ONT-Avocados/python-template/blob/master/DynamicCallContract/DynamicCallContract_compiler2.0.py)。
   
3. 合约Hash也是对应一个账户吗？有私钥吗？
    
    合约Hash也是对应一个帐户的。方式是把合约的Hash当作大端地址即为其自身帐户，常用于DAPP合约作为资金托管帐户。

    有，不过合约的帐户的私钥是不可知的。
    
    3.1 什么是大小端地址？什么是Base58地址？
        
        大端地址与小端地址与Base58地址是一一对应的关系。

        举例来说，某合约的Hash是：___，则合约的大端地址为其Hash，其小端地址为：___，其Base58地址(通常以大写的```A```开头)为___。
    
    3.2 转换工具有哪些？
        [SmartX](https://smartx.ont.io/#/)上面创建你的项目之后，在编译、布署一栏有一个工具选项。可以进行转换。
        同时，这里也有一个[网址](https://peterlinx.github.io/DataTransformationTools/)可以进行格式转换。
    
    3.3 即然合约帐户的私钥不可知，那该如何把资产转出合约呢？
        请参照下面关于```checkwitness```的功能。

4. 交易回滚
5. call back,
6. 如何拿到msg,sender
7.  支持的数据类型有哪些以及用法尤其是list 和map
8. 私有函数 公有函数
9.  重新部署合约
10. 合约迁移
11. 转账ong,ont, oep4，精度
12. 合约内数据可见性，如何做到不可见且可信
13. 合约内ont产生的ong解绑
14. 合约内验证签名
15. 执行与预执行的区别
16. 关于参数为args得函数调用时的参数构造
17. 交易落账时间
18. 关于地址，base58 bytearray, 大小端地址
19. 合约=逻辑+状态,如何写逻辑，如何存取改状态
20. 开发合约=>编译合约=>部署合约=>调用合约=>测试合约的流程 工具
21. OEP4, OEP5, OEP8协议的区别
22. 合约内序列化的用法，为啥要序列化
23. checkwitness是干啥的，功能，场景
24. 本地节点测试时，产生 ong的方式
25. 根据txHash查询交易详细信息，结构解析
26. for i in range不支持，但是支持 for i in LIST
27. debug合约所需的测试节点怎么产生
28. 测试合约时，对执行的notify或是预执行的数据解析
29. oracle合约的使用
30. 如何像eth上面那样做权限管理
31. 合约内没有定时器，需要外部触发
32. 在线转换数据工具两个，sdk也可转
33. 当key不存在时，vaule对应 什么