# 行为性模式
 行为模式设计到算法和对象间职责的分配。
 
 行为类模式使用继承机制在类间分派行为。行为对象模式使用对象复合。其他模式将行为封装在对象里。
## CHAIN OF RESPONSIBILITY
1. 意图
    使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间过于耦合。
2. 动机
    这一模式的想法是，给多个对象处理一个请求的机会，从而解耦发送者和接送者。该请求将沿着对象链条传递直至其中一个对象处理它。
    从第一个对象开始，链中的对象要么亲自处理它，要么转发给下一个对象。该请求有一个隐式的接受者（implicit receiver）
3. 适用性
    在以下条件适合使用任务链
    + 有多个对象可以处理一个请求，哪个对象处理该请求运行时刻自动确定
    + 你想在不明确指定接受者的情况下，向多个对象中的一个提交一个请求
    + 可处理一个请求的对象集合应被动态指定
4. 结构
5. 参与者
    + Handler（如HelpHandler）
        + 定义一个可处理请求的接口
        + （可选）实现后继链
    + ConcreteHandler（如PrintButton）
        + 处理它负责的请求
        + 可以访问它的后继者
        + 如果可以处理请求，就处理，否则转发给后继者
    + Client
        + 向链上的具体处理者（ConcreteHandler）提交请求
6. 协作
    当客户提交请求时，请求沿着链条传递直至一个ConcreteHandler处理它
7. 效果
    任务链的优点和缺点：
    + 降低耦合度 职责链简化了对象的相互链接，仅需保持一个对后继者的引用，不需保持它所有后选者的引用
    + 增强了对象指派职责的灵活性 可以在运行时刻对该链条进行动态的增加或修改 来增加或改变 处理一个请求的那些职责
    + 不保证被接受 既然一个请求没有明确的接受者，那么就不能保证它一定会被处理
8. 实现
    下面是在实现过程中要考虑的问题：
    + 实现后继者链
        + 定义新的链接（通常在handler定义，也可由ConcreteHandler来定义）
        + 使用已有的链接
    例子是定义了新的链接，也可以使用已有的链接来形成。比如composite。
    + 链接后继者
        如果没有已有的引用可定义一个链，你必须自己引入。这种情况下Handler不仅定义该请求的接口，通常也维护后继链（默认转发）。
    + 表示请求
        最简单的形式，请求是一个硬编码的操作调用，这种形式方便且安全，但你只能转发Handler定义的固定的一组请求。
        另一种是一个处理函数，这个函数以一个请求码为参数（传递函数），这种方法支持的请求数目不限，但他需要条件语句来区分请求代码，另外无法用类型
        安全的方法来传递。
        
        为标示请求，Request可定义一个访问器（accessor）返回该类的标识符，或者使用运行时的类型信息。
    + 在smallTalk中自动转发
11. 相关模式
    composite和此模式共用，一个构件的父级，可以作为它的后继者（冒泡啊）
## COMMAND
1. 意图
    将一个请求封装为一个对象,从而使你可用不同请求对客户进行参数化，对请求排队或者记录，或者撤销
2. 别名
    Action Transaction
3. 动机
    有时候，必须向某对象提交请求，但并不知道关于被请求但操作或请求但接受者的任何消息。命令模式通过将请求本身变成一个对象来使工具箱对象可向未指定
    的应用对象提出请求。这个对象可被存储并像其他对象一样被传递。注意command模式下解耦了调用操作的对象和具有执行该操作的所需信息的对象。
4. 适用性
    + 如同上面例子里多个对象，抽象出待执行的动作以及参数化某对象。你可以用面向过程中的回调函数来表达这种参数化机制，command是回调机制的一个OO里的替代品
    + 在不同时刻，指定，排列，执行请求。一个command对象可以有一个与初始请求无关的生存期。
    + 支持取消操作
    + 支持修改日志
    + 用构建在原语操作上的高层操作构建一个系统。这样一种结构，在事务的信息系统中很常见，一个事务封装了对数据的一组变动。
5. 结构
6. 参与者
    + Command
        声明执行操作的接口
    + ConcreteCommand
        + 将一个接受者对象绑定于一个动作
        + 调用接受者相应的操作，实现Execute
    + Client
        创建一个具体的命令对象并设定他的接受者
    + Invoker
        要求该命令执行这个请求
    + Receiver
        知道如何实施并执行一个请求相关的操作，任何类都能作为一个接受者
7. 协作
    + Client 创建一个ConcreteCommand对象并指定Receiver对象
    + 某Invoker对象存储该ConcreteCommand对象
    + 该Invoker调用Command的Execute提交请求
    + ConcreteCommand调用他的Receiver的一些操作执行请求
8. 效果
    + 将调用操作的对象和知道如何实现该操作的对象解耦
    + command对象是头等对象，可以像其他对象一样被操纵和扩展
    + 可以将多个command组装一个复合command
    + 增加新的command很容易
9. 实现
    + 一个command对象应达到何种智能程度。一种极是它仅确定接受者和执行该请求的动作，另一种极端是它实现所有功能，不需要额外的接受者。
    + 支持取消和重做 command可能需要额外存储状态信息，这个状态包括
        + 接受者对象，真正的执行者
        + 接受者执行操作时的参数
        + 如果参数会改变，先要保存下来
    + 避免取消操作过程中的错误积累
        在实现一个可靠的，能保持原先语义的取消/重做机制时，可能会遇到滞后影响问题。比如积累错误，这里可以用Mediator来让Command访问这些信息而不暴露。
    + 使用c++模板
12. 相关模式
    Composite 用来实现宏命令
    Mediator 用来保持某个状态，命令用这一状态去取消操作
    在被放入历史操作记录表前必须被拷贝的命令起到了原型的作用
## INTERPRETER
1. 意图
    给定一种语言，定义文法的表示，并定义解释器，解释器使用该表示来解释语言中的句子
2. 动机
    如果一种特定类型的问题发生的频率足够高，那么就值得将该问题的各个实例表述为一个简单句子，这样就可以构建一个解释器。解释器通过解释这些句子来解决该问题。
    
    比如搜索匹配一个模式字符串这个问题，正则表达式是描述pattern的一种标准语言，与其为每一个pattern构建一个特定的算法，不如使用一个通用的搜索算法来
    解释执行正则表达式。
    
    解释器模式描述了如何为简单的语言定义一个文法，如何在该语言中表示一个句子，以及如何解释句子。
3. 适用性
    当一个语句需要解释执行，并且你可将该语言中但句子表示为一个抽象语法树，可使用解释器模式，有以下情况时该模式效果最好：
    + 该文法简单但对于复杂的文法，文法的类层次变得庞大而无法管理。此时语法分析程序生成器这样的工具是更好的选择，他们无需构建抽象语法树即可表达。
    + 效率不是问题，最高校的解释器通常不是通过直接解释语法分析树实现，而是转换成另一种形式，比如正则变成状态机，但即使在这种情况，转换器扔可用解释器实现
4. 结构
5. 参与者
    + AbstractExpression（抽象表达式，如RegularExpression）
        声明一个抽象的解释操作，这个函数为所有抽象语法树中的所有节点共享
    + TerminalExpression（终结符表达式，如LiteralExpression）
        + 实现文法的终结符相关联的解释操作
        + 每一个语句都需要该类的一个实例
    + NonterminalExpression(非终结表达式，如AlternationExpression，RepetitionExpression，SequenceExpression)
        + 对文法中的每一条规则都需要一个NonterminalExpression
        + 每一个符号都需要维护一个抽象表达式的实例变量
        + 为文法里的非终结符实现解释操作，解释一般要递归地调用表示字符的对象的解释操作
    + Context
        包含解释器之外的全局信息
    + Client
        + 构建或被给定表示该文法定义语言中的一个特定的句子的抽象语法树
        + 调用解释操作
6. 协作
    + client构建一个句子，它是由表达式构建的一个抽象语法树，上下文执行解释操作
    + 为每个非终结的表达式定义相应的解释操作。
    + 每一节点的解释操作 由上下文来管理。
7. 效果
    解释器有如下的优点和缺点：
    + 易于改变和拓展文法 因为该模式用类来表示文法规则，可以使用继承来改变或扩展该文法。已有的表达式可被增量式地改变，而新的表达式可定义为旧表达式的变体。
    + 也易于实现文法 定义抽象语法树中的各个节点的类的实体大体类似，这些类易于直接编写，通常它们可用一个编译器或语法程序生成器自动生成。
    + 复杂的文法难以维护 解释器模式为文法中的每一条规则至少定义了一个类，因此包含许多规则的文法可能难以维护和管理。可应用其他设计模式来缓解这一问题。
    + 增加了新的解释表达式的方式 解释器模式使得实现新表达式计算变得容易
8. 实现
    Interpreter和Composite模式在实现上有许多共通之处，下面是Interpreter的一些特殊问题：
    + 创建抽象语法树 解释器并未结束如何创建一个抽象的语法树。换言之，它不涉及语法分析。抽象语法树可用一个表驱动的语法分析程序来生成，也可以用手写（递归）
    语法分析程序创建，或者直接由Client提供。
    + 定义解释操作 并不一定要在具体子类里定义解释操作，可以用Visitor模式将解释放入一个独立的访问者对象更好。
    + 于Flyweight模式共享终结符
11. 相关模式
    Composite 抽象语法树是一个复合模式的实例
    Flyweight 共享终结符
    Iterator  用迭代器遍历
    Visitor  可以用来在一个类中维护抽象语法树的各节点行为
## ITERATOR
1. 意图
    提供一种方法顺序访问一个聚合对象各个元素，而不暴露该对象的内部表示
2. 别名
    Cursor 游标
3. 动机
    一个聚合的对象，比如list，应该提供一种方法来让别人可以访问它的元素，而不暴露其自身的数据结构。迭代器可以解决这个问题，关键思想是将对列表的访问和遍历
    从对象中分离，并放入迭代器里。迭代器定义里一个访问该列表元素的接口，迭代去对象负责跟踪当前的元素。
4. 适用性
    迭代器可以用来
    + 访问一个聚合对象的内容而不必暴露内部的数据结构
    + 支持聚合对象的多种遍历
    + 为遍历不同聚合对象提供了一个统一的接口（即支持多态迭代）
5. 结构
6. 参与者
    + Iterator （迭代器）
        迭代器定义访问和遍历元素的接口
    + ConcreteIterator
        + 具体迭代器实现接口
        + 对该聚合遍历时跟踪当前位置
    + Aggregate
        聚合类定义创建相应迭代器对象的接口
    + ConcreteAggregate
        具体聚合实现创建相应迭代器对接口，返回一个具体迭代器对实例
7. 协作
    ConcreteIterator跟踪聚合中的当前对象，并能够计算出待遍历的后继对象
8. 效果
    迭代器主要有三个重要的作用
    1.  它支持以不同的方式遍历一个聚合
    2.  迭代器简化类聚合的接口  聚合本身不需要提供类似遍历的接口
    3.  在同一个聚合上可以由多个遍历
9. 实现
    迭代器常常需要根据所用语言提供的控制结构来进行权衡，某些语言直接支持了迭代器
    1. 谁该控制迭代   由客户来控制迭代，被成为外部迭代器，由迭代器控制时，称为内部迭代。使用外部必须主动推进迭代，而内部，只需提交一个待执行的操作，
    迭代器自身去对每一个元素实现。外部迭代器更加灵活，内部迭代器更加容易使用
    2. 谁定义遍历算法  迭代器不是唯一可以定义遍历算法的地方，聚合本身也可以定义，并在遍历过程中用迭代器来存储当前的迭代状态，我们称这种迭代器为一个
    游标，因为它仅用来指示当前位置，客户会以这个游标为参数调用该聚合的next，next改变游标。
        如果由迭代器负责遍历，那么将易于在相同的聚合上使用不同的迭代算法，同时也易于在不同的聚合桑使用相同的迭代算法，但是这样遍历算法可能需要访问
        聚合的私有变量，那么就破会了聚合的封装性。
    3. 迭代器健壮程度如何    在遍历一个聚合的同时更改它是危险的。简单的解决方案是备份它，但是成本又太高。一个健壮的迭代器在你对元素操作时，不会干扰遍历，
    且不需要备份该聚合。
    4. 附加的迭代器操作 比如First，isDone，CurrentItem，Previous，SkipTo
    5. 在C++中使用多态的迭代器 使用多态迭代器是有代价的，要求一个工厂函数动态的分配迭代器对象，因此当且仅当需要时使用，否则使用在栈中分配内存的具体
    的迭代器，另一个缺点是，客户必须负责删除它。代理模式是一个补救方案。
    6. 迭代器可有特权访问 迭代器可被看为创建它的聚合的一个扩展，迭代器和聚合紧密耦合，在C++中可以让迭代器作为它的Friend来表示这种关系。但是这种关系可能使
    定义新的遍历变得困难。为避免这种问题，迭代器可包含一些protected来访问该聚合的非公共可见方法，由其子类来重写，来访问该聚合对象。
    7. 用于复合对象的迭代器   比如Composite模式，外部迭代器难以实现，因为该对象的数据结构处于嵌套的不同层次，因此若用外部迭代器，必须有一条纵贯
    该Composite的路径。反而用内部迭代器更好，隐藏了路径在调用栈，无需维护当前对象位置。
    如果复合对象有一个接口可以从一个节点移到兄弟节点，父节点，子节点，那么基于游标的迭代器更好。
    复合对象常常需要多种迭代方式，需要不同的遍历的算法。
    8. 空迭代器 是一个退化的迭代器，处理边界条件，总是已经完成了遍历。
10. 代码实现
11. 已知应用
12. 相关模式
    Composite模式 迭代器经常使用在复合对象上
    Factory Method 多态迭代器靠工厂函数来实例化适当的迭代器
    Mediator 常与迭代器一起使用，可以使用一个中间件来捕获一个迭代状态，迭代器在其内部存储Mediator
## Mediator
1. 意图
    用一个中介对象封装一系列的对象交互。中介者对象使得对象不需要显式地相互引用，从而松散耦合。
2. 动机
    OO编程鼓励将行为分布到各个对象，这种分布导致对象间有许多链接，每个对象可能知道其他的对象。虽然将一个系统切分成许多对象可以提高复用性，但对象间
    相互链接但激增又降低其复用性，过于依赖。通常使用生成子类的方式来解决，但当子类过于冗余时就采用中间件的模式。
    比如一个窗口，组件情况不同触发的效果不同，不同的对话框之间有不同的窗口组件的依赖关系，因此很冗长。通过将集体行为封装在一个单独的中介者（Mediator）
    以避免这个问题，中介者负责控制和协调一组对象间的交互。中介者使对象间不再显式地引用，充当链接的管理者的作用。
3. 适用性
    + 一组对象以定义良好但是复杂的方式进行通信，产生的依赖关系结构复杂且难以理解
    + 一个对象的引用其他很多对象并且直接与这些对象通信，导致难以复用该对象
    + 想定制一个分布在多个类中的行为，又不想生产很多子类
4. 结构
5. 参与者
    + Mediator（中介者，如管理者）
        + 中介者定义一个接口用于各组件对象的通信
    + ConcreteMediator
        + 具体的中介者
        + 了解并管理链接
    + Colleague class
        + 每一个组件类都知道它的中介者对象
        + 每一个组件对象在需与其他的组件产生联系时，通过中介者联系
6. 协作
    组件向中介者发生和接受请求，中介者在各组件间适当地转发请求以实现协作
7. 效果
    中介者有以下优点和缺点：
    + 减少了子类生产   中介者将分布于多个对象间的行为集中在一起，改变这些行为，只需生成中介者的子类。
    + 将各个组件解耦   
    + 简化了对象协议   用中介者和各个组件的一对多的交互来替代多对多的交互，一对多利于理解，维护，扩展
    + 对对象间的协作进行了抽象  更集中于组件间的交互
    + 使控制集中化    将交互的复杂性变成了中介的复杂性
8. 实现
    + 忽略抽象的中介（父）类  当各组件仅与一个中介者工作时，没有必要声明一个抽象的中介类
    + 组件和中介者通信  一种是使用Observer模式，将中介者实现为一个Observer，各个Colleague作为subject，另一种是在中介者定义一个特殊的通知接口，
    各个colleague通信时调用该接口
11. 相关模式
    Facade与中介者不同在于，它是对一个对象子系统进行抽象，从而提供更方便的接口，它的协议是单向的，即Facade对象对子系统提出请求，但反之不行。
    而Mediator提供了各个组件对象不支持或不能支持的协作行为，而且协议是双向的。
    
    可以用Observer模式通信
    
## MEMENTO (备忘录)
1. 意图
    在不破坏封装的前提下，捕获一个对象的内部状态，并在对象之外保存这个状态，这样以后就可将对象恢复到原先保存了的状态
2. 别名
    Token
3. 动机
    有时候有必要记录一个对象的内部状态，但是对象通常封装了其部分或者所有信息，使得状态信息无法被访问，更别提保存了。而暴露状态又破坏了封装。
    
    一个Memento是一个对象，它存储另一个对象在某个瞬间的内部状态，而后者被称为Originator，至于Originator可以向Memento里存取信息。
4. 适用性
    + 必须保存一个对象在某一个时刻的（部分）状态，这样以后需要时它能恢复到先前的状态
    + 如果一个用接口来让其他对象直接得到这些状态，将会暴露对象的实现细节，并破坏封装
5. 结构
6. 参与者
    + Memento（备忘录，如SolverState）
        + 备忘录存储原发器对象的内部状态，原发器根据需要决定备忘录里存储哪些状态
        + 防止原发器以外的其他对象访问备忘录，备忘录实际上有两个接口，管理只能看到窄接口，只能将备忘录传递给其他对象，原发器是宽接口
    + Originator 原发器
        + 原发器创建一个备忘录，用以记录
        + 使用备忘录恢复状态
    + CareTaker
        + 负责保存好备忘录
        + 不能对备忘录的内容进行操作或检查
7. 协作
    + 管理器向原发器请求一个备忘录，有时候管理者不会把备忘录返回给原发器
    + 备忘录是被动的，只有创建备忘录的原发器会对它的状态进行检索和赋值
8. 效果
    + 保持封装边界    避免暴露一些只应由原发器管理却又必须存储在原发器之外的信息
    + 简化了原发器    
    + 使用备忘录的待机很高
    + 定义窄接口和宽接口
    + 维护备忘录的潜在成本
9. 实现
    + 语言支持 备忘录有两个接口，一个为原发器的宽接口，一个为其他对象所用的窄接口，宽接口私有，窄接口公开。
    + 存储增量改变 如果备忘录的创建及其返回的顺序是可以预测的，备忘录可以仅存储原发器内部状态的改变值。
12. 相关模式
    Command 命令模式可以使用备忘录来为可撤销的操作维护状态
    Iterator 基于备忘录的迭代器
    
## Observer

1. 意图
    定义对象间一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象得到通知并且自动更新
2. 别名
    Dependents Publish-Subscribe
3. 动机
    数据对象的任何状态的改变都应改变某些对象，观察者模式描述了如何建立这种关系。这一模式的关键对象是Subject和Observer。一个subject可以有任意个
    Observer，同时这种模式也被成为publish-subscribe
4. 适用性
    + 当一个抽象模型有两个方面，其中一个方面依赖于另一个方面，可以将这2者封装在独立的对象里使其各自独立的改变和复用
    + 当对一个对象改变时同时影响到其他对象，而不知道有多少时
    + 当一个对象必须通知其他对象
5. 结构
6. 参与者
    + Subject
        + 知道它的observer，可以有任意多个observer
        + 提供注册和删除observer的接口
    + Observer
        + 为那些subject发生变化时，需要获得通知的对象定义的一个更新接口
    + ConcreteSubject
        + 将有关状态存入各个ConcreteObserver
        + 当它的状态发生改变时，向它的各个Observer发出通知
    + ConcreteObserver
        + 维护一个指向ConcreteSubject对象的引用
        + 存储有关状态
        + 实现Observer接口
7. 协作
    + 当ConcreteSubject发生改变时，通知Observer
    + ConcreteObserver向目标对象查询信息，保持信息一致，注意发出请求的Observer并不是立即更新，而是推迟到从目标那获得通知，同时这个通知也不一定
    由目标对象调用，也可能由其他观察者或者其他对象调用。
8. 效果
    Observer模式允许你独立的改变目标和观察者，你可以单独复用目标对象而无需同时复用其观察者，反之亦然。你可以在不改动目标和观察者的前提下新增观察者。
    + 目标和观察者解耦，或者说抽象耦合  一个目标仅知道它有观察者，并不知道具体的观察者。
    + 支持广播通信    不需指定观察者，处理还是忽略通知由接观察者决定    
    + 意外的更新 因为一个观察者不知道其他的观察者存在，可能对改变目标对最终代价一无所知，引起错误的更新
9. 实现
    讨论下实现依赖机制相关的问题
    1. 创建目标到其观察者之间的映射   简单方法就是在其自身保存观察者的引用，当目标很多，观察者较少时，存储代价有点高。另一种就是用时间换空间，用hash
    表存储，这样一来，没有观察者的目标不产生存储开销，但是访问观察者需要时间。
    2. 观察多个目标   一个观察者依赖于多个目标，可以拓展update接口，把目标对象作为参数传递
    3. 谁触发更新    目标和观察者都依赖于通知机制，但具体由哪一个对象来触发通知呢？
        +   由目标对象的状态设定操作在改变目标对象的状态后自动调用通知，优点是好理解，客户不需要记住在目标对象上调用通知，缺点是多个连续操作效率低
        +   让客户负责在适当的时机调用通知，优点是客户可以在一系列的状态改完后再一次触发，避免了不必要的更新，缺点是给客户增加了负担，易出错
    4. 对已删除目标的悬挂引用  删除了就好好删除，避免依赖还存在
    5. 在发出通知前确保目标的状态自身是一致的 因为观察者在更新其状态时需要查询目标的状态，顺便说一下，要记录下哪个subject触发了通知
    6. 避免特定于观察者的更新协议-----推/拉模型  这两种模型的效率不高
    7. 显示地指定感兴趣的改变 扩展目标的注册接口，让观察者只对感兴趣的事件做出观察（切面）
    8. 封装复杂的更新语义 当目标和观察者之间的依赖比较复杂时，我们需要一个维护这个关系的对象，被称为ChangeManager，它有如下几个责任
        + 将一个目标映射到它的观察者并提供一个接口来维护映射
        + 定义一个特定的更新策略                                                        
        + 根据一个目标的请求，更新所有依赖于这个目标的观察者
        ChangeManager就是一个中间件，单例模式更好，应当全局可见。
    9.  结合目标类和观察者类  可以定义一个即是目标又是观察者的接口
12. 相关模式
    Mediator 封装复杂的更新语义，如ChangeManager
    SingleTon 单例的中间件
    
## STATE
        
1. 意图
    允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它的类。
2. 别名
    状态对象 Objects for states
3. 动机
    考虑一个表示网络链接的类TCPConnection，有以下状态Established，Listening，Closed，TCPConnection对象收到请求时，根据自身状态做出不同反应。
    
    思想的关键是用一个TCPState来表示状态，为各种不同操作状态的子类声明了一个公共接口，子类实现与状态相关的操作。Connection类维护一个STATE对象，把
    请求委托给它。
4. 适用性
    + 一个对象的行为取决它的状态，并且必须在运行时刻根据状态改变行为
    + 一个操作含有庞大的多分支语句，且这些分支依赖于该对象的状态。这个状态通常用一个或多个枚举类型表示，state模式将每一个条件分支放入一个独立的类中。
    这使得你可以根据对象自身的情况将对象的状态作为一个对象，这一对象不依赖于其他对象而独立变化。
5. 结构
6. 参与者
    + Context
        + 定义客户感兴趣的接口
        + 维护一个ConcreteState的实例，这个实例定义当前状态
    + State
        + 定义一个接口封装与Context的一个特定状态的行为
    + ConcreteState subClass
        + 每一个子类实现了一个与Context的一个状态相关的行为
7. 协作
    + Context将与状态相关的请求委托给当前的ConcreteState对象处理
    + Context可讲自身作为一个参数传递给处理该请求的状态对象，这使得状态对象可在必要时访问Context
    + Context是客户使用的主接口，客户可用状态对象来配置一个Context，一旦一个Context配置完毕，客户不需再直接与状态对象接触
    + Context或者ConcreteState子类都可以决定哪个状态是另外哪一个的后继者，以及在何种条件下进行状态转换
8. 效果
    + 它将与特定状态相关的行为局部化，并且将不同状态的行为分割开 state模式将所有与一个特定的状态相关的行为封装进了一个对象，仅此通过新定义的子类可以
    很容易增加新的状态和转换。
    另一种就是使用数据定义状态，让context操作显式地检查，这样会有很冗长和相似的条件语句和case，每增加一个新的状态过于复杂，维护代价很高。
    state模式避免了这种问题，当然引入了一个新问题，增加了子类数目。
    + 它使得状态转换显式化    当一个对象仅以内部数据值来定义当前状态,其状态仅为一些变量的赋值，不够明确，为不同的状态引入不同的独立的对象使得转换
    更加明确。
    + State对象可以被共享 
9. 实现
    + 谁定义状态转换 state并不指定哪一个参与者定义该状态的转换准则，可以通过context来负责，也可以让state子类来实现，更为灵活。缺点是state子类之间的依赖
    加重
    + 基于表的另一种方法 将条件代码映射为一个查找表，优点是你可以更改数据而不是修改代码来改变状态，缺点是，对表的查找不如虚函数高效，用表的转换逻辑
    使得转换准则不够明确且难以理解，且表只描述了状态之间的转换缺少具体的转换机制。
    表驱动的状态机和state模式的主要区别如下：state模式对与状态相关的行为进行建模，而表则着重于定义状态转换
    + 创建和销毁state对象  这是一个常见的问题，1.是仅当需要时创建随后销毁，2.提前创建始终不销毁。视情况而定。
    + 使用动态继承 动态语言方便，静态就。。
12. 相关模式
    Flyweight 解释和处理怎样共享状态对象
    状态对象通常是单例模式
    
## STRATEGY 对象行为

1.  意图
    定义一系列算法，把它们封装，使它们可以互相转换，本模式使得算法可独立于使用它的客户而变化
2.  别名
    Policy
3.  动机
    有许多算法可对正文流进行换行，将这些算法硬编进使用它们的类是不可取的，原因如下：
    + 需要换行的程序直接包含换行算法会使代码复杂，会使程序庞大难以维护，尤其需要多种换行方法时
    + 不同时候需要不同的算法，不需要支持不使用的算法
    + 当换行是程序的一个难以分割的成份时，增加新的或者改变旧有的算法及其困难
    
    因此我们定义一些类来封装算法，从而避免此类问题，一个以这种方法封装的算法称为一个策略。
4.  适用性
    当存在以下情况时使用策略模式
    + 许多相关的类仅仅是行为有异，策略模式提供类一种 用多个行为中的一个来配置一个类的方法
    + 需要使用一个算法的不同辩题
    + 算法使用客户不应知道的数据
    + 一个类定义了多种行为，这种行为在这个类的多个条件语句中出现，可以将分支移入策略子类
5.  结构
6.  参与者
    + Strategy 
        + 定义所有支持算法的公共接口，context使用这个接口调用某个ConcreteStrategy
    + ConcreteStrategy
        + 实现具体的算法
    + Context
        + 用一个ConcreteStrategy对象配置
        + 维护对策略对象的引用
        + 定义一个接口让策略来访问
7. 协作
    + 策略和上下文选定实现的算法
    + context将客户请求转发给它的策略
8. 效果
    strategy模式有以下优缺点
    + 相关算法系列 算法类层次为context定义了一系列可供重用的算法或行为，可以继承重用
    + 一个替代继承的方法 本来可以定义context子类，但难以维护吧啦吧啦
    + 消除了条件语句
    + 实现的选择
    + 客户必须了解不同的策略 缺点之一
    + 策略和上下文的通信开销 某些具体的子类可能不会用到所有接口
    + 增加了对象的开销
9.  实现
     + 定义strategy和context接口 concreteStrategy需要能有效访问Context对象获取需要的数据，一种是发送数据给
     strategy对象还有一种是将context发送给strategy
     + 将strategy作为模板参数  看语言特性
     + 使strategy对象成为可选的 
12. 相关模式
    Flyweight   strategy对象经常是很好的轻量级对象
    
## Template Method 从这开始都类行为型

1.  意图
    定义一个操作的骨架，而将一些实现步骤延迟到子类，子类可以在不改变一个算法的结构即可重新定义该算法的某些步骤
2.  动机
    用一些抽象的操作定义一个算法，子类来具体实现这些操作，这就是模板方法
3.  适用性
    +   一次性实现一个算法的不变的部分，并将可变的行为留给子类来实现
    +   各个子类中的公共行为提取出来并集中到父类避免代码重复
    +   控制子类扩展  模板方法只在特定点调用hook操作，这样就允许在这些点进行扩展
4.  结构
5.  参与者
    +   AbstractClass
        +   定义抽象的原语操作，具体的子类重定义它们以实现一个算法的各步骤
        +   实现一个模板方法，定义各算法的骨架，该模板方法不仅调用原语操作，也调用定义在AbstractClass或其他对象的操作
    +   ConcreteClass
        +   实现原语操作完成算法中与特定子类相关的步骤
6.  协作
    +   ConcreteClass靠抽象父类来定义算法的骨架
7.  效果
    模板方法是一种代码复用的技术，它们在类库中比较重要，这被描述为反向的控制结构，由父类来调用了子类。模板方法调用下列类型的操作：
    +   具体的操作   ConcreteClass对客户类的操作
    +   具体的AbstractClass的操作 通常对子类有帮助的操作
    +   原语操作    抽象操作
    +   FactoryMethod
    +   勾子操作    提供了默认行为，子类可以拓展
    
    很重要的一点是模板方法应该指明哪些是勾子（可以被重定义），哪些是抽象（必须被重定义），子类编写明确哪些是需要重定义的
    ，但是人们容易忘记调用被继承的方法，我们就可以把这种操作转换为一个模板方法，即在父类中调用勾子，指明了子类编写的重定义。
8.  实现
    + 使用c++访问控制
    + 尽量减少原语操作 定义模板方法的目的是减少子类具体实现时重定义那些原语的操作，如果需要重定义的越多，程序也就越冗长
    + 命名约定
11. 相关模式
    FactoryMethod 常被模板方法调用
    Strategy 模版方法通过继承来实现改变算法的一部分，策略使用委托来改变整个算法。
    
##  Visitor

1.  意图
    表示一个作用于某对象结构中各个元素的操作，它使你可以在不改变各个元素的类的前提下定义作用于这些元素的新操作
2.  动机
    使用visitor模式需要定义两个层次，一个接受操作的层次Node，另一个对应于对元素操作的层次NodeVisitor。
3.  适用性
    +   一个对象包含很多类对象，它们有不同的接口，而你相对这些对象做一些依赖于具体类的操作
    +   需要对一个对象结构中对对象进行很多不同对并且不相关对操作，而你想避免这些操作污染对象的类，Visitor将你的操作抽象了。
    +   定义对象结构的类很少改变，但经常需要在此结构上定义新的操作，改变对象结构类需要重定义对所有访问者的接口，代价很大，如果对象结构类经常改变，
    那不如在这些类里定义操作
4.  结构
5.  参与者
    +   Visitor（访问者，如NodeVisitor）
        +   为该对象结构中的ConcreteElement的每一个类声明一个Visit操作，该操作的名字和特征标志了发送Visit请求给该访问者的那个类，这使得访问者
        可以确定正被访问的元素的具体的类，这样访问者就可以通过该元素接口直接访问它
    +   ConcreteVisitor（具体的访问者，如TypeVisitor）
        +   实现操作
    +   Element
        +   定义一个Accept操作，以一个visitor为参数
    +   ConcreteElement
        +   实现Accept操作，该操作以一个访问者为对象
    +   ObjectStructure
        + 能枚举它的元素
        + 提供一个高层的接口允许访问者访问它的元素
        + 可以是一个复合结构或是一个集合
6.  协作
    +   一个使用Visitor模式的客户必须创建一个ConcreteVisitor对象，然后遍历对象结构，用该访问者访问所有元素
    +   当一个元素被访问时，调用对应于它的类的Visitor的操作
7.  效果
    +   访问者模式使得易于增加新的操作 访问者使得增加依赖于复杂对象结构的操作更加容易，想增加一个操作时，仅需增加一个新的访问者
    +   访问者集中相关的操作而分离无关的操作  相关的行为不是分布在定义该对象结构的各个类上，而是集中在一个访问者中
    +   增加新的ConcreteElement类苦难  访问者使得难以增加新的节点元素，因为每添加一个新的ConcreteElement都要在visitor里增加一个新的抽象，所以
    应该考虑的是系统的哪个部分经常变化，是作用于对象的结构上，还是构成该结构的元素上。
    +   通过类层次进行访问   迭代器通过调用节点对象的特定操作来遍历结构，但是迭代器不能对具有不同类型的对象结构操作，只能访问具有共同父类的元素。
    而访问者没有这种限制，可以访问具有不同父类的对象。
    +   累积状态
    +   破坏封装
8.  实现
    1.  双分派 访问者模式允许你不改变类即可有效地增加其上的操作，这被称为双分派，而一些静态语言只支持单分派，比如c++
    在单分派语言里，到底由哪一种操作将来实现一个请求取决于两个方面，该请求的名称和接受者类型。
    双分派语言意味着得到执行的操作决定于请求的种类和两个接受者的类型，accept的含义决定于两个类型，visitor和element类型，双分派使得访问者可以对每一个
    类元素请求进行不同的操作。
    2.  谁负责遍历对象结构   遍历的责任有三个地方1.对象结构中，2.访问者中，3.迭代器对象。
    通常由对象结构负责。另一个选择是迭代器。
11. 相关模式
    Composite   访问者用于对一个由Composite模式定义的结构
    Interpreter 用于解释器
    
##  相关讨论

行为模式的根本就是封装了行为（变化）。这些模式通常定义一个抽象类来描述这些封装变化的对象，并以此来命名模式。

大多数模式有连各种对象，封装该方面特征的新对象，和使用这些新对象的已有对象。如果不使用这些模式，新对象可能成为
旧对象难以分割的一部分，但也不是所有模式具有这样的分割功能，如职责链，职责链体型来行为模式间的另一个不同点，并非所有
行为模式都定义类之间的静态通信关系。指责链就是提供数目可变的对象间进行通信，其他模式则涉及一些作为参数传递的对象。
### 对象作为参数
一些模式总是被用在参数的对象，如Visitor，Command（宽接口，不同状态下的扔给不同的执行者），Memento（仅仅是存储部分状态）。区别在Memento是个窄接口，而Command是多态，而Visitor不是它所访问的元素的一部分。
### 通信应该被封装还是封闭
Mediator和Observer互相竞争的模式，一个是封装了通信，中间件管理了通信，便于理解，难以复用。一个是开放通信，由Observer和Subject共同维护通信，便于复用，难以理解。
### 对发送者和接受者解耦
command，observer，mediator，chain of responsibility都涉及了解耦，又有不同的考虑。
Command将发送者和接受者之间的链接定义在一个单独的对象使得该发送者可以和不同的接受者一起工作，使发送者更有复用性。
Observer定义一个接口来通知目标中发生的改变，尤其适合数据依赖，需要多个接受者时。
Mediator则把所有通信封装（DISPATCHER啊！！！），各个对象仅能通过中间件交流。
Chain of responsibility则是提供链式的传播
## 总结
除了少数情况，各个行为模式之间的关系是互相补充互相加强。

    
    

    