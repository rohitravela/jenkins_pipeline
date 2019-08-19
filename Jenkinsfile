pipeline {
    agent any
 
    
    stages {
	
        stage ('Artifactory configuration') {
            steps {
		// specify Artifactory server
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://artifactory:8081/artifactory",
		    credentialsId: 'admin.jfrog'
                )
	// specify the repositories to be used for deploying the artifacts in the Artifactory
                rtMavenDeployer (
                    id: "MY_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )
		// defines the dependencies resolution details
                rtMavenResolver (
                    id: "MY_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }
        stage ('Build & Upload Artifact') {
	    // run Maven Build and upload the built artifact to Artifactory
            steps {
                rtMavenRun (
                    pom: 'hello.go',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }
        stage ('Publish build info') {
	    // Publish the build info in the Artifactory
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }
    }
}
