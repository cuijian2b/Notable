---
tags: [C++]
title: C++ API设计
created: '2021-12-24T09:58:13.624Z'
modified: '2021-12-24T10:00:46.020Z'
---

# C++ API设计

## 1. API简介

### 1.1 什么是API：API(Application Programming
Interface)提供了对某个问题的抽象，以及客户与解决该问题的软件组件之间进行交互的方式。组件本身通常以软件类库形式分发，它们可以在多个应用程序中使用。
**概况地说，** **API** **定义了一些可复用的模块，使得各个模块化功能块可以嵌入到最终用户的应用程序中去**
。API是一个明确定义的接口，可以为其它软件提供特定服务。

在C++中，API一般包括一个或多个头文件(.h)以及辅助文档。某个特定API的具体实现通常是可以被链接到最终用户程序中的库文件，它也可以是静态库，又或者是动态库。

**C++ API** **通常会包含如下的元素：头文件；类库；文档** 。

API是软件组件的逻辑接口，隐藏了实现这个接口所需的内部细节。

### 1.2 API设计上有什么不同：
接口是开发者所编写的最重要的代码。因为比起相关的实现代码出现问题，修复接口出现的问题代价要大得多。

API开发中的一些关键因素：

(1).API是为开发者设计的接口。

(2).多个应用程序可以共享同一个API。

(3).修改API时，必须尽可能保证向后兼容。

(4).出于向后兼容的需求，一定要具有变更控制流程。

(5).API的生存周期一般都比较长。

(6).在编写API时，良好的文档必不可少，特别是当不提供实现的源代码时。

(7).自动化测试同样也很重要。

**API描述了其他工程师构建他们的应用软件所使用的软件。因此，API必须拥有良好的设计、文档、回归测试，并且保证发布之间的稳定性。**

### 1.3 为什么要使用API：

(1).更健壮的代码：隐藏实现、延长寿命、促进模块化、减少代码重复、消除硬编码假设、易于改变实现、易于优化。

(2).代码复用：就是使用已有的软件去构建新的软件。API提供了一种代码复用的机制。

(3).并行开发。

### 1.4 何时应当避免使用API：

(1).许可证限制。

(2).功能不匹配。

(3).缺少源代码：不能访问API的源代码就丧失了通过修改源代码修复错误的能力。

(4).缺乏文档。

### 1.5 API示例：
在现代软件开发中API无处不在，从OS、语言层面的API，到图像、声音、图形、并发、网络、XML、数学、Web浏览器以及GUI API。

术语SDK(软件开发工具包)和术语API是密切相关的。本质上讲，SDK是安装在计算机上的特定平台的包，其目的是使用一个或多个API构建应用。SDK至少要包含编译程序所需的头文件(.h)以及提供API实现的库文件(.dylib、.so、.dll)，用以链接到应用程序之中。然而，SDK还可能包含其它帮助使用API的资源，如文档、示例代码以及支持工具。

### 1.6 文件格式和网络协议：

在计算机应用中存在几个其它形式的常用通信”协议”，其中最常见的一个就是文件格式。它是使用众所周知的数据组织层次将内存中的数据存储到磁盘文件上的方法。比如，JPEG文件交换格式(JFIF)是用来交换JPEG编码图像的图像文件格式，通常使用.jpg或.jpeg文件扩展名。

客户端/服务端应用、点对点应用以及中间件服务，使用建立好的且通常基于网络套接字的协议发送和接收数据。

每当你创建一个文件格式或者客户端/服务器协议时，同时也要为其创建API。这样，规范的细节以及未来的任何变更都将是集中且隐藏的。

## 2. 特征

### 2.1 问题域建模：
编写API的目的是解决特定的问题或完成具体的任务。因此，API应该首先为问题提供一个清晰的解决方案，同时能对实际的问题域进行准确的建模。

API应该对它所解决的问题提供逻辑抽象。也就是说，在设计API时，应该阐述在选定问题域内有意义的深层概念，而不是公开低层实现细节。当你把API文档提供给一位非程序员时，他应当能够理解接口中的概念并且知道它的工作机制。

API同样需要对问题域的关键对象建模。该过程旨在描述特定问题域中对象的层次结构，因此经常被称作”面向对象设计”或者”对象建模”。对象建模的目的是确定主要对象的集合，这些对象提供的操作以及对象之间的关系。

### 2.2 隐藏实现细节：
创建API的主要原因是隐藏所有的实现细节，以免将来修改API对已有客户造成影响。任何内部实现细节(那些很可能变更的部分)必须对该API的客户隐藏。主要有两种技巧可以达到此目标：物理隐藏和逻辑隐藏。物理隐藏表示只是不让用户获得私有源代码。逻辑隐藏则需要使用语言特性限制用户访问API的某些元素。

物理隐藏：在C和C++中，声明和定义是有特定含义的精确术语。 **声明只是告诉编译器一个名字以及它的类型，并不为其分配任何内存**
。与之相对，定义提供了类型结构体的细节，如果是变量则为其分配内存。声明告诉编译器某个标识符的名称及类型。定义提供该标识符的完整细节，即它是一个函数体还是一块内存区域。
**物理隐藏表示将内部细节** **(.cpp)** **与公有接口(.h)** **分离，存储在不同的文件中** 。

逻辑隐藏：封装提供了限制访问对象成员的机制：public、protected、private。封装是将API的公有接口与其底层实现分离的过程。
**逻辑隐藏指的是使用** **C++** **语言中受保护的和私有的访问控制特性从而限制访问内部细节**
。类的数据成员应该始终声明为私有的，而不是公有的或受保护的。

永远不要返回私有数据成员的非const指针或引用，这会破坏封装性。

强烈建议在API中采用Pimpl惯用法，这样就可以将所有实现细节完全和公有头文件分开。 **Pimpl**
**惯用法：它将所有的私有数据成员隔离到一个.cpp** **文件中独立实现的类或结构体中。之后，.h**
**文件仅需要包含指向该类实例的不透明指针(opaque pointer)** **即可** 。

将私有功能声明为.cpp文件中的静态函数，而不要将其作为私有方法暴露在公开的头文件中。(更好的做法是使用Pimpl惯用法。)

隐藏实现类：除了隐藏类的内部方法和变量之外，还应该尽力隐藏那些纯粹是实现细节的类。实际上，一些类仅用于实现，因此应该将其从API的公有接口中移除。

### 2.3 最小完备性：

(1).优秀的API设计应该是最小完备的。即它应该尽量简洁，但不要过分简洁。

(2).谨记奥卡姆(Occam)剃刀原理：若无必要，勿增实体。

(3).疑惑之时，果断弃之，精简API中公有的类和函数。

谨慎添加虚函数：虚函数的调用必须在运行时查询虚函数表决定，而非虚函数的调用在编译时就能确定。使用虚函数一般需要维护指向虚函数表的指针，进而增加了对象的大小。不是所有的虚函数都能内联，因而将虚函数声明为内联是没有任何意义的。因为虚函数是运行时确定的，而内联是在编译时进行优化。谨记C++中的inline关键字仅仅是给编译器的一个提示。如果类包含任一虚函数，那么必须将析构函数声明为虚函数，这样子类就可以释放其可能申请的额外资源。
**绝不在构造函数或析构函数中调用虚函数，这些调用不会指向子类** 。

避免将函数声明为可以重写的函数(虚的)，除非你有合理且迫切的需求。

### 2.4 易用性：
优秀的API设计应该使简单的任务更简单,使人一目了然。例如，好的API可以让客户仅通过方法签名就能知晓使用方法，而不需要另写文档：

(1).使用枚举类型代替布尔类型，提高代码的可读性。

(2).避免编写拥有多个相同类型参数的函数。

(3).使用一致的函数命名和参数顺序。

(4).正交的API意味着函数没有副作用。设计正交API时需要铭记两个重要因素：减少冗余：确保只有一种方式表示相同的信息；增加独立性：确保暴露的概念没有重叠。

(5).当需要客户销毁指针时，使用智能指针返回动态申请的对象。

(6).将资源的申请与释放当作对象的构造和析构。

(7).不要将平台相关的#if或#ifdef语句放在公共的API中，因为这些语句暴露了实现细节，并使API因平台而异。

### 2.5 松耦合：
耦合：软件组件之间相互连接的强度的度量，即系统中每个组件对其它组件的依赖程度。内聚：单个软件组件内的各种方法相互关联或聚合强度的度量。

(1).优秀的API表现为松耦合和高内聚。

(2).除非确实需要#include类的完整定义，否则应该为类使用前置声明。如果类A仅需要知道类B的名字，即它不需要知道类B的大小或调用类B的任何方法，那么类A就不需要依赖类B的完整声明。在这种情况下，可以为类B使用前置声明，而非包含整个接口，这样就降低了这两个类之间的耦合。

(3).与成员函数相比，使用非成员、非有元的方法能降低耦合度。如果情况允许，那么优先声明非成员、非友元的函数，而非成员函数，这么做在促进封装的同时还降低了这些函数和类的耦合度。

(4).有时，使用数据冗余降低类之间的耦合是合理的。尽管如此，即使是有意的冗余，也是冗余，应该小心谨慎地使用，同时添加良好的注释。

(5).管理器类可以通过封装几个低层次类降低耦合。管理器类拥有并协调几个低层次的类。可以用它打破基于一组低层次的类的一个或多个类的依赖关系。

(6).回调：在C和C++中，回调是模块A中的一个函数指针，该指针被传递给模块B，这样B就能在合适的时候调用A中的函数。模块B对模块A一无所知，并且对模块A不存在”包含”(include)或者”链接”(link)依赖。回调的这种特性使得低层代码能够执行与其不能有依赖关系的高层代码。因此，在大型项目中，回调是一种用于打破循环依赖的常用技术。

### 3. 模式

设计模式是针对软件设计问题的通用解决方案。该术语源于《设计模式：可复用面向对象软件的基础》一书。书中介绍了下列通用的 **设计模式，它们可以分成三大类**
：

### 3.1 创建型模式：

抽象工厂模式：创建一组相关的工厂。

建造者模式：将复杂对象的构建与表示分离。

工厂方法模式：将类的实例化推迟到子类中。

原型模式：指定类的原型实例，克隆该实例可以生成新的对象。

单例模式：确保类只有一个实例。

### 3.2 结构型模式：

适配器模式：将类的接口转换为另一种接口。

桥接模式：将抽象部分与它的实现部分分离，使它们都可以独立地变化。

组合模式：将对象组合成树型结构，表示”部分----整体”的层次结构。

装饰模式：动态地给一个对象添加一些额外的行为。

外观模式：为子系统中的一组接口提供统一的高层次接口。

享元模式：利用共享技术高效地支持大量细粒度的对象。

代理模式：提供另一个对象的替代物或占位符，以便控制对该对象的访问。

### 3.3 行为模式：

职责链模式：使多个接收者对象有机会处理来自发送者对象的请求。

命令模式：将请求或操作封装成对象，并支持可撤销的操作。

解释器模式：指定如何对某种语言的语句进行表示和判断。

迭代器模式：提供一种顺序访问某种聚合对象元素的途径。

中介者模式：定义一个中介对象，用于封装一组对象的交互。

备忘录模式：捕获对象的内部状态，以便将来可将该对象恢复到保存时的状态。

观察者模式：定义对象间一种一对多的依赖关系，当对象的状态发生改变时，所有依赖于它的对象都将得到通知。

状态模式：当对象内部状态改变时，对象看起来好像修改了它所属的类。

策略模式：定义一组算法并封装每个算法，使它们在运行时可以相互替换。

模板方法模式：定义某操作中算法的框架，将其中一些步骤推迟到子类中。

访问者模式：表述对某对象结构的元素所执行的操作。

设计模式更多介绍参考：<https://blog.csdn.net/fengbingchun/category_2134223.html>

测试代码cplusplus_api_design.hpp内容如下：
```prettyprint
    // Pimpl惯用法："自动定时器"，当被销毁时打印出其生存时间
    class AutoTimer {
    public:
    	explicit AutoTimer(const std::string& name);
    	~AutoTimer();
    
    	AutoTimer(const AutoTimer&) = delete;
    	AutoTimer& operator=(const AutoTimer&) = delete;
    
    private:
    	class Impl; // 私有内嵌类
    	std::unique_ptr<Impl> impl_;
    };
    
    // 单例模式
    class Singleton {
    public:
    	static Singleton& GetInstance() // 既可以返回单例类的指针也可以返回引用,当返回指针时，客户可以删除该对象，因此最好返回引用
    	{
    		static Singleton instance;
    		return instance;
    	}
    
    	Singleton(const Singleton&) = delete;
    	Singleton& operator=(const Singleton&) = delete;
    
    private:
    	Singleton() { fprintf(stdout, "constructor\n"); }
    	~Singleton() { fprintf(stdout, "destructor\n"); }
    };
    
    // 单一状态设计模式
    class Monostate {
    public:
    	int GetTheAnswer() const { return answer_; }
    
    private:
    	static int answer_; // 也可以将该静态变量声明为.cpp文件作用域的静态变量，而不是私有的类静态变量
    };
    
    // 工厂模式
    class IRenderer {
    public:
    	virtual ~IRenderer() {}
    	virtual void Render() = 0;
    };
    
    class RendererFactory {
    public:
    	IRenderer* CreateRenderer(const std::string& type);
    };
    
    // 扩展工厂模式：将派生类与工厂方法解耦并支持在运行时添加新的派生类：工厂类维护一个映射，此映射将类型名与创建对象的回调关联起来。
    // 然后就可以允许新的派生类通过一对新的方法调用来实现注册和注销。
    // 需要注意的问题是，工厂对象必须保持其状态信息。因此，最好强制要求任一时刻都只能创建一个工厂对象，这也是为何多数工厂对象也是单例的原因
    class RendererFactory2 {
    public:
    	typedef IRenderer* (*CreateCallback)();
    	static void RegisterRenderer(const std::string& type, CreateCallback cb);
    	static void UnRegisterRenderer(const std::string& type);
    	static IRenderer* CreateRenderer(const std::string& type);
    
    private:
    	typedef std::map<std::string, CreateCallback> callback_map_;
    	static callback_map_ renderers_;
    };
    
    // 代理模式：尤其适用于Original类是第三方类库
    class IOriginal {
    public:
    	virtual void DoSomething(int value) = 0;
    };
    
    class Original : public IOriginal {
    public:
    	void DoSomething(int value) override { fprintf(stdout, "Original::DoSomething\n"); }
    };
    
    class Proxy : public IOriginal {
    public:
    	Proxy() : orig_(new Original()) {}
    	~Proxy() { delete orig_; }
    
    	void DoSomething(int value) override { return orig_->DoSomething(value); }
    
    	Proxy(const Proxy&) = delete;
    	Proxy& operator=(const Proxy&) = delete;
    
    private:
    	Original* orig_;
    };
    
    // 适配器模式
    class Rectangle {
    public:
    	Rectangle() = default;
    	~Rectangle() {}
    
    	void setDimensions(float cx, float cy, float w, float h) { fprintf(stdout, "width: %f, height: %f\n", w, h); }
    };
    
    class RectangleAdapter {
    public:
    	RectangleAdapter() : rect_(new Rectangle()) {}
    	~RectangleAdapter() { delete rect_; }
    
    	void Set(float x1, float y1, float x2, float y2)
    	{
    		float w = x2 - x1;
    		float h = y2 - y1;
    		float cx = w / 2.f + x1;
    		float cy = h / 2.f + y1;
    		rect_->setDimensions(cx, cy, w, h);
    	}
    
    	RectangleAdapter(const RectangleAdapter&) = delete;
    	RectangleAdapter& operator=(const RectangleAdapter&) = delete;
    
    private:
    	Rectangle* rect_;
    };
    
    // 外观模式
    class Subsystem1 {
    public:
    	Subsystem1() = default;
    	~Subsystem1() {}
    	void Operation() { fprintf(stdout, "subsystem1 operation\n"); }
    };
    
    class Subsystem2 {
    public:
    	Subsystem2() = default;
    	~Subsystem2() {}
    	void Operation() { fprintf(stdout, "subsystem2 operation\n"); }
    };
    
    class Facade {
    public:
    	Facade() : subs1_(new Subsystem1()), subs2_(new Subsystem2()) {}
    	~Facade()
    	{
    		delete subs1_;
    		delete subs2_;
    	}
    
    	void OperationWrapper()
    	{
    		subs1_->Operation();
    		subs2_->Operation();
    	}
    
    	Facade(const Facade&) = delete;
    	Facade& operator=(const Facade&) = delete;
    
    private:
    	Subsystem1* subs1_;
    	Subsystem2* subs2_;
    };
    
    // 观察者模式
    class SubjectBase; // 抽象主题
    class ObserverBase { // 抽象观察者
    public:
    	ObserverBase(std::string name, SubjectBase* sub) : name_(name), sub_(sub) {}
    	virtual void Update() = 0;
    
    protected:
    	std::string name_;
    	SubjectBase* sub_;
    };
    
    class StockObserver: public ObserverBase { // 具体观察者，看股票的
    public:
    	StockObserver(std::string name, SubjectBase* sub) : ObserverBase(name, sub) {}
    	void Update() override;
    };
    
    class NBAObserver : public ObserverBase { // 具体观察者，看NBA的
    public:
    	NBAObserver(std::string name, SubjectBase* sub) : ObserverBase(name, sub) {}
    	void Update() override;
    };
    
    class SubjectBase { // 抽象主题
    public:
    	virtual void Attach(ObserverBase* observer) = 0;
    	virtual void Notify() = 0;
    
    public:
    	std::string action_;
    	std::vector<ObserverBase*> observers_;
    };
    
    
    class SecretarySubject : public SubjectBase { // 具体主题
    public:
    	void Attach(ObserverBase* ob) { observers_.push_back(ob); }
    
    	void Notify()
    	{
    		for (auto it = observers_.cbegin(); it != observers_.cend(); ++it) {
    			(*it)->Update();
    		}
    	}
    };
    
    int test_api_design_3();
    int test_api_design_3_pimpl();
    int test_api_design_3_singleton();
    int test_api_design_3_monostate();
    int test_api_design_3_factory();
    int test_api_design_3_factory_expand();
    int test_api_design_3_proxy();
    int test_api_design_3_adapter();
    int test_api_design_3_facade();
    int test_api_design_3_observer();
```

测试代码cplusplus_api_design.cpp内容如下：   
```prettyprint    
    class AutoTimer::Impl {
    public:
    	double GetElapsed() const
    	{
    #ifdef _WIN32
    		return (GetTickCount() - start_time_) / 1e3;
    #else
    		struct timeval end_time;
    		gettimeofday(&end_time, nullptr);
    		double t1 = start_time_.tv_usec / 1e6 + start_time_.tv_sec;
    		double t2 = end_time.tv_usec / 1e6 + end_time.tv_sec;
    		return t2 - t1;
    #endif
    	}
    
    	std::string name_;
    #ifdef _WIN32
    	DWORD start_time_;
    #else
    	struct timeval start_time_;
    #endif
    };
    
    AutoTimer::AutoTimer(const std::string& name) : impl_(std::make_unique<Impl>())
    {
    	impl_->name_ = name;
    #ifdef _WIN32
    	impl_->start_time_ = GetTickCount();
    #else
    	gettimeofday(&impl_->start_time_, nullptr);
    #endif
    }
    
    AutoTimer::~AutoTimer()
    {
    	fprintf(stdout, "%s : took %f secs\n", impl_->name_.c_str(), impl_->GetElapsed());
    }
    
    int test_api_design_3_pimpl()
    {
    	AutoTimer auto_timer("Take");
    
    	return 0;
    }
    
    int test_api_design_3_singleton()
    {
    	Singleton& singleton1 = Singleton::GetInstance();
    	Singleton& singleton2 = Singleton::GetInstance();
    
    	return 0;
    }
    
    int Monostate::answer_ = 42;
    int test_api_design_3_monostate()
    {
    	Monostate m1, m2;
    	fprintf(stdout, "answer1: %d, answer2: %d\n", m1.GetTheAnswer(), m2.GetTheAnswer());
    
    	return 0;
    }
    
    class OpenGLRenderer : public IRenderer {
    public:
    	OpenGLRenderer() { fprintf(stdout, "constructor OpenGLRenderer\n"); }
    	void Render() override { fprintf(stdout, "OpenGLRenderer::Render\n"); }
    	~OpenGLRenderer() { fprintf(stdout, "destructor OpenGLRenderer\n"); }
    };
    
    class DirectXRenderer : public IRenderer {
    public:
    	DirectXRenderer() { fprintf(stdout, "constructor DirectXRenderer\n"); }
    	void Render() override { fprintf(stdout, "DirectXRenderer::Render\n"); }
    	~DirectXRenderer() { fprintf(stdout, "destructor DirectXRenderer\n"); }
    };
    
    IRenderer* RendererFactory::CreateRenderer(const std::string& type)
    {
    	if (type == "opengl")
    		return new OpenGLRenderer();
    	if (type == "directx")
    		return new DirectXRenderer();
    
    	return nullptr;
    }
    
    int test_api_design_3_factory()
    {
    	RendererFactory factory;
    
    	IRenderer* renderer1 = factory.CreateRenderer("opengl");
    	IRenderer* renderer2 = factory.CreateRenderer("directx");
    
    	if (renderer1) {
    		renderer1->Render();
    		delete renderer1;
    	}
    
    	if (renderer2) {
    		renderer2->Render();
    		delete renderer2;
    	}
    
    	return 0;
    }
    
    RendererFactory2::callback_map_ RendererFactory2::renderers_;
    
    void RendererFactory2::RegisterRenderer(const std::string& type, CreateCallback cb)
    {
    	renderers_[type] = cb;
    }
    
    void RendererFactory2::UnRegisterRenderer(const std::string& type)
    {
    	renderers_.erase(type);
    }
    
    IRenderer* RendererFactory2::CreateRenderer(const std::string& type)
    {
    	callback_map_::iterator it = renderers_.find(type);
    	if (it != renderers_.end()) {
    		// 调用回调以构造此派生类的对象
    		return (it->second)();
    	}
    
    	return nullptr;
    }
    
    // API用户现在可以在系统中注册(以及注销)新的渲染器
    class UserRenderer : public IRenderer {
    public:
    	UserRenderer() { fprintf(stdout, "constructor UserRenderer\n"); }
    	void Render() override { fprintf(stdout, "UserRenderer::Render\n"); }
    	~UserRenderer() { fprintf(stdout, "destructor UserRenderer\n"); }
    	static IRenderer* Create() { return new UserRenderer(); }
    };
    
    int test_api_design_3_factory_expand()
    {
    	// 注册一个新的渲染器
    	RendererFactory2::RegisterRenderer("user", UserRenderer::Create);
    	// 为新的渲染器创建一个实例
    	IRenderer* r = RendererFactory2::CreateRenderer("user");
    	if (r) {
    		r->Render();
    		delete r;
    	}
    
    	return 0;
    }
    
    int test_api_design_3_proxy()
    {
    	Proxy proxy;
    	proxy.DoSomething(3);
    
    	return 0;
    }
    
    int test_api_design_3_adapter()
    {
    	RectangleAdapter rect;
    	rect.Set(10.f, 5.f, 20.f, 25.f);
    
    	return 0;
    }
    
    int test_api_design_3_facade()
    {
    	Facade facade;
    	facade.OperationWrapper();
    
    	return 0;
    }
    
    void StockObserver::Update()
    {
    	fprintf(stdout, "%s: %s, can't play stock\n", name_.c_str(), sub_->action_.c_str());
    }
    
    void NBAObserver::Update()
    {
    	fprintf(stdout, "%s: %s, can't watch NBA\n", name_.c_str(), sub_->action_.c_str());
    }
    
    int test_api_design_3_observer()
    {
    	SubjectBase* subject = new SecretarySubject();
    
    	ObserverBase* observer1 = new NBAObserver("Jack", subject);
    	ObserverBase* observer2 = new StockObserver("Tom", subject);
    
    	subject->Attach(observer1);
    	subject->Attach(observer2);
    
    	subject->action_ = "boss comes";
    	subject->Notify();
    
    	delete subject;
    	delete observer1;
    	delete observer2;
    
    	return 0;
    }
    
    
    int test_api_design_3()
    {
    	//return test_api_design_3_pimpl();
    	//return test_api_design_3_singleton();
    	//return test_api_design_3_monostate();
    	//return test_api_design_3_factory();
    	//return test_api_design_3_factory_expand();
    	//return test_api_design_3_proxy();
    	//return test_api_design_3_adapter();
    	//return test_api_design_3_facade();
    	return test_api_design_3_observer();
    }
```

### 3.4 Pimpl惯用法： 
**”pointer to implementation”(** **指向实现的指针)**
**的缩写。该技巧可以避免在头文件中暴露私有细节**
。因此它是促进API接口和实现保持完全分离的重要机制。但是Pimpl并不是严格意义上的设计模式(它是受制于C++特定限制的变通方案)，这种惯用法可以看作是桥接设计模式的一种特例。

使用Pimpl惯用法将实现细节从公有头文件中分离出来。Pimpl利用了C++的一个特点，即可以将类的数据成员定义为指向某个已经声明过的类型的指针。这里的类型仅仅作为名字引入，并没有被完整地定义，因此我们就可以将该类型的定义隐藏在.cpp文件中。这通常称为
**不透明指针，因为用户无法看到它所指向的对象细节** 。本质上，Pimpl是一种同时在逻辑上和物理上隐藏私有数据成员与函数的办法。 **Pimpl**
**惯用法将所有私有成员放置在一个类(** **或结构体)** **中，这个类在头文件中前置声明，在.cpp** **文件中定义** 。

**使用Pimpl** **惯用法时，应采用私有内嵌实现类。只有在.cpp** **文件中其它类或自由函数必须访问Impl**
**成员时，才应采用公有内嵌类(** **或公有非内嵌类)** 。

如果没有为类显式定义复制构造函数和赋值操作符，C++编译器会默认创建。但是这种默认的构造函数只能指向对象的浅复制。这不利于采用Pimpl的类，因为这意味着如果客户复制了对象，则这两个对象将指向同一个Impl实现对象。这样一来，两个对象可能在析构函数中尝试删除同一个Impl对象，这很可能导致崩溃。处理这个问题有以下两种可选方案：禁止复制类或显式定义复制语义。

注意Pimpl类的复制语义，可以使用智能指针管理指向Impl对象的指针的初始化和销毁。

Pimpl惯用法的主要缺点是，必须为你创建的每个对象分配并释放实现对象。这使对象增加了一个指针，同时因为必须通过指针间接访问所有成员变量，这种额外的调用层次与新增的new和delete开销类似，可能引入性能冲击。如果关注内存分配器的性能，那么可以考虑使用”快速Pimpl”(Fast
Pimpl)惯用法，该方法为Impl类重载了new和delete操作符，以便使用更加高效的小内存定长分配器。

### 3.5 单例： 
**单例设计模式用来确保一个类仅存在一个实例** 。该模式亦提供对此唯一实例的全局访问点。 **可以认为单例是一种更加优雅的全局变量**。单例模式要求创建一个类，它包含一个静态方法，每次调用该方法时返回该类的同一个实例。

单例是一种更加优雅地维护全局状态的方式，但始终应该考虑清楚是否需要全局状态。

将构造函数、析构函数、复制构造函数以及赋值操作符声明为私有(或受保护的)，可以实现单例模式。

**不同编译单元中的非局部静态对象的初始化顺序是未定义的** 。因此，初始化单例的途径之一是在类的方法中创建静态变量。但是该方式不是线程安全的。

使用C++创建线程安全的单例是困难的。可以考虑使用静态构造函数或API初始化函数对其进行初始化。

依赖注入是一种将对象传入到某个类中(注入)，而不是让这个类自己创建并存储该对象的技巧。依赖注入使采用了单例的代码更易于测试。

单一状态(Monostate)设计模式允许创建类的多个实例，这些实例使用相同的静态数据。如果不需要惰性初始化全局数据，或者想让类的唯一性透明化，可以考虑使用单一状态代替单例。单例仅允许创建一个实例，因此强制了唯一性的结构，而单一状态使得所有实例共享同一份数据，因此强制了唯一性的行为。

单例模式有一些替代方案，包括依赖注入、单一状态模式以及使用会话上下文。

### 3.6 工厂模式：
它是一个创建型的设计模式。它允许创造对象时不指定要创建的对象的具体类型。本质上，工厂方法是构造函数的一般化。
**从基本层面来看，工厂方法仅是一个普通的方法调用，该调用返回类的实例。但是，它们经常和继承一起使用，即派生类能够重写工厂方法并返回派生类的实例。常见的做法是使用抽象基类实现工厂模式**
。

在C++中，构造函数没有运行时动态绑定的概念，不能声明虚构造函数，没有返回值，构造函数的名字与它所在的类的名字相同。

使用工厂方法提供更强大的类构造语义并隐藏子类的细节。

### 3.7 API包装器模式：
潜在副作用是影响性能，这主要因为额外增加的一级间接寻址以及存储包装层次状态带来的开销。结构化设计模式可以处理接口包装任务。

(1).代理模式： **代理设计模式为另一个类提供了一对一的转发接口**
。代理类和原始类有相同的接口。它可以被认为是一个单一组件包装器。此模式通常的实现是，代理类中存储原始类的副本，但更可能是指向原始类的指针，然后代理类中的方法将重定向到原始类对象中的同名方法。代理提供了一个接口，此接口将函数调用转发到具有同样形式的另一个接口。

(2).**适配器模式：将一个类的接口转换为一个兼容的但不相同的接口。与代理模式的相似之处是，适配器设计模式也是一个单一组件包装器，但适配器类和原始类的接口可以不相同**
。

适配器将一个接口转换为一个兼容的但不相同的接口。

适配器可以用”组合”或者”继承”来实现。这两种类型分别称为对象适配器和类适配器。

(3).**外观模式：能够为一组类提供简化的接口。它实际上定义了一个更高层次的接口，以使得底层子系统更易于使用。外观模式和适配器模式的区别是，外观模式简化了类的结构，而适配器模式仍然保持相同的类结构**
。

外观模式为一组类提供了简化的接口。在封装外观模式中，底层类不再可访问。

### 3.8  观察者模式：
**支持组件解耦且避免了循环依赖** 。

模型--视图--控制器(Model-View-Controller,
MVC)架构：此模式要求业务逻辑(Model)独立于用户界面(View)，控制器(Controller)接收用户输入并协调另外两者。MVC架构模式促使核心业务逻辑(或者说模型)与用户界面(或者说视图)分离。它还隔离了控制器逻辑，控制器逻辑会引起模型的改变并更新视图。

**观察者模式是”** **发布/** **订阅”** **范式(Publish/Subscribe** **，简写做pub/sub)**
**的一个具体实例。这些技术定义了对象之间一对多的依赖，使得发布者对象能够将它的状态变化通知给所有的订阅对象，而又不直接依赖于订阅者对象**
。实现观察者模式的典型做法是引入两个概念：主题(Subject)和观察者(Observer)，也称作发布者和订阅者。一个或多个观察者注册主题中感兴趣的事件，之后主题会在自身状态发生变化时通知所有注册的观察者。

## 4. 设计

### 4.1 良好设计的例子：

### 4.2 收集功能性需求：
软件产业中的需求可以分为不同的类型，包括如下几种：(1).业务需求：即软件如何满足组织的需求。(2).功能性需求：即软件应该完成什么功能。(3).非功能性需求：描述软件必须达到的质量标准。

功能性需求规定了API如何表现。

功能性需求一般用需求文档来管理，其中每个需求都有一个唯一的标识符和一个描述信息。好的功能性需求应该是简单、易读、清晰、可验证的，而且没有开发术语。

### 4.3 创建用例：
用例从用户的角度描述API的需求。

用例可以是面向目标的简短描述信息的简要列表，也可以是更为正式的模板定义的结构化说明。

用户故事是敏捷开发过程中一种从用户那获得最小需求的方法。用户故事是一个高层的需求概念,它仅包含了足够的信息,开发者可以利用这些信息对实现用户故事所需要付出的努力给出一个合理的评估。

### 4.4 API设计的元素：
产生好的API设计的秘诀是：对问题领域进行合理抽象，然后设计相应的对象与类的层次结构来表达该抽象。抽象是关于一些事务的简单描述，不需要对编程实现有任何了解即可理解。

API设计包括开发顶层架构和详细的类层次结构。

### 4.5 架构设计：
软件架构描述了一个完整系统的很粗糙的结构：API中顶层对象的集合以及它们彼此之间的关系。

架构设计被众多独特的组织、环境和运行等因素约束。有些约束是可以协商的。总是为变化而设计。变化是不可避免的。

API的关键对象并不容易识别，请尝试从不同的角度看待问题，并不断迭代和完善你的模型。

要避免API各个组件间的循环依赖。

在API的附属文档中要描述其高层架构并阐述其原理。

### 4.6 类的设计：
要集中精力设计定义了API 80%功能的20%的类。

避免深度继承层次结构。

除非使用接口和mixin类，否则要避免多重继承。组合优先于继承。

Liskov替换原则(Liskov Substitution Principle,
LSP)指出，在不修改任何行为的情况下用派生类替换基类，这应该总是可行的。

开放--封闭原则(Open/Closed Principle, OCP)：API应该为不兼容的接口变化而关闭，为功能扩展而开放。

迪米特法则(Law of Demeter, LoD)指出，你应该只调用自己类或直接相关对象的函数。

### 4.7 函数设计：
避免过长的参数列表。对于有很多可选参数的函数，你可以考虑通过传递结构体(struct)或映射(map)来代替。

API中处理错误条件的三种主要方式是：返回错误码；抛出异常；中止程序。使用一致的、充分文档化的错误处理机制。

如果你选择在代码中使用异常来通知意外情况，从std::exception派生自己的异常，并定义what()方法来描述失效信息。

在出现故障时，让API快速干净地退出，并给出完整精确的诊断细节。

## 5. 风格

### 5.1 纯C API：

为实现更严格的类型检查，并确保C++程序可以使用你的API，请尝试用C++编译器来编译C API。 **相对于** **C** **编译器而言，C++**
**编译器的类型检查更为严格** 。

**C** **函数的链接方式与C++** **函数不同** 。也就是说，同样的函数，如果由C和C++编译器分别生成目标文件，该函数的表示是不同的。将C
API包装在一个extern
“C”构造块中，这会告诉C++编译器，构造块中的函数应该使用C风格的链接方式。C编译器不能解析该语句，所以最好使用条件编译，使之仅支持C++编译器。

**在C API** **的头文件中使用extern “C”** **限制，以便于C++** **程序能正确地编译和链接C API** 。

### 5.2 面向对象的C++ API：
面向对象API允许使用对象而非动作来建模软件，同时也带来了继承和封装等优点。

### 5.3 基于模板的API：
模板是C++的一个特性，它支持使用泛化的、尚未具体指定的类型编写函数或类。然后，你可以用具体类型实例化这些泛化的类型，以此实现模板的特化。模块可以在编译时执行一些工作，进而改进运行时性能。

### 5.4 数据驱动型API：
数据驱动型程序指的是：通过每次运行时提供不同的输入数据，它可以指向不同的操作。程序的业务逻辑能够抽象到可以由人来编辑的数据文件中去。利用这种方法，可以在不重新编译可执行文件的情况下改变程序的行为。

数据驱动型API可以很好地映射到Web服务和其他客户端/服务器形式的API，它们也支持数据驱动型测试技术。

## 6. C++用法

### 6.1 命名空间：
是若干唯一符号的逻辑分组。它提供了一种避免命名冲突的方法，以防两个API使用相同的名字定义符号。任何时候都不应该在公用API头文件的全局作用域内使用using关键字。

应当始终使用一致的前缀或C++的namespace关键字为API符号提供命名空间。

### 6.2 构造函数和赋值：
如果类分配了资源，则应该遵循”三大件”(Big Tree)规则，同时定义析构函数、复制构造函数和赋值操作符。

**考虑在只带有一个参数的构造函数的声明前使用explicit** **关键字** ，用于阻止构造对象时特定的构造函数被隐式调用。

### 6.3 const正确性：
是指使用C++的const关键字将变量或者方法声明为不可变的。

尽可能早地将函数和参数声明为const。过后修正API中的const正确性会既耗时又麻烦。

当向const函数传入引用或指针时，也要考虑该参数是否可以声明为const。

**返回函数结果时，将结果声明为const** **的主要理由是其引用了对象的内部状态** 。首选传值方式而不是const引用方式返回函数的结果。

### 6.4 模板：
在编译时生成代码，它们对生成大量形式相似但只类型不同的代码尤其有用。

如果只需要一些确定的特化集合，那么尽量选择显式模板实例化。这样做可以隐藏私有细节并降低构建时间。

### 6.5 操作符重载：
应当只在有意义的地方使用操作符重载，即只在那些API用户看起来很自然、不会违反最小意外原理(rule of the least surprise)的地方使用。这指的是应该保持操作符的自然语义。

除非操作符必须访问私有成员或受保护的成员，或它是=、[]、->、->*、()、(T)、new、delete其中之一，否则应该尽量将其声明为自由函数。

转换操作符用于定义将对象自动转换成不同类型的对象。转换操作符没有指定返回值类型，这是因为编译器可以根据操作符名字推断出类型，而且转换操作符没有参数。给类添加转换操作符，从而利用自动类型强制转换。

### 6.6 函数参数：
尽量在可行的地方为输入参数使用const引用，而非指针。对于输出参数，考虑使用指针而不是非const引用，以便显式地向客户表面它们可能被修改。

当默认值会暴露实现中的常量时，尽量选择函数重载，而不是默认参数。尽量避免定义涉及构造临时对象的默认参数，因为这些参数会以传值的方式传递给方法，开销很大。

### 6.7 避免使用#define定义常量：
#define预处理指令本质上是用一个字符串替换源码中的另一个字符串。

使用静态const数据成员而非#define表示类常量。

### 6.8 避免使用友元：
在C++中，友元是一个类向另外一个类或函数授予其完全访问特权的方法。友元类或函数可以访问类中所有受保护成员和私有成员。

避免使用友元，它往往预示着糟糕的设计，这就等于赋予用户访问API所有受保护成员和私有成员的权限。

### 6.9 导出符号：
应该显式导出公有API的符号，以便维持对动态库中类、函数和变量访问性的直接控制。对于GNU
C++，可以使用__fvisibility_hidden选项。

使用内部链接以便隐藏.cpp文件内部的、具有文件作用域的自由函数和变量。也就是说，使用static关键字或匿名命名空间。

### 6.10 编码规范：
为API指定编码风格标准，这有助于保证一致性、明确流程，并总结常见的工程陷阱。

## 7. 性能

不要以扭曲API的设计为代价换取高性能。关于API的性能有几个主题：编译时速度、运行时速度、运行时内存开销、库的大小、启动时间。

如果没有必要使用dynamic_cast操作符，那么常见的做法是关闭运行时类型信息(RTTI)。GNU C++编译器的选项是-fno-rtti。

为优化API，应使用工具收集代码在真实运行实例中的性能数据，然后把优化精力集中在实际的瓶颈上。不要猜测性能瓶颈的位置。

### 7.1 通过const引用传递输入参数：
应该通过const引用而非传值方式传递不会改变的对象。这样可以避免创建和销毁对象的临时副本，及副本中所有的成员和继承对象的内存与性能开销。
**这条规则仅适用于对象，对于诸如** **int** **、bool** **、float** **、double** **及char**
**等内建类型不适用，因为它们以及很小了，能够放进CPU** **寄存器**
。此外，STL迭代器和函数对象是采用传值方式的，也不适用这条规则。对于用户自定义类型，应该尽量使用引用或const引用。

### 7.2 最小化#include依赖：
编译一个大工程需要花费的时间很大程度上取决于#include文件的数量和深度。如此而论，缩短构建时间的一种常见技巧是尝试减少头文件中#include语句的数量。

前置声明可以在下列几种情况下使用：(1).不需要知道类的大小；(2).没有引用类的任何成员方法；(3).没有引用类的任何成员变量。

一般来说，只有在自己的类中将某个类的对象作为数据成员使用时，或者需要继承某个类时，才应该包含那个类的头文件。

仅仅前置声明你自己API中的符号。使用前置声明，意味着你非常清楚符号在被省略的头文件中是怎样声明的。

不管使用前置声明还是#iniclude方式，头文件应该#include或者前置声明其所有的依赖项。

考虑在头文件中加入冗余的#include警戒语句，为客户优化编译时间：
``` 
    #ifndef XXXX_H
    #include “xxxx.h”
    #endif
```
### 7.3 声明常量：
应使用extern声明全局作用域的常量，或者在类中以静态const方式声明常量，然后在.cpp文件中定义常量值。这样就减少了包含这些头文件的模块的目标文件大小。更可取的方法是将这些常量隐藏在函数调用背后。

constexpr关键字可以用来标识已知为恒定不变的函数或变量，以便编译器执行更好的优化。

### 7.4 初始化列表：
使用构造函数初始化列表，从而为每个数据成员减少一次调用构造函数的开销。这些应在.cpp文件中声明，以便隐藏实现细节。成员变量在构造函数的函数体调用之前构造。

### 7.5 内存优化：

(1).根据成员变量类型对它们加以聚集，从而优化对象大小。

(2).考虑使用位域进一步压缩对象，但是要注意它对性能的影响。

(3).使用大小明确的类型(比如int32_t和uint16_t)指定变量所需的最大位数。

### 7.6 除非需要，勿用内联：
避免在公有头文件中使用内联代码，除非能证明代码会导致性能问题，并确认内联可以解决该问题。

### 7.7 写时复制：
节省内存最好的办法之一是到确实需要时再分配。这是写时复制技巧的本质目标。其方法是允许所有客户共享一份唯一的资源，直到他们中的一个需要修改这份资源为止。只有在那个时间点才会构造副本----这是”写时复制”名字的来由。使用写时复制语义，为对象的多份副本减少内存消耗。

### 7.8 迭代元素：
STL解决这个问题的方法是使用迭代器。迭代器可以对容器中的部分或全部元素进行遍历。采用迭代器模型遍历简单的线性数据结构。对于链表或树型数据结构，如果迭代器性能很重要，那么应该考虑使用数组引用。

### 7.9 性能分析：
工具：Gprof、OProfile、Open SpeedShop、CodeProphet
Profiler、Valgrind、Coverity、MALLOC_CHECK_。

## 8. 版本控制

### 8.1 版本号：
API的每次发布都应该附带一个唯一的标识符，以便其最新版本能够与之前版本有所区别。标准的做法是使用版本号。

大多数方案通过使用一系列数字反映一个发布版本的修改程度，这些数字通常用英文句点(.)分隔： **主版本号.次版本号.补丁版本号** 。

软件通常在最终版本发布之前就提供给用户使用，这些情况下，通常可以在版本号序列后添加一个符号用来表面软件开发过程的相关阶段。例如1.0.0alpha是指alpha发布，1.0.0b是指beta发布，而1.0.0rc是指候选发布版。

在库名中包含API的主版本号是良好的编程实践，尤其是在做了一些不能向后兼容的修改时，例如libFoo.so、libFoo2.so或libFoo3.so。

API的版本信息应该可以在代码中访问，以便允许客户以API版本号为条件编写代码。

### 8.2 软件分支策略：
每个软件项目都需要一条”主干”代码路线，它是项目源代码的持久存储库。对于每次版本发布，或者必须与下次发布区分的开发工作，可由主干代码派生出分支。

只在必要时再分支，尽量延迟创建分支的时机。尽量使用分支代码路线而非冻结代码路线。尽早且频繁地合并分支。

在创建相同API的Basic和Advanced分发版时，在所有生成文件的版本号里带上”Basic”或”Advanced”字符串。不要试图仅使用版本号识别文件是由Basic还是Advanced
API生成的。

### 8.3 API的生命周期：
API发布后，可以改进(evolve)但不应改变(change)。

### 8.4 兼容性级别：

向后兼容性可以定义为API提供与上一个版本相同的功能。换句话说，如果一个API不需要用户作出任何改变就能够完全取代上一个版本的API，那么它就是向后兼容的。这暗示了新版本API是旧版本API的超集。可以添加新的功能，但是不能对旧API定义的已有功能做不兼容的修改。

向后兼容性有不同的类型，包括：功能兼容性、源代码(或API)兼容性、二进制(或ABI)兼容性。

**向后兼容性意味着使用第N** **版本API** **的客户代码能够不加修改地升级到第N+1** **版本** 。

功能兼容性意味着第N+1版本API的行为和第N版本一致。

源代码兼容性意味着用户使用第N版本API编写的代码可以使用第N+1版本进行编译，而不用修改源代码。

二进制兼容性意味着使用第N版本API编写的应用程序可以仅通过替换或重新链接API的新动态链接库，就升级到第N+1版本。

有助于实现二进制兼容性的技巧：

(1).不要给已有方法增加参数，可以定义该方法新的重载版本。

(2).Pimpl模式可以用来帮助保持接口的二进制兼容性，因为它把那些将来很可能发生变化的实现细节移进了.cpp文件，使之不会影响公有的.h文件。

(3).采用纯C风格的API可以更容易地获得二进制兼容性。为了利用C和C++各自的优势，可以选择使用面向对象的C++风格开发API，然后用纯C风格封装C++
API。

(4).如果确实需要做二进制不兼容的修改，那么可以考虑为新库换个不同的名字，这样就不会破坏已有的应用程序。

向前兼容性：使用未来版本API编写的客户代码如果无须修改就能够编译使用较老版本的API，则API是向前兼容的。因此，向前兼容性意味着用户可以降级到之前的发布版本，代码无须修改，仍然能够正常工作。

**向前兼容性意味着使用第N** **版本API** **的客户代码可以不加修改地降级使用第N-1** **版本** 。

### 8.5 怎样维护向后兼容性：

在API初始版本发布后，不要为抽象基类添加新的纯虚成员函数。

大多数编译器提供了将类、方法或变量标记为”已弃用”的方法，只要程序访问了带有这种标记的符号，就会输出编译时警告。对于Visual Studio
C++，可在方法声明前添加__declspec(deprecated)前缀，而对于GNU
C++编译器，可使用__attribute__((deprecated))。

### 8.6 API审查：
在发布API新版本前，引入API审查过程，以便检查所有修改。

## 9. 文档

编写API文档的一种最简单的方式是使用工具自动地从头文件中提取注释来构建。在这类工具中最为流行且特性完善的一款是Doxygen。

### 9.1 编写文档的理由：
良好的文档描述了如何使用API，并会解释API对不同输入所产生的行为。在实现每个组件时编写API文档。在API完成后对文档进行修订。

契约编程(contractprogramming)核心观点是：软件组件为它实现的服务提供一种契约或责任。通过使用组件，客户同意契约中的条款。契约编程意味着为函数的前置条件、后置条件，以及类的不变式编写文档。

应当为API的每个公有元素编写文档，包括每个类、函数、枚举、常量及typedef。如果客户可以访问某个元素，那就应该告诉客户它是什么和怎样使用。

### 9.2 文档的类型：
使用自动生成文档的工具，从头文件注释中提取API文档。

除了自动生成的API文档以外，还应该由人工编写提供有关API更高层次信息的文档。这通常是一份概述，内容包括API能做什么、用户为什么应当关注它等。

首次发布之后，每个版本都应该包含发布说明。它告诉用户自上次发布以来有哪些改动。发布说明通常是一份简洁的文档。

在发布API时，始终应该说明API所使用的授权方式。这让客户知道你授予他们哪些权利以及他们具有什么义务。应当明确指定API的授权条款。

### 9.3 文档可用性：
索引页、一致的视图和体验、代码示例、图表、搜索、面包屑导航、术语。

### 9.4 使用Doxygen：
Doxygen是一种基于源代码中编写的注释自动生成多种格式API文档的实用工具。它支持多种语言，包括C、C++、Objective-C、Java、Python等。它能够生成多种格式的输出，包括HTML、LaTex、PDF、XML以及其它格式。Doxygen是开源的(以GNU通用公共许可证授权发布)，提供若干种平台的二进制发布包，包括Windows、Linux和Mac。

Doxygen可自由配置。Doxygen支持多种不同风格的注释。

Doxygen使用介绍可参考：<https://blog.csdn.net/fengbingchun/article/details/104734578>

## 10. 测试

为了确保不破坏用户程序，编写自动化测试是所能采取的措施中最重要的一项。

### 10.1 编写测试的理由：
自动化测试可以帮助你确定是否正在构建正确的东西(也称为确认)，以及是否构建正确(也称为验证)。

### 10.2 API测试的类型：
常见的软件测试活动分为：白盒测试：测试是在理解源代码的基础上进行的。黑盒测试：测试是基于产品说明进行的，而不关心任何实现细节。灰盒测试：白盒测试和黑盒测试的组合，其中黑盒测试是在获知软件实现细节的基础上进行的。

API测试应该组合使用单元测试和集成测试。也可以适当运用非功能性技术测试，比如性能测试、并发测试和安全性测试。

单元测试是一种白盒测试技术，用于独立验证函数和类的行为。如果代码依赖于不可靠的资源，比如数据库、文件系统或网络，那么可以使用桩对象或模拟对象创建更健壮的单元测试。

集成测试是一种黑盒技术，用于验证几个组件的交互过程。

对关键用例进行性能测试，有助于避免无意引入的速度问题或内存损失。如果API的性能很重要，考虑为关键用例编写性能测试，以避免引入性能损失。

### 10.3 编写良好的测试：

良好测试的特征：快速、稳定、可移植、高编码标准、错误可重现。

测试API的关键技术：条件测试、等价类、边界测试、参数测试、返回值断言、getter/setter对、操作顺序、回归测试、负面测试、缓冲区溢出、内存所有权、空输入。

### 10.4 编写可测试的代码：
不要等到最后再测试API。

**测试驱动开发(Test-Driven Development, TDD)**
**或者测试优先编程，指在编写实现功能的代码之前，先编写自动化测试验证代码功能**
。测试驱动开发指首先编写单元测试，然后编写代码使测试通过。这可以让你专注于API的主要用途。

桩对象和模拟对象都返回封装好的响应，但是模拟对象还会验证调用行为。

断言是一种用于验证代码所做假设的办法。你可以将假设信息编码到断言函数或宏中。表达式值为真时，一切正常，不会有任何事情发生。而表达式值为假时，则代码所做的假设无效，程序会中止并抛出相应的错误信息。使用断言记录和验证那些绝不应该发生的程序设计错误。

通过对断言的系统性使用，强制保证接口契约。对接口而不是实现执行契约检查。

**国际化(i18n)** **是使软件产品支持不同语言和区域差异的过程。相关术语”** **本地化”(i10n)**
**是指基于基本的国际化支持，把应用程序的文本翻译成特定的语言，并为特定区域定义区域设置，如日期格式或货币符号** 。

### 10.5 自动化测试工具：
分为4类：自动化测试框架、代码覆盖率工具、缺陷跟踪系统、持续构建系统。

自动化测试框架：CppUnit、Boost Test、Google Test、TUT(Template Unit Test)。

代码覆盖率：这些工具能够帮助你精确地发现代码中哪些语句被测试过，也就是说，这些工具可以帮你快速定位没有被测试覆盖到的代码。Bullseye
Converage、Rational PureConverage、Intel Code-Converage Tool、Gconv。

缺陷跟踪系统是一种能够在数据库中持续跟踪软件项目缺陷(通常也包括建议)的应用程序。缺陷跟踪系统不是故障通知单(trouble
ticket)系统或问题跟踪系统，这两个系统是用来接收用户反馈的客户支持系统，提出的很多问题都不是软件本身的问题。客户支持系统发现的有效软件问题会进入缺陷跟踪系统，并指派给一名工程师去解决。缺陷跟踪系统也不是任务或项目管理工具，也就是说，它不是跟踪任务和计划工作的系统。不过，有些缺陷跟踪系统厂商也提供了互补产品，使用底层基础设施来支持项目管理工具。这些工具包括：Bugzilla、JIRA。

持续构建系统是一套自动化过程：一旦向版本控制系统提交了新修改，就会重新构建软件。包括：Tindexrbox、Parabuild、TeamCity、Electric
Cloud。

## 11. 脚本化

### 11.1 添加脚本绑定：
脚本绑定提供了一种从脚本语言访问C++
API的方式。这通常涉及为C++的类和函数创建包装代码，然后利用脚本语言自身的模块加载特性将其导入到脚本语言之中，比如Python可以使用import关键字导入包装代码，Ruby可以使用require。

### 11.2 脚本绑定技术：
Boost Python：也写作boost::python或Boost.Python，是一个C++库，它支持C++
API与Python的互操作。它是Boost库中的一部分。利用Boost
Python，你可以在C++代码中以编程的方式创建绑定，然后将该绑定与Python和Boost
Python库链接起来。这会产生一个能够直接导入到Python中的动态库。

WING：是一个开源的实用工具，可以用于在多种不同的高级语言中创建对C和C++接口的绑定。

SIP：是一个工具，它支持为Python创建C和C++绑定。

COM(Component Object Model, 组件对象模型)：是一个二进制接口标准，它支持对象通过进程间通信机制进行彼此之间的交互。

### 11.3 使用Boost Python添加Python绑定：务必确认编译脚本绑定时使用的Python版本与构建Boost Python库时使用的是一致的。

### 11.4 使用SWIG添加Ruby绑定：

## 12. 可扩展性

所谓可扩展性，指的是对于客户的特定需求，在无需改进API的情况下，客户自行修改接口的行为。

### 12.1 通过插件扩展：
在最常见的场景下，同样是动态库，插件并不是在构建时链接的，而是在运行时发现并加载的。因此， **用户可以利用你定义好的插件**
**API** **来编写自己的插件。插件库是一个动态库，它可以独立于核心API**
**编译，在运行时根据需要显示加载。不过插件也可以使用静态库，比如在嵌入式系统中，所有插件都是在编译时静态链接到应用程序中的**
。对插件而言，确保以下两点是有帮助的：一是插件要能在运行时找到，二是插件应该使用与主程序相同的构建环境。

一般而言，如果要创建插件系统，有两个主要特性是必须要设计的：

(1).插件API：要创建插件，用户必须编译并链接插件API。这里将它与核心API区分开来，后者是个规模更大的代码库，插件系统就是添加在核心API之中的。

(2).插件管理器：这是核心API代码中的一个对象(一般设计为单例)，负责管理所有插件的声命周期，即插件的加载、注册和卸载等各个阶段。该对象也叫做插件注册表。插件API是你提供给用户的用于创建插件的接口。

你总是可以引入自己的插件文件扩展名。例如，Adobe Illustrator使用的插件扩展名是.aip，而Microsoft
Excel插件的扩展名是.xll。

C++插件示例参考：<https://blog.csdn.net/fengbingchun/article/details/105104659>

### 12.2 通过继承扩展：
继承是用于扩展类的主要面向对象机制。用户可以基于API中现有的类类定义新类并修改其功能。

在定义派生类时，你只应该考虑提供了虚析构函数的类。

访问者模式：其核心目标是允许客户遍历一个数据结构中的所有对象，并在每个对象上执行给定操作。本质上，该模式是对向现有类中添加虚方法这一方式的模仿。客户能够有效地将它们自己的方法插入到你的类层次结构中。

测试代码cplusplus_api_design.hpp内容如下：
```prettyprint
    class Element;
    
    // 访问者模式: 表示一个作用于某对象结构中的各元素的操作. 它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作
    // 访问者模式使得易于增加新的操作: 访问者使得增加依赖于复杂对象结构的构件的操作变得容易. 仅需增加一个新的访问者即可在一个对象结构上定义一个新的操作
    class Visitor {
    public:
    	virtual ~Visitor() = default;
    	virtual void VisitConcreteElementA(Element* element) = 0;
    	virtual void VisitConcreteElementB(Element* element) = 0;
    
    protected:
    	Visitor() = default;
    };
    
    class ConcreteVisitorA : public Visitor {
    public:
    	ConcreteVisitorA() = default;
    	~ConcreteVisitorA() = default;
    
    	void VisitConcreteElementA(Element* element) override
    	{
    		fprintf(stdout, "I will visit ConcreteElementA ...\n");
    	}
    
    	void VisitConcreteElementB(Element* element) override
    	{
    		fprintf(stdout, "I will visit ConcreteElementB ...\n");
    	}
    };
    
    class ConcreteVisitorB : public Visitor
    {
    public:
    	ConcreteVisitorB() = default;
    	~ConcreteVisitorB() = default;
    
    	void VisitConcreteElementA(Element* element) override
    	{
    		fprintf(stdout, "I will visit ConcreteElementA ...\n");
    	}
    
    	void VisitConcreteElementB(Element* element) override
    	{
    		fprintf(stdout, "I will visit ConcreteElementB ...\n");
    	}
    };
    
    class Element {
    public:
    	virtual ~Element() = default;
    	virtual void Accept(Visitor* visit) = 0;
    
    protected:
    	Element() = default;
    };
    
    class ConcreteElementA : public Element {
    public:
    	ConcreteElementA() = default;
    	~ConcreteElementA() = default;
    
    	void Accept(Visitor* visit) override
    	{
    		fprintf(stdout, "visiting ConcreteElementA ...\n");
    		visit->VisitConcreteElementA(this);
    	}
    };
    
    class ConcreteElementB : public Element {
    public:
    	ConcreteElementB() = default;
    	~ConcreteElementB() = default;
    
    	void Accept(Visitor* visit) override
    	{
    		fprintf(stdout, "visiting ConcreteElementB ...\n");
    		visit->VisitConcreteElementB(this);
    	}
    };
    
    int test_api_design_12_vistor();
    int test_api_design_12();
```
测试代码cplusplus_api_design.cpp内容如下：
```prettyprint
    int test_api_design_12_vistor()
    {
    	Visitor* visit = new ConcreteVisitorA();
    	Element* element = new ConcreteElementA();
    	element->Accept(visit);
    
    	delete visit;
    	delete element;
    
    	return 0;
    }
    
    int test_api_design_12()
    {
    	return test_api_design_12_vistor();
    }
```
### 12.3 通过模板扩展：
当使用模板编程时，扩充接口的默认方式是利用具体类型来特化模板。

## 附录A：

### A.1 静态库与动态库

静态库：静态库中包含目标代码，这些目标代码会被链接到最终用户的应用程序，并成为其中的一部分。静态库也称为存档文件，因为它本质上就是一些编译后目标文件组成的包。一般来说，在Unix或Mac
OS X机器上，静态库以.a为文件扩展名，而在Windows上，其文件扩展名为.lib。注意， **静态库中只有真正用到的目标文件才会被复制到应用程序中**
。

动态库：动态库在编译时链接，以便解析未定义引用；然后随客户的面向最终用户的应用程序一起发布，这样应用程序就能在运行时加载库的代码了。这通常要求利用最终用户机器上动态链接器在运行时判定并加载所有依赖的动态库，执行必要的符号重定位，然后将控制传给应用程序。比如说，Linux的动态链接器是ld.so，而Mac的动态链接器是dyld。一般而言，动态链接器支持很多环境变量，可以通过这些环境变量修改或调试其行为。

**动态库有时称为共享库，因为它们可以被多个程序共享**
。在不同的平台上，共享库的文件扩展名也有所不同，比如说Unix上是.so文件，Windows上是.dll文件，而Mac OSX上则是.dylib文件。

为了给客户更大的灵活性，在发布时，你应该首选动态库形式。如果库相当小，且非常稳定，你也可以提供一个静态库版本。

### A.2 Windows上的库：
在Windows上，静态库用.lib文件表示，而动态库用.dll文件表示。此外，每个.dll文件必须有一个相应的导入库，或.lib文件。导入库用来将引用解析为DLL中导出的符号。

DLL入口点：DLL可以提供一个可选的入口点函数，线程或进程加载DLL时可以用它初始化数据结构，卸载DLL的时候也可以用它清理内存。这是由DllMain()函数管理的，你可以定义并将其导出DLL。如果入口函数的返回值为FALSE，则是一个致命错误，应用程序会启动失败。

在Windows上加载插件：在Windows平台上，LoadLibrary()或LoadLibraryEx()函数用以将动态库加载到进程中，GetProcAddress()函数用以获取DLL中的导出符号的地址。注意，以这种方式加载动态库时，就不需要.lib导入库文件了。

### A.3 Linux上的库：

在Linux上创建静态库：在Linux上，静态库就是一个简单的存档文件，其中包含的是目标文件。利用Linux的ar命令，可以将一些目标文件编译到一个静态库中。

在Linux上创建动态库：利用GNU C++编译器，只要使用-shared链接器选项即可生成.so文件，而非可执行文件。你应该让编译器生成位置无关的代码(PIC)，不过这在有些平台上并非默认行为，因此需要使用命令行选项-fpic或-fPIC来指导编译器。这是必要的，因为当不同的可执行程序用到同一个动态库时，库中的代码可能会被加载到不同的内存位置。因此，为共享库生成PIC代码很重要，这样用户代码就不需要依赖于符号的绝对内存地址了。

如果在同一个目录中同时存在静态库和动态库，它们的基本文件名又是相同的，如libmyapi.a和libmyapi.so，这时编译器会选择动态库(除非使用-static选项，要求只使用静态库)。

**注意：在动态库中，所有代码本质上都被扁平化地组织到一个目标文件中了。这与静态库不同，因为静态库表示为一组目标文件的集合，其中的目标文件会根据需要复制到可执行程序中(**
**也就是说，对于静态存档文件中的目标文件，如果没有引用到，就不会复制到可执行程序映像中)** **。其结果就是，加载动态库时会将.so**
**文件中定义的所有代码都加载进来** 。

在Linux上加载插件：在Linux平台上，你可以调用dlopen()函数将.so文件加载到当前进程中，然后用dlsym()函数访问库中的符号。

### A.4 Mac OS X上的库：

在Mac OS X上创建静态库：Linux上创建静态库的方法也可以用于Mac OS
X。不过，在将静态库链接到应用程序时，有些行为是不同的。苹果公司并不鼓励使用-static编译器选项以静态链接所有依赖库的方式来生成可执行程序。基本上，Mac上的-static选项是为构建内核而保留的。Mac链接器默认情况下会扫描库查找路径中的所有位置来查找动态库。如果查找失败，链接器随后会再次扫描这些位置来寻找静态库。

在Mac OSX上创建动态库：与Linux上用到的指令非常相似。不过有一项重要的区别：在Mac上创建动态库时，要使用g++的-dynamiclib选项代替-shared。

**GitHub** ：<https://github.com/fengbingchun/Messy_Test>
