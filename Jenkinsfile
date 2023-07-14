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
            sh "chmod 400 key-python-app.pem"
            sh "ssh -o StrictHostKeyChecking=no ec2-13-229-235-236.ap-southeast-1.compute.amazonaws.com"
            sh "sleep 60"
        }
    }
}
