

## 并行的Stream流

### 概述

parallelStream其实就是一个并行执行的流。它通过默认的ForkJoinPool，可能提高多线程任务的速度



获取并行Stream流的两种方式：

* 直接获取并行的流
* 将串行流转成并行流





### 直接获取并行的流

```java
package mao;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;

/**
 * Project name(项目名称)：JDK8_Stream
 * Package(包名): mao
 * Class(类名): Test28
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2023/10/27
 * Time(创建时间)： 16:18
 * Version(版本): 1.0
 * Description(描述)： 无
 */

public class Test28
{
    public static void main(String[] args)
    {
        List<Integer> list = new ArrayList<>();
        for (int i = 1000; i < 1030; i++)
        {
            list.add(i);
        }
        Stream<Integer> stream = list.parallelStream();
        
    }
}
```





### 将串行流转成并行流

```java
package mao;

import java.util.ArrayList;
import java.util.List;
import java.util.function.Function;

/**
 * Project name(项目名称)：JDK8_Stream
 * Package(包名): mao
 * Class(类名): Test29
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2023/10/27
 * Time(创建时间)： 16:21
 * Version(版本): 1.0
 * Description(描述)： 无
 */

public class Test29
{
    public static void main(String[] args)
    {
        List<Integer> list = new ArrayList<>();
        for (int i = 1000; i < 1030; i++)
        {
            list.add(i);
        }
        list.stream().map(Object::toString).parallel()
                .forEach(s -> System.out.println(Thread.currentThread().getName() + "  " + s));
    }
}
```



```sh
main  1019
main  1018
main  1021
main  1020
ForkJoinPool.commonPool-worker-8  1003
ForkJoinPool.commonPool-worker-8  1000
ForkJoinPool.commonPool-worker-30  1007
ForkJoinPool.commonPool-worker-29  1013
ForkJoinPool.commonPool-worker-8  1029
ForkJoinPool.commonPool-worker-26  1014
ForkJoinPool.commonPool-worker-26  1022
ForkJoinPool.commonPool-worker-29  1015
ForkJoinPool.commonPool-worker-8  1028
ForkJoinPool.commonPool-worker-9  1026
ForkJoinPool.commonPool-worker-26  1017
ForkJoinPool.commonPool-worker-11  1004
ForkJoinPool.commonPool-worker-26  1002
ForkJoinPool.commonPool-worker-25  1009
ForkJoinPool.commonPool-worker-20  1010
ForkJoinPool.commonPool-worker-22  1001
ForkJoinPool.commonPool-worker-15  1006
ForkJoinPool.commonPool-worker-1  1023
main  1016
ForkJoinPool.commonPool-worker-19  1008
ForkJoinPool.commonPool-worker-9  1005
ForkJoinPool.commonPool-worker-4  1024
ForkJoinPool.commonPool-worker-9  1025
ForkJoinPool.commonPool-worker-4  1011
ForkJoinPool.commonPool-worker-18  1027
ForkJoinPool.commonPool-worker-30  1012
```





### 效率对比

使用for循环，串行Stream流，并行Stream流来对5亿个数字求和

```java
package mao;

import java.util.stream.LongStream;

/**
 * Project name(项目名称)：JDK_parallelStream
 * Package(包名): mao
 * Class(类名): Test3
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2023/10/27
 * Time(创建时间)： 16:29
 * Version(版本): 1.0
 * Description(描述)： 无
 */

public class Test3
{
    private static long times = 50000000000L;


    public static void main(String[] args)
    {
        testSerialStream();
        testStream();
        testFor();
    }

    private static void testSerialStream()
    {
        System.out.println("serialStream");
        long start = System.currentTimeMillis();
        LongStream.rangeClosed(0, times)
                .parallel()
                .reduce(0, Long::sum);
        long end = System.currentTimeMillis();
        System.out.println("时间：" + (end - start));
    }

    private static void testStream()
    {
        System.out.println("stream");
        long start = System.currentTimeMillis();
        LongStream.rangeClosed(0, times)
                .reduce(0, Long::sum);
        long end = System.currentTimeMillis();
        System.out.println("时间：" + (end - start));
    }

    private static void testFor()
    {
        System.out.println("for");
        long start = System.currentTimeMillis();
        long result = 0L;
        for (long i = 1L; i < times; i++)
        {
            result += i;
        }
        long end = System.currentTimeMillis();
        System.out.println("时间：" + (end - start));
    }
}

```



```sh
serialStream
时间：1259
stream
时间：50593
for
时间：10068
```



**parallelStream是线程不安全的**



