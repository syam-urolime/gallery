def appname = "Runner" //DON'T CHANGE THIS. This refers to the flutter 'Runner' target.
def xcarchive = "${appname}.xcarchive"

pipeline {
    agent { label 'Flutter_v2020_05' } //Change this to whatever your flutter jenkins nodes are labeled.
    environment {
        DEVELOPER_DIR="/Applications/Xcode.app/Contents/Developer/"  //This is necessary for Fastlane to access iOS Build things.
        PATH = "/Users/jenkins/.rbenv/shims:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Library/Apple/usr/bin:/Users/jenkins/Documents/flutter/bin:/usr/local/Caskroom/android-sdk/4333796//tools:/usr/local/Caskroom/android-sdk/4333796//platform-tools:/Applications/Xcode.app/Contents/Developer"
    }
    stages {
        stage ('Checkout') {
            steps {
                step([$class: 'WsCleanup'])
                checkout scm
                sh "rm -rf brbuild_ios" //This removes the previous checkout of brbuild_ios if it exists.
                sh "rm -rf ios/fastlane/brbuild_ios" //This removes the brbuild_ios from the fastlane directory if it somehow still exists
            }
        }
        stage ('Flutter Doctor') {
            steps {
                sh "pwd && ls && echo $PATH && ls /sdks/flutter/bin/flutter"
                sh "/sdks/flutter/bin/flutter doctor -v"
            }
        }
        stage ('Run Flutter Tests') {
            steps {
                sh "flutter test --coverage test/logic_tests.dart"
            }
        }
        stage ('Flutter Build APK') {
            steps {
                sh "flutter build apk --split-per-abi"
            }
        }
        stage('Distribute Android APK') {
            steps {
                appCenter apiToken: 'API_TOKEN_HERE',
                        ownerName: 'OWNER_NAME',
                        appName: 'APP_NAME',
                        pathToApp: 'build/app/outputs/apk/release/app-arm64-v8a-release.apk',
                        distributionGroups: 'DISTRIBUTION_GROUP'
            }
        }
        stage('Flutter Build iOS') {
            steps {
                sh "flutter build ios --release --no-codesign"
            }
        }
        stage('Make iOS IPA And Distribute') {
                steps {
                    dir('ios'){
                            sh "bundle install"
                            sh "bundle exec fastlane buildAdHoc --verbose"
                    }
                }
        }
        stage('Cleanup') {
            steps {
                sh "flutter clean"
            }
        }
    }
}

