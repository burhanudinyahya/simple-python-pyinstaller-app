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
    stage('Deploy') {
        withEnv(["IMAGE='cdrx/pyinstaller-linux:python2'"]) {
            dir(path: env.BUILD_ID) {
                sh "docker run --rm -v ./sources:/src ${IMAGE} 'pyinstaller -F add2vals.py'"
            }
            archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
            sh "docker run --rm -v ./sources:/src ${IMAGE} 'rm -rf build dist'"
        }
        // docker.image('cdrx/pyinstaller-linux:python2').inside("""--entrypoint=''""").withEnv {
        //     sh 'pyinstaller --onefile sources/add2vals.py'
        //     archiveArtifacts 'dist/add2vals'
        // }
    }
}