# The Complete Guide to Full Stack Ethereum and EVM Development

教程网站：https://dev.to/dabit3/the-complete-guide-to-full-stack-ethereum-development-3j13

#### 安装、配置环境（Node.js）

创建React application

```bash
npx create-react-app react-dapp
```

进入创建的文件夹

```bash
cd react-dapp
```

安装ethers.js和hardhat

```
npm install ethers hardhat @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers
```

安装并配置Ethereum开发环境

#### 用hardhat初始化Ethereum开发环境

```bash
npx hardhat
```

选择Create a JavaScript project并确认创建sample project

![image-20231010103851753](/Users/leo-lu/Library/Application Support/typora-user-images/image-20231010103851753.png)

打开hardhat.config.js文件，更新`module.exports`如下

```javascript
module.exports = {
  solidity: "0.8.9",
  paths: {
    artifacts: './src/artifacts',  
  },
  networks: {
    hardhat: {
      chainId: 1337
    }
  }
};
```

其中，

1. `solidity: "0.8.9"`： 这个配置指定了你在项目中使用的 Solidity 编译器版本。Solidity 是一种智能合约编程语言，不同的版本可能具有不同的语法和功能。指定一个特定的版本可以确保你的合约与该版本的编译器兼容。
2. `paths: { artifacts: './src/artifacts' }`： 这个配置指定了 Hardhat 在编译智能合约时生成的编译结果文件（通常是 JSON 格式的 ABI 文件和字节码文件）的存储路径。`artifacts` 文件夹通常用于存储与智能合约相关的编译输出文件，以便在后续的测试和部署中使用。
3. `networks: { hardhat: { chainId: 1337 } }`： 这个配置定义了 Hardhat 内置的本地测试网络（hardhat网络）的一些属性。`chainId` 是 Ethereum 网络的标识符，这里设置为 `1337`，表示你的本地测试网络的链 ID 是 1337。这个值通常用于区分不同的以太坊网络。

#### 查看contract中的智能合约示例Greeter.sol

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "hardhat/console.sol";


contract Greeter {
  string greeting;

  constructor(string memory _greeting) {
    console.log("Deploying a Greeter with greeting:", _greeting);
    greeting = _greeting;
  }

  function greet() public view returns (string memory) {
    return greeting;
  }

  function setGreeting(string memory _greeting) public {
    console.log("Changing greeting from '%s' to '%s'", greeting, _greeting);
    greeting = _greeting;
  }
}
```

1. **`contract`** 关键字用于定义一个智能合约，合约名称是 `Greeter`。合约是以太坊上的智能合约，它包含了状态变量和函数，用于处理数据和逻辑
2. **`memory`**：这是一个关键字，指示了参数的数据位置。在 Solidity 中，有三种数据位置：`storage`、`memory` 和 `calldata`。
   - `storage` 用于永久存储在区块链上，通常用于合约的状态变量。
   - `memory` 用于暂时存储，通常用于函数内的局部变量。
   - `calldata` 用于存储外部函数调用的数据。

3. **`constructor`** 是 Solidity 合约中的一个特殊函数，用于在合约被部署（deployed）到区块链上时执行初始化操作。它只会在合约部署的时候执行一次，用于设置初始状态和执行必要的初始化逻辑。
4. **`public` 可见性修饰符：** 在 Solidity 中，函数可以有不同的可见性修饰符，用于指定谁可以调用这个函数。`public` 是其中之一，它表示该函数可以被合约内部和外部的任何地址调用。这意味着任何人都可以调用这个函数来执行相应的操作，可见性修饰符还有
   1. **`external`：** 这个修饰符与 `public` 类似，但有一个关键区别。外部函数只能在合约外部调用，不能在合约内部调用。通常用于合约接口。
   2. **`internal`：** 这个修饰符表示函数只能被合约内部的其他函数调用，而不能被外部地址调用。这在实现合约内部逻辑时很有用。
   3. **`private`：** 这是最严格的修饰符，表示函数只能被合约内部的其他函数调用，并且不能被派生合约中的函数调用。它通常用于隐藏内部细节，以确保合约的安全性。
5. **`returns (string memory)` 返回类型声明：** 这部分说明了函数的返回类型。在这个例子中，函数将返回一个字符串类型的值。`string` 表示返回的数据类型是字符串。`memory` 是数据位置修饰符，表示返回的字符串将存储在内存中，而不是存储在存储器（storage）或者调用数据（calldata）中。
6. **`view`：** 这个修饰符表示函数不会修改合约的状态。它只能读取合约的数据，而不能写入数据。视图函数通常用于查询数据，不会消耗 Gas（在以太坊上调用函数时需要支付的费用，函数状态修饰符还有
   1. **`pure`：** `pure` 修饰符表示函数既不会修改合约状态，也不会读取合约状态。它用于执行纯粹的计算操作，不依赖于合约的任何状态。纯函数也不会消耗 Gas。示例：`function add(uint256 a, uint256 b) public pure returns (uint256)`
   2. **`payable`：** 这个修饰符用于函数，表示函数可以接收以太币（Ether）作为支付。它通常用于接收 Ether 的函数，如接受转账或购买操作。示例：`function receivePayment() public payable`
   3. **无修饰符：** 如果函数没有使用任何状态修饰符，默认情况下，它被认为是 `internal` 可见性，并且不会显式指定状态修改，但可以修改合约内部的状态。

> 在React app中，我们通过`ethers.js`和智能合约互动，合约地址和ABI将会从合约被hardhat创建。ABI是应用程序二进制接口（Application Binary Interface），是一种定义了如何在不同程序或模块之间以二进制形式交换数据的约定，它定义了智能合约的接口，包括函数名称、参数类型和返回值等信息，可以理解成客户端应用程序和以太坊区块链之间的接口，在这个接口部署智能合约

#### 编译ABI

```bash
npx hardhat compile
```

随后我们可以在`src/artifacts/contracts/Greeter.sol`内找到`Greeter.json`，它包含了ABI，当我们需要使用这个ABI的时候，我们可以将其import进JavaScript文件

```javascript
import Greeter from './artifacts/contracts/Greeter.sol/Greeter.json'
```

然后我们可以引用ABI

```javascript
console.log("Greeter ABI:", Greeter.abi)
```

#### 部署到本地区块链

开启本地测试节点

```bash
npx hardhat node
```

> 得到的20个测试账户可以用于部署并测试智能合约

更新`scripts/deploy.js`以部署`Greeter`合约

```javascript
const hre = require("hardhat");

async function main() {  
  const Greeter = await hre.ethers.getContractFactory("Greeter");
  const greeter = await Greeter.deploy("Hello World");
  await greeter.waitForDeployment();

  console.log(
    `contract successfully deployed to ${greeter.address}`
  );
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

1. `const Greeter = await hre.ethers.getContractFactory("Greeter");`：使用 Hardhat 的 `ethers` 模块中的 `getContractFactory` 函数来获取 `Greeter` 合约的工厂对象。这个工厂对象可以用来部署 `Greeter` 合约。
2. `const greeter = await Greeter.deploy("Hello World");`：使用 `Greeter` 合约工厂对象的 `deploy` 方法来部署一个新的 `Greeter` 合约。在这里，它传递了一个初始问候语 "Hello World" 作为构造函数参数。
3. `await greeter.waitForDeployment();`：等待部署完成。一旦合约被部署到以太坊网络，它就会返回一个已部署合约的实例，这个实例存储在 `greeter` 变量中。

> 注意！这里如果完全按照教程中的复制粘贴，会发现greeter.deployed()报错，原因是在新的hardhat中该函数已经被移除，要实现同样效果需要使用greeter.waitFordepolyment()

运行`deploy.js`并将其部署到本地网络上

```bash
npx hardhat run scripts/deploy.js --network localhost
```

> 部署合约的时候，我们使用的是本地测试网络中的第一个账户

得到返回

```bash
contract successfully deployed to undefined
```

查看另一个terminal可以发现

```bash
Contract deployment: Greeter
Contract address:    0xe7f1725e7734ce288f8367e1bb143e90bb3f0512
Transaction:         0x5a9ad22adabe543d1cc812294934844b15c164492477000a26d3279ec222274a
From:                0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
Value:               0 ETH
Gas used:            525279 of 30000000
Block #2:            0x50be2e76a6c2c586f06ecfd540395cd876c3f409a542a41d263addacbc0f3650
```

其中Contract address是用于连接用户端的

#### 连接MetaMask钱包

首先在MetaMask上添加本地测试网络

![image-20231010155536487](/Users/leo-lu/Library/Application Support/typora-user-images/image-20231010155536487.png)

用创建节点时候得到的账户私钥添加账户，切换至localhost网络

![image-20231010155754036](/Users/leo-lu/Library/Application Support/typora-user-images/image-20231010155754036.png)