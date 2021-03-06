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
		
		
		
		
- name: enable UFW
    ufw:
      state: enabled
  - name: Allow port 8080
    ufw:
      rule: allow
      port: 8080
      proto: tcp
  - name: Allow port 22
    ufw:
      rule: allow
      port: 22
      proto: tcp
  - name: Update Sources List
    commad: apt-get update
    
  - name: Install Jdk
    apt:
      name: openjdk-11-jdk
      state: present
      
  - name: add tomcat group
    group:
      name: tomcat
      
  - name: add tomcat user
    user:
      name: tomcat
      group: tomcat
      home: /user/share/tomcat
      createhome: no
      
  - name: create /opt/tomcat directory
    file:
      path: /opt/tomcat
      state: directory
      mode: 0777
      
  - name: Step 1
    unarchive: https://mirrors.estointernet.in/apache/tomcat/tomcat-9/v9.0.41/bin/apache-tomcat-9.0.41.tar.gz
    dest: /opt/tomcat
    remote_src: yes
    extra_opts: [--strip-components=1]
    
  - name: Step 2
    file:
      path: /opt/tomcat
      owner: tomcat
      group: tomcat
      mode: 0777
      recurse: yes
      state: directory
      
  - name: Creating service file
    copy:
      src: /home/dhanya/hostconfigfiles/tomcatservice.txt
      dest: /etc/systemd/system/tomcat.service
      follow: yes
      mode: 0777

  - name: Configure tomcat users
    copy: 
      src: /home/dhanya/hostconfigfiles/tomcatuser.txt
      dest: /opt/tomcat/conf/tomcat-users.xml
      follow: yes

  - name: Manager App Setup
    copy: 
      src: /home/dhanya/hostconfigfiles/hmacontext.txt
      dest: /opt/tomcat/webapps/manager/META-INF/context.xml
      follow: yes
    
  - name: Host Manager App Setup
    copy: 
      src: /home/dhanya/hostconfigfiles/hmacontext.txt
      dest: /opt/tomcat/webapps/host-manager/META-INF/context.xml
      follow: yes
      
      
- name: Copy Tomcat service from local to remote
      copy:
        src: tomcat.service
        dest: /etc/systemd/system/
        mode: 0755