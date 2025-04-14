# gitlab_on_k3s_vagrant_libvirt_ansible

Vagrant-libvirt setup that creates a VM with k3s and installs
[GitLab](https://about.gitlab.com/) into the cluster.

The installation is done using the [official helm
chart](https://docs.gitlab.com/charts/).

There is a branch `with-gitlab-runner` that also installs a [Gitlab
Runner](https://docs.gitlab.com/runner/install/kubernetes/).

This branch called `gitlab-operator` installs GitLab using the [GitLab
operator](https://operatorhub.io/operator/gitlab-operator-kubernetes), while the
`main` branch uses the official helm chart for that.

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

## Supplying a Docker Hub token to avoid rate limiting

To not run into Docker Hub's recent rate limiting when pulling without
authentication, you can create a file containing a token for the Docker Hub.
This file, when called
`ansible/group_vars/all/k3s_configure_private_registries.yml`, will be ignored
by Git.

```yaml
k3s_configure_private_registries: true
registry_configs:
  - url: docker.io
    auth:
      username: DOCKER_HUB_USER_NAME_GOES_HERE
      password: DOCKER_HUB_TOKEN_GOES_HERE
    tls:
      insecure_skip_verify: false
```

This setup will use this to set up k3s's `/etc/rancher/k3s/registries.yaml` file
so all image from the Docker Hub will be pulled using your token.

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the kubeconfig file `ansible/k3s-kubeconfig`.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
