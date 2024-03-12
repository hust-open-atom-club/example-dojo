# world
## venus

这个挑战被命名为`venus`，位于`world`模块内。

当用户开始这个挑战时，他们的环境将会有：
- `/flag`
- `/challenge/takeoff`
- `/challenge/launch`

在这个挑战中，`takeoff`是一个静态实例化的文件。
还有文件`.init`、`planets.txt`和`launch.j2`，学生永远不会看到这些文件。
在运行时，`.init`读取`planets.txt`来根据模板文件`launch.j2`构建`launch`。
这给挑战带来了依赖于flag的静态实例化。
一般来说，最好是事先定义一个静态实例化文件池，这样就不需要在每次挑战开始时运行时进行这种设置。
然而，对于简单的情况，这可能是可以接受的。
但重要的是要注意，这些静态实例的结果仍然依赖于flag，这意味着用不同的flag运行挑战（例如，用一个练习flag），将会根本改变实例。

