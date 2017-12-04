node {

    stage('Checkout Code') {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/DevTechnology/DHSFormG28.git']]])
    }

    // Perform the build in an appropriate docker container so we don't have to install
    // project specific artifacts on the Jenkins server itself.
    try {
        stage('Build UI') { 
            // TODO: Make these first three steps obsolete by creating a custom image.
            sh 'docker run -it -d --name="dhsg28-ui-build" \
                -v /var/lib/jenkins/workspace/DHSFormG28/UI:/app node:7'

            // Install angular ci so we can use 'ng' command in next step
            sh 'docker exec dhsg28-ui-build /bin/bash -c "npm install -g @angular/cli"'

            // Run npm install in the docker container
            sh 'docker exec dhsg28-ui-build /bin/bash -c "cd /app;npm install"'

            // Build artifacts
            sh 'docker exec dhsg28-ui-build /bin/bash -c "cd /app;ng build"'
        }

        stage('OWASP Dependency Security Scan') {

            echo('Running OWASP Vulnerability Security Scan on package.json...')

            sh '''
                # Define and create a directory for OWASP Dependency Data
                OWASP_DATA_DIR=${HOME}/owasp-data
                if [ ! -d "${OWASP_DATA_DIR}" ]; then
                    mkdir -p ${OWASP_DATA_DIR}
                    chmod -R 777 ${OWASP_DATA_DIR}
                fi

                # Define and create a directory for the OWASP Dependency Analysis Report run against node_modules
                OWASP_REPORT_DIR=${WORKSPACE}/owasp-report
                if [ ! -d "${OWASP_REPORT_DIR}" ]; then
                    mkdir -p ${OWASP_REPORT_DIR}
                    chmod -R 777 ${OWASP_REPORT_DIR}
                fi

                # Run the owasp/dependency-check Docker Container against the src directory of the earthquake-design-ws project
                docker run --name="owasp_scan" \
                    -v ${WORKSPACE}/UI:/src:ro \
                    -v ${OWASP_DATA_DIR}:/usr/share/dependency-check/data:rw \
                    -v ${OWASP_REPORT_DIR}:/report:rw \
                    owasp/dependency-check \
                    --scan /src \
                    --format "ALL" \
                    --project "DHSFormG28"

                docker logs owasp_scan
            '''

        }

        stage('Test UI') {
            echo 'Testing...'
        }

        stage('Build Docker Container') {
            echo 'Building DHS G-28 Form Docker Image...'
            sh 'cd /var/lib/jenkins/workspace/DHSFormG28/UI; docker build -t g28form:latest .'
        }
    } finally {
        sh 'docker container stop dhsg28-ui-build'
        sh 'docker container rm dhsg28-ui-build'
    }
}