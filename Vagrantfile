# -*- mode: ruby -*-
# vi: set ft=ruby :

# |
# | Require YAML module
# |
require 'yaml'

# |
# | Get dir path
# |
dir = File.dirname(File.expand_path(__FILE__))

# |
# | Read YAML files
# |
servers       = YAML.load_file("#{dir}/CONFIG.yaml")

# playbook_file = YAML.load_file("#{dir}/config.ansible.yml")

# |
# | Set values for message
# |


print servers

# | ············································································
# | Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
# | ············································································
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    servers["vms"].each do |server|
        config.vm.define server["name"] do |srv|


            # |
            # | :::::: Box
            # |
            srv.vm.box = server["box"]

            if server["box_version"]
                srv.vm.box_version = server["box_version"]
            end

            if server["box_check_update"]
                srv.vm.box_check_update = server["box_check_update"]
            end

            # |
            # | :::::: Networdk
            # |

            if server["private_network"]["ip_private"] && server["private_network"]["auto_config"]
                srv.vm.network "private_network",
                    ip:          server["private_network"]["ip_private"],
                    auto_config: server["private_network"]["auto_config"]
            elsif server["private_network"]["ip_private"]
                srv.vm.network "private_network",
                    ip:     server["private_network"]["ip_private"]
            else server["private_network"]["type"]
                srv.vm.network "private_network",
                    type: server["private_network"]["type"]
            end


            if defined? server["public_network"]["ip_public"]
                if server["public_network"]["ip_public"] == "auto"
                    srv.vm.network "public_network"
                elsif server["public_network"]["ip_public"] == "true"
                    srv.vm.network "public_network",
                        use_dhcp_assigned_default_route: true
                elsif server["public_network"]["ip_public"] && server["public_network"]["bridge"]
                    srv.vm.network "public_network",
                        ip:     server["public_network"]["ip_public"],
                        bridge: server["public_network"]["bridge"]
                else
                    srv.vm.network "public_network",
                        ip: server["public_network"]["ip_public"]
                end
            end

            # |
            # | :::::: Ports forwarded
            # |

            if server["ports"]
                server['ports'].each do |ports|
                    srv.vm.network "forwarded_port",
                    guest: ports["guest"],
                    host: ports["host"],
                    auto_correct: true
                end
            end

            # |
            # | :::::: Folder Sync
            # |

            if server["syncDir"]
                server['syncDir'].each do |syncDir|
                    if syncDir["owner"] && syncDir["group"]
                        srv.vm.synced_folder syncDir["host"],
                        syncDir["guest"],
                        owner: "#{syncDir["owner"]}",
                        group: "#{syncDir["group"]}",
                        mount_options:["dmode=#{syncDir["dmode"]}",
                        "fmode=#{syncDir["fmode"]}"],
                        create: true
                    else
                        srv.vm.synced_folder syncDir['host'],
                        syncDir['guest'],
                        create: true
                    end
                end
            end



# | ············································································
# | :::::: Vm Setup
# | ············································································

            srv.vm.provider :virtualbox do |vb|
                vb.name     = server["name"]
                vb.memory   = server["ram"]
                if server["gui"]
                    vb.gui      = server["gui"]
                end
                if server["cpus"]
                    vb.cpus     = server["cpus"]
                end
                vb.customize ["modifyvm", :id, "--usb", "off"]
                vb.customize ["modifyvm", :id, "--usbehci", "off"]
            end



# | ············································································
# | :::::: Provisions
# | ············································································

            # |
            # | :::::: Provisions - Bash
            # |
            if server["bash"]
                srv.vm.provision :shell, :path => server["bash"]
            end

            # |
            # | :::::: Provisions - Puppet
            # |
            if server["puppet"]
                srv.vm.provision :puppet do |puppet|
                    puppet.module_path    = "puppet/modules"
                    puppet.manifests_path = "puppet/manifests"
                    puppet.manifest_file  = server["puppet"]
                end
            end

            # |
            # | :::::: Provisions - Asible
            # |
            if server["ansible"]
                srv.vm.provision:ansible do |ansible|
                    if server["ansible"]["verbose"]
                        ansible.verbose = server["ansible"]["verbose"]
                    end
                    if server["ansible"]["playbook"]
                        ansible.playbook = server["ansible"]["playbook"]
                    end
                    if server["ansible"]["inventory_path"]
                        ansible.inventory_path = server["ansible"]["inventory_path"]
                    end
                    if server["ansible"]["host_key_checking"]
                        ansible.host_key_checking = server["ansible"]["host_key_checking"]
                    end
                    if server["ansible"]["limit"]
                        ansible.limit = server["ansible"]["limit"]
                    end
                end
                File.open('ansible/inventory' ,'w') do |f|
                    f.write "[vagrant]\n"
                    f.write "#{server["private_network"]["ip_private"]}\n"
                end
            end

# | ············································································
# | :::::: Vagrant Message
# | ············································································

srv.vm.post_up_message = " \e[0;37m

················································································
VAGRANT VM
················································································

Vm Name    : #{server['name']}
Private ip : \e[0;33m#{server["private_network"]["ip_private"]}\e[0;37m
Public  ip : \e[0;33m#{server["private_network"]["ip_public"]}\e[0;37m

················································································
\e[32m"

        end
    end
end
