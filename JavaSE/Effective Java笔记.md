_记录在阅读《Effective Java》过程中的内容_

# 一 目录
- [一. 目录](#一-目录)
- [二. 创建和销毁对象](#二-创建和销毁对象)
    - [1.遇到多个构造参数时要考虑使用构建器](#1-遇到多个构造参数时要考虑使用构建器)
    - [2.用私有构造器或枚举类型强化Singleton属性](#2-用私有构造器或枚举类型强化singleton属性)
    - [3.通过私有构造器强化不可实例化的能力](#3-通过私有构造器强化不可实例化的能力)
    - [4.消除过期的对象引用](#4-消除过期的对象引用)
- [三. 对于所有对象都通用的方法](#三-对于所有对象都通用的方法)
    - [1.覆盖equals时总要覆盖hashCode](#1-覆盖equals时总要覆盖hashcode)
    - [2.始终要覆盖toString](#2-始终要覆盖tostring)
    - [3.谨慎地覆盖clone()](#3-谨慎地覆盖clone)
    - [4.考虑实现Comparable接口](#4-考虑实现comparable接口)
- [四. 类和接口](#四-类和接口)





# 二 创建和销毁对象
## 1. 遇到多个构造参数时要考虑使用构建器
当构造方法的参数有很多的时候，客户端在使用构造器时有可能因为一长串的参数而填写错误，比较好的方式是使用构造器模式(Builder Pattern)：

```java
	public class NutritionFacts {
		private final int servingSize;
		private final int servings;
		private final int calories;
		private final int fat;
		private final int sodium;
		private final int carbohydrate;

		public static class Builder {
			//Required parameters
			private final int servingSize:
			private final int servings;

			//Optional parameters - initialized to default values
			private int calories		   = 0;
			private int fat 			     = 0;
			private int carbohydrate 	 = 0;
			private int sodium 			   = 0;

			public Builder (int servingSize, int servings) {
				this.servingSize = servingSize;
				this.servings = servings;
			}

			public Builder calories (int val) {
				calories = val;
				return this;				
			}

			public Builder fat (int val) {
				fat = val;
				return this;				
			}

			public Builder carbohydrate (int val) {
				carbohydrate = val;
				return this;				
			}

			public Builder sodium (int val) {
				sodium = val;
				return this;				
			}

			public NutritionFacts build(){
				return new NutritionFacts(this);
			}
		}

		private NutritionFacts(Builder builder){
			servingSize		= builder.servingSize;
			servings 		= builder.servings;
			calories		= builder.calories;
			fat 			= builder.fat;
			sodium 			= builder.sodium;
			carbohydrate		= builder.carbohydrate;
		}
	}
```
**_客户端调用方式_**
```java
    NutritionFacts cocaCola = new NutritionFacts.Builder(240,8).calories(100).sodium(35).carbohydrate(27).build();
```
客户端代码很容易编写，更重要的是便于阅读。builder可以对其参数增加约束条件，build方法可以检验这些约束条件。

但是Builder模式也有自身的不足：为了创建的对象，必须先创建它的构建器。

## 2. 用私有构造器或枚举类型强化Singleton属性
单元素的枚举类型已经成为实现*Singleton*的最佳方式
```java
  public enum Elvis {
    INSTANCE;
    
    public void leaveTheBuilding() { ... }
  }
```

## 3. 通过私有构造器强化不可实例化的能力
* 通过将类做成抽象类来强制该类不可被实例化，是行不通的，因为该类可以被子类化
* 将构造方法私有化，可以避免被实例化，但这也有副作用，它使得一个类不能被子类化
* 通过Java反射机制，即使构造方法是私有的，也可以通过`setAccessible(true)`来实例化该类，这可以在私有构造方法中抛出异常来避免
```java
  public class UtilityClass {
    private UtilityClass () {
      throw new RuntimeException();
    }
  }
```

## 4. 消除过期的对象引用
内存泄漏是很隐蔽的，如果一个对象引用被无意识的保留起来了，那么，Java的垃圾回收机制不仅不会处理这个对象，而且也不会处理被这个对象所引用的所有其他对象。

这类问题修复也很简单：一旦对象引用已经过期，只需清空这些引用即可。例如：
```java
  public Object pop() {
    Object result = elements[--size];
    elements[size] = null; //清除引用
    return result;
  }
```
清除过期引用另一个好处是，如果它们以后又被错误的解除引用，程序就会立即抛出`NullPointerException`异常。

一般而言，**只要类是自己管理内存，程序员就应该警惕内存泄漏问题。** 一旦元素被释放掉，则该元素中包含的任何对象引用都应该被清空。

**内存泄漏的另一个常见来源是缓存。** 一旦把对象引用放到缓存中，就容易被遗忘。对于这个问题有几种解决方式，可以使用`WeakHashMap`代表缓存；对于过期的缓存要及时清理。对于更加复杂的缓存，考虑直接使用java.lang.ref


# 三 对于所有对象都通用的方法
## 1. 覆盖equals时总要覆盖hashCode
在每个覆盖了**equals** 方法的类中，也必须覆盖**hashCode** 方法。否则违反了Object.hashCode的通用约定，从而导致该类无法结合所有基于散列的集合一起工作，这样的集合类包括HashMap, HashSet
和HashTable.

下面是约定的内容：

* 在应用程序执行期间，只要对象的equals方法的比较操作所用到的信息没有被修改，那么对这同一个对象调用多次，hashCode方法都必须始终如一地返回同一个整数
* 如果两个对象根据equals(Object)方法比较是相等的，那么调用这两个对象任意一个对象的hashCode方法都必须产生相同的整数结果
* 如果两个对象根据equals(Object)方法比较是不相等的，那么调用这两个对象中任意一个对象的hashCode方法，则一定要产生不同的整数结果

关键是第二条：相等的对象必须具有相等的散列码(hash code)。

## 2. 始终要覆盖toString
toString方法的默认实现包含了类的名称，以及一个“@”符号，接着是散列码的无符号十六进制表示法。子类覆盖toString方法应该返回对象中包含的所有值得关注的信息，这可以使类用起来更加舒适。

## 3. 谨慎地覆盖clone()
虽然Cloneable接口没有包含任何方法，但是如果需要覆盖Object.clone()方法，则必须实现Cloneable，否则会抛出`CloneNotSupportedException`

**总结：** 所有实现Clonealbe接口都应该：
* 覆盖`clone()`
* 返回类型为该类本身
* 调用`super.clone()`
* 修正任何需要修正的域    
	* 拷贝任何包含内部“深层结构”的可变对象

**拷贝构造器（copy constructor）**
```java
    public Yum(Yum yum);
```
拷贝构造器用的较多，所有通用集合实现都提供了一个拷贝构造器。例如有一个HashSet，希望把它拷贝成一个TreeSet，clone无法提供这样的功能，可以使用转换构造器：`new TreeSet(s)`。

**拷贝工厂（copy factory）**
```java
    public static Yum newInstance(Yum yum)
```

## 4. 考虑实现Comparable接口
类实现了`Comparable`接口，就表明它的实例具有内在的排序关系（natural ordering）。对于实现了Comparable接口的对象数组进行排序，只需要：
```java
    Arrays.sort(a);
```

如果你正在编写一个值类（value class），它具有非常明显的内在排序关系，比如按字母顺序、按数值顺序或者年代顺序，那应该考虑实现`Comparable`接口

强烈建议，但不是必须：`a.equals(b) == a.comparaTo(b)`

> BigDecimal是不可变类(Immutable)，做运算以后都会返回一个新的BigDecimal类

# 四 类和接口








