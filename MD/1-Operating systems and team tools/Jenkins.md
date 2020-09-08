

# 简介
## 持续继承（continuous integration）
持续继承是一种软件开发实践，要求团队成员经常集成他们的工作。开发人员每次代码合并都会出发持续继承服务器进行自动部署，这个过程包括了编译、测试单元、继承测试、质量分析等步骤，通过自动化的过程进行验证，以尽快检测继承错误。这种方法会使得继承问题大幅减少，更快地实现由凝聚力的软件开发。

持续继承原则：
- 需要版本控制软件保障团队成员提交的代码不会导致集成失败。常用的版本控制软件有Git、CVS、Subversion等
- 开发人员必须及时向版本控制库中提交代码，也必须经常性地从版本控制库中更新代码到本地
- 需要有专门的集成服务器来执行集成构建。根据项目的具体实际、集成构建可以被软件的修改来直接出发、也可以定时启动，如每半个小时构建一次
- 必须保证构建的成功。如果构建失败，修复构建过程中的错误是优先级最高的工作。一旦修复，需要手动启动一次构建

![continuous-integration process](./img/14570-a8c0bf0318fa35e4.png)

### Jenkins对持续集成的支持
#### 构建触发器
- 其他工程构建后触发
- 定时构建
- Build when a change is pushed to GitLab. [GitLab webhook](https://deploy.skyunion.net/project/friend)
- GitHub hook trigger for GITScm polling

## DSL(Domain Specific Language)
DSL-专门针对一个特定的问题领域含有建模所需的语法和语义，在与问题域相同的抽象层次对概念建模

# 如何使用Jenkins

# 项目构建

# 通知和报告

# 用户与权限管理

# Jenkins集成容器

# 应用案例