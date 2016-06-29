# -*- mode: ruby -*-
# vi: set ft=ruby :

host_ssh_port = 12222
ENV["VAGRANT_DEFAULT_PROVIDER"] ||= "docker"
Vagrant.configure(2) do |config|
  # If DOCKER_HOST is set for connecting dockerd on different host
  # (eg. Docker-in-Docker), we can't connect container IP address
  # directly. But we can connect DOCKER_HOST with specified SSH port.
  begin
    docker_host = URI.parse(ENV["DOCKER_HOST"])
    if "tcp" == docker_host.scheme
      config.ssh.host = docker_host.host
      config.ssh.port = host_ssh_port
      config.vm.network(:forwarded_port,
                        guest: 22, host: host_ssh_port, id: "ssh")
      config.vm.synced_folder(".", "/vagrant", type: "rsync",
                              rsync__exclude: ".git/")
    end
  rescue URI::InvalidURIError
  end

  config.vm.provider(:docker) do |d|
    d.image = "nishidayuya/docker-vagrant-debian"
    d.has_ssh = true

    # for `sudo service postfix status`.
    #
    # https://github.com/docker/docker/issues/11049
    #d.create_args = %w(--cap-add=SYS_PTRACE)
  end

  config.vm.provision(:shell, inline: <<EOS)
set -xe

sudo apt-get update
ls -alF /vagrant/
EOS
end
