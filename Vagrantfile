Vagrant.configure("2") do |config|
    # Ansible control server:
    config.vm.define "acs" do |acs|
        acs.vm.box = "generic/ubuntu2204"
        acs.vm.hostname = "acs"

        # Provision script to register this VM with the RHEL subscription.
        acs.vm.provision "shell" do |shell|
            shell.inline = <<-SHELL
                set -euo pipefail

                echo "Installing Ansible..."
                # Prevent any interaction from apt:
                export DEBIAN_FRONTEND=noninteractive
                apt-get update
                apt-get install -y python3-venv

                # The recommended way of installing Ansible is in a Python virtual environment.
                sudo -u vagrant bash -c '
                    python3 -m venv /home/vagrant/venv-ansible

                    . /home/vagrant/venv-ansible/bin/activate
                    # Sometimes older versions of pip will result in errors when installing Ansible,
                    # so upgrade it to the latest first.
                    pip install --upgrade pip

                    # Installing Ansible 9.8.0 since that is the latest that supports
                    # Python 3.6 of Alma Linux 8 and Python 2.7 of CentOS 7.
                    pip install ansible==9.8.0 ansible-lint

                    # Make sure we always use ansible from the virtualenv.
                    echo ". ~/venv-ansible/bin/activate" >> ~/.bashrc
                '
            SHELL
        end

        # Make sure all sensitive info is only readable by user.
        acs.vm.synced_folder ".", "/vagrant", mount_options: ["dmode=700,fmode=600"]
    end

    # Gitlab server:
    config.vm.define "gitlab-server" do |gitlab_server|
        gitlab_server.vm.box = "generic/alma9"
        gitlab_server.vm.hostname = "gitlab-server"

        gitlab_server.vm.network "private_network", ip: "192.168.16.45"

        gitlab_server.vm.provider "virtualbox" do |vb|
            vb.memory = "4096"
            vb.cpus = 4
        end
    end

    # Gitlab runner CentOS 7:
    config.vm.define "gitlab-runner-7" do |gitlab_runner_7|
        gitlab_runner_7.vm.box = "eurolinux-vagrant/centos-7"
        gitlab_runner_7.vm.hostname = "gitlab-runner-7"

        gitlab_runner_7.vm.network "private_network", ip: "192.168.16.46"
   end

    # Gitlab runner AlmaLinux 8:
    config.vm.define "gitlab-runner-8" do |gitlab_runner_8|
        gitlab_runner_8.vm.box = "generic/alma8"
        gitlab_runner_8.vm.hostname = "gitlab-runner-8"

        gitlab_runner_8.vm.network "private_network", ip: "192.168.16.47"
   end
end
