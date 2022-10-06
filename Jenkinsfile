node {
    checkout scm
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } catch(Exception e) {
                sh "echo ${e}"
            } finally {
                junit 'test-reports/results.xml'
            }
        }
    }
    stage('Deploy') {
        docker.image('cdrx/pyinstaller-linux:python2').inside("""--entrypoint=''""") {
            try {
                dir(path: env.BUILD_ID) { 
                    unstash(name: 'compiled-results') 
                    sh 'pyinstaller -F add2vals.py'
                }

                archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
                sh 'rm -rf build dist'
            } catch(Exception e) {
                sh "echo ${e}"
            } finally {
            }
        }
    }
}