node {
    stage('Build') {
        sh 'docker run --rm python:2-alpine python -m py_compile sources/add2vals.py sources/calc.py'
        stash name: 'compiled-results', includes: 'sources/*.py*'
    }
    stage('Test') {
        sh 'docker run --rm qnib/pytest py.test --junit-xml test-reports/results.xml sources/test_calc.py'
    }
    stage('Deliver') {
        environment {
            VOLUME = '$(pwd)/sources:/src'
            IMAGE = 'cdrx/pyinstaller-linux:python2'
        }
        dir(path: env.BUILD_ID) {
            unstash name: 'compiled-results'
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
        }
        if (currentBuild.result == 'SUCCESS') {
            archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
        }
    }
}