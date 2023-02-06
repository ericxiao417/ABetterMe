# Introduction

DevOps是Development和operations的组合，是一种方法论，是一组过程、方法与系统的统称，用于促进应用开发、应用运维和质量保障部门之间的沟通、协调与整合，以打破传统开发和运营之间的壁垒和鸿沟。

DevOps是一种重视“软件开发人员（Dev）”和“IT运维技术人员（Ops）”之间沟通合作的文化、运动或惯例。通过自动化“软件交付”和“架构变更”的流程，来使得构建、测试、发布软件能够更加地快捷、频繁和可靠。

DevOps是一个方法论，并不是指某个软件工具组合。它与OOP/AOP/IOC等类似，都是一个理论或者过程方法的抽象或者代称。

## CI

CI的英文名称是 Continuous Integration, 即 持续集成。在CI中，开发人员将会频繁的向主干提交代码，这些新提交的代码在最终合并到主干前，需要经过编译和自动化测试流进行验证。

CI是在源代码变更后自动检测、拉取、构建和（在大多数情况下）进行单元测试的过程。CI的目标是快速确保开发人员新提交的变更是好的，并且适合在代码库中进一步使用。

## CD

CD可以对应多个英文名称，Continuous Delivery和Continuous Deployment。

### Continuous Delivery

完成CI中构建及单元测试和集成测试的自动化流程后，持续交付可自动将已经验证的代码发布到存储库。为了实现高效的持续交付流程，务必要确保 CD 已内置于开发管道。持续交付的目标是拥有一个可随时部署到生产环境的代码库。

### Continuous Deployment

对于一个成熟的CI/CD 管道（Pipeline）来说，最后的阶段是持续部署。持续部署可以自动将应用发布到生产环境。

# 教程

常用的工具有 Jenkins, Travis CI, GoCD等。

 CI: 持续集成，即在代码构建过程中持续的进行代码的集成、构建、以及自动化测试。有了CI，可以在代码提交的过程中通过单元测试等尽早的发现引入的错误。

CD: 持续交付，在代码构建完毕以后，可以方便的将新版本部署上线，有利于快速迭代产品。

**Job:**  任务，是GitLab CI中可以独立控制并运行的最小单位。在提交代码后，可以针对特定的commit完成一个或者多个Job，从而进行CI/CD。

**Pipeline:** 流水线，可以像流水线一样执行多个Job。GitLab可以在最新生成的commit上建立一个pipeline，在同一个pipeline上产生的多个任务中，所用到的代码都是一致的。

**Stage:** 在pipeline中，可以将多个任务划分在多个阶段中，每个阶段就叫做 stage。只有当前阶段的所有任务都完成并且执行成功后，下一个阶段的任务才可以被执行。如果某个阶段的任务被设定为“允许失败”，那么这个阶段的任务执行情况，不会影响到下一个阶段的执行。

![image-20230206093019236](https://raw.githubusercontent.com/ericxiao417/Pics/main/image-20230206093019236.png)

上图中，整条流水线从左向右依次执行，每一列均为一个阶段，而列中的每个可操控元素均为任务。

## CI/CD流程配置

### 完整定义

GitLab允许在项目中编写.gitlab-ci.yml文件，来配置CI/CD流程。

下面是一个简单的测试->构建->部署的CI/CD流程。

首先，可以定义流程所包含的阶段，我们的流程包含三个阶段：测试、构建和部署。

在.gitlab-ci.yml的开头，定义好所有的阶段，以及执行每个任务之前所需要的环境变量以及准备工作，然后定义整个流程中包含的所有任务：

```yaml
stages:
	- test
	- build
	- deploy
	
variables:
	IMAGE: docker.registry/name/${CI_PROJECT_NAMESPACE}-${CI_PROJECT_NAME}
	
before_script:
	- IMAGE_TAG=${IMAGE}:${CI_COMMIT_SHA:0:8}
	
test_all:
	image: "pymicro"
	stage: test
	services:
		- name: mysql:5.6
		  alias: mysql
	variables:
		MYSQL_DATEBASE: db
		MYSQL_ROOT_PASSWORD: password
	before_script:
		- pip install -U -r requirements.txt
	script:
		- flake8 app
		- pytest tests
		
build_image:
	image: "docker:17.11"
	stage: build
	services:
		- name: "docker:17.12.0-ce-build"
		  alias: dockerd
	variables:
		DOCKER_HOST: tcp://dockered:2375
	only:
		- master
	tags:
		- build
	scripts:
	  - docker build -t ${IMAGE_TAG} -f Dockerfile .
	  - docker push ${IMAGE_TAG}
	  
deploy_production:
	stage: deploy
	variables:
		GIT_STRATEGE: none
	only:
		- master
	when: manual
	tags:
		- kubectl set image deploy/myproject "app=${IMAGE_TAG}"  --record
```

在每个任务中，通常会包含image, stage, services, script等字段。

- stage：定义了任务所属的阶段。
- image:  指定了执行任务时所需要的docker镜像。
- services：指定了执行任务时所需的依赖服务，比如数据库、docker服务器等。
- script：直接定义了任务所需要执行的命令。

### 测试

在测试任务中，我们启动了MySQL服务，并通过环境变量注入了MySQL的初始数据库以及ROOT密码。在服务启动后，Runner会运行 before_script 中的命令来安装所需要的依赖。安装成功后，就会运行script属性中的命令来进行代码风格检查以及单元测试。

### 构建

在构建任务中，我们会用Dockerfile注入依赖，将工程打包成Docker镜像并上传。我们为这个任务定义了一些额外的属性：tag 属性可以标记