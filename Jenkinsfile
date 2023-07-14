node {
    withDockerContainer(image: 'python:2-alpine') {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }
    withDockerContainer(image: 'qnib/pytest') {
        stage('Test') {
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk melanjutkan)'
        }
    }
    withEnv([
        'VOLUME=$(pwd)/sources:/src',
        "IMAGE=cdrx/pyinstaller-linux:python2"
    ]) {
        stage('Deploy') {
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 
            archiveArtifacts 'sources/dist/add2vals'
            // sh "ssh -o StrictHostKeyChecking=no ec2-user@ec2-13-215-228-199.ap-southeast-1.compute.amazonaws.com"
            sh "docker run --rm -v ${env.WORKSPACE}/${env.BUILD_ID}/sources:/src cdrx/pyinstaller-linux:python2 'rm -rf build dist'"
            sshPublisher(
                publishers: [
                    sshPublisherDesc(
                        configName: 'submission-cicd-pipeline-nafifurqon-ec2', 
                        transfers: [
                            sshTransfer(
                                cleanRemote: false,
                                excludes: '',
                                execCommand: "chmod a+x ${env.BUILD_ID}/sources/dist/add2vals && ./${env.BUILD_ID}/sources/dist/add2vals 10 20",
                                execTimeout: 120000,
                                flatten: false,
                                makeEmptyDirs: false,
                                noDefaultExcludes: false,
                                patternSeparator: '[, ]+',
                                remoteDirectory: '',
                                remoteDirectorySDF: false,
                                removePrefix: '',
                                sourceFiles: "${env.BUILD_ID}/sources/dist/*"
                            )
                        ],
                        usePromotionTimestamp: false,
                        useWorkspaceInPromotion: false,
                        verbose: false
                    )
                ]
            )
            sh "sleep 60"
        }
    }
}
