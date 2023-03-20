FROM almalinux:8

RUN dnf -y clean all ;\
dnf -y update ;\
dnf -y install epel-release glibc-locale-source ;\
dnf -y install ansible git

RUN dnf install -y which selinux-policy-targeted libselinux-utils policycoreutils firewalld scl-utils initscripts iproute

RUN dnf groups mark install -y "Development Tools"  ;\
dnf group install -y "Development Tools"

WORKDIR /tmp
RUN git clone https://github.com/yoheier/redmine-centos-ansible.git

WORKDIR /tmp/redmine-centos-ansible
RUN ansible-playbook -i hosts site.yml