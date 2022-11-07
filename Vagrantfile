IMAGE_NAME = "generic/debian11"
N = 2

Vagrant.configure("2") do |config|
    config.vm.box_check_update = true
    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 2
        v.customize ["modifyvm", :id, "--nicpromisc1", "allow-all", "--nicpromisc2", "allow-all"]
        v.customize [ "guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 1000 ]        
    end
      
    config.vm.define "k8s-controlplane" do |controlplane|
        controlplane.vm.box = IMAGE_NAME
        controlplane.vm.network "private_network", ip: "192.168.56.10",
            auto_config: true
        controlplane.vm.hostname = "k8s-controlplane"
        controlplane.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible/kubernetes-setup/controlplane-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.56.10",
                crio_version: "1.25",
                crio_os_release: "Debian_11",
                kubernetes_release: "1.25.3",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "k8s-node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.56.#{i + 10}",
                auto_config: true
            node.vm.hostname = "k8s-node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "ansible/kubernetes-setup/workers-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.56.#{i + 10}",
                    crio_version: "1.25",
                    crio_os_release: "Debian_11",
                    kubernetes_release: "1.25.3",
                }
            end
            if i == N
                node.vm.provision :ansible do |ansible|
                    ansible.limit = "k8s-controlplane"
                    ansible.playbook = "ansible/kubernetes-setup/kubelet-certificates-approve.yml"

                end
            end
        end
    end
end