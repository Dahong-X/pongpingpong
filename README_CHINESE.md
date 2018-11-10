# pongpingpong - EOS智能合约和一些Windows工具教程

[EOS账户](https://bloks.io/account/pongpingpong)

## Introduction

您好，欢迎来到这个有趣的小教程。这个教程关于EOS纽约开发的不错的小工具，希望这将使Windows EOS开发者更容易上手。

您可能会问什么是**pongpingpong**？**pongpingpong**是一个EOS智能合约教程，它围绕[EOS Easy Contract](https://github.com/eosnewyork/EOSEasyContract)、Windows EOS开发者和一些更高级的智能合约展开。第一部分将重点关注让大家快速创建，编译和部署他们的智能合约。后续部分将围绕更高级的智能合约内容展开，例如内联操作（inline actions），多索引数据库（multi-index databases）以及“信任和确认模型”（"the Trust and Confirm model"）。在本教程结束时，您应该能够为智能合约开发配置Windows环境。

**预备知识**

这个项目需要[Docker for Windows](https://www.docker.com/get-started)和我最喜欢的文本编辑器IDE -  [VS Code](https://code.visualstudio.com/download)。这两个工具都有很好的开发社区，有大量配置工具的参考信息。最后还需要我们团队成员[Warrick](https://github.com/eosnewyork/EOSEasyContract/commits?author=warrick-eosny)的心血 - [EOS Easy Contract](https://github.com/eosnewyork/EOSEasyContract)。他在一个下雨的周末，静悄悄得创建了EOS Easy Contract。我们将用这个工具来创建，编译和部署智能合约。

## 目标

我们的目标群体是所有Windows平台上不知道如何开始EOS开发的程序员。Mac和Linux上有很多工具，但对Windows开发真却不太友好。希望这个教程为他们打开了一扇门。

##环境配置

我将假设您已使用上面的链接安装了Docker和VS Code。现在我们去 [github](https://github.com/eosnewyork/EOSEasyContract) 链接获取[EOS Easy Contract](https://github.com/eosnewyork/EOSEasyContract) ，然后跟着那里的配置教程设置。

完成了[EOS Easy Contract](https://github.com/eosnewyork/EOSEasyContract)的配置之后，开始构建您的第一份智能合约吧。


## 第一部分: 创建、编译和部署您的智能合约

#### 1. 首先创建pongpingpong项目
```
> EOSEasyContract.exe template new --path %USERPROFILE% --name pongpingpong
```
![Template output ](images/template_creation_output.png?raw=true)

#### 2. 在VS Code中打开项目
```
code %USERPROFILE%\pongpingpong
```

#### 3. 编译代码
点击 ```Ctrl-Shift-B``` 来编译例子合约
![Compile output](images/build_sample_contract_output.png?raw=true)

#### 4. 检查编译文件
在编译文件夹中，您应该可以看到wasm和abi文件。

![build output](images/build_output.png?raw=true)

#### 5. 启动开发docker镜像
##### a.  首先在编译输出里面找到docker镜像的名字。
在这个例子里面是： **EOSCDT-CB6FACF96E87DAA7FAB531911B0B8683**

![container output](images/container_output.png?raw=true)

###### b. 打开终端

点击 ``` Ctrl-Shift-\` ``` 打开一个终端

```
docker exec -it EOSCDT-CB6FACF96E87DAA7FAB531911B0B8683 /bin/bash
```
![start docker output](images/start_docker_output.png?raw=true)

#### 6. 启动一个单节点测试网

下列步骤包含在一个 [脚本](scripts\setup_env.sh) 中，可以直接拷贝、执行。

##### a. 启动nodeos
```
# send output to a log file and start a background process
# 设置输出至一个log文件，然后启动一个后台进程
nodeos -e -p eosio --plugin eosio::chain_api_plugin --plugin eosio::history_api_plugin &> /var/log/nodeos.log & 
tail /var/log/nodeos.log
```
![nodeos output](images/nodeos_output.log.png?raw=true)

##### b. 创建钱包、导入钥匙对
```
# create wallet named "default"
# 创建名为default的钱包
cleos wallet create --file /tmp/default.w

# import the default private key
# 导入钱包私钥
cleos wallet import --private-key 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3

# the wallet will lock occasionally so you would need to unlock it
# 钱包偶尔会锁上，所以您需要解锁钱包
cleos wallet unlock --password `cat /tmp/default.w`
```
![wallet output](images/wallet_output.png?raw=true)

##### c. 加载bios合约
```
cleos set contract eosio /opt/eosio/contracts/eosio.bios/ -p eosio@active
```

![bios output](images/bios_output.png?raw=true)

##### d. 创建账户

*本教程使用的缺省的密钥。不用将该密钥用作开发之外的它用。*
```
# create the account
# 创建账户
cleos create account eosio pongpingpong EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV 

# check the account
# 检查账户
cleos get account pongpingpong
```

![account output](images/account_output.png?raw=true)

##### e. 加载合约
```
# load up the wasm file
# 加载wasm文件
cleos set code pongpingpong /data/build/pongpingpong.wasm

# load the abi
# 加载abi
cleos set abi pongpingpong /data/build/pongpingpong.abi
```
![set code output](images/set_code_output.png?raw=true)

###### f. 提交一个测试交易
```
cleos push action pongpingpong hi '{"user" : "pongpingpong"}' -p pongpingpong
```

![action output](images/action_output.png?raw=true)
### 