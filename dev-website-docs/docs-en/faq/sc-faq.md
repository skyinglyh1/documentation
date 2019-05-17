

#### 1. What are Native Smart Contracts, and which methods can possibly be invoked?
    
Native Smart Contracts include ONT, ONG and other governing smart contracts. The methods that potentially can be invoked can be found [here](https://github.com/ontio/ontology/tree/master/smartcontract/service/native). 

Native Smart Contract [Hash Address](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/utils/params.go).

[ONT and ONG Native Contracts Methods](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/ont/utils.go).

[ONTID Contract Methods](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/ontid/init.go).

[Param Contract Methods](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/global_params/global_params.go).

[Auth Contract Methods](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/auth/auth.go).

[Governance Contract Methods](https://github.com/ontio/ontology/blob/master/smartcontract/service/native/governance/governance.go).

#### 2. What is cross-contract invocation, and what are the notes worthy of notice?                    

Cross-contract invocation means when we invoke method A1 within contract A, the method A1 will invoke the method B1 within another contract B. The 
cross-contract invocation can be split into two types.

```
1. The normal cross-contract invocation among contracts, which is not the native contracts, like OEP4, OEP5, and other dapp smart contracts.

2. Cross-native-contract invocation, which means the method within the non-native contract will invoke native contract.
```

The difference lies in,

In the first scenario, we should use the ```DynamicAppCall``` or ```RegisterAppCall``` methods within [ontology.interop.System.App.py](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/App.py) while utilizing the ```state``` or ```[]``` directly to construct the parameters.

In the second scenario, we should use ```Invoke``` method within [ontology.interop.Ontology.Native](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/Ontology/Native.py) while using ```state``` method within ```ontology.builtins``` to contruct parameters.

For the details, please refer to the [template](https://github.com/ONT-Avocados/python-template/blob/master/DynamicCallContract/DynamicCallContract_compiler2.0.py).
   
#### 3. Is there an account corresponding to the contract script hash? If so, is there the private key?

Contract hash corresponds with one account. If we take the contract script hash as the big-endian address, the corresponding address (little-endian or base58 address) is the account address, the frequently used scenario is that we consider the contract account as a custodian acccount for the dapp players, and the contract logic code will manage all the players' digital assets.

Yes, there exists the private, but unknown to all of us.

3.1 What is the big-endian and little endian address? What is Base58 address?

TThere is a corresponding relationship amount the big-endian, little-endian, and base58 address.

For instance, after we compile a contract, it has the script hash (or contract hash) 996a2e5905752b88e6fdb337dbb9852889efe2fa. We know the contract hash equals the big-endian address of the contract account. 
Hence the little-endian address is fae2ef892885b9db37b3fde6882b7505592e6a99, and the Base58 address (usually begins with a capital ```A```) is AeeSSDwLehqHqmcf8eHbciXAsGzVk93X4w. 

3.2 Where can I find transformation tools?
    
In [SmartX](https://smartx.ont.io/#/), you can create your project, compile, deploy your smart contract. There is a choice, named ```Tool```, in the upper right section. Some convenient transformation options are provided there.

Meanwhile, there is a much more convenient [website](https://peterlinx.github.io/DataTransformationTools/), where we can transform data between different format.

3.3 How can we transfer assets out from the contract since the private key of the the smart contract account is unknown?

Please refer to the ```CheckWitness``` part to grap the further understanding of it.

3.4 How can we obtain the contract account address within the contract?
    
We have provided the library in the compiler, which is ```GetExecutingScriptHash()``` method witin [```ontology.interop.System.ExecutionEngine```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/ExecutionEngine.py).
The returned value is the reversed contract script hash, or the contract account little-endian address. We can use that to construct transaction parameters directly within the contract.

#### 4. About Transaction Roll Back？

Within the contract, when execution condition is not satisfied, the method will raise exception and make the changed states back to the states before the transaction was executed, which means the on-chain states will not be changed if the transaction fails. This is transaction roll back.

In [compiler 1.0](https://github.com/ontio/neo-boa), we use ```raise Exception("error msg")``` to make that happen.

In [compiler 2.0](https://github.com/ontio/ontology-python-compiler), we can use ```raise Exception("error msg")``` or ```assert(condition)```. 

```assert()``` in Compiler 2.0 is recommended. The feature is no transaction fee or gas if the transaction fails.

#### 5. Does there exist modifyer and call back mechanism in Ontology smart contract, like ```payerable``` in Ethereum smart contract?

No. We have to invoke the contract method actively sent by the account or another smart contract.

#### 6. How to obtain the values including invoker, and transferred amount, like ```msg.sender``` and ```msg.value``` in Ethereum smart contract?
    
When the invoker actively invokes the contract's method, the invoker should pass his address and transferred asset amount as the parameters into the contract's method. 
Then contract's method has to verify the signature of the invoker using ```CheckWitness()``` method, and check the transferred asset amount is legal.

#### 7. Which types of data are supported in Ontology smart contract and how to use them?

The supported data types include ```ByteArray```, ```Integer(uint255)```, ```String```, ```List```, ```Map``` .

**ByteArray** usually used as the address.

**Integer** usually used as value, like account balance, index value, states and so on.

**String** usually used as the key words when the transaction is broadcast to the ontology net, or the key value in the ```Map``` struct.

**List** ususally used to store a series of data, the element can be the same type or different types. Before we save or query the data, using ```Serialize()```与```Deserialize()``` within [```ontology.interop.System.Runtime```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/Runtime.py) is recommended.
This type of data supports ```append()``` operation, and soon ```remove()``` will also be supported.

**Map** usually used ot save the data table with key , Before we save or query the data, using ```Serialize()```与```Deserialize()``` within [```ontology.interop.System.Runtime```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/Runtime.py) is recommended.
Trying to get the data with nonexistent key is illegal, and will raise exception.

#### 8. Do we differentiate public and private method within Ontology smart contract?

We can consider all the methods included in the ```Main()``` function as the public method. For a smart contract, we can and ONLY invoke the methods included in the ```Main()``` function.
It's recommended that the first letter of the public method name is lowercase. The methods excluded in the ```Main()``` function can be considerred as the private methods, the name of which is recommended starting with underline ```_```. These methods can only be invoked within the contract by other methods.
There is no way we can reach and invoke these private methods outside the current contract. In another word, the ```Main()``` function is the entrance to the current contract.

#### 9.  What is contract migration?

Ontology smart contract supports contract migration operation using ```Migrate()``` method within [```ontology.interop.Ontology.Contract```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/Ontology/Contract.py).
The result shows the old contract will disappear, and the new contract will be valid. Meanwhile, all the states stored in the old contract will be migrated into the new contract. And the logic part follow the new contract.

Notice that the invocation fee or the migration operation fee will be affected by the new contract scale, state size within the old contract.

#### 10. Within the contract, how to fill the transferred asset amount when we want to transfer ONT, ONG, and OEP4 token?
    
ONT has no decimals, meaning the smallest unit is 1. Say if we want to transfer 2 ONT using ```Invoke()```, we should construct the transaction this way.
```
ONTAddress = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01')
param = state(fromAcct, toAcct, 2)
res = Invoke(0, ONTAddress, "transfer", [param])
# make sure transfer ONT succeed
assert(res)
```

The decimasls of ONG is 9, meaning the smallest unit is 1 * 10 ^ (-9). Say if want to transfer 3.12 ONG using ```Invoke()```, we should construct the transaction this way.
```
ONGAddress = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02')
param = state(fromAcct, toAcct, 3120000000)
res = Invoke(0, ONGAddress, "transfer", [param])
# make sure transfer ONG succeed
assert(res)
```

Suppose there exists OEP4 contract, the token is MST with decimals equal to 12. Say if want to transfer 1.234567891234 MST using ```DynamicAppCall``` or ```RegisterAppCall```, we should contruct the transaction this way (take the former for instance).
```
# Suppose the contract hash of OEP4 is b55f2af8d0d8d2bd3d398ced3b3559caa894b536
OEP4ReversedContractHash = Base58ToAddress(ALm9fJfhgNrwt4TM6bmmqvQbZs3hugi3et)
param = [fromAcct, toAcct, 1234567891234]
res = DynamicAppCall(OEP4ReversedContractHash, "transfer", param)
# make sure transfer MST succeed
assert(res)
```

#### 11. About data visibility within contract, how to make the data invisible yet credible?
    
11.1 Hash the ```originData``` with ```salt```, get the result ```resHash```, and save the ```originData```, ```salt``` and ```resHash```.

11.2 Put and save ```resHash``` on blockchai.

11.3 The ```originData``` is generated and invisible.

11.4 When the data needs to be public, provide ```originData```, ```salt```, so that the public can verify the ```resHash``` comes from ```originData```.

#### 12. How to unbound the ONG produced by the smart contract account?
    
Please refer to [unboundWithdrawONG_compiler2.0.py](https://github.com/ONT-Avocados/python-template/blob/master/UnboundWithdrawONG/unboundWithdrawONG_compiler2.0.py).
    
#### 13. How to verify the signature of account within the contract?
    
Using ```CheckWitness()``` method within [```ontology.interop.System.Runtime```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/Runtime.py).

**CheckWitness(fromAcct)** has two functions, in general.

1. Check if the current invoker is ```fromAcct```, or equivalently, check if the transaction contains the ```fromAcct```'s signature. If so (signature verified), it will return True.

2. Check if current invoker is a contract, and if the transaction was launched by another contract. If so, verified (```fromAcct``` == [```GetCallingScriptHash()```](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/ExecutionEngine.py)).

#### 14. What is the difference between execute and pre-execute?

The object of execution and pre-execution is the methods of contract.

Execution means the on-chain states will be changed. The consumed gas or the transaction fee will be effected by the logic complexity, the amount of times the states are modified, and the data size that will be changed.

Pre-execution menas querying the on-chain states, there will be no gas fee.

#### 15. About transaction confirmation time
    
LocalNet or  ```TestMode```, which is ```http://127.0.0.1``` Solo mode，in general, it needs 6 seconds to confirm a transaction, it will bypass consensus, the block generation time is 6 seconds.

TestNet, or ```polaris```, which is ```http://polaris1.ont.io``` mode, in general, it needs 1 second to confirm a transaction. the block generation time is 1 second if there exist transactions, otherwise, it's 30 seconds.

MainNet, or ```dappnode```, which is ```http://dappnode1.ont.io``` mode, it's the same as the testnet.

#### 16. In localnet Cli, how to check the account balance and how to generate ONG so as to test transaction?
    
16.1 To check the first account ONT and ONG balance,
```
ontology.exe asset balance 1
```

16.2 To genrate ONG, we need to transfer ONT, then unbound ONG and then withdraw ONG to the account. Note that the below ```1``` mean the first account in the wallet.
```
ontology.exe asset transfer --from=1 --to=1 --amount=100000000 --asset=ont

ontology.exe asset unboundong 1

ontology.exe asset withdrawong 1

```


#### 17. About address, Base58 address, ByteArray address, big-endian address, little-endian address, and reverse, it's so annoying. How to deal with that?
    
Base58 address usually starts with a capital ```A```. 

Little-endian address is the hex format of bytearray of base58 address.

Big-endian address is the reversed result of little-endian address.

When we use sdk to invoke the smart contract, we need to convert base58 address to hex format and take that as bytearray to construct the transaction.

Usually, there are only few scenarios where it's necessary for us to use the conversion between little-endian and big-endian address.

17.1 When we want to transfer digital asset to the smart contract account (not through invoke the method within the current contract), we need to take the contract script hash (or the contract hash) as the big-endian of contract account, in order to construct the transaction.

17.2 When we want to do cross-contract invocation and use ```DynamicAppCall``` method, the first parameter, which should be the reversed version of being invoked contract script hash. And that is the little-endian of account of being invoked contract.


#### 18. Contract = Logic + States. So, how to develop the logic, and how to Change the state and query the state?
    
18.1 About logic part, we can refer to [compiler 2.0](https://github.com/ontio/ontology-python-compiler), or directly refer to [contract template](https://github.com/ONT-Avocados/python-template).

18.2 About Change and query states,
    
    
18.2.1 Change State, utilize ```Put(GetContext(), theKey, theValue)```, this operation will change on-chain states, which is modifying the corresponding value of ```theKey``` to ```theValue```.

18.2.2 Query State, utilize ```value = Get(GetContext(), theKey)``` to check the corresponding value of ```theKey``` within the smart contract.

#### 19.  开发合约=>编译合约=>部署合约=>调用合约=>测试合约的流程与工具有哪些？

19.1 开发合约：

19.1.1 智能合约中使用的函数可以在[编译器1.0](https://github.com/ontio/neo-boa)和[编译器2.0](https://github.com/ontio/ontology-python-compiler)找到引用接口，推荐使用2.0编译器。

19.1.2. 可以使用的函数也可以在[SmartX](https://smartx.ont.io/)上面得到提示。注：SmartX是一种在线合约开发、编译、布署网站。[这里](https://ontio.github.io/documentation/SmartX_Tutorial_en.html)是SmartX使用教程。

19.2 合约编译:

19.2.1 使用本地编译器编译

19.2.2 通过[SmartX](https://smartx.ont.io/)编译，推荐使用SmartX。

19.3 合约布署：

19.3.1 可以通过SmartX，连上cyano wallet(Ontology的cyano wallet是一种谷歌浏览器钱包，可以用Ethereum的MetaMask来类比)，然后指定网络(或IP地址)，可将合约布署到本地网、或测试网、或主网。

19.3.2 在启动节点的情况下，可以[通过[Cli](https://github.com/ontio/ontology/blob/master/docs/specifications/cli_user_guide_CN.md#5-%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6)，将合约布署到本地、测试网或主网。

19.4 合约测试：

19.4.1 SmartX上可以进行运行、调试合约内的函数。

19.4.2 可以通过Cli运行、调试合约函数。

19.4.3 可以以通过go/java/ts/python sdk 来测试合约，sdk中包含一些OEP4 OEP5 OEP8调用的相关例子可供参考。详细信息，请在[官方github帐号](https://github.com/ontio)下面找到对应的sdk查看。


#### 20.  OEP4, OEP5, OEP8协议的区别？
    
如果你对```ERC20```，```ERC721```以及```ERC1155```熟悉，那么```OEP4```对应于```ERC20```，```OEP5```对应于```ERC721```，```OEP8```对应于```ERC1155```。

如果对Ethereum中的这三种协议不熟悉的话， 简单来说，

OEP4是同质化资产的协议，资产之间互不区分。

OEP5是非同质化资产的协议，每个资产是唯一的，不同资产是互相区分的。

OEP8相当于OEP4与OEP5的结合体，即包含不同种类的资产，每种资产的数量是多个的，每种资产内部是同质的，不同资产是不同质的。

[此处](https://github.com/ontio/OEPs/tree/master/OEPS)有更详细的协议介绍，[此处](https://github.com/ONT-Avocados/python-template)有协议实现的合约模版。

#### 21.  暂时目前```for i in range```不支持，但是支持```for i in list```。

```
# Below wrong 
i = 0
for i in range(10):
    Notify([i])
# Below corret
L = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
for i in L:
    Notify([i])
```

#### 22.  debug合约所需的测试节点怎么产生，即调试合约的本地测试节点？

可以在[```runtime.go```](https://github.com/ontio/ontology/blob/master/smartcontract/service/neovm/runtime.go)中，增加打印信息来调试被调用的合约函数，观察合约函数执行到哪一步出了问题。


#### 23.  测试合约时，如何对执行的notify或是预执行的数据解析？
    
23.1 解析```Notify```，以OEP4的```transferMulti```为例：

    ```
    ontology.exe info status d8e5863a54f368ad950d0993d38eb504c7eddafdd39dcc81d5676887fe35 b1cf
    Transaction states:
    {
    "TxHash": "d8e5863a54f368ad950d0993d38eb504c7eddafdd39dcc81d5676887fe35b1cf",
    "State": 1,
    "GasConsumed": 13509000,  // 执行合约函数所消耗的ONG即0.013509
    "Notify": [
        {
            "ContractAddress": "3310277e27a0ed749a3525ca2f898ebcd7d6631e",
            "States": [
                "7472616e73666572", // transfer的HexString
                "616f2a4a38396ff203ea01e6c070ae421bb8ce2d",  // fromAcct(AQf4Mzu1YJrhz9f3aRkkwSm9n3qhXGSh4p)的小端地址
                "aa6e06c79f864152ab7f3139074aad822ffea855",  // toAcct(AXK2KtCfcJnSMyRzSwTuwTKgNrtx5aXfFX)的小端地址
                "00e40b5402"  // amount=10000000000的HexNumber的反序
            ]
        },
        {
            "ContractAddress": "3310277e27a0ed749a3525ca2f898ebcd7d6631e",
            "States": [
                "7472616e73666572",
                "616f2a4a38396ff203ea01e6c070ae421bb8ce2d",
                "aa6e06c79f864152ab7f3139074aad822ffea855",   // toAcct(AVXf5w8WD2y6jV1Lzi36oSKYNif1C7Surc)的小端地址
                "00e40b5402"
            ]
        },
        {
            "ContractAddress": "3310277e27a0ed749a3525ca2f898ebcd7d6631e",
            "States": [
                "7472616e73666572",
                "616f2a4a38396ff203ea01e6c070ae421bb8ce2d",
                "aa6e06c79f864152ab7f3139074aad822ffea855",    // toAcct(ANTPeXCffDZCaCXxY9u2UdssB2EYpP4BMh)的小端地址
                "00e40b5402"
            ]
        },
        {
            "ContractAddress": "0200000000000000000000000000000000000000",
            "States": [
                "transfer",
                "AQf4Mzu1YJrhz9f3aRkkwSm9n3qhXGSh4p",
                "AFmseVrdL9f9oyCzZefL9tG6UbviEH9ugK",
                13509000
            ]
        }
    ]
    }
    ``` 

23.2 解析预执行返回的数据：

23.2.1 合约返回的是数字：将预执行的结果(若为空或```00```则为零)进行反序(每一个字节作为一个单位进行反序)，然后作为十六进制数字转换为十进制，即为我们想要得到的数据。

23.2.2 合约返回的是字符串：将预执行的结果作为十六进制字符串，转换为字符串，即为我们想要的字符串。

23.2.3 合约返回的是地址：将预执行的结果作为小端地址，转换为Base58地址，即为我们想要得到的地址。

23.2.4 合约返回的是布尔：将预执行的结果转为数字后，若为零则```False```，若为1则```True```。

23.2.5 合约返回的是List: 将预执行的结果，依次取出，按照以上基本数据类型进行解析，即得到想要的结果。

#### 24.  How to manage the authority management?
    
[Here]() gives authority management contract template.

#### 25.  Notice there is no watchdog timer within the contract, all the action should be triggered outside.

There is no timer within the contract, but we can get the current time stamp, which is ```GetTime()``` method within [ontology.interop.System.Runtime](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/Runtime.py), the unit of returned value is second.
Since we need to actively trigger the smart contract action, we can add some functional models in the backend, like a times to invoke the contract at a specific time. Better to check the current time using the time stamp within the contract mehtod in order to make sure the method is more normative.

#### 26.  THere are two online data transformation tools provided, notice that all the sdks support data transformation.

Tool 1：[DataTransformationTools](https://peterlinx.github.io/DataTransformationTools/)。

Tool 2：[SmartX](https://smartx.ont.io/#/), notice ```Tool``` keyword in the upper right section.
