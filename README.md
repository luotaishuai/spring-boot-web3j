# Web3j Spring Boot Starter

####From [https://github.com/web3j/web3j-spring-boot-starter](https://github.com/web3j/web3j-spring-boot-starter)

[![Build Status](https://travis-ci.org/web3j/web3j-spring-boot-starter.svg?branch=master)](https://travis-ci.org/web3j/web3j-spring-boot-starter)

通过Spring IOC将web3j集成到Spring项目中

## Getting started

Web3j官方示例应用 [here](https://github.com/web3j/examples/tree/master/spring-boot)

SpringBoot模型 [Spring Boot Application](https://spring.io/guides/gs/spring-boot/)

先将项目编译在本地生成jar包
```
mvn install
```

Maven:

```xml
<dependency>
    <groupId>org.web3j</groupId>
    <artifactId>spring-boot-web3j</artifactId>
    <version>1.0-web3j-3.6.0</version>
</dependency>
```

使用方法
```
@Resource
private Web3j web3j
```

如果想通过HTTP连接本地默认的RPC节点 http://localhost:8545，就不需要额外的配置，否则就要在配置文件中添加相应的RPC节点配置

```properties
# An infura ropsten endpoint
web3j.client-address = https://ropsten.infura.io/v3/<apiKey>
```

创建一个钱包
```
public String createWallet(){
    File file = new File("D://temp");
    return WalletUtils.generateNewWalletFile("password", file, true);
}
```
ERC20代币交易
```java
private static final BigInteger GAS_PRICE = new BigInteger("21");
private static final BigInteger GAS_LIMIT = new BigInteger("7000000");
public String transfer(){
    Credentials credentials = WalletUtils.loadCredentials("password", walletFile or walletJson);
    String from = credentials.getAddress();
    EthGetTransactionCount ethGetTransactionCount = web3j.ethGetTransactionCount(from, DefaultBlockParameterName.LATEST).sendAsync().get();
    BigInteger nonce = ethGetTransactionCount.getTransactionCount();
    Address address = new Address(to);
    Uint256 value = new Uint256(amount);
    List<Type> intputParameters = new ArrayList<>(2);
    intputParameters.add(address);
    parametersList.add(value);
    List<TypeReference<?>> outputParameters = new ArrayList<>();
    Function function = new Function("transfer", intputParameters, outputParameters);
    String encoder = FunctionEncoder.encode(function);
    RawTransaction rawTransaction = RawTransaction.createTransaction(nonce, GAS_PRICE, GAS_LIMIT, contract, encoder);
    byte[] signedMessage = TransactionEncoder.signMessage(rawTransaction, credentials);
    String hex = Numeric.toHexString(signedMessage); 
    return web3j.ethSendRawTransaction(hex).sendAsync().get().getTransactionHash();
}
```

交易验证

以上ERC20代币交易实例产生的txHash到 [以太坊浏览器](https://etherscan.io/) 上查询，要选择对应的网络