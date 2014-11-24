title: 现代java开发指南：第一部分 Java已不是你父亲那一代的样子
---

与历史上任何其他的语言相比，可能要排除c语言和cobol语言，越来多能工作、有用的代码用Java语言写出。在20年前Java首次发布时，它引了软件界的风暴。在那时，相对c++语言，Java语言要更简单，更安全，而且在一段时间后，Java语言的性能也得到了提升（这依赖于具体的使用情况，一个大型的Java程序于相同的c++程序相比，可能会慢一点，或者一样快，或者更快一些）。比起c++，Java牺牲非常少性能，却提供了巨大的生产力提升。

Java是一门工作语言，程序员值得信任的工具，它只会采用已经被别的语言尝试过的正确的理念，同时增加新的特性只会去解决主要的痛点问题。Java是否一直忠于它的使命是一个开放性的问题，但它确实是努力让自已的道路不被当前的时尚所左右太远。在智能芯片，嵌入式设备和大型主机上，java都在用于编写代码。甚至被用来编写对任务和安全要求苛刻的硬件实时软件。

然而，最近一些年，Java得到了不少负面的评价，特别是在互联网初创公司中。相对于别的语言如Ruby和python，Java显得死板，而且与配置自由的框架如Rails相比，java的网页开发框架需要使用大量的xml文件做为配置文件。进一步说，java在大型企业中广泛使用导致了java所采用的编程模式和做法在一个非常大的具有鲜明等级关系的技术团队中会很有用，但是这些编程模式和做法对于快速开发打破常规的初创公司来说，不是很合适。

但是，Java已经改变。Java最近增加了lambda表达式和traits。以库的形式提供了像erlang和go所支持的轻量级线程。并且最重要的是，提供了一个现代的、轻量级的方式用于取代陈旧笨重以大量xml为基础的方法，指导API、库和框架的设计。

最近一些年，Java生态圈发生了一些有趣的事：大量的以jvm为基础的程序语言变得流行；其中一些语言设计的十分好（我个人喜欢Clojure和Kotlin）。但是与这些可行或者推荐的语言相比，Java与其它基于JVM的语言来说，确实有几个优点：熟悉，技持，成熟，和社区。通过新代工具和新代的库，Java实际上在这几个方面做了很多的工作。因此，许多的硅谷初创公司，一但他们成长壮大后，就会回到Java，或者至少是回到JVM上，这点就不会另人惊奇了。

这份介绍性指南的目标是想学习如何写现代精简Java代码的程序员（900万），或者是那些听到了或体验过Java坏的方面的Python/Ruby/Javascript程序员。并且指南展示了Java中已经改变的方面和这些改变的方面如何让Java获得另人赞叹的性能，灵活性和可监控性而不会牺牲太多的Java沉稳方面。

## JVM

对Java术语简单价绍一下，Java在概念上被分为三个部分：Java，Java运行时库和J虚拟机，或者叫JVM。如果你熟悉Node.js，Java语言类同于JavaScript，运行时库类同于Node.js，JVM类同于V8引擎。JVM和运行时库被打包成大家所熟知的Java运行时环境，或者叫JRE（虽然常常人们说JVM实际上指的是JRE）。Java开发工具，JDK，是指某一个JRE的发行版，通常包括很多开发工具像javac，java编绎器，还有很多程序监控和性能分析工具。JRE通常有几个分支，如支持嵌入式设备开发版本，但是本博客中，我们只会涉及到JRE支持服务器（桌面）开发的版本，这就是众所周知的 JavaSE（Java标准版）。

有一些项目实现了JVM和JRE的标准，其中一些是开源的项目，还有一些是商业项目。有些JVM非常特殊，如有些JVM运行硬件实时嵌入式设备软件，还有JVM可以在巨大的内存上运行软件。但是我们将会使用HotSpot，一个由Oracle支持的的自由，通用的JVM实现，同时HotSpot也是开源OpenJDK项目的一部分。

Java构建JVM，JVM同时运行Java（虽然JVM最近为了其它语言做了一些专门的修改）。但是什么是JVM，Cliff Click的这个演讲解释了什么是JVM，简单来说，JVM是一台抽象现实的魔法机器。JVM使用漂亮，简单和有用的抽象，好像无限的内存和多态，这些听起来实现代价很高，并且实现这些特征用如此高效的形式以致于他们能很容易能与没有提供这些有用抽象的运行时竞争。更需要说明的是，JVM拥有最好内存回收算法并能在大范围的产品中使用，JVM的JIT允许内联和优化虚方法的调用（这是许多语言中最有用的抽像的核心），在保存虚方法的用处的同时，使调用虚方法非常方便和快捷。JVM的JIT（即时编绎器）是基础的高级性能优化编绎器，和你的应用一起运行。

当然JVM也隐藏了很多的操作系统级别的细节，如内存模型（代码在不同的CPU上运行怎样看待其它的CPU操作引起的变量的状态的变化）和使用定时器。JVM还提供运行时动态链接，热代码交换，监控几乎所有在JVM上运行的代码，还有库中的代码。

这并不是说JVM是完美的。当前Java的数组缺失存放复杂结构体的能力（计划将在Java9中解决），还有适当的尾调用优化。尽管JVM如此的成熟，测试良好，快速，灵活，还有丰富的运行时分析和监控，我也不会考虑运行一个关键重要的服务器进程在任何基础之上。

理论已经足够了。在我们深入讲解之前，你应该下载在这里下载最新的JDK，或者使用你系统自代的包管理器安装最新的OpenJDK。

## 构建

我们开启我们现代Java构建工具旅程。在Java很长的历史时间内，曾有几个构建工具，如Ant和Maven，他们大多数都基于XML。但是现代的Java开发者使用Gradle（最近成为Android的官方构建工具）。Gradle是一个成熟，深入开发，现代Java构建工具，它使用了在Groovy基础上的DSL语言来说明构建过程。他集成了Maven中的简单性和Ant的强大性和灵活性，同时抛弃所有的XML。但是Gradle并不是没有错误：当他使最通用的部分简单和可声明式的同时，就会有很多事情变得非常不通用，这就要求返回来使用命令式的Groovy。

现在让我们使用Gradle创建一个新的Java项目。首先，我们从这里下载Gradle，安装。现在我们开始创建项目，项目名叫JModern。创建一个叫Jmodern的目录，切换到击刚才创建的目录，执行：

```bash
gradle init --type java-library
```
Gradle 创建了项目的初始文件夹结构，包括子类（Library.java和LibraryTest.java），我们将在后面删除这两个文件：

![figure1](http://blog.paralleluniverse.co/images/jmodern/figure1.png)

源代码在`src/main/java/`目录下。我们将主类命名为`jmodern.Main`（所以主类的源文件就在`src/main/java/jmodern/Main.java`），这个程序将会把`Hello World`程序做一点小小的变化。同时为了测试Gradle，将会使用`Google's Guava`。使用你喜欢的编辑器创建`src/main/java/jmodern/Main.java`，源代码如下：

```java
package jmodern;

import com.google.common.base.Strings;

public class Main {
    public static void main(String[] args) {
        System.out.println(triple("Hello World!"));
        System.out.println("My name is " + System.getProperty("jmodern.name"));
    }

    static String triple(String str) {
        return Strings.repeat(str, 3);
    }
}

```

相应创建一个小的测试用例：在`src/test/java/jmodern/MainTest.java`:

```java
package jmodern;

import static org.hamcrest.CoreMatchers.*;
import static org.junit.Assert.*;
import org.junit.Test;

public class MainTest {
    @Test
    public void testTriple() {
        assertThat(Main.triple("AB"), equalTo("ABABAB"));
    }
}
```

在项目根目录，找到`build.gradle`文件，修改该文件：

```groovy
apply plugin: 'java'
apply plugin: 'application'

sourceCompatibility = '1.8'

mainClassName = 'jmodern.Main'

repositories {
    mavenCentral()
}

dependencies {
    compile 'com.google.guava:guava:17.0'

    testCompile 'junit:junit:4.11' // A dependency for a test framework.
}

run {
    systemProperty 'jmodern.name', 'Jack'
}

```

构建程序设置`jmoder.Main`为主类，声明`Guava`为该程序的依赖库，并且`jmodern.name`为系统属性，方便运行时读取。当输入以下命令：

```bash
gradle run
```

Gradle会从Maven中心仓库下载Guava，编绎程序，然后运行程序，把`jmodern.name`设置成`"Jack"`。总的过程就是这样。

接下来，运行一下测试：

```bash
gradle build
```

生成的测试报告在`build/reports/tests/index.html`：

![figure2](http://blog.paralleluniverse.co/images/jmodern/figure2.png)


## IDE
有些人说IDE会稳藏编程语言的问题。好吧，对于这个问题，我没有意见，但是不管你使用任何语言，一个好的IDE总是有帮助的，而Java在这方面做的最好。当然在文章中选择IDE不是重要的部分，总是要提一下，在Java世界中，有三大IDE： Eclipse，IntelliJ IDEA，和NetBeans，你应该以后使用一下后两者。IntelliJ可能是三者之中最强大的IDE，而NetBeans应该是最符合程序员直觉和最易于使用（我认为也最好看）的IDE。NetBeans通过Gradle的插件对Gradle有最好的支持。Eclipse是最受欢迎的IDE。我在很多年前感觉Eclipse变得混乱，就不使用Eclipse了。当然如果你是一个长期使用Eclipse的用户，也没有什么问题。

安装完Gradle插件，我们的小项目在NetBeans中的样子如下：

![figure3](http://blog.paralleluniverse.co/images/jmodern/figure3.png)

我最喜欢NetBeans的Gradle插件功能不仅是因为IDE列出了所有有关项目的依赖，还有其它的配置插件也能列出，所以我们只需要在构建文件中声明他们一次。如果你在项目中增加新的依赖库，在NetBeans中右键单击项目，选择`Reload Project`，然后IDE将下载你新增加的依赖库。如果你右键单击`Dependencies`结点，选择`Download Sources`，IDE会下载依赖库的源代码和相关javadoc，这样你就可以调试第三方库的代码，还能查看第三方库的文档。

## 用Markdown编写文档

长期以来，Java通过Javadoc生成很好的API文档，而且Java开发者也习惯写Javadoc形式的注释。但是现代的Java开发者喜欢使用Markdown，喜欢使用Markdown为Javadoc增加点乐趣。为了达在Javadoc使用Markdown，我们在构建文件中`dependencies`部分的前面，增加`Pegdown Doclet`Javadoc插件：

```groovy
configurations {
    markdownDoclet
}
```

然后，在`dependencies`中添加一行：

```groovy
markdownDoclet 'ch.raffael.pegdown-doclet:pegdown-doclet:1.1.1'

```

最后，构建文件的最后增加这个部分：

```groovy
javadoc.options {
    docletpath = configurations.markdownDoclet.files.asType(List) // gradle should relly make this simpler
    doclet = "ch.raffael.doclets.pegdown.PegdownDoclet"
    addStringOption("parse-timeout", "10")
}
```

终于，可以在Javadoc注释使用Markdown，还有语法高亮。

你可能会想关掉你的IDE的注释格式化功能（在Netbeans: Preferences -> Editor -> Formatting, choose Java and Comments, and uncheck Enable Comments Formatting）。IntelliJ 有一个插件能高亮在Javadoc中的Markdown语法。

为了测试新增的设置，编写如下的函数：

```java
/**
 * ## The Random String Generator
 *
 * This method doesn't do much, except for generating a random string. It:
 *
 *  * Generates a random string at a given length, `length`
 *  * Uses only characters in the range given by `from` and `to`.
 *
 * Example:
 *
 * ` ``java
 * randomString(new Random(), 'a', 'z', 10);
 * ` ``
 *
 * @param r      the random number generator
 * @param from   the first character in the character range, inclusive
 * @param to     the last character in the character range, inclusive
 * @param length the length of the generated string
 * @return the generated string of length `length`
 */
public static String randomString(Random r, char from, char to, int length) ...
```

然后使用命令`gradle javadoc`在`build/docs/javadoc/`生成html格式文档：

![figure4](http://blog.paralleluniverse.co/images/jmodern/figure4.png)

一般我不常用这个功能，因为IDE对这个功能的语法高亮支持的不太好。但是当你需要在文档中写例子时，这个功能能让你的工作变得更轻松。

## 用Java8写简洁的代码

最近发布的Java8给Java语言带来了很大的改变，因为java原生支持lambda表达式。lambda表达式解决了一个重大的问题，在过去人们解决做一些简单事却写不合理的冗长的代码。为了展示lambda有多大的帮助，我拿出我能想到的令人很恼火的，简单的数据操作代码，并把他用JAva8写出。例子产生了一个list，里面包含了随机生成的学生名字，然后进行按他们的头字母进行分组，并以美观的形式打印出来。现在，修改Main类：

```java
package jmodern;

import java.util.List;
import java.util.Map;
import java.util.Random;
import static java.util.stream.Collectors.*;
import static java.util.stream.IntStream.range;

public class Main {
    public static void main(String[] args) {
        // generate a list of 100 random names
        List<String> students = range(0, 100).mapToObj(i -> randomString(new Random(), 'A', 'Z', 10)).collect(toList());

        // sort names and group by the first letter
        Map<Character, List<String>> directory = students.stream().sorted().collect(groupingBy(name -> name.charAt(0)));

        // print a nicely-formatted student directory
        directory.forEach((letter, names) -> System.out.println(letter + "\n\t" + names.stream().collect(joining("\n\t"))));
    }

    public static String randomString(Random r, char from, char to, int length) {
        return r.ints(from, to + 1).limit(length).mapToObj(x -> Character.toString((char)x)).collect(Collectors.joining());
    }
}
```

Java自动推导了所有lambda的参数类型，Java确保了参数是类型安全的，并且如果你使用IDE，IDE中的自动完成和重构功能对这些参数都可以用的。Java不会像c++使用auto和c#中的var一样，自动推导局部变量，因为这样会让代码的可读性降低。但是这并不意味着要需要手动输入这些类型。例如，光标在students.stream().sorted().collect(Collectors.groupingBy(name -> name.charAt(0)))这一行代码上，在NetBeans中按下Alt+Enter，IDE会推导出结果适当的类型。

如果想感觉一下函数式编程的风格，将Main函数改成下面的形式：

```java
public static void main(String[] args) {
    range(0, 100)
            .mapToObj(i -> randomString(new Random(), 'A', 'Z', 10))
            .sorted()
            .collect(groupingBy(name -> name.charAt(0)))
            .forEach((letter, names) -> System.out.println(letter + "\n\t" + names.stream().collect(joining("\n\t"))));
}
```
跟以前的代码确实不一样（看哪，没有类型），但是这应该不太容易理解这段代码的意思。

就算Java有lambda，但是Java仍然没有函数类型。其实，lambda在java中被转换成近似为functional接口，即有一个抽象方法的接口。这种自动转换使遗留代码能够和lambda在一起很好的工作。例如：Arrays.sort方法是需要一个Comparateor接口的实例，这个接口简单描述成单一的揭抽象 `int compare(T o1, T o2)`方法。在java8中，可以使用lambda表达式对字符串数组进行排序，根据数组元素的第三个字符：

```java
Arrays.sort(array, (a, b) -> a.charAt(2) - b.charAt(2));
```
Java也增加了能实现方法的接口（将这种接口换变成“traits”）。例如，FooBar接口有两个方法，一个是抽象方法foo，另一个是有默认实现的bar。别一个useFooBar调用FooBar：

```java

interface FooBar {
    int foo(int x);
    default boolean bar(int x) { return true; }
}

int useFooBar(int x, FooBar fb) {
    return fb.bar(x) ? fb.foo(x) : -1;
}
```

虽然FooBar有两个方法，但是只有一个foo是抽象的，所以FooBar也是一个函数接口，并且可以使用lambda表达式创建FooBar，例如：

```java
useFooBar(3, x -> x * x)
```

将会返回9。

## 通过Fibers实现轻量级并发控制

有许多人和我一样，都对并发数据结构感兴趣，而这一块是JVM的后花园。一方面，JVM对于CPU的并发原语提供了低级方法如CAS结构和内存栅栏，另一方面结合内存回收机制提供了平台中立的内存模型。但是，对那些使用并发控制的程序员来说，并不是为了扩展他们的软件，而使用并发控制，而是他们不得不使用并发控制使自己的软件可扩展。从这方面说，Java并发控制并不是很好，是有问题。

真的，Java从开始就被设计成为并发控制，并且在每一个版本中都强调他的并发控制数据结构。Java已经高质量的实现了很多非常有用的并发数据结构（如并发HashMap，并发SkipListMap，并发LinkedQueue），有些都没有在Erlang和Go中实现。Java的并发控制通常领先c++5年或者更长的时间。但是你会发现正确高效地使用这些并发控制数据结构非常困难。当我们使用线程和锁时，刚开始你会发现它们工作的很好，到了后面当你需要更多并发控制时，发现这些方法不能很好的扩展。然后我们使用线程池和事件，这两个东西有很好的扩展性，但是你会发现很难去解释共享变量，特别是在语言级别没有对共享变量的可变性进行限制。进一步说，如果你的问题是内核级线程不能很好的扩展，那么对事件的异步处理是一个坏想法。为什么不简单修复线程的问题呢？这恰恰是Erlang和Go所采用的方式：轻量级的用户线程。轻量级用户线程通过简单，阻塞式的编程方法高效使用同步结构，将内核级的并发控制映射到程序级的并发控制，而不用牺牲可扩展性，同时比锁和信号更简单。

Quasar是一个我们创建的开源库，它给JVM增加了真正的轻量级线程（在Quasar叫纤程），同得能够很好的同系统级线程很好在一起的工作。Quasar同Go的CSP一样，同时有一个基结Erlang的Actor系统。对付并发控制，纤程是一个很好的选择。纤程简单、优美和高效。现在让我们来看看它：

首先，我们设置构建脚本，添加以下的代码在build.gradle 中：

```groovy
configurations {
    quasar
}

dependencies {
    compile "co.paralleluniverse:quasar-core:0.5.0:jdk8"
    quasar "co.paralleluniverse:quasar-core:0.5.0:jdk8"
}

run {
    jvmArgs "-javaagent:${configurations.quasar.iterator().next()}" // gradle should make this simpler, too
}
```

更新依赖，编辑Main.java:

```java
package jmodern;

import co.paralleluniverse.fibers.Fiber;
import co.paralleluniverse.strands.Strand;
import co.paralleluniverse.strands.channels.Channel;
import co.paralleluniverse.strands.channels.Channels;

public class Main {
    public static void main(String[] args) throws Exception {
        final Channel<Integer> ch = Channels.newChannel(0);

        new Fiber<Void>(() -> {
            for (int i = 0; i < 10; i++) {
                Strand.sleep(100);
                ch.send(i);
            }
            ch.close();
        }).start();

        new Fiber<Void>(() -> {
            Integer x;
            while((x = ch.receive()) != null)
                System.out.println("--> " + x);
        }).start().join(); // join waits for this fiber to finish
    }
}
```

现在有通过channel，有两个纤程可以进行通信。

Strand.sleep，和Strand类的所有方法，在原生Java线程和fiber中都能很好的运行。现在我们将第一个fiber替换成原生的线程：

```java
new Thread(Strand.toRunnable(() -> {
    for (int i = 0; i < 10; i++) {
        Strand.sleep(100);
        ch.send(i);
    }
    ch.close();
})).start();
```

这也运行的很好（当然我们已在我们的应用中运行百万级的fiber，也用了几千线程）。

我们处一下channel selection （模拟Go的select）。

```java
package jmodern;

import co.paralleluniverse.fibers.Fiber;
import co.paralleluniverse.strands.Strand;
import co.paralleluniverse.strands.channels.Channel;
import co.paralleluniverse.strands.channels.Channels;
import co.paralleluniverse.strands.channels.SelectAction;
import static co.paralleluniverse.strands.channels.Selector.*;

public class Main {
    public static void main(String[] args) throws Exception {
        final Channel<Integer> ch1 = Channels.newChannel(0);
        final Channel<String> ch2 = Channels.newChannel(0);

        new Fiber<Void>(() -> {
            for (int i = 0; i < 10; i++) {
                Strand.sleep(100);
                ch1.send(i);
            }
            ch1.close();
        }).start();

        new Fiber<Void>(() -> {
            for (int i = 0; i < 10; i++) {
                Strand.sleep(130);
                ch2.send(Character.toString((char)('a' + i)));
            }
            ch2.close();
        }).start();

        new Fiber<Void>(() -> {
            for (int i = 0; i < 10; i++) {
                SelectAction<Object> sa
                        = select(receive(ch1),
                                receive(ch2));
                switch (sa.index()) {
                    case 0:
                        System.out.println(sa.message() != null ? "Got a number: " + (int) sa.message() : "ch1 closed");
                        break;
                    case 1:
                        System.out.println(sa.message() != null ? "Got a string: " + (String) sa.message() : "ch2 closed");
                        break;
                }
            }
        }).start().join(); // join waits for this fiber to finish
    }
}
```

从Quasar 0.6.0开始，可以在选择状态中使用使用lambda表达式，最新的代码可以写成这样：

```java
for (int i = 0; i < 10; i++) {
    select(
        receive(ch1, x -> System.out.println(x != null ? "Got a number: " + x : "ch1 closed")),
        receive(ch2, x -> System.out.println(x != null ? "Got a string: " + x : "ch2 closed")));
}
```

看看fiber的高性能io：

```java
package jmodern;

import co.paralleluniverse.fibers.*;
import co.paralleluniverse.fibers.io.*;
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.*;
import java.nio.charset.*;

public class Main {
    static final int PORT = 1234;
    static final Charset charset = Charset.forName("UTF-8");

    public static void main(String[] args) throws Exception {
        new Fiber(() -> {
            try {
                System.out.println("Starting server");
                FiberServerSocketChannel socket = FiberServerSocketChannel.open().bind(new InetSocketAddress(PORT));
                for (;;) {
                    FiberSocketChannel ch = socket.accept();
                    new Fiber(() -> {
                        try {
                            ByteBuffer buf = ByteBuffer.allocateDirect(1024);
                            int n = ch.read(buf);
                            String response = "HTTP/1.0 200 OK\r\nDate: Fri, 31 Dec 1999 23:59:59 GMT\r\n"
                                            + "Content-Type: text/html\r\nContent-Length: 0\r\n\r\n";
                            n = ch.write(charset.newEncoder().encode(CharBuffer.wrap(response)));
                            ch.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }).start();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }).start();
        System.out.println("started");
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

我们做了什么？首先我们启动了一个一直循环的fiber，用于接收TCP连接。对于每一个连接上的连接，这个fiber会启动另外一个fiber去读请求，发送回应，然后关闭。这段代码是阻塞IO的，在后台使用异步EPoll IO，所以它和异步IO服务器，有一样的扩展性。（我们将在Quasar中极大的提高IO性能）。

## 可容错的Actor和热代码的更换

Actor模型，受欢迎是有一半原因是Erlang，意图是编写可容错，高可维护的应用。它将应用分割成独立可容错的容器单元-Actors，标准化处理错误中恢复方式。

当我们开始Actor，将`compile "co.paralleluniverse:quasar-actors:0.5.0"` 加到你的构建脚本中的依赖中去。

我们重写Main函数，要让我们的应用可容错，代码会变的更加复杂。

```java
package jmodern;

import co.paralleluniverse.actors.*;
import co.paralleluniverse.fibers.*;
import co.paralleluniverse.strands.Strand;
import java.util.Objects;
import java.util.concurrent.ThreadLocalRandom;
import java.util.concurrent.TimeUnit;

public class Main {
    public static void main(String[] args) throws Exception {
        new NaiveActor("naive").spawn();
        Strand.sleep(Long.MAX_VALUE);
    }

    static class BadActor extends BasicActor<String, Void> {
        private int count;

        @Override
        protected Void doRun() throws InterruptedException, SuspendExecution {
            System.out.println("(re)starting actor");
            for (;;) {
                String m = receive(300, TimeUnit.MILLISECONDS);
                if (m != null)
                    System.out.println("Got a message: " + m);
                System.out.println("I am but a lowly actor that sometimes fails: - " + (count++));

                if (ThreadLocalRandom.current().nextInt(30) == 0)
                    throw new RuntimeException("darn");

                checkCodeSwap(); // this is a convenient time for a code swap
            }
        }
    }

    static class NaiveActor extends BasicActor<Void, Void> {
        private ActorRef<String> myBadActor;

        public NaiveActor(String name) {
            super(name);
        }

        @Override
        protected Void doRun() throws InterruptedException, SuspendExecution {
            spawnBadActor();

            int count = 0;
            for (;;) {
                receive(500, TimeUnit.MILLISECONDS);
                myBadActor.send("hi from " + self() + " number " + (count++));
            }
        }

        private void spawnBadActor() {
            myBadActor = new BadActor().spawn();
            watch(myBadActor);
        }

        @Override
        protected Void handleLifecycleMessage(LifecycleMessage m) {
            if (m instanceof ExitMessage && Objects.equals(((ExitMessage) m).getActor(), myBadActor)) {
                System.out.println("My bad actor has just died of '" + ((ExitMessage) m).getCause() + "'. Restarting.");
                spawnBadActor();
            }
            return super.handleLifecycleMessage(m);
        }
    }
}
```

代码中有一个`NaiveActor`产生一个`BadActor`，这个产生出来的的Actor会偶然失败。由于我们的父actor监控子Actor，当子Actor过早的死去，父actor会得到通知，然后重新启动一个新的Actor。

这个例子，Java相当的恼人，特别是当它用`instanceof`测试消息的类型和转换消息的类型的时候。这一方面通过模式匹配Clojure和Kotlin做的比较好（以后我会发一篇关于Kotlin的文章）。所以，是的，所有的类型检查和类型转换相当另人讨厌。这种类型代码鼓励你去试一下Kotlin，你真的该去使用一下（我就试过，我非常喜欢Kotlin，但是要用于生产环境使用它还有待成熟）。就个人来说，这种恼人非常小。

回到主要问题来。一个基于Actor的可容错系统关键的组件是减少宕机时间不管是由于应用的错误，还是由于系统维护。我们将在第二部分探索JVM的管理，接下来展示一下Actor的热代码交换。

在热代码交换的问题上，有几种方法（例如：JMX，将在第二部分讲）。但是现在我们通过监控文件系统来实现。首先在项目目录下创建一个叫`modules`子文件夹，在build.gradle的run添加以下代码：

```groovy

systemProperty "co.paralleluniverse.actors.moduleDir", "${rootProject.projectDir}/modules"
```

打开终端，启动程序。程序启动后，回到IDE，修改`BadActor`：

```java
@Upgrade
static class BadActor extends BasicActor<String, Void> {
    private int count;

    @Override
    protected Void doRun() throws InterruptedException, SuspendExecution {
        System.out.println("(re)starting actor");
        for (;;) {
            String m = receive(300, TimeUnit.MILLISECONDS);
            if (m != null)
                System.out.println("Got a message: " + m);
            System.out.println("I am a lowly, but improved, actor that still sometimes fails: - " + (count++));

            if (ThreadLocalRandom.current().nextInt(100) == 0)
                throw new RuntimeException("darn");

            checkCodeSwap(); // this is a convenient time for a code swap
        }
    }
}
```

我们增加了`@Upgrade`注解，因为我们想让这个类进行升级，这个类修改后失败变少了。现在程序还在运行，新开一个终端，通过`gradle jar`，重新构建程序。不熟悉java程序员，JAR（Java Archive）用来打包Java模块（在第二部分会讨论Java打包和部署）。最后，在第二个终端中，复制`build/libs/jmodern.jar`到`modeules`文件夹中，使用命令：

```bash
cp build/libs/jmodern.jar modules
```

你会看到程序更新运行了（这个时候取决于你的操作系统，大概要十秒）。注意不像我们在失败后重新启动`BadActor`,当我们交换代码时，程序中的中间变量保存下来了。

设计一个基于Actor设计可容错的系统是一个很大的主题，但是我希望你已经对它有点感觉。

## 高级话题：可插拔类型
结束之前，我们将探索一个危险的领域。我们接下来介绍的工具还没有加入到现代Java开发工具箱中，因为使用它仍然很繁琐，不过它将会从IDE融合中得到好处，现在这个工具仍然很陌生。虽然如此，如果这个工具持继开发并且不断充实，它带来的可能性非常的酷，如果他不会在疯子手中被乱用，它将会非常有价值，这就是为什么我们把它列在这里。

在Java8中，一个潜在最有用的新特性，是类型注解和可拔类型系统。Java编绎器现在允许在任何地方增加对类型的注解（一会我们举个例子）。这里结合注解预处理器，打发可插拔类型系统。这些是可选的类型系统，可以关闭或打开，能给Java代码够增加强大的基于类型检查的静态验证功能。Checker框架就这样一个库，它允许高级开发者写自己的可插拔类型系统，包括继承，类型接口等。它自己包括了几种类型系统，如检查可空类型，污染类型，正则表达式，物理单位类型，不可变数据等等。

Checker目前还不能很好的与IDE一起工作，所有这节，我将不使用IDE。首先修改`build.gradle`，增加：

```groovy
configurations {
    checker
}

dependencies {
    checker 'org.checkerframework:jdk8:1.8.1'
    compile 'org.checkerframework:checker:1.8.1'
}
```
到相应的`configurations`,`dependencies`部分。

然后，增加下面部分到构建文件中：

```groovy
compileJava {
    options.fork = true
    options.forkOptions.jvmArgs = ["-Xbootclasspath/p:${configurations.checker.asPath}:${System.getenv('JAVA_HOME')}/lib/tools.jar"]
    options.compilerArgs = ['-processor', 'org.checkerframework.checker.nullness.NullnessChecker,org.checkerframework.checker.units.UnitsChecker,org.checkerframework.checker.tainting.TaintingChecker']
}
```
正如我说的，笨重的。

最后一行说明我们使用Checker的空值类型系统，物理单位类型系统，污染数据类型系统。

现在我们做一些实验。首先，试一下空值类型系统，他能防止空指针的错误。

```java
package jmodern;

import org.checkerframework.checker.nullness.qual.*;

public class Main {
    public static void main(String[] args) {
        String str1 = "hi";
        foo(str1); // we know str1 to be non-null

        String str2 = System.getProperty("foo");
        // foo(str2); // <-- doesn't compile as str2 may be null
        if (str2 != null)
            foo(str2); // after the null test it compiles
    }

    static void foo(@NonNull String s) {
        System.out.println("==> " + s.length());
    }
}
```
Checker的开发者很友好，注解了整个JD可空的返回类型，所以你当你从库中反回值时不要返回null值当有@NonNull注解时。

接下来，我们试一下单位类型系统，防止单位类型转换错误。

```java
package jmodern;

import org.checkerframework.checker.units.qual.*;

public class Main {
    @SuppressWarnings("unsafe") private static final @m int m = (@m int)1; // define 1 meter
    @SuppressWarnings("unsafe") private static final @s int s = (@s int)1; // define 1 second

    public static void main(String[] args) {
        @m double meters = 5.0 * m;
        @s double seconds = 2.0 * s;
        // @kmPERh double speed = meters / seconds; // <-- doesn't compile
        @mPERs double speed = meters / seconds;

        System.out.println("Speed: " + speed);
    }
}
```

非常酷吧，根据Checker的文档，你也可以定义自己的物理单位。

最后，试试污染类型系统，它能帮你跟踪被污染（潜在的危险）的数据，例如用户数录入的数据：

```java
package jmodern;

import org.checkerframework.checker.tainting.qual.*;

public class Main {
    public static void main(String[] args) {
        // process(parse(read())); // <-- doesn't compile, as process cannot accept tainted data
        process(parse(sanitize(read())));
    }

    static @Tainted String read() {
        return "12345"; // pretend we've got this from the user
    }

    @SuppressWarnings("tainting")
    static @Untainted String sanitize(@Tainted String s) {
        if(s.length() > 10)
            throw new IllegalArgumentException("I don't wanna do that!");
        return (@Untainted String)s;
    }

    // doesn't change the tainted qualifier of the data
    @SuppressWarnings("tainting")
    static @PolyTainted int parse(@PolyTainted String s) {
        return (@PolyTainted int)Integer.parseInt(s); // apparently the JDK libraries aren't annotated with @PolyTainted
    }

    static void process(@Untainted int data) {
        System.out.println("--> " + data);
    }
}
```

Checker通过类型接口给于Java可插拔交互类型。并且可以通过工具和预编绎库增加类型注解。Haskell都做不到这一点。

Checker还没有到他的黄金时段，如果使用明智的话，它会成为现代Java开发者手中强有力的工具之一。

## 结束

我们已经看到了Java8中的变化，还有相应现代的工具和库，Java相对于与旧的版本来说，相似性不高。但是Java仍然是大型应用中的亮点，而且Jva和它的生态圈比新的简单的语言，更为成熟和高效。我们了解现代Java程序员是怎样写代码的，但是我们很难一开始就解开Java和Jvm的全部力量。特别当我们知道了Java的监控和性能分析工具，和新的微应用网络应用开发框架。在接下来的文章中我们会谈到这几个话题。

假如你想了解一个开头，第二部分，我们会讨论现代Java打包方法（使用Capsule，有点像npm，但是更酷），监控和管理（使用VisualVM, JMX, Jolokia 和Metrics）
，性能分析（使用 Java Flight Recorder, Mission Control, and Byteman），基准测试（JMH）。第三部分，我们会讨论用Dropwizard，Comsat和Web Actors，JSR-330写一个轻量级可扩展的HTTP服务。

原文地址：[Not Your Father's Java: An Opinionated Guide to Modern Java Development, Part 1](http://blog.paralleluniverse.co/2014/05/01/modern-java/)