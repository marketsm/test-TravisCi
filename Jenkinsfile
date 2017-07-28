
pipeline {
	agent any	
	tools { 
        maven 'mvn-3.3.9' 
    		}
	
	stages {
		
		

		stage ('checkout and copy'){
			steps {
				checkout scm
				sh 'cp /christophTests/collect* .'
				sh 'cp /christophTests/*Config.xml .'
				sh 'chmod u+rx collect*'

			}
		}
		stage ('studentsTests'){
			steps {
				sh 'mvn -B verify cobertura:cobertura -Dmaven.test.failure.ignore -Dmaven.compiler.showDeprecation -Dmaven.compiler.showWarnings'
				sh 'perl collectResults.pl vortragConfig.xml getTestCoverage'
				sh 'perl collectResults.pl vortragConfig.xml switchClasses "mvn -B verify -Dmaven.test.failure.ignore -Dmaven.compiler.showDeprecation -Dmaven.compiler.showWarnings"'			
			}
		}
		
		stage ('teachersTests'){
			steps {
				sh 'perl collectResults.pl vortragConfig.xml addTeachersTests'
				sh 'mvn -B verify checkstyle:checkstyle pmd:pmd pmd:cpd cobertura:cobertura -Dmaven.test.failure.ignore -Dmaven.compiler.showDeprecation -Dmaven.compiler.showWarnings'
				step([$class: 'ArtifactArchiver', artifacts: '**/target/*.jar', fingerprint: true])
  				step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
			}
		}

		stage ('collectResults'){
			when { branch 'master'}
			steps {
				sh 'perl collectResults.pl vortragConfig.xml'
			}
		}
		
		stage ('Results') {
			steps {
				junit '**/target/surefire-reports/*.xml'
				openTasks canComputeNew: false, defaultEncoding: '', excludePattern: '', healthy: '', high: 'FIXME', low: '', normal: 'TODO', pattern: '', unHealthy: ''
				checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
				pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
				dry canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '**/target/cpd.xml', unHealthy: ''
				warnings canComputeNew: false, canResolveRelativePaths: false, consoleParsers: [[parserName: 'Java Compiler (javac)']], defaultEncoding: '', excludePattern: '', healthy: '', includePattern: '', messagesPattern: '', unHealthy: ''
				
			}
		}
	}

}	
