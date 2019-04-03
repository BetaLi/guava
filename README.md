# Java编程规范

## 一、基础操作

### 1. 对象非空断言
```
Objects.requireNonNull(t,"param t should not be null");
```

### 2. 入参非空断言并赋值
```
this.id = Objects.requireNonNull(idVal,"param idVal should not be null");
```

### 3. 使用@NonNull限制入参非空
推荐使用lombok的@NonNull注解
```
import lombok.NonNull;

public void setId(@NonNull String id){
    if(id.contains("xx")){
        // ...
    }
}
```

### 4. 使用Optional包装可能为空的返回值
```
return Optional.ofNullable(System.gerenv("version"));
```

### 5. 使用@Nonnull标记返回值不为空
```
import javax.annotation.Nonnull;

@Nonnull
private String getVersion(){
    String version = System.getEnv
}
```

### 6. 使用Optional进行映射操作
* 判空->映射->执行操作
```
Optional.ofNullable(device).map(Device::getGroup).ifPresent(DummyOper::doIt);
```

### 7. 使用Optional进行过滤操作
* 判空->过滤->执行操作
```
Optional.ofNullable(device).filter(d->d.getName()!=null).ifPresent(DummyOper::doIt)
```

### 8. 使用Optional替代多次if判空
```
Optional.ofNullable(result)
    .map(DeviceResult::getDevice)
    .map(Device:getGroup)
    .map(DeviceGroup:getId)
    .ifPresent(DummyOper:doIt)
```

### 9. 使用Optional替代if/else并直接返回结果
```
return Optional.ofNullable(device).map(Device:getName).orElse("UnKnow");
```

### 10. 使用Optional替代if/else，else操作比较复杂
```
return Optional.ofNullable(device).map(Device::getName).orElseGet(()->{
    log.debug("return other");
    return getNameOther();
})
```

### 11. 使用Optional进行条件判断并抛出异常
```
return Optional.ofNullable(result)
    .map(DeviceResult::getDevice)
    .map(Device::getGroup)
    .map(DeviceGroup::getName)
    .orElseThrow(()->new SomeException());
```

### 12. 使用lombok无参构造器注解
```
import lombok.NoArgsConstructor;

@NoArgsConstructor
public class AnnoNoArgConstructor{
    private String id;
    private String name;
}
```
等同于
```
public class AnnoNoArgConstructor{
    private String id;
    private String name;
    public AnnoNoArgsConstructor(){

    }
}
```

### 13. 使用lombok的全参构造器
```
import lombok.AllArgsConstructor

@AllArgsConstructor
public class AnnoAllArgsConstructor{
    private String id;
    private String name;
}
```
等同于
```
public class AnnoAllArgsConstructor{
    private String id;
    private String name;
    public AnnoAllArgsConstructor(String id, String name){
        this.id = id;
        this.name = name;
    }
}
```

### 14. 使用lombok的必要参数构造器
```
import lombok.RequiredArgsConstructor;
import lombok.NonNull;

@RequiredArgsConstructor
public class AnnoReqArgConstructor{
    @NonNull
    private final String id;

    private String name;

    private String desc;
}
```
等同于
```
import lombok.NonNull;

public class AnnoReqArgConstructor{
    @NonNull
    private final String id;

    private String name;

    private String desc;

    public AnnoReqArgConstructor(String id){
        this.id = id;
    }
}
```

### 15. 使用lombok的@RequiredArgsConstructor注解，指定一个静态的构造函数方法
```
import lombok.RequiredArgsConstructor;
import lombok.NonNull;

@RequiredArgsConstructor
public class AnnoReqArgConstructorStatic{
    @NonNull
    private final String id;

    private String name;

    private String desc;
}
```
等同于
```
import lombok.NonNull;

public class AnnoReqArgConstructorStatic{
    private final String id;

    private String name;

    private String desc;

    private AnnoReqArgConstructorStatic(String id){
        this.id = id;
    }

    public static AnnoReqArgConstructor of(@NonNull String id){
        return this.AnnoReqArgConstructor(id);
    }
}
```

### 16. 多种构造器并存
```
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
public class AnnoMultiArgConstructor{
    private String id;
    private String name;
}
```

### 17. 指定函数构造器的访问级别
```
import lombok.AllArgsConstructor;

@AllArgsConstructor(access = AccessLevel.PROTECTED)
public class AnnoAllowArgConstructorLevel{
    private String id;
    private String name;
}
```
等同于
```
import lombok.AllArgsConstructor;

public class AnnoAllowArgConstructor{
    private String id;
    private String name;

    protected AnnoAllowArgConstructor(String id, String name){
        this.id = id;
        this.name = name;
    }
}
```

### 18. 为所有属性提供getter、setter方法
```
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class AnnoClass{
    private String id;
    private String name;
}
```

### 19. 为部分属性添加getter、setter方法
```
import lombok.Getter;
import lombok.Setter;

public class AnnoClass{
    @Getter
    private String id;

    @Getter
    @Setter
    private Sting name;
}
```

### 20. 为部分属性提供getter、setter细粒度定制
```
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class AnnoClass{
    
    private String id;

    @Getter(AccessLevel.PROTECTED)
    private String name;
}
```

### 21. 为部分属性提供getter懒加载
* 使用场景：使用lombok的@Getter(lazy=true)注解，给某个属性提供lazy加载的功能，这种做法可以减少系统初始化资源时的占用率
```
import lombok.Getter;
import lombok.RequiredArgsConstructor;
import lombok.NonNull;

@RequiredArgsConstructor
public class AnnoFiledGetterLazy{
    @NonNull
    private final String id;

    @Getter(lazy=true)
    private final String name = RegCenter.getDeviceName(id);
}
```
若不是用lombok，则实现代码如下：
```
import lombok.NonNull;

public class AnnoFiledGetterLazy{
    private final String id;

    private final AtomicReference<Object> name = new AtomicReference();

    public GetterLazy(@NonNull String id){
        if(id == null){
            throw new NullPointerException("id");
        }else{
            this.id = id;
        }
    }

    @NonNull
    public String getId(){
        return this.id;
    }

    public String getName(){
        Object value = this.name.get();
        if(value == null){
            synchronized(this.name){
                value = this.name.get();
                if(value == null){
                    String actualValue = RegCenter.getDevice(this.id);
                    value = actualValue == null ? this.name : actualValue;
                    this.name.set(value);
                }
            }
        }
        return (String) ((String) (value == this.name ? null : value))
    }
}
```

### 22. 为所有属性提供getter、setter、tostring、equal等功能
```
import lombok.Data;

@Data
public classs AnnoClassData{
    private String id;
    private String name;
}
```

### 23. 为属性setter提供链式操作功能
```
import lombok.Setter;
import lombok.experimental.Accessors;

@Setter
@Accessors(chain=true)
public class AnnoAccessChain{
    private String id;
    private String name;
}
```
e.g 用法举例:
```
AnnoAccessChain bean = new AnnoAccessChain().setId("aaa").setName("Huawei");
```

### 24. Validator使用方式
* 手工创建基于Hibernate的Validator，举例如下：
```
public class ValidationUtils{
    // failFast默认值为false，返回所有的校验错误
    private static Validator validator = Validation.byProvider(HibernateValidator.class).configure().failFast(false).buildValidatorFactory().getValidator();

    // failFast默认值为true，遇到第一个校验错误就直接返回
    private static Validator validatorFailFast = Validation.byProvider(HibernateValidator.class).configure().failFast(true).buildValidatorFactory().getValidator();

    private ValidationUtils(){

    }

    // 返回所有校验错误
    public static <T> List<String> validate(T object){
        return validateExt(obj,validator);
    }

    // 遇到第一个校验错误就返回
    public static <T> List<String> validateFastFail(T obj){
        return validateExt(obj,validatorFailFast);
    }

    private static <T> List<String> validateExt(final T obj, Validator validator){
        Set<ConstraintViolation<T>> constraintViolations = validator.validate(obj);
        if(!constraintViolations.isEmpty()){
            return constraintViolations.stream().map(cv->String.format("{prop=\"%s\",msg=\"%s\"}",cv.getPropertyPath(),cv.getMessage())).collect(Collerctors.toList());
        }else{
            return Collections.emptyList();
        }
    }
}
```

### 25. 限定属性为Null/不为Null
* 使用@Null/@NotNull注解
* 注意和lombok中的必要参数构造器中设计的@NonNull进行区分
* lombok的主要场景不是校验，而是减少编码量
* validation的@NotNull和lombok中的@NonNull可以组合使用
* 依赖Javax Validator API库
* 依赖Hibernate Validator库
```
import lombok.Data;
import lombok.AllArgsConstructor;
import javax.validation.constrains.Null;
import javax.validation.constrains.NotNull;

@Data
@AllArgaConstructor
public class ValidSampleNullAndNotNull{
    @Null
    private String ext;

    @NotNull
    private String name;

    @NotNull(message="自定义输出，id不能为空")
    private String id;
}
```
e.g 用法举例
```
ValidSampleNullAndNotNull bean = new ValidSampleNullAndNotNull("aa",null,null);
// 结果：[{prop="ext", msg="必须为null"}, {prop="id", msg="自定义输出，id不能为null"},{prop="name", msg="不能为null"}]
List<String> result = ValidationUtils.validate(bean);

bean = new ValidSampleNullAndNotNull("a","","123");
// 结果：[{prop="ext", msg="必须为null"}]
result = ValidationUtils.validateFastFail(bean);
```
**[注意：]** lombok.NonNull、javax.javax.annotation.NonNull和javax.validation.constrains.NotNull的区别和使用场景
* @NonNull用在强制入参非空、属性非空的场景下，编译时会生成空指针检查的代码，如果不满足，则会抛出NullPointerException
* @NonNull用在返回值不为空的场景下时，主要由IDE识别并给出告警提示
* @NotNull主要用在Validator校验场景下，限制参数非空，如果不满足，则校验失败

### 26. 限定属性不为Blank
* 使用@NotBlank注解，限定属性不为Blank
* @NotBlank为hibernate-validator扩展的功能
* 依赖javax Validator API库
* 依赖Hibernate Validator库
```
import lombok.Data;
import lombok.experimental.Accessors;
import org.hibernate.validator.constrains.NotBlank;

@Data
@Accessors(chain=true)
public class ValidSampleNotBlank{
    @NotBlank
    private String cost;
}
```
e.g 用法举例
```
ValidSampleNotBlank bean = new ValidSampleNotBlank().setCost(" ");
// 结果：[{prop="cost", msg="不能为空"}]
List<String> result = ValidationUtils.validate(bean);

bean = new ValidSampleNotBlank().setCost("\t");
// 结果：[{prop="cost", msg="不能为空"}]
result = ValidationUtils.validate(bean);

bean = new ValidSampleNotBlank().setCost("\t 12");
// 结果：返回空List，校验通过
result = ValidationUtils.validate(bean);
```

## 二、字符串操作

### 1. 创建空字符串
* 使用StringUtils.EMPTY定义空字符串
* 依赖Apache Commons Lang3库
```
return StringUtils.EMPTY;
```

### 2. 创建重复的字符串
* 使用StringUtils.repeat创建重复字符串
```
String result = StringUtils.repeat("abc",3); //"abcabcabc"
```

### 3. 创建带分隔符的重复字符串
```
String result = StringUtils.repeat("abc","-",3); //"abc-abc-abc"
```

### 4. 字符串检查是否为Empty/NotEmpty
```
StringUtils.isEmpty(null); // true
StringUtils.isEmpty(""); // true
StringUtils.isEmpty(" "); //false
```

### 5. 字符串是否为Blank/NotBlank
```
StringUtils.isBlank(""); // true
StringUtils.isBlank(" "); //true
StringUtils.isBlank("\t"); //true
```

### 6. 字符串是否全由字母构成
```
StringUtils.isAlpha(null); //false
StringUtils.isAlpha(""); //false
StringUtils.isAlpha("a"); //true
StringUtils.isAlpha("a b"); //false
StringUtils.isAlpha("\tabx"); //false
```

### 7. 字符串是否全由数字构成
```
StringUtils.isNumeric("123"); // true
StringUtils.isNumeric("as"); //false
StringUtils.isNumeric("1023 "); //false
```

### 8. 字符串是否全由字母和数字构成
```
StringUtils.isAlphanumeric("123zxcv"); //true
StringUtils.isAlphanumeric("13sd "); //false
```

### 9. 字符串是否和另一个字符串相等
```
StringUtils.equals("Huawei","huawei"); // false
StringUtils.equalsIgnoreCase("Huawei","huawei"); // true
StringUtils.equals(null,null); // true
```

### 10. 字符串中是否包含某个指定的字符串
```
StringUtils.contains("Huawei.com","huawei"); // false
StringUtils.containsIgnoreCase("Huawei.com","huawei"); // true
```

### 11. 字符串是否以某个字母开头
```
StringUtils.startsWith(null,""); //false
StringUtils.startsWith("",""); //true
StringUtils.startsWith("huawei.com",null); //false
StringUtils.startsWith("huawei.com","h"); //true
StringUtils.startsWith("huawei.com","huawei.com"); //true
StringUtils.startsWith("huawei.com",""); //true
StringUtils.startsWithIgnoreCase("huawei.com","Hawei"); //true
```

### 12. 字符串是否以列表的某一个字符串开头
```
StringUtils.startsWithAny("huawei.com"," ","h"); //true
StringUtils.startsWithAny("huawei.com",new String[]{"n","h"}); //true
StringUtils.startsWithAny("huawei.com",null); //false
```

### 13. 字符串是否以某一个字符串结尾
```
StringUtils.endsWith("huawei.com","COM"); //false
StringUtils.endsWithIgnoreCase("huawei.com","COM"); //true
```

### 14. 字符串是否以列表的某一个字符串结尾
```
StringUtils.endsWithAny("huawei.com","net","com"); //true
StringUtils.endsWithAny("huawei.com",new String[]{"net","com"}); //true
```

### 15. 字符串拼接
* 简单字符串拼接直接用"+"
* 大量字符串拼接在不考虑线程安全的情况下用StringBuilder
* 考虑线程安全的情况下用StringBuffer

### 16. 规律字符串的拼接
* 使用Java8函数式Collectors.joining
```
String[] names = {"zebe","hebe","Mary","Lily"};
Arrays.stream(names).collect(Collectors.joining(",")); // zebe,hebe,Mary,Lily

String.join("-",names); // zebe-hebe-Mary-Lily

Arrays.stream(names).collect(Collectors.joining(",","{","}")); // {zebe,hebe,Mary,Lily}
```
* 使用StringUtils.join拼接字符串数组
```
StringUtils.join(names,","); // zebe,hebe,Mary,Lily
```

### 17. 字符串查找：查找子字符串首次出现的位置
```
String str = "The reslut id the fox jump the lazy dog."
StringUtils.indexOf(str,"the"); // 14
StringUtils.indexOf(str,"fox"); // 18
StringUtils.indexOf(str,"cat"); // -1

StringUtils.indexOfIgnoreCase(str,"the"); //0
```

### 18. 字符串查找：查找子字符串末次出现的位置
```
StringUtils.lastIndexOf(str,"the"); // 33
StringUtils.lastIndexOf(str,"fox"); // 18
StringUtils.lastIndexOfIgnoreCase(str,"THe"); // 33
```

### 19. 查找子字符串出现的次数
```
String str = "the fire fox jump the lazy dog";
assertEqual(StringUtils.countMatches(str,"the"),2);
assertEqual(StringUtils.countMatches(str,"cat"),0);
```

### 20. 字符串替换
```
String str = "ssdfssddesss";
StringUtils.replace(str,"ss","aa"); //表示全部替换 aadfaaddeaas
StringUtils.replace(str,"ss","aa",2); //表示最多替换两次 aadfaaddesss
```
