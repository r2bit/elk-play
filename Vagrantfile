IMAGE_NAME = "centos/8"
BASENET = "192.168.56"
ESNODES = 3
NETDATA_CLAIM = ""

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 1
  end

  # generate inventory
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/init.yml"
  end

  # kibana node
  config.vm.define "kibana" do |node|
    node.vm.box = IMAGE_NAME
    node.vm.network "private_network", ip: "#{BASENET}.20", hostname: true
    node.vm.hostname = "kibana"
  end

  # logstash node
  config.vm.define "logstash" do |node|
    node.vm.box = IMAGE_NAME
    node.vm.network "private_network", ip: "#{BASENET}.30", hostname: true
    node.vm.hostname = "logstash"
  end

  esnode_ips = []
  esnode_names = []

  # elasticsearch nodes
  (1..ESNODES).each do |i|
    config.vm.define "esnode-#{i}" do |node|
      esnode_ip = "#{BASENET}.#{i + 10}"
      esnode_ips << esnode_ip
      esnode_names << "esnode-#{i}"

      node.vm.box = IMAGE_NAME
      node.vm.network "private_network", ip: esnode_ip, hostname: true
      node.vm.hostname = "esnode-#{i}"

      if i == ESNODES
        node.vm.provision "ansible" do |ansible|
          ansible.limit = "all"
          ansible.playbook = "ansible/playbook.yml"
          ansible.groups = {
            "esnodes" => ["esnode-[1:#{ESNODES}]"],
            "esnodes:vars" => {
              "esnode_ips" => esnode_ips.join(",") ,
              "esnode_names" => esnode_names.join(",")
            },
            "all:vars" => {
              "netdata_claim_command" => NETDATA_CLAIM
            }
          }
          ansible.verbose = true
        end
      end
    end
  end
end