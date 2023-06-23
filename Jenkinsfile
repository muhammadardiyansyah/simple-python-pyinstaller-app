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
    }
    withDockerContainer(image: 'cdrx/pyinstaller-linux:python2') {
        stage('Deliver') {
            unstash(name: 'compiled-results') 
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
            archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
        }
    }

//     stage('Deliver') { 
//         agent any
//         environment { 
//             VOLUME = '$(pwd)/sources:/src'
//             IMAGE = 'cdrx/pyinstaller-linux:python2'
//         }
//         steps {
//             dir(path: env.BUILD_ID) { 
//                 unstash(name: 'compiled-results') 
//                 sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 
//             }
//         }
//         post {
//             success {
//                 archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
//                 sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
//             }
//         }
//     }
}