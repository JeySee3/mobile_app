class Constants {

    static final String MASTER_BRANCH = 'master'

    static final String QA_BUILD = 'release'
    static final String RELEASE_BUILD = 'Release'

    static final String INTERNAL_TRACK = 'internal'
    static final String RELEASE_TRACK = 'release'
}

def getBuildType() {
    switch (env.BRANCH_NAME) {
        case Constants.MASTER_BRANCH:
            return Constants.RELEASE_BUILD
        default:
            return Constants.QA_BUILD
    }
}

def getTrackType() {
    switch (env.BRANCH_NAME) {
        case Constants.MASTER_BRANCH:
            return Constants.RELEASE_TRACK
        default:
            return Constants.INTERNAL_TRACK
    }
}

def isDeployCandidate() {
    return ("${env.BRANCH_NAME}" =~ /(develop|master)/)
}

pipeline {

    agent { dockerfile true }

    environment {
        appName = 'jenkins-blog'

        KEY_PASSWORD = credentials('keyPassword')
        KEY_ALIAS = credentials('keyAlias')
        KEYSTORE = credentials('keystore')
        STORE_PASSWORD = credentials('storePassword')
    }
    stages {

        stage('Checkout Source') {
	
	        steps {
                git branch: '$BRANCH_NAME', credentialsId:'githubjey', url: 'https://github.com/JeySee3/mobile_app.git'
            }
        }


        stage('Run Tests') {
            steps {
                echo 'Running Tests'
                script {
                    VARIANT = getBuildType()
                    sh "./gradlew testZip${VARIANT}UnitTest"
                }
            }
        }

        stage('Build Bundle') {
            steps {
                echo 'Building'
                script {
                    VARIANT = getBuildType()
                    sh "pwd"
                    sh "./gradlew -PstorePass=${STORE_PASSWORD} -Pkeystore=${KEYSTORE} -Palias=${KEY_ALIAS} -PkeyPass=${KEY_PASSWORD} bundle${VARIANT}"
                }
            }
        }


    }
}
