pipeline{
      // 定义groovy脚本中使用的环境变量
      environment{

        IMAGE_TAG =  sh(returnStdout: true,script: 'echo $image_tag').trim()
        ORIGIN_REPO =  sh(returnStdout: true,script: 'echo $origin_repo').trim()
        REPO =  sh(returnStdout: true,script: 'echo $repo').trim()
        BRANCH =  sh(returnStdout: true,script: 'echo $branch').trim()
      }

      // 定义本次构建使用哪个标签的构建环境，本示例中为 “slave-pipeline”
      // agent{
        // node{
          // label 'slave-pipeline'
        // }
      // }

      // "stages"定义项目构建的多个模块，可以添加多个 “stage”， 可以多个 “stage” 串行或者并行执行
      stages{
        // 定义第一个stage， 完成克隆源码的任务
        stage('Git'){
          steps{
            git branch: '${BRANCH}', credentialsId: '', url: 'https://github.com/jinglina/testJenkins.git'
          }
        }

        // 添加第二个stage， 运行源码打包命令
        stage('Package'){
          steps{
              container("maven") {
                  sh "mvn package -B -DskipTests"
              }
          }
        }


        // 添加第四个stage, 运行容器镜像构建和推送命令， 用到了environment中定义的groovy环境变量
        stage('Image Build And Publish'){
          steps{
              sh "mvn clean install -DskipTests"
              sh "mvn deploy -dskipTests"
              sh "mvn docker:build -DpushImageTag -DdockerImageTags=latest,${env.BRANCH_NAME}-latest,${build_tag} -DskipTests"
          }
        }
      }
    }