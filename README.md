# mavenapi
stage ('Deployment Stage') {
			steps {
				sh 'ansible-playbook deploytohost.yml'
			}
		}