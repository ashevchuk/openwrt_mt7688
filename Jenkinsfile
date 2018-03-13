node {
    def commitHash = checkout(scm).GIT_COMMIT
    def app

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        sh 'apt-get update'
        sh 'apt-get install -y awk file'
        sh 'cp ./SGD-def.config .config'
        sh './scripts/feeds update'
        sh './scripts/feeds install -a'
        app = docker.build("acourdavault/openwrt")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}

