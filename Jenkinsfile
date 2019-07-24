pipeline {
  agent any
  triggers {
    pollSCM('H/15 * * * *')
  }
  stages {
    stage('Build Comtainer') {
      parallel {
        stage('Build ARM') {
          agent {
            label "Pi_3"
          }
          steps {
            sh "docker build -t influx arm/"
          }
        }
        stage('Build amd64') {
          agent {
            label "master"
          }
          steps {
            sh "docker build -t influx amd64/"
          }
        }
      }
    }
    stage('Tag Container') {
      parallel {
        stage('Tag ARM') {
          agent {
            label "Pi_3"
          }
          steps {
            sh "docker tag influx fx8350:5000/influx:arm"
            sh "docker tag influx leonhess/influx:arm"
          }
        }
        stage('Tag amd64') {
          agent {
            label "master"
          }
          steps {
            sh "docker tag influx fx8350:5000/influx:amd64"
            sh "docker tag influx leonhess/influx:amd64"
          }
        }
      }
    }
    stage('Push to Registries') {
      parallel {
        stage('Push ARM to local Registry') {
          agent {
            label "Pi_3"
          }
          steps {
            sh "docker push fx8350:5000/influx:arm"
          }
        }
        stage('Push ARM to DockerHub') {
          agent {
            label "Pi_3"
          }
          steps {
            withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
              sh "docker push leonhess/influx:arm"
            }
          }
        }
        stage('Push amd64 to local Registry') {
          agent {
            label "master"
          }
          steps {
            sh "docker push fx8350:5000/influx:amd64"
          }
        }
        stage('Push amd64 to Dockerhub') {
          agent {
            label "master"
          }
          steps {
            withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
              sh "docker push leonhess/influx:amd64"
            }
          }
        }
      }
    }
    stage('Cleanup') {
      parallel {
        stage('Cleanup ARM') {
          agent {
            label "Pi_3"
          }
          steps {
            sh "docker rmi fx8350:5000/influx:arm"
            sh "docker rmi leonhess/influx:arm"
          }
        }
        stage('Cleanup amd64') {
          agent {
            label "master"
          }
          steps {
            sh "docker rmi fx8350:5000/influx:amd64"
            sh "docker rmi leonhess/influx:amd64"
          }
        }
      }
    }
    /*
    stage('Create Manifest') {
    parallel{

    stage('Create DockerHub manifest') {
    agent {
    label "master"
  }
  steps {
  sh "docker manifest create leonhess/telegraf leonhess/telegraf:arm leonhess/telegraf:amd64"
}
}

stage('Create local Registry manifest') {
agent {
label "master"
}
steps {
sh "docker manifest create --amend --insecure fx8350:5000/telegraf:latest fx8350:5000/telegraf:arm fx8350:5000/telegraf:amd64"
}
}

}
}
stage('Push Manifest') {
parallel {

stage('Create DockerHub manifest') {
agent {
label "master"
}
steps {
withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
sh "docker manifest push leonhess/telegraf"
}
}
}


stage('Create local Registry manifest') {
agent {
label "master"
}
steps {
sh "docker manifest push -p fx8350:5000/telegraf:latest"
}
}

}
}
*/
stage('Deploy') {
  agent {
    label "master"
  }
  steps {
    ansiblePlaybook(
      playbook: 'deploy.yml',
      credentialsId: 'd4eb3f5d-d0f5-4964-8bad-038f0d774551'
      )
    }
  }
}
}
