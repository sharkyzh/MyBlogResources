---
title: 'MVC中编译时,让View也弹出异常'
category:
  - .net
  - mvc
tags:
  - .net
  - mvc
abbrlink: eb7d2892
date: 2017-12-25 22:46:17
---
1. 卸载项目
2. 编辑项目
3. 在打开页面中 找到** <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|AnyCPU' "> ** (每个VS版本的节点可能有不同)
　在次节点中增加 **<MvcBuildViews>true</MvcBuildViews>**的属性，如果你用 Debug 编译请在 Debug节点下增加
4. 将页面拖到最下面，在最后一段处</Project>前面加上以下内容
```
<Target Name="MvcBuildViews" AfterTargets="AfterBuild" Condition="'$(MvcBuildViews)'=='true'">
　　<AspNetCompiler VirtualPath="temp" PhysicalPath="$(WebProjectOutputDir)" />
</Target>
```
5. 重新加载项目