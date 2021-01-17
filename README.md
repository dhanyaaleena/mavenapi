# mavenapi
stage ('Quality Gate') {
			steps {
					waitForQualityGate abortPipeline: true
			}
		}


stage ('Deployment Stage') {
			steps {
				sh 'ansible-playbook deploytohost.yml'
			}
		}
