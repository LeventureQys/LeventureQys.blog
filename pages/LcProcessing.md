# 大通文学部官网开发进度

## 2022.4.5

### hexo部署问题

简单说说昨天部署hexo时遇到的问题，和解决方案：

首先，整体的部署方案来自:[超详细Hexo+Github Pages](https://zhuanlan.zhihu.com/p/370635512)

这里写的比较清楚了，整个安装方案是比较顺利的，出问题的主要原因是之前某一步报错我却没看到就直接进行下一步了，导致整个流程出错了。

至于报错原因它在黑白板上写的很清楚，百度一下，其实都有，不用自己去瞎猜。如果实在是运行不了了，可以直接将整个文件夹的东西全删掉再重新进行步骤，这些都是合法操作，不用担心注册表这些的问题

有个比较明显的问题，就是我们在修改_config.yml的时候，最下端，教程上写的部署参数是

deploy:
  type: git
  repo: git@github.com:LeventureQys/LeventureQys.github.io.git
  branch: master

而我实际页面的部署在gh-pages，所以我怎么都看不到我自己的部署情况，但是那个_config.yml又传过去的，所以我就可以看到我的title和subtitle，之前一直没搞明白，所以我之前一直很抓狂

有两种解决方法，把 branch: 后面的参数改成当前你的source名称就可以，在你的github的库后台，找到setting -> page里可以看到。或者调整你当前的source文件夹与之对应，都是可以的
