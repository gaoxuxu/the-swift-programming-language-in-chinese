# 继承（Inheritance）
-------------------

本页包含内容：

- [定义一个基类（Base class）](#defining_a_base_class)
- [子类生成（Subclassing）](#subclassing)
- [重写（Overriding）](#overriding)
- [防止重写](#preventing_overrides)

一个类可以*继承（inherit）*另一个类的方法（methods），属性（property）和其它特性。当一个类继承其它类时，继承类叫*子类（subclass）*，被继承类叫*超类（或父类，superclass）*。在 Swift 中，继承是区分「类」与其它类型的一个基本特征。

在 Swift 中，类可以调用和访问超类的方法，属性和下标脚本（subscripts），并且可以重写（override）这些方法，属性和下标脚本来优化或修改它们的行为。Swift 会检查你的重写定义在超类中是否有匹配的定义，以此确保你的重写行为是正确的。

可以为类中继承来的属性添加属性观察器（property observer），这样一来，当属性值改变时，类就会被通知到。可以为任何属性添加属性观察器，无论它原本被定义为存储型属性（stored property）还是计算型属性（computed property）。

<a name="defining_a_base_class"></a>
## 定义一个基类（Base class）

不继承于其它类的类，称之为*基类（base calss）*。

> 注意：  
> Swift 中的类并不是从一个通用的基类继承而来。如果你不为你定义的类指定一个超类的话，这个类就自动成为基类。

下面的例子定义了一个叫`Vehicle`的基类。这个基类声明了一个名为`currentSpeed`、默认值为`0.0`（由此可以推断它的类型为`Double`）的存储型变量。一个名为`description`的`String`类型的只读计算型变量使用`currentSpeed`的值来创建机动车的描述。

`Vehicle`类还定义了一个名为`makeNoise`的方法。对`Vehicle`基类的实例来说，这个方法不做任何事情，这个方法是留给子类实现的：

    class Vehicle {
        var currentSpeed = 0.0
        var description: String {
            return "traveling at \(currentSpeed) miles per hour"
        }
        func makeNoise() {
            // do nothing - an arbitrary vehicle doesn't necessarily make a noise
        }
    }

如果要创建一个`Vehicle`类的新实例，使用*构造器*语法调用上面的初始化器，即类名后面跟一个空的小括号：

    let someVehicle = Vehicle()

有了新创建的`Vehicle`实例，你就可以使用它的`description`属性来打印人类可读的该车当前速度了：

    println("Vehicle: \(someVehicle.description)")
    // Vehicle: traveling at 0.0 miles per hour

`Vehicle`类定义了车辆的共同特性，但这个类本身并没太大用处。为了使它更为实用，你需要进一步细化它来描述更具体的车辆。

<a name="subclassing"></a>
## 子类生成（Subclassing）

*子类生成（Subclassing）*指的是在一个已有类的基础上创建一个新的类。子类继承超类的特性，并且可以优化或改变它。你还可以为子类添加新的特性。

为了指明某个类的超类，将超类名写在子类名的后面，用冒号分隔：

    class SomeClass: SomeSuperclass {
        // 类的定义
    }

下面的例子定义了一个`Vehicle`类的子类`Bicycle`：

    class Bicycle: Vehicle {
        var hasBasket = false
    }

新的`Bicycle`类自动获得`Vehicle`类的特性，比如 `currentSpeed`和`description`属性，以及`makeNoise`方法。

除它继承得到的特性外，`Bicycle`类还定义了一个新的存储型变量`hasBasket`，其默认值为`false`（推断其类型为`bool`）。

默认情况下，任何新创建的`Bicycle`实例都没有篮子。你可以在`Bicycle`实例创建后将其`hasBasket`属性设置为`true`：

    let bicycle = Bicycle()    
    bicycle.hasBasket = true

子类本身也可以有子类。下面的例子中创建了一个继承自`Bicycle`类的双人自行车类：

    class Tandem: Bicycle {
        var currentNumberOfPassengers = 0
    }

`Tandem`类继承了`Bicycle`类的所有属性和方法，而后者也继承了`Vehicle`类的所有属性和方法。`Tandem`类还添加了一个新的名为`currentNumberOfPassengers`的存储型变量，默认值为0。

如果创建一个`Tandem`类的实例，就可以使用任意的无论是它自己的还是继承得到的属性，并且可以查询它从`Vehicle`类继承得到的只读属性`description`：

    let tandem = Tandem()
    tandem.hasBasket = true
    tandem.currentNumberOfPassengers = 2
    tandem.currentSpeed = 22.0
    println("Tandem: \(tandem.description)")
    // Tandem: traveling at 22.0 miles per hour

<a name="overriding"></a>
## 重写（Overriding）

子类可以为继承来的实例方法（instance method），类方法（class method），实例属性（instance property），类属性或下标脚本（subscript）提供自己定制的实现（implementation）。我们把这种行为叫*重写（overriding）*。

如果要重写某个特性，你需要在重写定义的前面加上`override`关键字。这么做，你就表明了你是想提供一个重写版本，而非错误地提供了一个相同的定义。意外的重写行为可能会导致不可预知的错误，任何缺少`override`关键字的重写都会在编译时被诊断为错误。

`override`关键字会提醒 Swift 编译器去检查该类的超类（或其中一个父类）是否有匹配重写版本的声明。这个检查可以确保你的重写定义是正确的。

### 访问超类的方法，属性及下标脚本

当你在子类中重写超类的方法，属性或下标脚本时，有时在你的重写版本中使用已经存在的超类实现会大有裨益。比如，你可以优化已有实现的行为，或在一个继承来的变量中存储一个修改过的值。

在合适的地方，你可以通过使用`super`前缀来访问超类版本的方法，属性或下标脚本：

* 在方法`someMethod`的重写实现中，可以通过`super.someMethod()`来调用超类版本的`someMethod`方法。
* 在属性`someProperty`的 getter 或 setter 的重写实现中，可以通过`super.someProperty`来访问超类版本的`someProperty`属性。
* 在下标脚本的重写实现中，可以通过`super[someIndex]`来访问超类版本中的相同下标脚本。

### 重写方法

在子类中，你可以重写继承来的实例方法或类方法，提供一个定制或替代的方法实现。

下面的例子定义了`Vehicle`的一个新的子类，叫`Train`，它重写了从`Vehicle`类继承来的`makeNoise`方法：

    class Train: Vehicle {
        override func makeNoise() {
            println("Choo Choo")
        }
    }

如果创建了`Train`类的实例并且调用它的`makeNoise`方法，就可以看到这个方法的`Train`子类版本的实现：

    let train = Train()
    train.makeNoise()
    // prints "Choo Choo"

### 重写属性

你可以重写继承来的实例属性或类属性，提供自己定制的getter和setter，或添加属性观察器使重写的属性观察属性值什么时候发生改变。

#### 重写属性的Getters和Setters

你可以提供定制的 getter（或 setter）来重写任意继承来的属性，无论继承来的属性是存储型的还是计算型的属性。子类并不知道继承来的属性是存储型的还是计算型的，它只知道继承来的属性会有一个名字和类型。你在重写一个属性时，必需将它的名字和类型都写出来。这样才能使编译器去检查你重写的属性是与超类中同名同类型的属性相匹配的。

你可以将一个继承来的只读属性重写为一个读写属性，只需要你在重写版本的属性里提供 getter 和 setter 即可。但是，你不可以将一个继承来的读写属性重写为一个只读属性。

> 注意：  
> 如果你在重写属性中提供了 setter，那么你也一定要提供 getter。如果你不想在重写版本中的 getter 里修改继承来的属性值，你可以直接返回`super.someProperty`来返回继承来的值。正如下面的`SpeedLimitedCar`的例子所示。

以下的例子定义了一个新类，叫`Car`，它是`Vehicle`的子类。类`Car`引入了一个新的名为`gear`的存储型属性，其默认值为整数1.`Car`类还重写了它继承自类`Vehicle`类的`descripiton`属性，该属性用来提供一个自定义的包含当前装置的描述：

    class Car: Vehicle {
        var gear = 1
        override var description: String {
            return super.description + " in gear \(gear)"
        }
    }

`description`属性的重写实现首先调用`super.description`方法，后者返回`Vehicle`类的`description`属性。`Car`类版本的`description`属性在末尾添加了一些额外的文本来提供当前装置的信息。

创建一个`Car`类的实例并设置它的`gear`和`currentSpeed`属性，你就可以看到这种改变：

    let car = Car()
    car.currentSpeed = 25.0
    car.gear = 3
    println("Car: \(car.description)")
    // Car: traveling at 25.0 miles per hour in gear 3

#### 重写属性观察器（Property Observer）

你可以在属性重写中为一个继承来的属性添加属性观察器。这样一来，当继承来的属性值发生改变时，你就会被通知到，无论那个属性原本是如何实现的。关于属性观察器的更多内容，请看[属性观察器](../chapter2/_10Properties.html#property_observer)。

> 注意：  
> 你不可以为继承来的常量存储型属性或继承来的只读计算型属性添加属性观察器。这些属性的值是不可以被设置的，所以，为它们提供`willSet`或`didSet`实现是不恰当。  
> 此外还要注意，你不可以同时提供重写的 setter 和重写的属性观察器。如果你想观察属性值的变化，并且你已经为那个属性提供了定制的 setter，那么你在 setter 中就可以观察到任何值变化了。

下面的例子定义了一个新类叫`AutomaticCar`，它是`Car`的子类。`AutomaticCar`表示自动挡汽车，它可以根据当前的速度自动选择合适的挡位：

    class AutomaticCar: Car {
        override var currentSpeed: Double {
            didSet {
                gear = Int(currentSpeed / 10.0) + 1
            }
        }
    }

当你设置`AutomaticCar`的`speed`属性，属性的`didSet`观察器就会自动地设置`gear`属性，为新的速度选择一个合适的挡位。具体来说就是，属性观察器将新的速度值除以10，然后向下取得最接近的整数值，最后加1来得到档位`gear`的值。例如，速度为10.0时，挡位为1；速度为35.0时，挡位为4：

    let automatic = AutomaticCar()
    automatic.speed = 35.0
    println("AutomaticCar: \(automatic.description())")
    // AutomaticCar: 4 wheels; up to 5 passengers; traveling at 35.0 mph in gear 4

<a name="preventing_overrides"></a>
## 防止重写

你可以通过把方法，属性或下标脚本标记为*`final`*来防止它们被重写，只需要在声明关键字前加上`final`特性即可。（例如：`final var`, `final func`, `final class func`, 以及 `final subscript`）

如果你重写了`final`方法，属性或下标脚本，在编译时会报错。在扩展中，你添加到类里的方法，属性或下标脚本也可以在扩展的定义里标记为 final。

你可以通过在关键字`class`前添加`final`特性（`final class`）来将整个类标记为 final 的，这样的类是不可被继承的，否则会报编译错误。

