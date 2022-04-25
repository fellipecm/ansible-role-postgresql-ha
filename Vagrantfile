# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
#

cluster = 'prod'

pgpool_memory = 2048
pgpool_disk_size = '20GB'
pgpool_cpus = 1
pgpool_cpu_cap = 80

vms = {
  'pgsql01' => { 'memory' => "#{pgpool_memory}", 'disk' => "#{pgpool_disk_size}", 'ip' => '192.168.1.171', 'cpu' => "#{pgpool_cpus}", 'cpu_cap' => "#{pgpool_cpu_cap}" },
  'pgsql02' => { 'memory' => "#{pgpool_memory}", 'disk' => "#{pgpool_disk_size}", 'ip' => '192.168.1.172', 'cpu' => "#{pgpool_cpus}", 'cpu_cap' => "#{pgpool_cpu_cap}" },
  'pgsql03' => { 'memory' => "#{pgpool_memory}", 'disk' => "#{pgpool_disk_size}", 'ip' => '192.168.1.173', 'cpu' => "#{pgpool_cpus}", 'cpu_cap' => "#{pgpool_cpu_cap}" },
  'pgsql04' => { 'memory' => "#{pgpool_memory}", 'disk' => "#{pgpool_disk_size}", 'ip' => '192.168.1.174', 'cpu' => "#{pgpool_cpus}", 'cpu_cap' => "#{pgpool_cpu_cap}" },
  # 'pgsql05' => { 'memory' => "#{pgpool_memory}", 'disk' => "#{pgpool_disk_size}", 'ip' => '192.168.1.175', 'cpu' => "#{pgpool_cpus}", 'cpu_cap' => "#{pgpool_cpu_cap}" },
  # 'pgsql06' => { 'memory' => "#{pgpool_memory}", 'disk' => "#{pgpool_disk_size}", 'ip' => '192.168.1.176', 'cpu' => "#{pgpool_cpus}", 'cpu_cap' => "#{pgpool_cpu_cap}" },
  }

Vagrant.configure("2") do |config|

  # GENERIC CONFIG
  config.vm.box = "ubuntu/focal64"
  # config.vm.box_check_update = true
  vms.each do |name, conf|
    config.vm.define "#{name}" do |k|
      # k.vm.hostname = "#{name}-#{cluster}"
      k.vm.hostname = "#{name}"
      k.vm.network 'public_network', ip: "#{conf['ip']}", bridge: "eno1"
      k.vm.disk :disk, size: "#{conf['disk']}", primary: true
      k.vm.provider 'virtualbox' do |vb|
        vb.cpus = conf['cpu']
        vb.customize ["modifyvm", :id, "--cpuexecutioncap", "#{conf['cpu_cap']}"]
        vb.linked_clone = true
        vb.memory = conf['memory']
        # vb.name = "#{name}-#{cluster}"
        vb.name = "#{name}"
        # Copy private key
        k.vm.provision "file", source: "id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
        public_key = File.read("id_rsa.pub")
        k.vm.provision "shell", privileged: false, inline: <<-SCRIPT
            # if [[ ! -f /home/vagrant/.ssh/config ]];then
            echo 'Copying ansible-vm public SSH Keys to the VM'
            mkdir -p /home/vagrant/.ssh
            chmod 700 /home/vagrant/.ssh
            echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
            chmod -R 600 /home/vagrant/.ssh/authorized_keys
            echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
            echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
            echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
              chmod -R 600 /home/vagrant/.ssh/config
            # fi
            SCRIPT
        k.vm.provision "shell", privileged: true, inline: <<-SCRIPT        
        # if [[ $(grep "192.168.1.171" /etc/hosts | wc -l) == "0" ]]; then
        echo 'Setting hos/etc/hosts'
        echo -e "192.168.1.171 pgsql01\n192.168.1.172 pgsql02\n192.168.1.173 pgsql03\n192.168.1.174 pgsql04" >> /etc/hosts
        # fi
        SCRIPT
      end
    end
  end  
end
