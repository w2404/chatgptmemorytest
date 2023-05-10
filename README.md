# chatgptmemorytest
我说一个写论文的点子。 

首先，大家都知道chatgpt。chatgpt有一个特性是，它的对话长度是有限制的，这个限制不是厂商的商业决定，而是它使用的叫做transformer的技术本身有这么一个先天限制。

然后呢，有一个叫做autogpt的项目，它会让chatgpt不断的自动和自己对话，就像是自言自语的思考一样，不断的改善它的最终输出结果。因为chatgpt有长度限制，所以这个思考过程就被chatgpt的长度限制了。换个角度来说，对话长度的限制也可以理解为对它的记忆能力的限制，autogpt将不得不把chatgpt自动对话产生的内容尽量压缩到有限的字数中（一种比较拙劣的手法），被压缩的这部份就是被记住的，可以进入下一个思考循环，其余的就是被丢出记忆的。

因此对话长度看起来是一个很重要的特性。

然后有一个叫做chatrwkv的项目，它是诸多模仿chatgpt的开源项目之一，我可以在8G显存上运行它。这个项目有一个特点是，它没有使用transformer技术，因此它在理论上没有对话长度限制。它是在近期变得比较热门的，甚至相关论文都几乎没有。你可以在reddit上搜索下，关于它的性能的风评还可以的，认为它在同等资源条件下不弱于竞争者。

那么既然chatrwkv没有对话长度限制，如果把autogpt应用到chatrwkv上，chatrwkv是否真的能记住很早以前的对话，把它们纳入到当前的思考中呢？

那么我的点子就是对chatrwkv的记忆能力进行实验测试。

这个点子对于关注chatgpt的人来说是很难想到的，因为chatgpt有长度限制，其记忆能力在长度限制范围内的有效性也不会有人特意去质疑，想当然是有效的。而rwkv自己是一个相对较小的圈子（有一个原开发者的qq群），所以他们不一定有精力会想到这点。事实上关于rwkv的论文几乎没有，一方面因为原开发者不喜欢发论文，另一方面这是一个最近刚刚开始受到关注的项目，受关注的原因是他发布了一个14B参数量的英文模型，正是这个14B的模型的口碑很好。

下面我说下具体的实验办法，非常简单。用python脚本生成很多随机的名字，然后随机指定它们是一只猫或者一只狗，最后随机抽取其中一个名字，问AI这是不是一只狗。如此来生成一个任意长度的提问，来检查AI是否能再一个很长的对话跨度（记忆跨度）中抽取出正确的信息。

一个带有6个名字的提问范例是这样的：
I have 6 pets. Edeph is a dog. Ybtwd is a cat. Jmhcr is a cat. Oaaxo is a cat. Fdekr is a dog. Yukom is a cat.  Is Oaaxo a dog? Answer yes or no.

AI被要求按顺序读完这句话，记住每只宠物是狗或是猫，最后回答。以此来考验AI对上下文的记忆能力。

我在rwkv 3B模型上测试过，10个名字也无法正确回答。在rwkv 14B模型上测试，100个名字也可以正确回答。而测试1000个名字的时候，我的显存炸了。毕竟我只有8G显存。

我们还可以把猫狗提问替换成年龄提问，因为前者有50%的机率蒙对。名字的生成现在是随机的字母，如果换成从一个常用名字列表中抽取，或许AI的表现会更好，因为常用名AI在模型训练中见到的更多，所以它可能更擅长记住那些名字，就像人类也更擅长记忆熟悉的名字而不是乱序的字母。这些我们都可以添加进来，作为一种实验项目，或者你有更好的主意？

参考文献方面，我觉得要找一下现存的AI测试指标。这个点子本身可以说就是增加了一个对AI性能测试的指标。比较著名的AI测试有比如说MMLU，这是chatgpt4在自己论文中标榜自己高分，第一名通过的测试。类似的测试是很多，单单chatgpt4的论文中就提到了MMLU,HellaSwag, AI2 Reasoning Challenge, WinoGrande, HumanEval, DROP, GSM-8K。 如果这些测试中早就有了对AI记忆能力的测试，那么这个点子就不新了。

不过其实NLP（自然语言处理）领域中，最初就已经有了阅读能力测试，就是给AI一片文章，然后关于文章对其提问。如果把这个文章顺序输入chatgpt中，这本身就会检验AI的全文记忆能力，它不能读了后面的文章就忘了前面的文章。但是呢，我可以说这个点子和一般的阅读理解的关注点是不同的，因为任意的一篇文章，长度总是有限的，这些测试会预先告诉你，我们的文章有多长，你的AI模型需要有能力处理多少长度的文本。

可是我们现在是从autogpt的角度出发去思考的，autogpt需要用chatgpt不断生成回答，然后把回答再反馈给chatgpt，让它不断自言自语的去思考。因此这里的需求是把没有长度限制的，任意长的思考历史记录，不断的喂入chatrwkv中， 然后需要chatrwkv能尽力记住那些很早就输入的，可能很重要的信息，并在用户提问的时候，回答用户。

autogpt的这种自我循环的场景，就像alphago和自己下棋一样的场景，我觉得以前的nlp能力测试文献里面应该是没有的，这是新事物。

如果你读懂了我上面所说的话，你就知道，这个点子非常简单，需要的基础知识很少，所以任何有闲暇的人都可以联系我，考虑下合作？它或许会很重要，autogpt现在可是大热门。
这个实验的重要性应该会取决于实验结果，如果实验结果显示chatrwkv的记忆长度可以超出chatgpt，那么就会成为chatrwkv比chatgpt更适合搭配autogpt的一个证明。

# 补充

下面列举一些网上关于chatrwkv的对话长度的讨论：

https://github.com/BlinkDL/RWKV-LM/issues/83

这里https://twitter.com/dpkingma/status/1638925275873579008 ，有人提到 RWKV is unable to utilize its full context length。而我们的实验，就是关于这一点的。

# 补充

虽然我自己只有8GB显存，运行更大尺度的测试比较困难。但是rwkv他们开发者自己部署了一个14B模型服务器，在 https://huggingface.co/spaces/BlinkDL/ChatRWKV-gradio

他们的硬件更好，所以可以试试在他们的服务器上问1000个名字或是10000个名字。

但是，我这里打不开这个网页，“blinkdl-chatrwkv-gradio.hf.space unexpectedly closed the connection.”

至于我自己，要花时间微调配置，才能在有限的8GB上运行起来，而且运行中要浪费很多时间在缓存交换上，运行速度可能慢到离谱，无法在可以预期的时间内完成实验。

此外还可以租在线的gpu服务器，收费的价格似乎都很高。google的collab有12GB，可以免费用，但是要驾驭好它还挺难的，用起来没有我本地的gpu顺手。

# 补充，
rwkv是一种rnn（递归神经网络），rnn设计出来的初衷，就是为了让网络有记忆。因此当人们把rnn应用到nlp的时候，或许已经有人想到过要像我们这里一样去测试网络的记忆能力了？
这一点我不是很确定，因为rnn曾经的性能是很差的，自从google发明transformer后，大家都只用transformer了，而如果使用transformer，那么就像我上面所说，好像没有什么必要去测试模型对长文本的记忆力。
至于说在当时rnn性能还很差的时候，有没有人想到过设计实验去测试它的长文本记忆力的话，我就不知道了
