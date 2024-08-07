pipeline {
  agent any
    environment {
      // The following variable is required for a Semgrep AppSec Platform-connected scan:
      SEMGREP_APP_TOKEN = credentials('SEMGREP_APP_TOKEN')

      // Uncomment the following line to scan changed
      // files in PRs or MRs (diff-aware scanning):
      // SEMGREP_BASELINE_REF = "main"

      // Troubleshooting:

      // Uncomment the following lines if Semgrep AppSec Platform > Findings Page does not create links
      // to the code that generated a finding or if you are not receiving PR or MR comments.
      // SEMGREP_JOB_URL = "${BUILD_URL}"
      // SEMGREP_COMMIT = "${GIT_COMMIT}"
      // SEMGREP_BRANCH = "${GIT_BRANCH}"
      // SEMGREP_REPO_NAME = env.GIT_URL.replaceFirst(/^https:\/\/github.com\/(.*).git$/, '$1')
      // SEMGREP_REPO_URL = env.GIT_URL.replaceFirst(/^(.*).git$/,'$1')
      // SEMGREP_PR_ID = "${env.CHANGE_ID}"
    }
    stages {
    //   stage('Semgrep-Scan') {
    //     steps {
    //         sh '''docker pull semgrep/semgrep && \
    //         docker run \
    //         -e SEMGREP_APP_TOKEN=$SEMGREP_APP_TOKEN \
    //         -e SEMGREP_REPO_URL=$SEMGREP_REPO_URL \
    //         -e SEMGREP_REPO_NAME=$SEMGREP_REPO_NAME \
    //         -e SEMGREP_BRANCH=$SEMGREP_BRANCH \
    //         -e SEMGREP_COMMIT=$SEMGREP_COMMIT \
    //         -e SEMGREP_PR_ID=$SEMGREP_PR_ID \
    //         -v "$(pwd):$(pwd)" --workdir $(pwd) \
    //         semgrep/semgrep semgrep ci --json --json-output=semgrep.json'''
    //   }
    // }

    stage('Trivy-Scan') {
            steps {
                // Install trivy
                sh 'curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /var/lib/jenkins/workspace/JuiceShop/lib/ v0.18.3'
                sh 'curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl > html.tpl'

                // Scan all vuln levels
                sh 'mkdir -p reports'
                sh 'trivy filesystem --ignore-unfixed --vuln-type os,library --format template --template "@html.tpl" -o reports/nodjs-scan.html ./nodejs'
                publishHTML target : [
                    allowMissing: true,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'nodjs-scan.html',
                    reportName: 'Trivy Scan',
                    reportTitles: 'Trivy Scan'
                ]

                // Scan again and fail on CRITICAL vulns
                sh 'trivy filesystem --ignore-unfixed --vuln-type os,library --exit-code 1 --severity CRITICAL ./nodejs'

            }
  }
}
}