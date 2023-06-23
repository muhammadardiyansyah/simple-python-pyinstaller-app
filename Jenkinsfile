node{
    withDockerContainer(image: 'python:2-alpine') {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
        stage('Test') {
            withDockerContainer(image: 'qnib/pytest') {
            }
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
}
