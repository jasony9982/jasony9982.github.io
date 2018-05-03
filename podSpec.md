---
layout: default
---

# cocoapodsPods 公有库创建

1. pod lib create KKLPush
2. 添加文件到 classes中
3. 执行 pod install  
4. 推送到远端
4. 添加 tag
5. 检查有效性 
   >pod lib lint --allow-warnings
5. >pod trunk push --allow-warnings






# 开课啦CocoaPods私有库创建教程

### 创建私有 Spec Repo

先来说第一步，什么是Spec Repo？它是所有的Pods的一个索引，就是一个容器，所有公开的Pods都在这个里面，它实际是一个Git仓库remote端在GitHub上，但是当你使用了Cocoapods后它会被clone到本地的~/.cocoapods/repos目录下，可以进入到这个目录看到master文件夹就是这个官方的Spec Repo了。

因此我们需要创建一个类似于master的私有Spec Repo。我们可以创建一个私有的git仓库当做我们自己的Spec Repo。（已经在公司的gitlab上创建好了名叫[mistong-repo](http://git.mistong.com/ios-framework/mistong-repo)的私有Spec Repo）



创建完成之后在Terminal中执行如下命令

```shell
pod repo add mistong ssh://git@git.mistong.com:10022/ios-framework/mistong-repo.git
```

此时如果成功的话进入到~/.cocoapods/repos目录下就可以看到 `mistong` 这个目录了。至此我们的pod就能在本地search到 `mistong` 里申明的所有库了。



### 创建CocoaPods私有库

参考[CocoaPods建立私有仓库 spec repo](http://www.jianshu.com/p/c6c227c0c221)

现在我们来创建一个私有库，以KKLKit为例。

在Terminal中执行如下命令

```shell
pod lib create KKLKit
```


之后他会问你几个问题，1. 选择开发语言 2.是否需要一个例子工程；3.选择一个测试框架；4.是否基于View测试；5.类的前缀；这几个问题的具体介绍可以去看官方文档，我这里选择的是1.ObjC  ;2.Yes；3.None；4.None；4.KKL。 问完这几个问题他会自动执行pod install命令创建项目并生成依赖。

_Pods.xcodeproj文件在这里并没有什么作用，可以直接删掉。 其中KKLKit目录仅包含了KKLKit的源文件及图片资源等文件，Example目录则包含了KKLKit相关的demo。我们在编写KKLKit的源码的时候，直接在Example的工程中进行。

现在我们进入Example目录，打开.workspace文件。
可以看到，Development Pods目录下的KKLKit目录就是我们的库源码。可以直接修改该目录下的代码。需要注意的是，如果需要在KKLKit中增加或删除文件，在增加文件后，需要在Example的根目录，执行一下`pod install ` 命令。



### 发布私有库

* 编辑podspec文件，发布版本库前，需要先配置好podspec文件，具体模板可以参考[CKKit.podspec](http://git.mistong.com/ios-framework/CKKit/blob/master/CKKit.podspec)。
```
#
# Be sure to run `pod lib lint CKKit.podspec' to ensure this is a
# valid spec before submitting.
#
# Any lines starting with a # are optional, but their use is encouraged
# To learn more about a Podspec see http://guides.cocoapods.org/syntax/podspec.html
#
Pod::Spec.new do |s|
  s.name             = 'CKKit'
  s.version          = '0.1.11'
  s.summary          = 'CKKit是一个工具类的库'
# This description is used to generate tags and improve search results.
#   * Think: What does it do? Why did you write it? What is the focus?
#   * Try to keep it short, snappy and to the point.
#   * Write the description between the DESC delimiters below.
#   * Finally, don't worry about the indent, CocoaPods strips it!
  s.description      = <<-DESC
  CKKit是一个工具类的库，主要包含容器类、通知、数据源相关的管理类及工具类
                       DESC
  s.homepage         = 'http://git.mistong.com/ios-framework/CKKit'
  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
  s.license          = { :type => 'MIT', :file => 'LICENSE' }
  s.author           = { 'jiangjunchen' => 'jiangjunchen@kaike.la' }
  s.source           = { :git => 'ssh://git@git.mistong.com:10022/ios-framework/CKKit.git', :tag => s.version.to_s }
  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'
  # s.module_map = 'CKKit/CKKit.modulemap'
  s.ios.deployment_target = '8.0'
  s.source_files = 'CKKit/Classes/CKKit.h'
  s.subspec 'Base' do |ss|
      ss.source_files = 'CKKit/Classes/Base/**/*'
  end
  s.subspec 'UIKit' do |ss|
      ss.source_files = 'CKKit/Classes/UIKit/**/*'
      ss.dependency 'CKKit/Base'
  end
  # s.source_files = 'CKKit/Classes/**/*'
  # s.resource_bundles = {
  #   'CKKit' => ['CKKit/Assets/*.png']
  # }
  # s.public_header_files = 'Pod/Classes/**/*.h'
  # s.frameworks = 'UIKit', 'MapKit'
  # s.dependency 'AFNetworking', '~> 2.3'
end
```

上传后保存会有目录结构  
![](http://note.youdao.com/yws/res/1892/WEBRESOURCEba18671e1fe66bf403fecade8204e488)

* git commit后，需要打tag，并push到远程。如

  ```shell
  $ git tag -m "first release" "0.1.0" 
  $ git push --tags     #推送tag到远端仓库
  ```

* 验证私有库的正确性

  ```shell
  pod lib lint --allow-warnings
  ```

  验证成功

* 向Spec Repo提交podspec。 向我们的私有Spec Repo提交podspec只需要一个命令

  ```shell
  pod repo push mistong ./KKLKit.podspec --allow-warnings
  ```
  其中，`mistong`就是我们的私有Spec Repo，`./KKLKit.podspec`则是我们私有库的描述文件路径

#### 注：如果podspec的依赖包含你自己的私有库。那么验证命令后面要填写你所依赖的那个私有库的连接地址和cocoapods的默认地址

如：$ pod lib lint --sources='ssh://git@git.mistong.com:10022/ios-framework/mistong-repo.git,https://github.com/CocoaPods/Specs' --use-libraries --allow-warnings



### 安装私有库

安装私有库需要在Podfile文件顶部申明一下我们的私有Repo Spec地址，以及pod公共的Repo Spec地址。 再在podfile中通过`pod 'CKKit', '~> 0.0.1'`来申明私有库

```shell
source 'ssh://git@git.mistong.com:10022/ios-framework/mistong-repo.git'
source 'https://github.com/CocoaPods/Specs.git'
```



### [关于Resources](http://blog.xianqu.org/2015/08/pod-resources/)

新版的cocoapods推荐在podspec使用`resource_bundle`方式引用资源文件：

```
s.resource_bundle = {'LibName' => ['LibName/*.png']}
```

理论上一个 bundle 里可以有一个 asset catalog。Xcode 最后会把它们编译成 `Assets.car` 文件。但是使用`resource_bundle`这种方式对`Image.xcassets`支持不太好，会出现找不到图片的情况。所以增对图片资源，要么不使用xcassets，直接将图片放到文件目录中；要么使用podspec的resources方式添加图片的xcassets。

我这里推荐采用resources方式添加图片资源，因为pdf格式的图片需要放到xcassets中才能直接使用：

```
s.resources = 'XHXUser/Assets/**/*'
```

那么在代码中可以用如下方式获取pod库中的图片资源:

```objective-c
NSBundle *bundle = [NSBundle bundleForClass:[XHXUserAPI class]];
UIImage *image = [UIImage imageNamed:@"LoginLogo" inBundle:bundle compatibleWithTraitCollection:nil];
```

当然，你可以在pod库内部创建一个宏`ModuleImage`，用于获取私有库内部的图片:

```objective-c
// 根据图片名，获取模块内部的图片
#define ModuleImage(name) [UIImage imageNamed:(name) inBundle:[NSBundle bundleForClass:[self class]] compatibleWithTraitCollection:nil]
```


[返回](./)