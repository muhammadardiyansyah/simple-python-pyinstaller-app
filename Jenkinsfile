node{
    withDockerContainer(image: 'python:2-alpine','qnib/pytest') {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
        stage('Test') {
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
}
