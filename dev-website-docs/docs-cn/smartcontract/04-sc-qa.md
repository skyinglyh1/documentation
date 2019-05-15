

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

        举例来说，某合约的Hash是：996a2e5905752b88e6fdb337dbb9852889efe2fa，则合约帐户的大端地址为其Hash，其小端地址为：fae2ef892885b9db37b3fde6882b7505592e6a99，其Base58地址(通常以大写的```A```开头)为AeeSSDwLehqHqmcf8eHbciXAsGzVk93X4w。
    
    3.2 转换工具有哪些？
        
        [SmartX](https://smartx.ont.io/#/)上面创建你的项目之后，在编译、布署一栏有一个工具选项。可以进行转换。
        同时，这里也有一个非常好用的[网址](https://peterlinx.github.io/DataTransformationTools/)可以进行格式转换！！！
    
    3.3 即然合约帐户的私钥不可知，那该如何把资产转出合约呢？
        
        请参照下面关于```CheckWitness```的功能。
    
    3.4 怎样在合约内部拿到合约帐户的地址？
        
        编译器里提供了库函数，即[```ontology.interop.System.ExecutionEngine```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/ExecutionEngine.py)中的```GetExecutingScriptHash()```方法，返回值却为合约Hash的反序，或合约帐户的小端地。在合约内部可直接用来作为地址来使用，构造转账交易。

4. 关于交易回滚？
    
    交易回滚一般是在合约执行时，当不满足执行条件时，被调用的函数自动抛出错误或异常，在此次调用中所有被改变的状态回滚到被调用之前的状态，即链上状态不会改变。在[编译器1.0](https://github.com/ontio/neo-boa)中，使用```raise Exception("error msg")```。
    在[编译器2.0](https://github.com/ontio/ontology-python-compiler)中，可使用```raise Exception("error msg")``` 或```assert(condition)```。推荐使用编译器2.0的```assert()```方法。其特点为如果被调用的函数抛错，不会产生手续费。

5. Ontology智能合约存在不存在像Ethereum合约那样的修饰符```payable```类似的回调机制？
    
    不存在。必须通过帐户主动调用合约函数来触发合约函数的执行。

6. 怎样能像Ethereum合约那样在合约内部拿到```msg.sender```及```msg.value```这种值？
    
    调用帐户在主动调用合约函数时，需要把自已的帐户地址及转账资产的大小以参数的形式传入合约。在合约内部使用```CheckWitness```对调用帐户进行验签，及进行资产额度的合法性判断。

7.  支持的数据类型有哪些以及用法？

    数据类型有```ByteArray```, ```Integer(uint255)```, ```String```, ```List```, ```Map``` 。

    ```ByteArray```常用于地址参数。

    ```Integer```常用于值，如帐户余额、 索引值、状态值等等。

    ```String```常用于推送消息至全网的```Notify```中的关键字、```Map```中的关键字。

    ```List```常用于存放一列的数据，其元素的类型可以相同， 也可以不同，存取时常使用[```ontology.interop.System.Runtime```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/Runtime.py)中的```Serialize()```与```Deserialize()```。

    ```Map```常用于存入一个带有键值的数据表，存取时常使用[```ontology.interop.System.Runtime```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/Runtime.py)中的```Serialize()```与```Deserialize()```。根据不存在的键值取```Map```中不存在的值是不合法的，会直接抛错。

8. Ontology合约中有私有函数或公有函数的区分吗？

    我们可以将```Main()```中包括的函数看作公有函数，对于一本合约，我们只能调用```Main()```中包含的那些函数，建议函数名以小写字母开头。
    对于未被包含在```Main()```中的函数，可以视为私有函数，建议以下划线```_```开头，这些函数，只能被本合约中的其他函数调用，我们不可能在合约下部直接调用这些未包含在```Main()```中的函数。
    换句话说，```Main()```是调用合约函数的入口。


9.  什么是合约迁移？
    
    Ontology合约是支持合约迁移的功能的，方法是使用[```ontology.interop.Ontology.Contract```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/Ontology/Contract.py)中的```Migrate()```方法。
    其结果表现为旧合约失效，新合约生效，但旧合约的数据会全部被迁移到新合约中。
    注意调用该函数的手续费会受到新合约规模大小以及旧合约里的状态数据量有关。

10. 合约内部，转账ONT, ONG,与OEP4时，数量应该怎么填写？
    
    ONT没有精度，即最小单位为1，如果要转2个ONT，使用```Invoke()```，调用时应该写：
    ```
    ONTAddress = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01')
    param = state(fromAcct, toAcct, 2)
    res = Invoke(0, ONTAddress, "transfer", [param])
    # make sure transfer ONT succeed
    assert(res)
    ```

    ONG精度为9，即最小单位为1*10^(-9)，如果要转3.12个ONG，使用```Invoke()```，调用时应该写：
    ```
    ONGAddress = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02')
    param = state(fromAcct, toAcct, 3120000000)
    res = Invoke(0, ONGAddress, "transfer", [param])
    # make sure transfer ONG succeed
    assert(res)
    ```

    假设某OEP4代币MST精度为12，如果要转1.234567891234，可使用```DynamicAppCall```或是```RegisterAppCall```方法。以前者为例：
    ```
    # Suppose the contract hash of OEP4 is b55f2af8d0d8d2bd3d398ced3b3559caa894b536
    OEP4ReversedContractHash = Base58ToAddress(ALm9fJfhgNrwt4TM6bmmqvQbZs3hugi3et)
    param = [fromAcct, toAcct, 1234567891234]
    res = DynamicAppCall(OEP4ReversedContractHash, "transfer", param)
    # make sure transfer MST succeed
    assert(res)
    ```

11. 合约内数据可见性，如何做到对某些人不可见且可信?
    
    11.1 将原始数据```originData```加盐值```salt```进行哈希，保存好原始数据及盐值及哈希结果```hash```。
    
    11.2 将哈希结果```hash```，保存到链上。
    
    11.3 原始数据已产生且不可见阶段。
    
    11.4 等需要公开数据时，提供原始数据```originData```及盐值```salt```，可对```hash```进行验证。

12. 如何对合约帐户内的ONT产生的ONG解绑？
    
    详见[unboundWithdrawONG_compiler2.0.py](https://github.com/ONT-Avocados/python-template/blob/master/UnboundWithdrawONG/unboundWithdrawONG_compiler2.0.py)。
    
13. 合约内如何验证帐户签名？
    
    使用[```ontology.interop.System.Runtime```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/Runtime.py)```CheckWitness```方法。
    ```CheckWitness(fromAcct)```有两个功能：

    1. 验证当前的函数调用者是不是```fromAcct```,若是（验证签名），则验证通过。

    2. 检查当前函数调用者是不是一个合约A，若是合约A，且是从合约A发起的去执行函数，则验证通过(验证```fromAcct```是不是[```GetCallingScriptHash()```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/ExecutionEngine.py)的返回值)。 


14. 执行与预执行的区别是什么？

    执行与预执行的对象是合约函数。
    
    执行是指会改变合约中的状态，根据逻辑运算的复杂度、改变合约中状态的次数以及被改变状态所占存储的大小收取燃料费。

    预执行是指查询链上合约中的状态，不会收取燃料费。

15. 关于参数为args的函数调用时的参数构造。
    
16. 交易落账时间
    
    本地```TestMode```即```http://127.0.0.1```模式下，一般要6秒落帐，不经过共识，出块时间为6秒。

    测试网```polaris```即```http://polaris1.ont.io```模式下，正常落帐时间为1秒。有交易时的出块速度为1秒，无交易时的出块速度为30秒。

    主网```dappnode```即```http://dappnode1.ont.io```模式下，正常落帐时间为1秒。有交易时的出块速度为1秒，无交易时的出块速度为6秒。

17. 本地节点CLI测试时，我看到了帐户下面的ONT，但怎样产生ONG阿？
    
    查看钱包里第一个帐户的ONT, ONG余额：
    ```
    ontology.exe asset balance 1
    ```
    
    产生ONG的方式，要进行ONT的转账，然后解绑，再提取ONG。
    ```
    ontology.exe asset transfer --from=1 --to=1 --amount=100000000 --asset=ont

    ontology.exe asset unboundong 1

    ontology.exe asset withdrawong 1

    ```


18. 关于地址，Base58地址，ByteArray地址，大端地址，小端地址，还有反序，好烦人，怎么办？
    
    
19. 合约=逻辑+状态,如何写逻辑，如何存取改状态
    


20. 开发合约=>编译合约=>部署合约=>调用合约=>测试合约的流程 工具
    
21. OEP4, OEP5, OEP8协议的区别？
    
    如果你对```ERC20```，```ERC721```以及```ERC1155```熟悉，那么```OEP4```对应于```ERC20```，```OEP5```对应于```ERC721```，```OEP8```对应于```ERC1155```。

    如果对Ethereum中的这三种协议不熟悉的话， 简单来说，

    OEP4是同质化资产的协议，资产之间互不区分。

    OEP5是非同质化资产的协议，每个资产是唯一的，不同资产是互相区分的。

    OEP8相当于OEP4与OEP5的结合体，即包含不同种类的资产，每种资产的数量是多个的，每种资产内部是同质的，不同资产是不同质的。

    [此处](https://github.com/ontio/OEPs/tree/master/OEPS)有更详细的协议介绍，[此处](https://github.com/ONT-Avocados/python-template)有协议实现的合约模版。

22. 合约内序列化的用法，为啥要序列化

23. for i in range不支持，但是支持 for i in LIST
24. debug合约所需的测试节点怎么产生
25. 测试合约时，对执行的notify或是预执行的数据解析
26. oracle合约的使用
27. 如何像eth上面那样做权限管理
28. 合约内没有定时器，需要外部触发
29. 在线转换数据工具两个，sdk也可转
30. 当key不存在时，vaule对应 什么