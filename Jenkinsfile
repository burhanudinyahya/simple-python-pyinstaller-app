node {
    checkout scm
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    stage('Deploy') {
        withEnv(["IMAGE='cdrx/pyinstaller-linux:python2'"]) {
            sh "docker run --rm -v ${env.WORKSPACE}/sources:/src ${IMAGE} 'pyinstaller -F add2vals.py'"
            archiveArtifacts "sources/dist/add2vals"
            sh "docker run --rm -v ${env.WORKSPACE}/sources:/src ${IMAGE} 'rm -rf build dist'"
        }
        sleep(time:1,unit:'MINUTES')
    }
}