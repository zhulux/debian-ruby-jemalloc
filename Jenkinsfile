#!groovy

// keep 20 builds history
properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '20']]]);
def lib = library('ci-shared-libs').com.starup
def msgUtils = lib.MessageLego.new()
def buildAgent = 'docker-build-bj3a'
def dockerHub = 'registry.astarup.com/astarup'
def projectName = 'debian-stretch'
buildHttpProxy = "http://proxy_hk.astarup.com:39628"
currentBuild.result = "SUCCESS"
imageName = "${dockerHub}/${projectName}"
imageTag = ''
versionTagRegex = /^ruby(\d+\.){0,2}\d+$/

def commitMsg = ''
def branch = env.BRANCH_NAME
def bearychatHookUrl = env.BEARYCHAT_HOOK_URL
def bearychatHookGroup = 'ci'
def kubeTestNs = 'test'
def kubeQaNs = 'qa'
def kubeStageNs = 'staging'
def kubeProdNs = 'production'


def Object image

/*
 Jenkinsfile main body
*/

try {
  node("${buildAgent}") {
    stage("Checkout Project") {
      def scmVars = checkout scm
      imageTag = genImageTag(env.BRANCH_NAME)
    }

    stage("Build Image") {
      image = dockerBuild("${imageName}","${buildHttpProxy}")
    }
  }

  if (branch ==~ versionTagRegex) {

    stage("Publish Image to Registry") {
      node("${buildAgent}") {
        dockerPush(image,"$imageTag")
      }
    }

  }
   
} catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException e) {
  node("${buildAgent}") {
    echo e.getCauses().join(", ")
    currentBuild.result = "ABORTED"
    errorMsg = msgUtils.errorMsg("${aborteMsg}")
    bearyNotifySend attachments: "${errorMsg}", channel: "${bearychatHookGroup}", endpoint: "${bearychatHookUrl}"
  }
} catch (err) {
  node("${buildAgent}") {
    currentBuild.result = "FAILURE"
    errorMsg = msgUtils.errorMsg(err,'')
    bearyNotifySend attachments: "${errorMsg}", channel: "${bearychatHookGroup}", endpoint: "${bearychatHookUrl}"
    throw err
  }
}

