Vagrant.configure('2') do |config|
  config.vm.box = 'bento/ubuntu-22.04'

  ### uncomment this to add custom DNS to your VM
  # $dns_script = <<-'SCRIPT'
  # echo "nameserver 172.30.0.3" > /etc/resolv.conf
  # echo "127.0.0.1 localhost" >> /etc/hosts
  # SCRIPT

  

  

  $common_installations = <<-'SCRIPT'
  echo "192.168.1.10 "$cluster_name"-master-0" >> /etc/hosts
  echo "192.168.1.11 "$cluster_name"-master-1" >> /etc/hosts
  echo "192.168.1.12 "$cluster_name"-master-2" >> /etc/hosts
  echo "192.168.1.20 "$cluster_name"-worker-0" >> /etc/hosts
  echo "192.168.1.21 "$cluster_name"-worker-1" >> /etc/hosts
  echo "192.168.1.22 "$cluster_name"-worker-2" >> /etc/hosts
  echo "192.168.1.30 "$cluster_name"-deployer" >> /etc/hosts
  SCRIPT

  $deployer_installations = <<-'SCRIPT'
  sudo apt-get update -y
  sudo apt-get install ansible sshpass lvm2 -y
  SCRIPT

  # $controller_installations = <<-'SCRIPT'
  # SCRIPT

  # $worker_installations = <<-'SCRIPT'
  # SCRIPT

  ### cluster name configuration
  ### configure this also in the group_vars/all.yml and hosts/hosts
  cluster_name = "kubernetes-cluster-01"

  ### cluster network configuration
  ### configure this also in the group_vars/all.yml and hosts/hosts
  config.vm.network "public_network", bridge: "wlp2s0: Wi-Fi (AirPort)", auto_config: true

  ### currently only support /24
  ### please provide only the network section
  ### TODO: make this more configurable
  cluster_network = "192.168.1"

  (0..2).each do |i|
    config.vm.define "#{cluster_name}-master-#{i}" do |master|
      master.vm.hostname = "#{cluster_name}-master-#{i}"
      # master.vm.provision 'shell', inline: "echo '#{cluster_network}.1#{i} #{cluster_name}-master-#{i}' > /etc/hosts"
      master.vm.provision 'shell', inline: $common_installations
      # master.vm.network 'private_network', ip: "#{cluster_network}.1#{i}"
      master.vm.network :public_network, ip: "#{cluster_network}.1#{i}"
      

      ### uncomment this to add custom DNS to your VM
      # master.vm.provision 'shell', inline: $dns_script, run: "always"

      ### uncomment this to add host public ssh key to your VM
      ### you need to have the public ssh key in ~/.ssh/id_rsa.pub 
      # master.vm.provision "shell" do |s|
      #   ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
      #   s.inline = <<-SHELL
      #     echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      #   SHELL
      # end
      
      ### Uncomment this if you need to add self signed CA to trust
      ### you need to install vagrant-certificates plugin
      ### vagrant plugin install vagrant-certificates
      # master.certificates.enabled = true
      # master.certificates.certs = Dir.glob('../../template/ca/root-cert.pem')

      master.vm.provider 'virtualbox' do |vb|
        vb.name = "#{cluster_name}-master-#{i}"
        vb.memory = 2048
        vb.cpus = 2
        vb.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
        vb.customize ['modifyvm', :id, '--nested-hw-virt', 'on']
      end
    end
  end

  #  WORKERS DEFFERED TO ANOTHER MACHINE DUE TO RAM LIMITS (SEE ./vagrant_extra_host)
  #  -----------------------------------------------------------------------------------
  # (0..2).each do |i|
  #   config.vm.define "#{cluster_name}-worker-#{i}" do |worker|
  #     worker.vm.hostname = "#{cluster_name}-worker-#{i}"
  #     worker.vm.network 'private_network', ip: "#{cluster_network}.2#{i}"
  #     worker.vm.provision 'shell', inline: "echo '#{cluster_network}.2#{i} #{cluster_name}-worker-#{i}' > /etc/hosts"
  #      worker.vm.provision 'shell', inline: $common_installations

  #     ### uncomment this to add custom DNS to your VM
  #     # worker.vm.provision 'shell', inline: $dns_script, run: "always"

  #     ### uncomment this to add host public ssh key to your VM
  #     ### you need to have the public ssh key in ~/.ssh/id_rsa.pub 
  #     # worker.vm.provision "shell" do |s|
  #     #   ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
  #     #   s.inline = <<-SHELL
  #     #     echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
  #     #   SHELL
  #     # end

  #     ### Uncomment this if you need to add self signed CA to trust
  #     ### you need to install vagrant-certificates plugin
  #     ### vagrant plugin install vagrant-certificates
  #     # worker.certificates.enabled = true
  #     # worker.certificates.certs = Dir.glob('../../template/ca/root-cert.pem')

  #     worker.vm.provider 'virtualbox' do |vb|
  #       vb.name = "#{cluster_name}-worker-#{i}"
  #       vb.memory = 4096
  #       vb.cpus = 4
  #       vb.customize ['modifyvm', :id, '--nicpromisc2', 'allow-all']
  #       vb.customize ['modifyvm', :id, '--nested-hw-virt', 'on']

  #       unless File.exist?("./#{cluster_name}-worker-#{i}-disk-02.vdi")
  #         vb.customize ['createhd', '--filename', "./#{cluster_name}-worker-#{i}-disk-02.vdi", '--variant', 'Standard', '--size', 100 * 1024]
  #       end

  #       vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', "./#{cluster_name}-worker-#{i}-disk-02.vdi"]
  #     end
  #   end
  # end

  config.vm.define "#{cluster_name}-deployer" do |deployer|
    deployer.vm.hostname = "#{cluster_name}-deployer"
    # deployer.vm.network 'private_network', ip: "#{cluster_network}.30"
    deployer.vm.network :public_network, ip: "#{cluster_network}.30"
    # deployer.vm.provision 'shell', inline: "echo '#{cluster_network}.30 #{cluster_name}-deployer' > /etc/hosts"
    deployer.vm.provision 'shell', inline: $common_installations
    deployer.vm.provision 'shell', inline: $deployer_installations
          
    ### uncomment this to add custom DNS to your VM
    # deployer.vm.provision 'shell', inline: $dns_script, run: "always"

    ### uncomment this to add host public ssh key to your VM
    ### you need to have the public ssh key in ~/.ssh/id_rsa.pub 
    # deployer.vm.provision "shell" do |s|
    #   ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    #   s.inline = <<-SHELL
    #     echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
    #   SHELL
    # end

    ### Uncomment this if you need to add self signed CA to trust
    ### you need to install vagrant-certificates plugin
    ### vagrant plugin install vagrant-certificates
    # deployer.certificates.enabled = true
    # deployer.certificates.certs = Dir.glob('../../template/ca/root-cert.pem')

    
    deployer.vm.provider 'virtualbox' do |vb|
      vb.name = "#{cluster_name}-deployer"
      vb.memory = 1024
      vb.cpus = 2
      vb.customize ['modifyvm', :id, '--nested-hw-virt', 'on']
    end
  end
end
