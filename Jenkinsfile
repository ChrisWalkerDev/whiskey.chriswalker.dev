node {
    def app
    def appName = "whiskey.chriswalker.dev"

    stage('Clone Repo') {
        checkout scm
    }

    stage('Build') {
        app = docker.build(appName + ":v" + currentBuild.number)
    }

    stage('Save Image to Archive') {
        sh "/usr/local/bin/save_image.sh " + appName + ":v" + currentBuild.number
    }

    stage('Delete Local Previous Images') {
        sh "/usr/local/bin/clean_local_images.sh "+ appName
    }

    stage('Delete Previous Archived Images') {
        sh "/usr/local/bin/delete_old.sh " + appName
    }

    stage('Delete Remote Existing Container') {
        sh "/usr/local/bin/stop_previous_image.sh " + appName
    }

    stage('Delete Remote Images') {
        sh "/usr/local/bin/clean_remote_images.sh " + appName
    }

    stage('Install New Image on Remote') {
        sh "/usr/local/bin/install_image.sh " + appName + ":v" + currentBuild.number
    }

    stage('Deploy Image') {
        sh "/usr/local/bin/deploy.sh 9000:8080 " + currentBuild.number + " " + appName + ":v" + currentBuild.number
    }

    stage('Verify Image') {
        sleep(time: 10, unit:"SECONDS")
        sh "/usr/local/bin/verify_image.sh https://" + appName + "/version " + currentBuild.number
    }

}
