# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.network "private_network", ip: "192.168.50.4",
    auto_config: false,
    libvirt__dhcp_enabled: false

  config.vm.define "router" do |router|
    router.vm.hostname = "router"

    # Which OPNsense release to install
    $opnsense_release = '21.7'

    # Enable SSH keepalive to work around https://github.com/hashicorp/vagrant/issues/516
    router.ssh.keep_alive = true

    # Configure proper shell for FreeBSD
    router.ssh.shell = '/bin/sh'

    config.vm.network "forwarded_port", guest: 443, host: 8443

    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://atlas.hashicorp.com/search
    router.vm.box = 'sompani/FreeBSD-12.1'

    # Bootstrap OPNsense
    router.vm.provision 'shell', inline: <<-SHELL
      pkg install --help
      # Download the OPNsense bootstrap script
      fetch -o opnsense-bootstrap.sh https://raw.githubusercontent.com/opnsense/update/master/src/bootstrap/opnsense-bootstrap.sh.in

      # Remove reboot command from bootstrap script
      sed -i '' -e '/reboot$/d' opnsense-bootstrap.sh

      # fix mising /etc/ssl/cert.pem
      ln -s /usr/local/share/certs/ca-root-nss.crt /etc/ssl/cert.pem

      # Start bootstrap
      sh ./opnsense-bootstrap.sh -r #{$opnsense_release} -y

      # apply modified config with 
      # - Set correct interface names so OPNsense's order matches Vagrant's
      # - Enable SSH by default
      # - Allow SSH on all interfaces
      # - Do not block private networks on WAN
      # - Create XML config for Vagrant user
      cp /vagrant/config.xml config.xml
      key=$(b64encode -r dummy <.ssh/authorized_keys | tr -d '\n')
      sed -i '' -e "s|KEY_PLACEHOLDER|${key}|" config.xml
      cp config.xml /usr/local/etc/config.xml

      # Change home directory to group nobody
      chgrp -R nobody /usr/home/vagrant

      # Change sudoers file to reference user instead of group
      # sed -i '' -e 's/^%//' /usr/local/etc/sudoers.d/vagrant

      # Reboot the system
      shutdown -r now

    SHELL
  end

  config.vm.define "pc1" do |web|
    web.vm.hostname = "pc1"
    web.vm.box = "generic/arch"
    web.vm.provision "shell", run: "always", inline: "pacman -S --noconfirm nmap"
    web.vm.provision "shell", run: "always", inline: "ip route del default"
  end

  config.vm.define "pc2" do |web|
    web.vm.hostname = "pc2"
    web.vm.box = "generic/arch"
    web.vm.provision "shell", run: "always", inline: "pacman -S --noconfirm nmap"
    web.vm.provision "shell", run: "always", inline: "ip route del default"
  end
end
