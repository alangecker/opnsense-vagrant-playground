# OPNSense Playground
easy to use virtual machines with OPNSense

## Requirements
- running libvirt
- vagrant + vagrant-libvirt
  I use simply added following to my `.zshrc` (requires docker):
  ```
  vagrant(){
    docker run -it --rm \
        -e LIBVIRT_DEFAULT_URI \
        -v /var/run/libvirt/:/var/run/libvirt/ \
        -v ~/.vagrant.d:/.vagrant.d \
        -v $(realpath "${PWD}"):${PWD} \
        -w $(realpath "${PWD}") \
        --network host \
        vagrantlibvirt/vagrant-libvirt:latest \
        vagrant $@
    }
  ```

## Start
```
vagrant up
```


## SSH
```sh
# router
vagrant ssh router
# hint: use sudo -i to get a root shell

# first vm
vagrant ssh pc1

# second vm
vagrant ssh pc2
```

## Webinterface
- you can find out the routers IP with `vagrant ssh-config`
- use that IP in the browser like https://192.168.121.134
- Credentials:
    - Username: `root`
    - Passwort: `opnsense`
    