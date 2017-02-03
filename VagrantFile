Vagrant.configure("2") do |config|
	agents = 3
	gateways = 2

	config.vm.box = "ubuntu/trusty64"


	(1..agents).each do |machine_id|
	  config.vm.define "agent-#{machine_id}" do |machine|
		machine.vm.hostname = "agent-#{machine_id}"
		machine.vm.network "private_network", ip: "10.2.0.#{128+machine_id}"
	  end
	end

	(1..gateways).each do |machine_id|
	  config.vm.define "gateway-#{machine_id}" do |machine|
		machine.vm.hostname = "gateway-#{machine_id}"
		machine.vm.network "private_network", ip: "10.#{machine_id}.0.254"
		machine.vm.network "private_network", ip: "10.128.0.#{128+machine_id}"
	  end
	end

	config.vm.define "ossec-master" do |machine|
	  machine.vm.hostname = "ossec-master"
      machine.vm.network "private_network", ip: "10.1.0.129"
	end

    config.vm.provision :ansible do |ansible|
      # Disable default limit to connect to all the machines
      ansible.limit = "all"
      ansible.playbook = "provisioning/main.yml"
      ansible.verbose = true
      ansible.groups = {
        "ossec-agents" => ["agent-[1:#{agents}]"],
        "gateways" => ["gateway-*"],
      }
    end
end
