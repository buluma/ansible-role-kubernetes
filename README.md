# [Ansible role kubernetes](#ansible-role-kubernetes)

Kubernetes for Linux.

|GitHub|GitLab|Downloads|Version|
|------|------|---------|-------|
|[![github](https://github.com/buluma/ansible-role-kubernetes/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-kubernetes/actions)|[![gitlab](https://gitlab.com/shadowwalker/ansible-role-kubernetes/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-kubernetes)|[![downloads](https://img.shields.io/ansible/role/d/buluma/kubernetes)](https://galaxy.ansible.com/buluma/kubernetes)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-kubernetes.svg)](https://github.com/buluma/ansible-role-kubernetes/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-kubernetes/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- become: true
  hosts: all
  name: Converge
  post_tasks:
  - ansible.builtin.command: kubectl cluster-info
    changed_when: false
    name: Get cluster info.
    register: kubernetes_info
  - ansible.builtin.debug:
      var: kubernetes_info.stdout
    name: Print cluster info.
  - ansible.builtin.command: kubectl get pods --all-namespaces
    changed_when: false
    name: Get all running pods.
    register: kubernetes_pods
  - ansible.builtin.debug:
      var: kubernetes_pods.stdout
    name: Print list of running pods.
  pre_tasks:
  - ansible.builtin.apt:
      cache_valid_time: '600'
      update_cache: 'true'
    name: Update apt cache.
    when: ansible_os_family == 'Debian'
  - ansible.builtin.package:
      name: iproute
      state: present
    name: Ensure test dependencies are installed (RedHat).
    when: ansible_os_family == 'RedHat'
  - ansible.builtin.package:
      name: iproute2
      state: present
    name: Ensure test dependencies are installed (Debian).
    when: ansible_os_family == 'Debian'
  - ansible.builtin.setup:
    name: Gather facts.
  roles:
  - role: buluma.kubernetes
  vars:
    docker_install_compose: false
    kubernetes_kubelet_extra_args: --fail-swap-on=false --cgroup-driver=cgroupfs
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-kubernetes/blob/master/molecule/default/prepare.yml):

```yaml
---
- become: true
  gather_facts: false
  hosts: all
  name: Prepare
  roles:
  - role: buluma.bootstrap
  - role: buluma.core_dependencies
  - role: buluma.setuptools
  - role: buluma.docker
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-kubernetes/blob/master/defaults/main.yml):

```yaml
---
kubernetes_allow_pods_on_master: true
kubernetes_apiserver_advertise_address: ""
kubernetes_apt_ignore_key_error: false
kubernetes_apt_release_channel: main
kubernetes_apt_repository: deb http://apt.kubernetes.io/ kubernetes-xenial {{ kubernetes_apt_release_channel }}
kubernetes_calico_manifest_file: https://projectcalico.docs.tigera.io/manifests/calico.yaml
kubernetes_config_cluster_configuration:
  kubernetesVersion: "{{ kubernetes_version_kubeadm }}"
  networking:
    podSubnet: "{{ kubernetes_pod_network.cidr }}"
kubernetes_config_init_configuration:
  localAPIEndpoint:
    advertiseAddress: "{{ kubernetes_apiserver_advertise_address | default(ansible_default_ipv4.address, true) }}"
kubernetes_config_kube_proxy_configuration: {}
kubernetes_config_kubelet_configuration:
  cgroupDriver: cgroupfs
kubernetes_flannel_manifest_file: https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubernetes_flannel_manifest_file_rbac: https://raw.githubusercontent.com/coreos/flannel/master/Documentation/k8s-manifests/kube-flannel-rbac.yml
kubernetes_ignore_preflight_errors: all
kubernetes_join_command_extra_opts: ""
kubernetes_kubeadm_init_extra_opts: ""
kubernetes_kubeadm_kubelet_config_file_path: /etc/kubernetes/kubeadm-kubelet-config.yaml
kubernetes_kubelet_extra_args: ""
kubernetes_packages:
  - name: kubelet
    state: present
  - name: kubectl
    state: present
  - name: kubeadm
    state: present
  - name: kubernetes-cni
    state: present
kubernetes_pod_network:
  cidr: 10.244.0.0/16
  cni: flannel
kubernetes_role: master
kubernetes_version: "1.20"
kubernetes_version_kubeadm: stable-{{ kubernetes_version }}
kubernetes_version_rhel_package: 1.20.4
kubernetes_yum_arch: $basearch
kubernetes_yum_base_url: https://packages.cloud.google.com/yum/repos/kubernetes-el7-{{ kubernetes_yum_arch }}
kubernetes_yum_gpg_check: true
kubernetes_yum_gpg_key:
  - https://packages.cloud.google.com/yum/doc/yum-key.gpg
  - https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
kubernetes_yum_repo_gpg_check: true
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-kubernetes/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Build Status GitHub](https://github.com/buluma/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-bootstrap/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.docker](https://galaxy.ansible.com/buluma/docker)|[![Build Status GitHub](https://github.com/buluma/ansible-role-docker/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-docker/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-docker/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-docker)|
|[buluma.setuptools](https://galaxy.ansible.com/buluma/setuptools)|[![Build Status GitHub](https://github.com/buluma/ansible-role-setuptools/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-setuptools/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-setuptools/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-setuptools)|
|[buluma.core_dependencies](https://galaxy.ansible.com/buluma/core_dependencies)|[![Build Status GitHub](https://github.com/buluma/ansible-role-core_dependencies/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-core_dependencies/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-core_dependencies/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-core_dependencies)|

## [Context](#context)

This role is part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-kubernetes/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/buluma/enterpriselinux)|all|
|[Debian](https://hub.docker.com/r/buluma/debian)|all|
|[Ubuntu](https://hub.docker.com/r/buluma/ubuntu)|all|

The minimum version of Ansible required is 2.1, tests have been done on:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them on [GitHub](https://github.com/buluma/ansible-role-kubernetes/issues).

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-kubernetes/blob/master/LICENSE).

## [Author Information](#author-information)

[Michael Buluma](https://buluma.github.io/)

