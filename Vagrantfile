# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.define "apache1" do |apache1|
    apache1.vm.box ="centos/7"
    apache1.vm.hostname = "apache1"
    apache1.vm.network "forwarded_port", guest:80, host:8085
    apache1.vm.network :private_network, ip: "10.10.10.2"
    apache1.vm.provision "shell", inline: "sudo yum update -y ;sudo yum install httpd -y; echo 'I’m server 1' > /var/www/html/index.html; rm -rf /etc/httpd/conf.d/* ; sudo systemctl enable httpd.service; sudo systemctl start httpd.service"
    apache1.vm.provider "virtualbox" do |vbox|
  end
  config.vm.provider "virtualbox" do |vb|
        vb.gui = true
        vb.memory = "512"
        vb.cpus = 1

  end
end

  config.vm.define "apache2" do |apache2|
    apache2.vm.box ="centos/7"
    apache2.vm.hostname = "apache2"
    apache2.vm.network "forwarded_port", guest:80, host:8086
    apache2.vm.network :private_network, ip: "10.10.10.3"
    apache2.vm.provision "shell", inline: "sudo yum update -y ;sudo yum install httpd -y; echo 'I’m server 2' > /var/www/html/index.html; rm -rf /etc/httpd/conf.d/* ; sudo systemctl enable httpd.service; sudo systemctl start httpd.service"
    apache2.vm.provider "virtualbox" do |vbox|
  end
  config.vm.provider "virtualbox" do |vb|
        vb.gui = true
        vb.memory = "512"
        vb.cpus = 1

  end
end

  config.vm.define "haproxy" do |haproxy| 
    haproxy.vm.box = "centos/7"  
    haproxy.vm.hostname = "haproxy"
    haproxy.vm.network "forwarded_port", guest:80, host:8087
    haproxy.vm.network :private_network, ip: "10.10.10.4"
    haproxy.vm.provision "file", source: "./haproxy.cfg", destination: "/tmp/haproxy.cfg"
    haproxy.vm.provision "shell", inline: "sudo yum update -y; sudo yum install haproxy -y; sudo cat /tmp/haproxy.cfg > /etc/haproxy/haproxy.cfg; sudo systemctl enable haproxy.service; sudo systemctl start haproxy.service" 
    haproxy.vm.provider "virtualbox" do |vbox|
  end
  config.vm.provider "virtualbox" do |vb|
        vb.gui = true
        vb.memory = "512"
        vb.cpus = 1

  end
end


  config.vm.define "jenkins" do |jenkins| 
    jenkins.vm.box = "centos/7"  
    jenkins.vm.hostname = "jenkins"
    jenkins.vm.network "forwarded_port", guest:8080, host:8088
    jenkins.vm.network :private_network, ip: "10.10.10.5" 
    jenkins.vm.provision "file", source: "./basic-security.groovy", destination: "/tmp/basic-security.groovy"
    jenkins.vm.provision "file", source: "./config.xml", destination: "/tmp/config.xml"
    jenkins.vm.provision "shell", inline: <<-SHELL 
      sudo yum install wget java -y
      sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
      sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
      sudo yum install jenkins -y
      sudo echo 'JENKINS_ARGS="-Djenkins.install.runSetupWizard=false"' >> /etc/sysconfig/jenkins
      sudo systemctl enable jenkins.service
      sudo systemctl start jenkins.service
      sudo mkdir -p /var/lib/jenkins/init.groovy.d/
      sudo chmod 777 /var/lib/jenkins/init.groovy.d/
      sudo cat /tmp/basic-security.groovy > /var/lib/jenkins/init.groovy.d/basic-security.groovy
      sudo chown -R jenkins:jenkins /var/lib/jenkins/init.groovy.d
      sudo systemctl restart jenkins.service
      sudo sleep 30
      sudo wget http://localhost:8080/jnlpJars/jenkins-cli.jar
      sudo sleep 20
      sudo java -jar jenkins-cli.jar -s http://127.0.0.1:8080/ -auth admin:admin install-plugin http_request
      sudo sleep 5
      sudo java -jar jenkins-cli.jar -s http://127.0.0.1:8080/ -auth admin:admin create-job mainjob < /tmp/config.xml
      sudo systemctl restart jenkins.service
      sleep 10
      sudo systemctl restart jenkins.service
    SHELL
    jenkins.vm.provider "virtualbox" do |vbox|
  end
  config.vm.provider "virtualbox" do |vb|
        vb.gui = true
        vb.memory = "1024"
        vb.cpus = 2

  end
end
end