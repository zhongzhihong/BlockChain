# java实现一个简单的区块链

本项目借鉴以下文章：

```
https://github.com/hanshuaikang/HanShu-Note/tree/master/%E7%94%A8java%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E5%8C%BA%E5%9D%97%E9%93%BE
```



## 什么是区块链

区块链是分布式数据存储、点对点传输、共识机制、加密算法等计算机技术的新型应用模式。，区块链是比特币的一个重要概念，它本质上是一个去中心化的数据库，同时作为比特币的底层技术，是一串使用密码学方法相关联产生的数据块，每一个数据块中包含了一批次比特币网络交易的信息，用于验证其信息的有效性（防伪）和生成下一个区块。

通过一下这个简单的故事了解区块链。

明天就是小明和小红在一起100天的纪念日了，小明想给小红一个大大的惊喜，就是把送她自己这三个月辛苦研发的机器人，想到小红收到礼物手舞足蹈的样子，小明就觉得这简直太浪漫了。

当第二天小明把准备好的礼物送给小红的时候，意外发生了。

`小黑`：你们不能在一起，其实小红...

`小明`：难道小红是我亲妹妹？

`小黑`：不，我想说的是，其实小红是我的女朋友。

`小明`：你凭什么说小红是你的女朋友？

`小黑`：那你凭什么说小红是你的女朋友，我说是我的就是我的。

`小明`：我们谈恋爱的第一天，我送了小红一个鼠标，第二天，我送了她一个键盘，第三天，我送了她一个屏幕，第四天..... 第一百天，我送了她一个我辛苦开发的机器人，这些，就是证据！

`小黑`：啊啊啊啊啊，我输了，好吧，小红是你的女朋友。

小黑由于故意篡改小红是小明女朋友的事实，被拉入黑名单，从此再也没有找到女朋友。

在这个例子中，`小红`，`小明`，和`小黑`就是区块链中的链，而之前`小明`和`小红`从相识，到相知，再到相爱期间每发生一个故事就会形成一个区块。**而且小明和小红之间发生的所有故事都会以直播的形式被区块链中的所有链知道**，所以小黑说小红是他的女朋友自然就不可能是真的了，因为整个区块链所有的链都见证了小明和小红是情侣这个事实，如果小黑要篡改事实说小红是他的女朋友，那么他要修改整个区块链中所有链对于小明和小红这对情侣的记忆，这是几乎无法做到的。

这就好比全世界都知道特朗普是现在美国的总统，你现在说特朗普不是美国总统，美国总统是川建国，你这么说外人一看就知道是假的，如果要把它变成真的，就需要改变全世界所有知道这件事的人的记忆才行。

这就是区块链的**不可篡改性**。

同时，如果有一天小明不爱小红了，爱上了小绿，于是把手机上和小红有关的东西全部删掉，告诉小绿，小绿是自己的初恋，小明只爱小绿一个人。这样做有用吗？没用，因为整个区块链中的链都观看过小明和小红的直播，记录着小明和小红曾经在一起过的证据。

这就是区块链中的**不可修改性**。

综上所述，如果区块链应用于金融会怎么样？

之前我们的钱都是存在银行的账户管理系统里面，如果有人侵入银行的账户管理系统，只需要把他账号下代表余额的那串数字改了就可以决定自己有多少钱了，**而在区块链中，每个人都是银行，每个人都是账户管理系统，如果需要修改自己账户的余额，则需要修改全网所有节点的信息才行**，这几乎是不可能实现的，所以大大的提高了安全性。更不要说应用于其他领域了。

而比特币，就是区块链在割韭菜领域的一个重要应用。



## 用java实现一个简单的区块链

首先我们得有一个区块类，而且这个类要有一个最重要的特征，能够形成链。我们新建一个Block 类，代码如下

```java
public class Block {

    /**
     * 当前区块的hash
     */
    public String hash;

    /**
     * 前一个区块的hash,本例中，靠这个实现链的
     */
    public String previousHash;

    /**
     * 当前区块的数据，比如交易信息啊等等，在谈恋爱例子中代表小红和小明具体发生的事件
     */
    private String data;

    /**
     * 时间戳
     */
    private long timeStamp;

    private int nonce;

    public Block(String hash, String previousHash, String data) {
        this.hash = hash;
        this.previousHash = previousHash;
        this.data = data;
    }

    public Block(String data, String previousHash) {
        this.previousHash = previousHash;
        this.data = data;
        this.timeStamp = new Date().getTime();
        this.hash = calculateHash();
    }

    public String calculateHash() {
        String calculatedhash = StringUtil.applySha256(previousHash + timeStamp + nonce + data);
        return calculatedhash;
    }

    public void mineBlock(int difficulty) {
        String target = new String(new char[difficulty]).replace('\0', '0');
        while (!hash.substring(0, difficulty).equals(target)) {
            nonce++;
            hash = calculateHash();
        }
    }
}
```

**变量解释:**

- hash :当前区块的哈希值
- previousHash ：上一个区块的哈希值，就是靠这个实现链的，怎么有点像链表（嘘）
- data：当前区块所存储的信息，比如小明给小红买口红这一件事。
- timeStamp：时间戳， 比如小明给小红买口红这一件事发生的时间信息。
- nonce： 只是一个普通的基数变量。

这个类中需要去理解的可能就是`mineBlock ()`和`calculateHash()`这个两个方法了，而`nonce`就是**关键变量**。

在`mineBlock()`方法中会不停的执行hash运算，直到算出符合条件的区块，而`nonce`就是算出来改区块所需要的次数，在`calculateHash()`中，我们加上`nonce`次数，就可以一下子计算出来这个`hash`了。

这个`calculateHash()`这个方法，我们在执行hash计算的时候，是以上一个区块的`hash`为参数进行的，一旦上一个区块的`hash`是个假的，或者被篡改了，那么无论怎么计算，`calculateHash()`方法返回的hash值，和该区块本身的hash值是几乎不可能一样的，也就很容易发现区块被人篡改了。

而加密算法选用的是SHA-256， 也是比特币所采用的加密算法，被公认为最安全最先进的算法之一 。

`StringUtil `类的代码如下

```java
public class StringUtil {
    public static String applySha256(String input) {
        try {
            MessageDigest digest = MessageDigest.getInstance("SHA-256");
            byte[] hash = digest.digest(input.getBytes(StandardCharsets.UTF_8));
            StringBuilder hexString = new StringBuilder();
            for (byte b : hash) {
                String hex = Integer.toHexString(0xff & b);
                if (hex.length() == 1) hexString.append('0');
                hexString.append(hex);
            }
            return hexString.toString();
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

`JsonUtil `类代码如下

```java
public class JsonUtil {
    public static String toJson(Object object) {
        GsonBuilder gsonBuilder = new GsonBuilder();
        gsonBuilder.setPrettyPrinting();
        Gson gson = gsonBuilder.create();
        return gson.toJson(object);
    }
}
```

最基础的区块已经写好了，可以简单地进行测试一下。

新建三个Block区块，把`mineBlock()`方法的参数设置为5。测试类代码如下

```java
public class BlockChainTest {
    public static void main(String[] args) {
        // 第1个区块
        Block firstBlock = new Block("我是第1个区块", "0");
        firstBlock.mineBlock(5);
        System.out.println("第1个区块hash: " + firstBlock.hash);

        // 第2个区块
        Block secondBlock = new Block("我是第2个区块", firstBlock.hash);
        secondBlock.mineBlock(5);
        System.out.println("第2个区块hash: " + secondBlock.hash);

        // 第3个区块
        Block thirdBlock = new Block("我是第3个区块", secondBlock.hash);
        thirdBlock.mineBlock(5);
        System.out.println("第3个区块hash: " + thirdBlock.hash);
    }
}
```

运行结果如下

```
第1个区块hash: 00000a944adbbdbfa71db1c6dccbaea077968c2a55d9f40d0da53d6c5ec82eef
第2个区块hash: 00000d9279503bf16fb7536399bc055b2b25d3c4853aa51a2e93f322a2431a54
第3个区块hash: 0000079ca229e54943f7adcd364e86fdbbae0fffd6a133d8ce2d2d71a5d809eb
```

不难发现，三个区块，前五个数字都是0

**所以挖矿的本质其实就是通过哈希计算得到符合条件的hash的过程。**

由此知道比特币为什么越挖越少。

**因为在所有计算结果之中，符合条件的hash是有限的，而且越算越少，刚开始计算的时候，由于符合条件的hash实在是太多了，所以所需的算力比较小，很容易就计算出来了，而越往后，未被计算出来过的符合条件的hash值就越少，算出来所需要的算力越大，当最后只剩一个符合条件的hash的时候，那时候就真的无异于大海捞针了。这可能就是为什么十年前随便一台电脑都能轻轻松松地挖出来比特币，而现在却需要几千台矿机的矿场才能挖出来的原因吧**

也就是刚开始沙漠里有五百万个宝藏，全世界的人都去找，刚开始宝藏很多，大家走一步就发现一个宝藏，后来宝藏被找的只剩几个了，那么大的沙漠，为了挖到宝藏，就不得不派出更多的人去找。

这个时候可能会提出疑问，如何知道这个区块是不是合法的。看着是这样，万一它不合法或者被改了该何从得知。鉴别的代码如下

```java
public class BlockChainListTest {
    // 这玩意就是我们的区块链，存储我们所有的区块信息。（简陋版）
    public static ArrayList<Block> blockChain = new ArrayList();

    // 挖矿的难度，就是计算出来的hash前几个字符是0才是合法的。
    public static int difficulty = 5;

    public static void main(String[] args) {
        blockChain.add(new Block("我是第1个区块", "0"));
        blockChain.get(0).mineBlock(difficulty);

        blockChain.add(new Block("我是第2个区块", blockChain.get(blockChain.size() - 1).hash));
        blockChain.get(1).mineBlock(difficulty);

        blockChain.add(new Block("我是第3个区块", blockChain.get(blockChain.size() - 1).hash));
        blockChain.get(2).mineBlock(difficulty);

        System.out.println("区块链是否合法: " + isChainValid());
        System.out.println(JsonUtil.toJson(blockChain));
    }


    public static Boolean isChainValid() {

        Block currentBlock;
        Block previousBlock;
        boolean flag = true;
        String hashTarget = new String(new char[difficulty]).replace('\0', '0');

        // 循环遍历列表检验hash
        for (int i = 1; i < blockChain.size(); i++) {
            currentBlock = blockChain.get(i);
            previousBlock = blockChain.get(i - 1);
            // 比较注册的hash和计算的hash
            if (!currentBlock.hash.equals(currentBlock.calculateHash())) {
                System.out.println("当前hash不相等");
                flag = false;
            }
            // 比较当前的前一个hash与注册的前一个hash
            if (!previousBlock.hash.equals(currentBlock.previousHash)) {
                System.out.println("前一个hash不相等");
                flag = false;
            }

            // 检查该区块是不是已经被算出来过了。
            if (!currentBlock.hash.substring(0, difficulty).equals(hashTarget)) {
                System.out.println("这个区块还没有被开采，也就是你这个区块他不是合格的");
                flag = false;
            }
        }

        return flag;
    }
}
```

运行结果如下

```
区块链是否合法: true
[
  {
    "hash": "00000baef64ba04b6b0f538bad85601157401fd989d2d1775d56a256cec97cc5",
    "previousHash": "0",
    "data": "我是第1个区块",
    "timeStamp": 1702883362763,
    "nonce": 585087
  },
  {
    "hash": "00000a34b763e5c36b6220be94358531e0fe2103ac7944652afc6d1736bf0277",
    "previousHash": "00000baef64ba04b6b0f538bad85601157401fd989d2d1775d56a256cec97cc5",
    "data": "我是第2个区块",
    "timeStamp": 1702883364640,
    "nonce": 138799
  },
  {
    "hash": "00000423a90fa3653f63d91d8c31e880fd855bacc50d0cf30c45ec7af97fba42",
    "previousHash": "00000a34b763e5c36b6220be94358531e0fe2103ac7944652afc6d1736bf0277",
    "data": "我是第3个区块",
    "timeStamp": 1702883365023,
    "nonce": 648812
  }
]
```

`isChainValid()`即为检查区块链是否合法的方法。
