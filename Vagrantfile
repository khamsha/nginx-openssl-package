# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "centos/stream8"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    sudo -i
    yum install -y redhat-lsb-core
    yum install -y wget
    yum install -y rpmdevtools
    yum install -y rpm-build
    yum install -y createrepo
    yum install -y yum-utils
    yum install -y gcc
    wget https://nginx.org/packages/centos/8/SRPMS/nginx-1.20.2-1.el8.ngx.src.rpm
    rpm -i nginx-1.20.2-1.el8.ngx.src.rpm
    wget https://github.com/openssl/openssl/archive/refs/tags/OpenSSL_1_1_1s.tar.gz
    tar -xvf OpenSSL_1_1_1s.tar.gz
    yum-builddep -y /root/rpmbuild/SPECS/nginx.spec
    sed -i 's#--with-debug#--with-openssl=/home/vagrant/openssl-OpenSSL_1_1_1s#' /root/rpmbuild/SPECS/nginx.spec
    rpmbuild -bb /root/rpmbuild/SPECS/nginx.spec
    yum localinstall -y /root/rpmbuild/RPMS/x86_64/nginx-1.20.2-1.el8.ngx.x86_64.rpm
    systemctl start nginx
    mkdir /usr/share/nginx/html/repo
    cp /root/rpmbuild/RPMS/x86_64/nginx-1.20.2-1.el8.ngx.x86_64.rpm /usr/share/nginx/html/repo/
    wget https://downloads.percona.com/downloads/percona-distribution-mysql-ps/percona-distribution-mysql-ps-8.0.28/binary/redhat/8/x86_64/percona-orchestrator-3.2.6-2.el8.x86_64.rpm
    cp percona-orchestrator-3.2.6-2.el8.x86_64.rpm /usr/share/nginx/html/repo/percona-orchestrator-3.2.6-2.el8.x86_64.rpm
    createrepo /usr/share/nginx/html/repo/
    sed -i '/index.html index.htm;/a\        autoindex on;' /etc/nginx/conf.d/default.conf
    nginx -t
    nginx -s reload
    cat << EOF > /etc/yum.repos.d/otus.repo
    [otus]
    name=otus-linux
    baseurl=http://localhost/repo
    gpgcheck=0
    enabled=1
    EOF
    yum install percona-orchestrator.x86_64 -y
  SHELL
end
