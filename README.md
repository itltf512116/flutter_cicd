*** Fluttter 使用CI自动打包，目前仅实现了安卓部分，IOS需要Mac机器
*** 搭配CI实现多个分支自动编译打包，提升相关环节的效率

### Android

```shell
# 主要逻辑为在装安卓编译环境的容器中打包编译，完成后使用fastlane上传到蒲公英，然后向企业微信推送一条编译完成的消息（消息附有下载地址和简短说明）

# 进入android 目录，查看Dockerfile 中使用的sdk,buildtools等等版本
#	platform安装了 27-33版本
#	build-tools 安装了33.0.1和30.0.3 
#	文档中设置了Flutter 的pub地址为https://storage.flutter-io.cn，有需要可以更改
#	设置了gradle的 GRADLE_USER_HOME=/cache/gradle，这个是为了把目录挂载出来，减少下次编译时的一些下载，不需要缓存可以不挂载出来

# 检查没问题之后，假设flutter 版本需要为3.7.0 则执行
make flutter_version=3.7.0 image  

# fastlane
# 参考地址 https://flutter.cn/docs/deployment/cd#fastlane
# https://docs.fastlane.tools/
# 建议先在本地按教程安装fastlane，然后按教程在代码的android目录下初始化
cd ./android && fastlane init

# 执行完成之后，应该在flutter代码中的android 目录中生成类似 ./android的目录文件，主要是Gemfile和fastlane文件夹

# 如果感觉ruby加载慢，可以参考 android/Gemfile 中的source
# ./android/fastlane/Fastfile 是fastlane执行时的脚本文件，参考下注释和官网文档，填充里面的变量信息（这里整理的示例文档，使用了CI系统中的环境变量注入的功能，比如这个ENV["ENV_WECHAT_WEBHOOK_URL"] 表示读取这个环境变量）

# 执行完成后按教程设置一些信息，提交到代码库

# 如果使用了CI，可以直接参考自家用的CI系统，主要需要执行命令
cd android && bundle install && bundle exec fastlane android beta

# 执行完成并处理成功后，应该可以看到输出日志并推送消息到企业微信
# 如果不想推送到企业微信，可以去 https://docs.fastlane.tools/plugins/available-plugins/ 这个地址查找可用的其他类型插件

# 目前容器主要用到三个缓存目录，有需要可以把目录挂载出来加快编译速度
#   /var/lib/gems/2.7.0/cache ==》这个是ruby gem 用的，目前镜像里面是这个版本目录
#   /usr/local/bin/flutter/.pub-cache ==>这个是flutter用的，可以通过给容器指定环境变量 PUB_CACHE 来修改这个目录
#   /cache/gradle ==》这个是gradle用的，可以通过给容器指定环境变量 GRADLE_USER_HOME 来修改


```

