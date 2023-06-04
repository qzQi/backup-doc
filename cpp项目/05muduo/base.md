muduo base源码分析，将可以改为modern cpp的全部修改。比如使用C++的线程库，尽量修改吧。

学习muduo CMakeLists.txt的书写，对于提供了脚本构建的使用脚本最方便，自己shell脚本方面也是不熟悉！

配置好是真的舒服！

有一个就是学习下`compile_command.json`文件的配置与使用，对于Makefile组织的文件，直接项目源文件下使用`bear make`即可，这个时候不用配置`.vscode/c_cpp_properties.json`即可（好像是这样的）因为项目根文件夹下；对于CMake管理的工程，生成的json文件应该在build文件夹下，这个时候需要我们配置`.vscode/c_cpp_properties.json`里面的文件路径。



开始！



