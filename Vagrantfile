Dotenv.load

Vagrant.configure(2) do |config|

  config.vm.box = "debian/buster64"

  config.vm.provider :virtualbox do |v|
    v.memory = 2048
    v.cpus = 2
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root","1"]
  end

  config.vm.network "private_network", type: "dhcp", ip: "192.168.33.0"
  config.vm.hostname = ENV['VAGRANT_HOST']

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
    ip_address = ''
    if hostname = (vm.ssh_info && vm.ssh_info[:host])
      vm.communicate.execute("/bin/ip addr show dev eth1 | grep 'inet ' | tail -n 1 | egrep -o '[0-9\.]+' | head -n 1 2>&1") do |type, contents|
        ip_address = contents.split("\n").first
      end
    end
    ip_address
  end

  config.hostmanager.aliases = ["#{ENV['VAGRANT_HOST']}"]

  config.vm.synced_folder ".", "/vagrant", type:"virtualbox", create: true

  config.vm.provision :docker
  config.vm.provision :docker_compose

  # Docker SSH Connection
  config.vm.provision "shell", privileged: true, inline: <<-SHELL
    sed -i -e 's|^#\\?MaxSessions.*|MaxSessions 100|' /etc/ssh/sshd_config
    /etc/init.d/ssh restart
  SHELL

  # ファイル監視数の上限
  config.vm.provision "shell", privileged: true, inline: <<-SHELL
    echo fs.inotify.max_user_watches= 65536 | tee -a /etc/sysctl.conf && sysctl -p
  SHELL

end
