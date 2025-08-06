---
title: "Java接口详解：从基础概念到高级应用"
date: 2024-02-28T15:00:00+08:00
draft: false
tags: ["Java", "接口", "面向对象", "设计模式", "编程基础"]
categories: ["Java编程"]
description: "深入解析Java接口的概念、特性、应用场景和最佳实践，包括标记接口、函数式接口、默认方法等高级特性"
cover:
    image: "https://images.unsplash.com/photo-1517077304055-6e89abbf09b0?w=800&h=400&fit=crop"
    alt: "Java接口与面向对象编程"
---

## Java接口概述

**接口（Interface）**是Java中实现多重继承和定义契约的重要机制。接口定义了类必须实现的方法签名，但不提供具体实现（除了默认方法和静态方法）。接口是Java面向对象编程的核心概念之一。

### 接口的基本特性

| 特性 | 描述 | 示例 |
|------|------|------|
| **抽象性** | 接口中的方法默认是抽象的 | `void method();` |
| **多重实现** | 一个类可以实现多个接口 | `class A implements B, C` |
| **常量定义** | 接口中的变量默认是常量 | `int CONSTANT = 10;` |
| **继承关系** | 接口可以继承其他接口 | `interface A extends B` |
| **多态支持** | 接口引用可以指向实现类对象 | `Interface obj = new Implementation();` |

## 接口的基本语法

### 接口定义和实现

```java
// 基本接口定义
public interface Drawable {
    // 常量（默认 public static final）
    String DEFAULT_COLOR = "BLACK";
    int MAX_SIZE = 1000;
    
    // 抽象方法（默认 public abstract）
    void draw();
    void setColor(String color);
    void resize(int width, int height);
    
    // 默认方法（Java 8+）
    default void reset() {
        setColor(DEFAULT_COLOR);
        System.out.println("图形已重置为默认状态");
    }
    
    // 静态方法（Java 8+）
    static void printInfo() {
        System.out.println("这是一个可绘制接口");
    }
    
    // 私有方法（Java 9+）
    private void validateColor(String color) {
        if (color == null || color.isEmpty()) {
            throw new IllegalArgumentException("颜色不能为空");
        }
    }
}

// 接口实现
public class Circle implements Drawable {
    private String color;
    private int radius;
    
    public Circle(int radius) {
        this.radius = radius;
        this.color = DEFAULT_COLOR;
    }
    
    @Override
    public void draw() {
        System.out.println("绘制一个" + color + "的圆形，半径：" + radius);
    }
    
    @Override
    public void setColor(String color) {
        this.color = color;
    }
    
    @Override
    public void resize(int width, int height) {
        // 对于圆形，使用较小的值作为半径
        this.radius = Math.min(width, height) / 2;
    }
    
    // 可以重写默认方法
    @Override
    public void reset() {
        this.radius = 10;
        Drawable.super.reset(); // 调用接口的默认实现
    }
}

// 多接口实现
public interface Movable {
    void move(int x, int y);
    void stop();
}

public interface Rotatable {
    void rotate(double angle);
}

public class Shape implements Drawable, Movable, Rotatable {
    private String color = Drawable.DEFAULT_COLOR;
    private int x, y;
    private double rotation;
    
    @Override
    public void draw() {
        System.out.println("绘制形状在位置 (" + x + ", " + y + ")，旋转角度：" + rotation);
    }
    
    @Override
    public void setColor(String color) {
        this.color = color;
    }
    
    @Override
    public void resize(int width, int height) {
        System.out.println("调整大小为：" + width + "x" + height);
    }
    
    @Override
    public void move(int x, int y) {
        this.x = x;
        this.y = y;
        System.out.println("移动到位置：(" + x + ", " + y + ")");
    }
    
    @Override
    public void stop() {
        System.out.println("停止移动");
    }
    
    @Override
    public void rotate(double angle) {
        this.rotation = angle;
        System.out.println("旋转角度：" + angle + "度");
    }
}

// 使用示例
public class InterfaceExample {
    public static void main(String[] args) {
        // 多态使用
        Drawable drawable = new Circle(5);
        drawable.draw();
        drawable.setColor("RED");
        drawable.draw();
        drawable.reset();
        
        // 静态方法调用
        Drawable.printInfo();
        
        // 多接口实现
        Shape shape = new Shape();
        shape.draw();
        shape.move(10, 20);
        shape.rotate(45.0);
        
        // 接口引用
        Movable movable = shape;
        movable.move(30, 40);
        
        Rotatable rotatable = shape;
        rotatable.rotate(90.0);
    }
}
```

## 标记接口详解

### Serializable接口

**Serializable**是Java中最重要的标记接口之一，用于标识类的对象可以被序列化。

```java
import java.io.*;
import java.util.*;

// 实现Serializable接口
public class Person implements Serializable {
    // 序列化版本号
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
    private transient String password; // transient字段不会被序列化
    private static String company = "TechCorp"; // static字段不会被序列化
    
    public Person(String name, int age, String password) {
        this.name = name;
        this.age = age;
        this.password = password;
    }
    
    // 自定义序列化方法
    private void writeObject(ObjectOutputStream out) throws IOException {
        out.defaultWriteObject(); // 执行默认序列化
        // 可以添加自定义序列化逻辑
        out.writeObject(encrypt(password)); // 加密密码后序列化
    }
    
    // 自定义反序列化方法
    private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
        in.defaultReadObject(); // 执行默认反序列化
        // 可以添加自定义反序列化逻辑
        String encryptedPassword = (String) in.readObject();
        this.password = decrypt(encryptedPassword); // 解密密码
    }
    
    private String encrypt(String text) {
        // 简单的加密示例
        return Base64.getEncoder().encodeToString(text.getBytes());
    }
    
    private String decrypt(String encryptedText) {
        // 简单的解密示例
        return new String(Base64.getDecoder().decode(encryptedText));
    }
    
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + 
               ", password='" + password + "', company='" + company + "'}";
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }
}

// 序列化工具类
public class SerializationUtil {
    
    public static void serialize(Object obj, String filename) throws IOException {
        try (FileOutputStream fos = new FileOutputStream(filename);
             ObjectOutputStream oos = new ObjectOutputStream(fos)) {
            oos.writeObject(obj);
            System.out.println("对象已序列化到文件：" + filename);
        }
    }
    
    public static Object deserialize(String filename) throws IOException, ClassNotFoundException {
        try (FileInputStream fis = new FileInputStream(filename);
             ObjectInputStream ois = new ObjectInputStream(fis)) {
            Object obj = ois.readObject();
            System.out.println("对象已从文件反序列化：" + filename);
            return obj;
        }
    }
    
    public static byte[] serializeToBytes(Object obj) throws IOException {
        try (ByteArrayOutputStream baos = new ByteArrayOutputStream();
             ObjectOutputStream oos = new ObjectOutputStream(baos)) {
            oos.writeObject(obj);
            return baos.toByteArray();
        }
    }
    
    public static Object deserializeFromBytes(byte[] data) throws IOException, ClassNotFoundException {
        try (ByteArrayInputStream bais = new ByteArrayInputStream(data);
             ObjectInputStream ois = new ObjectInputStream(bais)) {
            return ois.readObject();
        }
    }
}

// 序列化示例
public class SerializationExample {
    public static void main(String[] args) {
        try {
            // 创建对象
            Person person = new Person("张三", 25, "secret123");
            System.out.println("原始对象：" + person);
            
            // 序列化到文件
            SerializationUtil.serialize(person, "person.ser");
            
            // 从文件反序列化
            Person deserializedPerson = (Person) SerializationUtil.deserialize("person.ser");
            System.out.println("反序列化对象：" + deserializedPerson);
            
            // 序列化到字节数组
            byte[] data = SerializationUtil.serializeToBytes(person);
            System.out.println("序列化数据大小：" + data.length + " 字节");
            
            // 从字节数组反序列化
            Person fromBytes = (Person) SerializationUtil.deserializeFromBytes(data);
            System.out.println("从字节数组反序列化：" + fromBytes);
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 其他重要的标记接口

```java
// Cloneable接口示例
public class Student implements Cloneable, Serializable {
    private String name;
    private int age;
    private List<String> courses;
    
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
        this.courses = new ArrayList<>();
    }
    
    // 浅克隆
    @Override
    public Student clone() throws CloneNotSupportedException {
        return (Student) super.clone();
    }
    
    // 深克隆
    public Student deepClone() throws CloneNotSupportedException {
        Student cloned = (Student) super.clone();
        cloned.courses = new ArrayList<>(this.courses); // 深拷贝集合
        return cloned;
    }
    
    public void addCourse(String course) {
        courses.add(course);
    }
    
    @Override
    public String toString() {
        return "Student{name='" + name + "', age=" + age + ", courses=" + courses + "}";
    }
    
    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
    public List<String> getCourses() { return courses; }
}

// RandomAccess接口示例（标记接口，表示支持快速随机访问）
public class CustomList<T> extends AbstractList<T> implements RandomAccess {
    private Object[] elements;
    private int size;
    
    public CustomList(int capacity) {
        elements = new Object[capacity];
        size = 0;
    }
    
    @Override
    public T get(int index) {
        if (index >= size) throw new IndexOutOfBoundsException();
        return (T) elements[index];
    }
    
    @Override
    public int size() {
        return size;
    }
    
    @Override
    public boolean add(T element) {
        if (size >= elements.length) {
            throw new IllegalStateException("List is full");
        }
        elements[size++] = element;
        return true;
    }
}

// 标记接口使用示例
public class MarkerInterfaceExample {
    public static void main(String[] args) throws CloneNotSupportedException {
        // Cloneable示例
        Student student1 = new Student("李四", 20);
        student1.addCourse("Java");
        student1.addCourse("Python");
        
        // 浅克隆
        Student student2 = student1.clone();
        student2.setName("王五");
        
        // 深克隆
        Student student3 = student1.deepClone();
        student3.addCourse("C++");
        
        System.out.println("原始学生：" + student1);
        System.out.println("浅克隆学生：" + student2);
        System.out.println("深克隆学生：" + student3);
        
        // RandomAccess示例
        CustomList<String> customList = new CustomList<>(10);
        customList.add("A");
        customList.add("B");
        customList.add("C");
        
        // 检查是否支持随机访问
        if (customList instanceof RandomAccess) {
            System.out.println("支持快速随机访问");
            for (int i = 0; i < customList.size(); i++) {
                System.out.println("元素[" + i + "]: " + customList.get(i));
            }
        }
    }
}
```

## 函数式接口

### 基本函数式接口

```java
import java.util.function.*;
import java.util.*;
import java.util.stream.Collectors;

// 自定义函数式接口
@FunctionalInterface
public interface Calculator {
    double calculate(double a, double b);
    
    // 默认方法不影响函数式接口的性质
    default void printResult(double a, double b) {
        System.out.println("计算结果：" + calculate(a, b));
    }
    
    // 静态方法
    static Calculator add() {
        return (a, b) -> a + b;
    }
    
    static Calculator multiply() {
        return (a, b) -> a * b;
    }
}

// 函数式接口应用示例
public class FunctionalInterfaceExample {
    
    public static void main(String[] args) {
        // 1. 自定义函数式接口
        demonstrateCustomFunctionalInterface();
        
        // 2. 内置函数式接口
        demonstrateBuiltInFunctionalInterfaces();
        
        // 3. 方法引用
        demonstrateMethodReferences();
        
        // 4. 实际应用场景
        demonstratePracticalUsage();
    }
    
    private static void demonstrateCustomFunctionalInterface() {
        System.out.println("=== 自定义函数式接口示例 ===");
        
        // Lambda表达式实现
        Calculator add = (a, b) -> a + b;
        Calculator subtract = (a, b) -> a - b;
        Calculator multiply = (a, b) -> a * b;
        Calculator divide = (a, b) -> b != 0 ? a / b : 0;
        
        double x = 10, y = 5;
        
        System.out.println("加法：" + add.calculate(x, y));
        System.out.println("减法：" + subtract.calculate(x, y));
        System.out.println("乘法：" + multiply.calculate(x, y));
        System.out.println("除法：" + divide.calculate(x, y));
        
        // 使用静态方法
        Calculator staticAdd = Calculator.add();
        staticAdd.printResult(x, y);
    }
    
    private static void demonstrateBuiltInFunctionalInterfaces() {
        System.out.println("\n=== 内置函数式接口示例 ===");
        
        // Predicate<T> - 断言型接口
        Predicate<Integer> isEven = n -> n % 2 == 0;
        Predicate<Integer> isPositive = n -> n > 0;
        Predicate<Integer> isEvenAndPositive = isEven.and(isPositive);
        
        List<Integer> numbers = Arrays.asList(-2, -1, 0, 1, 2, 3, 4, 5);
        List<Integer> evenPositive = numbers.stream()
                .filter(isEvenAndPositive)
                .collect(Collectors.toList());
        System.out.println("偶数且为正数：" + evenPositive);
        
        // Function<T, R> - 函数型接口
        Function<String, Integer> stringLength = String::length;
        Function<Integer, String> intToString = Object::toString;
        Function<String, String> stringToUpper = String::toUpperCase;
        
        // 函数组合
        Function<String, String> lengthToString = stringLength.andThen(intToString);
        System.out.println("字符串长度：" + lengthToString.apply("Hello World"));
        
        // Consumer<T> - 消费型接口
        Consumer<String> printer = System.out::println;
        Consumer<String> upperPrinter = s -> System.out.println(s.toUpperCase());
        Consumer<String> combinedPrinter = printer.andThen(upperPrinter);
        
        combinedPrinter.accept("hello world");
        
        // Supplier<T> - 供给型接口
        Supplier<String> randomString = () -> "Random-" + Math.random();
        Supplier<Date> currentDate = Date::new;
        
        System.out.println("随机字符串：" + randomString.get());
        System.out.println("当前时间：" + currentDate.get());
        
        // BiFunction<T, U, R> - 双参数函数型接口
        BiFunction<Integer, Integer, Integer> max = Integer::max;
        BiFunction<String, String, String> concat = (s1, s2) -> s1 + " " + s2;
        
        System.out.println("最大值：" + max.apply(10, 20));
        System.out.println("连接字符串：" + concat.apply("Hello", "World"));
    }
    
    private static void demonstrateMethodReferences() {
        System.out.println("\n=== 方法引用示例 ===");
        
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");
        
        // 静态方法引用
        names.stream()
                .map(String::toUpperCase)
                .forEach(System.out::println);
        
        // 实例方法引用
        String prefix = "Mr. ";
        Function<String, String> addPrefix = prefix::concat;
        names.stream()
                .map(addPrefix)
                .forEach(System.out::println);
        
        // 构造器引用
        Supplier<List<String>> listSupplier = ArrayList::new;
        Function<String, StringBuilder> sbCreator = StringBuilder::new;
        
        List<String> newList = listSupplier.get();
        StringBuilder sb = sbCreator.apply("Initial content");
        System.out.println("新列表：" + newList);
        System.out.println("StringBuilder：" + sb);
    }
    
    private static void demonstratePracticalUsage() {
        System.out.println("\n=== 实际应用场景 ===");
        
        // 策略模式的函数式实现
        Map<String, Function<Double, Double>> taxCalculators = new HashMap<>();
        taxCalculators.put("STANDARD", amount -> amount * 0.1);
        taxCalculators.put("PREMIUM", amount -> amount * 0.15);
        taxCalculators.put("VIP", amount -> amount * 0.05);
        
        double amount = 1000.0;
        String customerType = "PREMIUM";
        
        double tax = taxCalculators.get(customerType).apply(amount);
        System.out.println("客户类型：" + customerType + "，税额：" + tax);
        
        // 事件处理的函数式实现
        EventProcessor processor = new EventProcessor();
        processor.addHandler("LOGIN", user -> System.out.println("用户登录：" + user));
        processor.addHandler("LOGOUT", user -> System.out.println("用户登出：" + user));
        processor.addHandler("PURCHASE", user -> System.out.println("用户购买：" + user));
        
        processor.processEvent("LOGIN", "Alice");
        processor.processEvent("PURCHASE", "Bob");
        
        // 数据验证的函数式实现
        DataValidator validator = new DataValidator();
        validator.addRule("email", email -> email.contains("@"));
        validator.addRule("password", pwd -> pwd.length() >= 8);
        validator.addRule("age", age -> {
            try {
                int ageInt = Integer.parseInt(age);
                return ageInt >= 18 && ageInt <= 120;
            } catch (NumberFormatException e) {
                return false;
            }
        });
        
        Map<String, String> userData = new HashMap<>();
        userData.put("email", "user@example.com");
        userData.put("password", "password123");
        userData.put("age", "25");
        
        boolean isValid = validator.validate(userData);
        System.out.println("数据验证结果：" + isValid);
    }
}

// 事件处理器
class EventProcessor {
    private Map<String, Consumer<String>> handlers = new HashMap<>();
    
    public void addHandler(String eventType, Consumer<String> handler) {
        handlers.put(eventType, handler);
    }
    
    public void processEvent(String eventType, String data) {
        Consumer<String> handler = handlers.get(eventType);
        if (handler != null) {
            handler.accept(data);
        } else {
            System.out.println("未知事件类型：" + eventType);
        }
    }
}

// 数据验证器
class DataValidator {
    private Map<String, Predicate<String>> rules = new HashMap<>();
    
    public void addRule(String field, Predicate<String> rule) {
        rules.put(field, rule);
    }
    
    public boolean validate(Map<String, String> data) {
        return rules.entrySet().stream()
                .allMatch(entry -> {
                    String field = entry.getKey();
                    Predicate<String> rule = entry.getValue();
                    String value = data.get(field);
                    boolean valid = value != null && rule.test(value);
                    if (!valid) {
                        System.out.println("字段验证失败：" + field);
                    }
                    return valid;
                });
    }
}
```

## 接口的高级特性

### 默认方法和静态方法

```java
// 接口演化示例
public interface Vehicle {
    // 抽象方法
    void start();
    void stop();
    
    // 默认方法（Java 8+）
    default void honk() {
        System.out.println("嘟嘟！");
    }
    
    default void displayInfo() {
        System.out.println("这是一个交通工具");
        System.out.println("最大速度：" + getMaxSpeed() + " km/h");
    }
    
    // 抽象方法，子类必须实现
    int getMaxSpeed();
    
    // 静态方法（Java 8+）
    static void printManufacturingInfo() {
        System.out.println("车辆制造信息：");
        System.out.println("- 遵循国际安全标准");
        System.out.println("- 环保认证");
    }
    
    static boolean isValidSpeed(int speed) {
        return speed >= 0 && speed <= 500;
    }
    
    // 私有方法（Java 9+）
    private void validateOperation() {
        System.out.println("执行安全检查...");
    }
    
    // 私有静态方法（Java 9+）
    private static String formatSpeed(int speed) {
        return speed + " km/h";
    }
}

// 电动车接口
public interface ElectricVehicle extends Vehicle {
    void charge();
    int getBatteryLevel();
    
    // 重写默认方法
    @Override
    default void displayInfo() {
        Vehicle.super.displayInfo(); // 调用父接口的默认方法
        System.out.println("电池电量：" + getBatteryLevel() + "%");
        System.out.println("这是一个电动车");
    }
    
    // 新的默认方法
    default void checkBattery() {
        int level = getBatteryLevel();
        if (level < 20) {
            System.out.println("警告：电池电量低，请及时充电！");
        } else if (level < 50) {
            System.out.println("提示：电池电量中等");
        } else {
            System.out.println("电池电量充足");
        }
    }
}

// 具体实现类
public class ElectricCar implements ElectricVehicle {
    private String model;
    private int batteryLevel;
    private boolean isRunning;
    
    public ElectricCar(String model, int batteryLevel) {
        this.model = model;
        this.batteryLevel = batteryLevel;
        this.isRunning = false;
    }
    
    @Override
    public void start() {
        if (batteryLevel > 0) {
            isRunning = true;
            System.out.println(model + " 启动成功");
        } else {
            System.out.println("电池没电，无法启动");
        }
    }
    
    @Override
    public void stop() {
        isRunning = false;
        System.out.println(model + " 已停止");
    }
    
    @Override
    public void charge() {
        System.out.println(model + " 正在充电...");
        batteryLevel = Math.min(100, batteryLevel + 20);
        System.out.println("充电完成，当前电量：" + batteryLevel + "%");
    }
    
    @Override
    public int getBatteryLevel() {
        return batteryLevel;
    }
    
    @Override
    public int getMaxSpeed() {
        return 180;
    }
    
    // 可以重写默认方法
    @Override
    public void honk() {
        System.out.println(model + " 发出电子喇叭声：嘀嘀！");
    }
    
    @Override
    public String toString() {
        return "ElectricCar{model='" + model + "', batteryLevel=" + batteryLevel + 
               ", isRunning=" + isRunning + "}";
    }
}

// 混合动力车
public class HybridCar implements Vehicle {
    private String model;
    private int fuelLevel;
    private int batteryLevel;
    
    public HybridCar(String model, int fuelLevel, int batteryLevel) {
        this.model = model;
        this.fuelLevel = fuelLevel;
        this.batteryLevel = batteryLevel;
    }
    
    @Override
    public void start() {
        if (fuelLevel > 0 || batteryLevel > 0) {
            System.out.println(model + " 混合动力启动");
        } else {
            System.out.println("燃料和电池都没有，无法启动");
        }
    }
    
    @Override
    public void stop() {
        System.out.println(model + " 已停止");
    }
    
    @Override
    public int getMaxSpeed() {
        return 200;
    }
    
    // 使用默认的honk()和displayInfo()方法
}

// 使用示例
public class InterfaceAdvancedExample {
    public static void main(String[] args) {
        System.out.println("=== 接口高级特性示例 ===");
        
        // 静态方法调用
        Vehicle.printManufacturingInfo();
        System.out.println("速度验证：" + Vehicle.isValidSpeed(150));
        
        // 电动车示例
        ElectricCar tesla = new ElectricCar("Tesla Model 3", 30);
        System.out.println("\n" + tesla);
        
        tesla.start();
        tesla.honk(); // 重写的方法
        tesla.displayInfo(); // 继承的默认方法
        tesla.checkBattery(); // ElectricVehicle的默认方法
        tesla.charge();
        tesla.checkBattery();
        tesla.stop();
        
        // 混合动力车示例
        HybridCar prius = new HybridCar("Toyota Prius", 80, 60);
        System.out.println("\n混合动力车示例：");
        prius.start();
        prius.honk(); // 使用默认实现
        prius.displayInfo(); // 使用默认实现
        prius.stop();
        
        // 多态使用
        System.out.println("\n多态示例：");
        Vehicle[] vehicles = {tesla, prius};
        for (Vehicle vehicle : vehicles) {
            vehicle.displayInfo();
            System.out.println("---");
        }
    }
}
```

## 接口设计模式

### 策略模式

```java
// 策略接口
public interface SortingStrategy {
    <T extends Comparable<T>> void sort(T[] array);
    String getAlgorithmName();
}

// 具体策略实现
public class BubbleSortStrategy implements SortingStrategy {
    @Override
    public <T extends Comparable<T>> void sort(T[] array) {
        int n = array.length;
        for (int i = 0; i < n - 1; i++) {
            for (int j = 0; j < n - i - 1; j++) {
                if (array[j].compareTo(array[j + 1]) > 0) {
                    T temp = array[j];
                    array[j] = array[j + 1];
                    array[j + 1] = temp;
                }
            }
        }
    }
    
    @Override
    public String getAlgorithmName() {
        return "冒泡排序";
    }
}

public class QuickSortStrategy implements SortingStrategy {
    @Override
    public <T extends Comparable<T>> void sort(T[] array) {
        quickSort(array, 0, array.length - 1);
    }
    
    private <T extends Comparable<T>> void quickSort(T[] array, int low, int high) {
        if (low < high) {
            int pi = partition(array, low, high);
            quickSort(array, low, pi - 1);
            quickSort(array, pi + 1, high);
        }
    }
    
    private <T extends Comparable<T>> int partition(T[] array, int low, int high) {
        T pivot = array[high];
        int i = low - 1;
        
        for (int j = low; j < high; j++) {
            if (array[j].compareTo(pivot) <= 0) {
                i++;
                T temp = array[i];
                array[i] = array[j];
                array[j] = temp;
            }
        }
        
        T temp = array[i + 1];
        array[i + 1] = array[high];
        array[high] = temp;
        
        return i + 1;
    }
    
    @Override
    public String getAlgorithmName() {
        return "快速排序";
    }
}

// 上下文类
public class SortingContext {
    private SortingStrategy strategy;
    
    public SortingContext(SortingStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void setStrategy(SortingStrategy strategy) {
        this.strategy = strategy;
    }
    
    public <T extends Comparable<T>> void executeSort(T[] array) {
        long startTime = System.nanoTime();
        strategy.sort(array);
        long endTime = System.nanoTime();
        
        System.out.println("使用 " + strategy.getAlgorithmName() + 
                          " 排序完成，耗时：" + (endTime - startTime) / 1_000_000.0 + " ms");
    }
}

// 观察者模式
public interface Observer {
    void update(String message);
}

public interface Subject {
    void addObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers(String message);
}

public class NewsAgency implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String news;
    
    @Override
    public void addObserver(Observer observer) {
        observers.add(observer);
    }
    
    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }
    
    @Override
    public void notifyObservers(String message) {
        for (Observer observer : observers) {
            observer.update(message);
        }
    }
    
    public void setNews(String news) {
        this.news = news;
        notifyObservers("新闻更新：" + news);
    }
}

public class NewsChannel implements Observer {
    private String name;
    
    public NewsChannel(String name) {
        this.name = name;
    }
    
    @Override
    public void update(String message) {
        System.out.println(name + " 收到消息：" + message);
    }
}

// 设计模式示例
public class DesignPatternExample {
    public static void main(String[] args) {
        // 策略模式示例
        System.out.println("=== 策略模式示例 ===");
        
        Integer[] numbers = {64, 34, 25, 12, 22, 11, 90};
        
        SortingContext context = new SortingContext(new BubbleSortStrategy());
        Integer[] bubbleArray = numbers.clone();
        System.out.println("原始数组：" + Arrays.toString(bubbleArray));
        context.executeSort(bubbleArray);
        System.out.println("排序结果：" + Arrays.toString(bubbleArray));
        
        context.setStrategy(new QuickSortStrategy());
        Integer[] quickArray = numbers.clone();
        System.out.println("\n原始数组：" + Arrays.toString(quickArray));
        context.executeSort(quickArray);
        System.out.println("排序结果：" + Arrays.toString(quickArray));
        
        // 观察者模式示例
        System.out.println("\n=== 观察者模式示例 ===");
        
        NewsAgency agency = new NewsAgency();
        NewsChannel cnn = new NewsChannel("CNN");
        NewsChannel bbc = new NewsChannel("BBC");
        NewsChannel fox = new NewsChannel("Fox News");
        
        agency.addObserver(cnn);
        agency.addObserver(bbc);
        agency.addObserver(fox);
        
        agency.setNews("重大科技突破：量子计算机实现新里程碑");
        
        System.out.println("\nBBC 退订新闻");
        agency.removeObserver(bbc);
        
        agency.setNews("经济新闻：股市创历史新高");
    }
}
```

## 接口最佳实践

### 接口设计原则

```java
// 1. 接口隔离原则 - 好的设计
public interface Readable {
    String read();
}

public interface Writable {
    void write(String content);
}

public interface Seekable {
    void seek(long position);
}

// 组合接口
public interface RandomAccessFile extends Readable, Writable, Seekable {
    long size();
}

// 2. 依赖倒置原则
public interface PaymentProcessor {
    boolean processPayment(double amount, String currency);
    String getPaymentMethod();
}

public class CreditCardProcessor implements PaymentProcessor {
    @Override
    public boolean processPayment(double amount, String currency) {
        System.out.println("处理信用卡支付：" + amount + " " + currency);
        return true;
    }
    
    @Override
    public String getPaymentMethod() {
        return "信用卡";
    }
}

public class PayPalProcessor implements PaymentProcessor {
    @Override
    public boolean processPayment(double amount, String currency) {
        System.out.println("处理PayPal支付：" + amount + " " + currency);
        return true;
    }
    
    @Override
    public String getPaymentMethod() {
        return "PayPal";
    }
}

// 高层模块依赖抽象
public class OrderService {
    private PaymentProcessor paymentProcessor;
    
    public OrderService(PaymentProcessor paymentProcessor) {
        this.paymentProcessor = paymentProcessor;
    }
    
    public boolean processOrder(double amount, String currency) {
        System.out.println("处理订单，使用 " + paymentProcessor.getPaymentMethod());
        return paymentProcessor.processPayment(amount, currency);
    }
}

// 3. 接口版本控制
public interface UserService {
    User getUserById(Long id);
    List<User> getAllUsers();
    
    // V2 新增方法，使用默认实现保持向后兼容
    default List<User> getUsersByRole(String role) {
        return getAllUsers().stream()
                .filter(user -> role.equals(user.getRole()))
                .collect(Collectors.toList());
    }
    
    // V3 新增方法
    default Optional<User> findUserByEmail(String email) {
        return getAllUsers().stream()
                .filter(user -> email.equals(user.getEmail()))
                .findFirst();
    }
}

// 用户实体类
class User {
    private Long id;
    private String name;
    private String email;
    private String role;
    
    public User(Long id, String name, String email, String role) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.role = role;
    }
    
    // Getters
    public Long getId() { return id; }
    public String getName() { return name; }
    public String getEmail() { return email; }
    public String getRole() { return role; }
    
    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "', email='" + email + "', role='" + role + "'}";
    }
}

// 4. 接口文档和契约
/**
 * 缓存接口定义
 * 
 * @param <K> 键类型
 * @param <V> 值类型
 */
public interface Cache<K, V> {
    
    /**
     * 存储键值对
     * 
     * @param key 键，不能为null
     * @param value 值，不能为null
     * @throws IllegalArgumentException 如果key或value为null
     */
    void put(K key, V value);
    
    /**
     * 获取值
     * 
     * @param key 键，不能为null
     * @return 对应的值，如果不存在则返回null
     * @throws IllegalArgumentException 如果key为null
     */
    V get(K key);
    
    /**
     * 移除键值对
     * 
     * @param key 键，不能为null
     * @return 被移除的值，如果不存在则返回null
     * @throws IllegalArgumentException 如果key为null
     */
    V remove(K key);
    
    /**
     * 检查是否包含指定键
     * 
     * @param key 键，不能为null
     * @return 如果包含则返回true，否则返回false
     * @throws IllegalArgumentException 如果key为null
     */
    boolean containsKey(K key);
    
    /**
     * 获取缓存大小
     * 
     * @return 当前缓存中的元素数量
     */
    int size();
    
    /**
     * 清空缓存
     */
    void clear();
    
    /**
     * 获取缓存统计信息
     * 
     * @return 缓存统计信息，包含命中率、miss率等
     */
    default CacheStats getStats() {
        return new CacheStats(0, 0, 0.0);
    }
}

// 缓存统计信息
class CacheStats {
    private final long hits;
    private final long misses;
    private final double hitRate;
    
    public CacheStats(long hits, long misses, double hitRate) {
        this.hits = hits;
        this.misses = misses;
        this.hitRate = hitRate;
    }
    
    public long getHits() { return hits; }
    public long getMisses() { return misses; }
    public double getHitRate() { return hitRate; }
    
    @Override
    public String toString() {
        return "CacheStats{hits=" + hits + ", misses=" + misses + ", hitRate=" + hitRate + "}";
    }
}

// 最佳实践示例
public class InterfaceBestPracticesExample {
    public static void main(String[] args) {
        // 依赖倒置原则示例
        System.out.println("=== 依赖倒置原则示例 ===");
        
        OrderService creditCardService = new OrderService(new CreditCardProcessor());
        OrderService paypalService = new OrderService(new PayPalProcessor());
        
        creditCardService.processOrder(100.0, "USD");
        paypalService.processOrder(200.0, "EUR");
        
        // 接口版本控制示例
        System.out.println("\n=== 接口版本控制示例 ===");
        
        UserService userService = new UserServiceImpl();
        
        // V1 功能
        List<User> allUsers = userService.getAllUsers();
        System.out.println("所有用户：" + allUsers);
        
        // V2 功能（默认实现）
        List<User> admins = userService.getUsersByRole("admin");
        System.out.println("管理员用户：" + admins);
        
        // V3 功能（默认实现）
        Optional<User> user = userService.findUserByEmail("alice@example.com");
        System.out.println("邮箱查找用户：" + user);
    }
}

// UserService实现类
class UserServiceImpl implements UserService {
    private List<User> users;
    
    public UserServiceImpl() {
        users = Arrays.asList(
            new User(1L, "Alice", "alice@example.com", "admin"),
            new User(2L, "Bob", "bob@example.com", "user"),
            new User(3L, "Charlie", "charlie@example.com", "admin")
        );
    }
    
    @Override
    public User getUserById(Long id) {
        return users.stream()
                .filter(user -> user.getId().equals(id))
                .findFirst()
                .orElse(null);
    }
    
    @Override
    public List<User> getAllUsers() {
        return new ArrayList<>(users);
    }
}
```

## 学习建议和总结

### Java接口学习路径

1. **基础概念**：理解接口的定义、特性和语法
2. **标记接口**：掌握Serializable、Cloneable等重要标记接口
3. **函数式接口**：学习Lambda表达式和方法引用
4. **高级特性**：掌握默认方法、静态方法和私有方法
5. **设计模式**：学习基于接口的设计模式
6. **最佳实践**：掌握接口设计原则和实际应用

### 关键要点总结

| 概念 | 用途 | 特点 | 应用场景 |
|------|------|------|----------|
| **抽象方法** | 定义契约 | 必须实现 | 核心业务逻辑 |
| **默认方法** | 接口演化 | 可选重写 | 向后兼容 |
| **静态方法** | 工具方法 | 接口级别 | 辅助功能 |
| **标记接口** | 类型标识 | 无方法 | 特殊能力标记 |
| **函数式接口** | Lambda支持 | 单一抽象方法 | 函数式编程 |

### 实际应用建议

- **接口设计**：遵循单一职责和接口隔离原则
- **版本控制**：使用默认方法保持向后兼容性
- **文档编写**：详细描述接口契约和使用约定
- **测试策略**：为接口实现编写充分的单元测试
- **性能考虑**：注意接口调用的性能开销

Java接口是实现抽象、多态和解耦的重要工具，掌握其各种特性和应用模式对于编写高质量的Java代码至关重要。随着Java版本的演进，接口功能越来越强大，为现代Java开发提供了更多的设计选择。