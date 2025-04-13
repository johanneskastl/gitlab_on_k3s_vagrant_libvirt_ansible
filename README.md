# gitlab_on_k3s_vagrant_libvirt_ansible

Vagrant-libvirt setup that creates a VM with k3s and installs
[GitLab](https://about.gitlab.com/) into the cluster.

The installation is done using the [official helm
chart](https://docs.gitlab.com/charts/).

If I get it working, there will be a branch called `gitlab-operator` that
installs GitLab using the [GitLab
operator](https://operatorhub.io/operator/gitlab-operator-kubernetes).

Default OS is openSUSE Leap 15.6, but that can be changed in the Vagrantfile.
Please be aware, that this might break the Ansible provisioning.

For Ansible to talk to the k3s cluster you need the `kubernetes.core` collection
and the Python modules required by this collection **on the Ansible control
host**, i.e. the machine where you are executing Vagrant and Ansible from.

## Vagrant

1. You need `vagrant`, obviously. And `git`. And Ansible...
1. Fetch the box, per default this is `opensuse/Leap-15.6.x86_64`, using
   `vagrant box add opensuse/Leap-15.6.x86_64`.
1. Make sure the git submodules are fully working by issuing
   `git submodule init && git submodule update`
1. Run `vagrant up`
1. Run `kubectl --kubeconfig ansible/k3s-kubeconfig get nodes` and you should
   see your server.
1. Open the URL that Ansible printed out at the end of the provisioning (it
   should look something like `https://gitlab.192.0.2.13.sslip.io`). Best is
   to open it in a private browser window, as it uses a self-signed certificate
   that you need to trust.
1. Log in using the username `root` and the password `atotallysecurepassword`.
1. Party!

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the kubeconfig file `ansible/k3s-kubeconfig`.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
