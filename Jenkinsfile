node {
    def server = Artifactory.server 'ART'
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    def oldWarnings

    stage ('Clone') {
        checkout scm
    }

    stage ('Artifactory configuration') {
        rtMaven.tool = 'M3' 
        rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot', server: server
        buildInfo = Artifactory.newBuildInfo()
        buildInfo.env.capture = true
    }


    try{
        stage ('Exec Maven') {
            rtMaven.run pom: 'pom.xml', goals: 'clean install -P dev', buildInfo: buildInfo
        }
    } finally {
        junit '**/surefire-reports/**/*.xml'
        checkstyle pattern: '**/target/checkstyle-result.xml'
        findbugs pattern: '**/target/spotbugsXml.xml'
    }

    if (env.BRANCH_NAME == 'dev') {
        stage ('Publish build info') {
            server.publishBuildInfo buildInfo
        }
    }
}
