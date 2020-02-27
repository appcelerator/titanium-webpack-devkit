#! groovy
library 'pipeline-library'

def publishableBranches = ['master']
def nodeVersion = '8.9.1'
def npmVersion = 'latest'

timestamps {
  node('(osx || linux) && git && npm-publish') {
    nodejs(nodeJSInstallationName: "node ${nodeVersion}") {
      ansiColor('xterm') {
        stage('Checkout') {
          checkout([
            $class: 'GitSCM',
            branches: scm.branches,
            extensions: scm.extensions + [[$class: 'CleanBeforeCheckout'], [$class: 'LocalBranch', localBranch: "**"]],
            submoduleCfg: [],
            userRemoteConfigs: scm.userRemoteConfigs
          ])
          ensureNPM(npmVersion)
        }
        stage('Security') {
          // TODO: Add security checks for the actual packages
          //step([$class: 'WarningsPublisher', canComputeNew: false, canResolveRelativePaths: false, consoleParsers: [[parserName: 'Node Security Project Vulnerabilities'], [parserName: 'RetireJS']], defaultEncoding: '', excludePattern: '', healthy: '', includePattern: '', messagesPattern: '', unHealthy: ''])
        }
        stage('Unit tests') {
          //sh 'npm test'
        }
        stage('Publish') {
          if(publishableBranches.contains(env.BRANCH_NAME)) {
            sh 'npm ci'
            sh 'npm run lerna-publish'
            pushGit(name: env.BRANCH_NAME)
          }
        }
      }
    }
  }
}
