FROM almalinux:8

RUN dnf -y clean all
RUN dnf -y update 
RUN dnf -y install epel-release glibc-locale-source
RUN dnf -y install ansible git 

RUN dnf install -y which selinux-policy-targeted libselinux-utils policycoreutils firewalld scl-utils initscripts iproute

RUN dnf groups mark install -y "Development Tools" 
RUN dnf groups mark convert -y "Development Tools" 
RUN dnf group install -y "Development Tools"

RUN cd tmp
RUN git clone https://github.com/yoheier/redmine-centos-ansible.git

RUN cd redmine-centos-ansible
RUN ansible-playbook -i hosts site.yml