node {
   docker.image('maven:3.8.5-openjdk-8').inside('-v /root/.m2:/root/.m2') {
      stage('Checkout Code') {
         git 'https://github.com/hritvikpatel4/java-maven-calculator-web-app.git'
      }

      stage('Code Analysis') {
         sh 'mvn clean checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs'

         def checkstyle = scanForIssues tool: checkStyle(pattern: '**/target/checkstyle-result.xml')
         publishIssues issues: [checkstyle]

         def pmd = scanForIssues tool: pmdParser(pattern: '**/target/pmd.xml')
         publishIssues issues: [pmd]
         
         def cpd = scanForIssues tool: cpd(pattern: '**/target/cpd.xml')
         publishIssues issues: [cpd]

         def spotbugs = scanForIssues tool: spotBugs(pattern: '**/target/findbugsXml.xml')
         publishIssues issues: [spotbugs]

         def maven = scanForIssues tool: mavenConsole()
         publishIssues issues: [maven]
         
         publishIssues id: 'analysis', name: 'All Issues', 
            issues: [checkstyle, pmd, spotbugs], 
            filters: [includePackage('io.jenkins.plugins.analysis.*')]
      }
      
      stage('Unit Tests') {
         sh "mvn test"
      }

      stage('Publish Test Reports and Coverage') {
         junit '**/target/surefire-reports/*ServiceTest.xml'
         cobertura coberturaReportFile: '**/target/site/cobertura/coverage.xml'
      }
   }
}
