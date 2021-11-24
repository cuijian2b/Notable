---
tags: [JSON]
title: json与C++类互转
created: '2021-11-19T07:47:05.322Z'
modified: '2021-11-19T07:48:10.635Z'
---

# json与C++类互转

## 背景与需求

之前写C#的时候，解析json字符串一般使用的是开源的类库Newtonsoft.Json，方法十分简洁，比如：

    
    
    class Project
    {
        public string Input { get; set; }
        public string Output { get; set; }
    }
    JavaScriptSerializer serializer = new JavaScriptSerializer();
    Project test = serializer.Deserialize<Project>(@"{"Input":"1","Output":"2"}");
    

一行代码就能将json字符串转为相应的类对象。

最近写C++需要处理一下json数据，于是上github找了很多很强大的开源库，像[jsoncpp](https://github.com/open-source-parsers/jsoncpp)、[rapidjson](https://github.com/Tencent/rapidjson)、[json](https://github.com/nlohmann/json)，基本上都满足了开发需求，但想要做成像写C#那样子就要做二次开发。于是有了自己写一个简单的json转类
| 结构体的工具的想法。

需求如下：

  * 只有头文件，方便使用
  * 最多三行代码解决转换
  * 支持类|结构体 与 json的相互转换
  * 支持多种基本数据类型，如int、float、string、bool等
  * 支持STL基本类型，如vector、list、map<string,T>等
  * 支持嵌套关系
  * 支持成员重命名，比方说json中的关键字是name,成员命名可写成Name或其他。

## 最终使用的样例代码

    
    
    class Student
    {
    public:
        string Name;
        int Age;
    
        AIGC_JSON_HELPER(Name, Age)//成员注册
        AIGC_JSON_HELPER_RENAME("name","age")//成员重命名，不需要可以删除这条
    };
    
    int main()
    {
        //json转类对象
        Student person;
        JsonHelper::JsonToObject(person, R"({"name":"XiaoMing", "age":15})");
    	//类对象转json
        string jsonStr;
        JsonHelper::ObjectToJson(person, jsonStr);
        return 0;
    }
    

## 实现方法

因为刚好[rapidjson](https://github.com/Tencent/rapidjson)只需要头文件就可以使用，所以选择了[rapidjson](https://github.com/Tencent/rapidjson)作为基础库，进行二次开发。

### 基础类型的转换

作为最底层的接口，只需要进行一个赋值的操作即可，后续如果想要增加一些其他类型支持，添加起来也比较方便。

    
    
    static bool JsonToObject(int &obj, rapidjson::Value &jsonValue)
    {
        if (jsonValue.IsNull() || !jsonValue.IsInt())
            return false;
        obj = jsonValue.GetInt();
        return true;
    }
    
    static bool JsonToObject(unsigned int &obj, rapidjson::Value &jsonValue)
    {
        if (jsonValue.IsNull() || !jsonValue.IsUint())
            return false;
        obj = jsonValue.GetUint();
        return true;
    }
    
    static bool JsonToObject(int64_t &obj, rapidjson::Value &jsonValue)
    {
        if (jsonValue.IsNull() || !jsonValue.IsInt64())
            return false;
        obj = jsonValue.GetInt64();
        return true;
    }
    
    //其他类型... ...
    

### 类成员注册

这里使用宏定义方式 + 可变参数模板的方式来实现，即可依次对注册的成员进行赋值

    
    
    template <typename TYPE, typename... TYPES>
    static bool WriteMembers(std::vector<std::string> &names, int index, rapidjson::Value &jsonValue, TYPE &arg, TYPES &... args)
    {
        if (!WriteMembers(names, index, jsonValue, arg))
            return false;
        return WriteMembers(names, ++index, jsonValue, args...);
    }
    
    template <typename TYPE>
    static bool WriteMembers(std::vector<std::string> &names, int index, rapidjson::Value &jsonValue, TYPE &arg)
    {
        const char *key = names[index].c_str();
        if (!jsonValue.HasMember(key))
            return true;
    
        if (!JsonToObject(arg, jsonValue[key]))
            return false;
        return true;
    }
    
    
    
    #define AIGC_JSON_HELPER(...)  \
    bool AIGC_CONVER_JSON_TO_OBJECT(rapidjson::Value &jsonValue, std::vector<std::string> &names) \
    {     \
        if (names.size() <= 0)  \
            names = aigc::JsonHelper::GetMembersNames(#__VA_ARGS__); \
        return aigc::JsonHelper::WriteMembers(names, 0, jsonValue, __VA_ARGS__); \
    }                                                                                                                                            
    

### 自定义类的转换

自定义类由于并不清楚外界使用时，是否有按规定添加好 **成员注册接口** ，所以这里采用 **enable_if**
的方式来尝试调用，编译的时候也就不会报错。

    
    
    template <bool, class TYPE = void>
    struct enable_if
    {
    };
    
    template <class TYPE>
    struct enable_if<true, TYPE>
    {
        typedef TYPE type;
    };
    
    template <typename T>
    struct HasConverFunction
    {
        template <typename TT> static char func(decltype(&TT::AIGC_CONVER_JSON_TO_OBJECT));
        template <typename TT> static int func(...);
        const static bool has = (sizeof(func<T>(NULL)) == sizeof(char));
    };
    
    template <typename T, typename enable_if<HasConverFunction<T>::has, int>::type = 0>
    static inline bool JsonToObject(T &obj, rapidjson::Value &jsonValue)
    {
        std::vector<std::string> names = LoadRenameArray(obj);
        return obj.AIGC_CONVER_JSON_TO_OBJECT(jsonValue, names);
    }
    
    template <typename T, typename enable_if<!HasConverFunction<T>::has, int>::type = 0>
    static inline bool JsonToObject(T &obj, rapidjson::Value &jsonValue)
    {
        return false;
    }
    

### 外部调用接口

    
    
    /**
     * @brief conver json string to class | struct
     * @param obj : class or struct
     * @param jsonStr : json string 
     */
    template <typename T>
    static inline bool JsonToObject(T &obj, const std::string &jsonStr)
    {
        rapidjson::Document root;
        root.Parse(jsonStr.c_str());
        if (root.IsNull())
            return false;
    
        return JsonToObject(obj, root);
    }
    

最核心的部分也就上面的几个模块，其他的都是一些琐碎的增加类型支持等操作。

## 学习与引用

  1. 腾讯json解析库：[Tencent](https://github.com/Tencent)/ **[rapidjson](https://github.com/Tencent/rapidjson)**
  2. 杨昕: [C++ 轻量级对象JSON序列化实现](https://zhuanlan.zhihu.com/p/166524394)

## 备注

工程已经放在Github，名字为[AIGCJson](https://github.com/yaronzz/AIGCJson)，有什么建议或者想法的，欢迎提出issue或者pull
request




