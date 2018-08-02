Ansible Role: Varnish 5.x
======================================

[![Build Status](https://travis-ci.org/davepanarese/ansible-varnish.svg?branch=master)](https://travis-ci.org/davepanarese/ansible-varnish)
[![Galaxy](https://img.shields.io/badge/galaxy-davepanarese.varnish-blue.svg?style=flat-square)](https://galaxy.ansible.com/davepanarese/varnish)  

Install varnish on Ubuntu 16.04 (Xenial)

## Requirements

This role requires Ansible 2.4 or higher.

## Role Variables

The role defines most of its variables in `defaults/main.yml`:

## Example Playbook

Example wit wordpress installation wit consul dinamic backend.

```yaml

- name: Get Facts all hosts
  hosts: all
  become: true
  become_user: root
  become_method: sudo 
  serial: 1
  gather_facts: true
  roles:
    role: entercloudsuite.varnish
    use_consul: true
    consul_server_address: "myconsulserver.example.com:8500"
    consul_backend_group: "MyWebserverGroupName"
    varnish_application:
      - name: myaplication
        varnish_adm_port: 6081
        varnish_port: "8081"
        varnish_memory: "1g" 
        varnish_probe_check:
          probe_name: "probe"
          probe_url: "/check.html" # page to check
          probe_interval: "5s"
          probe_timeout: "3s"
          probe_window: "5"
          probe_threshold: "3"
        varnish_backend:
          service_proxy_port: "80"
        varnish_hosts_allow_purge: # who can purge cache
          - "10.2.0.0/24"
        varnish_url_cache:
          - name: content
            url: "www.example.com"
            path: "/wp-content"
            http_header: "cache - content"
            cache_ttl: "86400"
          - name: scripts
            url: "www.example.com"
            path: "/wp-includes"
            http_header: "cache - scripts"
            cache_ttl: "86400"
          - name: all_pages
            url: "www.example.com"
            path: "/"
            http_header: "cache - site"
            cache_ttl: "3600"
        varnish_cookie_not_cache:
          - cookie: wordpress_logged_in
        varnish_url_not_cache:
          - name: login
            url: "www.example.com"
            path: "/wp-login"
            http_header: "no cache - wp login"
          - name: admin
            url: "www.example.com"
            path: "/wp-admin"
            http_header: "no cache - wp admin"
          - name: xmlrpc
            url: "www.example.com"
            path: "/xmlrpc"
            http_header: "no cache - xmlrpc"
          - name: sitemap
            url: "www.example.com"
            path: "/sitemap"
            http_header: "no cache - sitemap"

```
## Testing

Tests are performed using [Molecule](http://molecule.readthedocs.org/en/latest/).

Install Molecule or use `docker-compose run --rm molecule` to run a local Docker container, based on the [enterclousuite/molecule](https://hub.docker.com/r/fminzoni/molecule/) project, from where you can use `molecule`.

1. Run `molecule create` to start the target Docker container on your local engine.  
2. Use `molecule login` to log in to the running container.  
3. Edit the role files.  
4. Add other required roles (external) in the molecule/default/requirements.yml file.  
5. Edit the molecule/default/playbook.yml.  
6. Define infra tests under the molecule/default/tests folder using the goos verifier.  
7. When ready, use `molecule converge` to run the Ansible Playbook and `molecule verify` to execute the test suite.  
Note that the converge process starts performing a syntax check of the role.  
Destroy the Docker container with the command `molecule destroy`.   

To run all the steps with just one command, run `molecule test`. 

In order to run the role targeting a VM, use the playbook_deploy.yml file for example with the following command: `ansible-playbook ansible-varnish/molecule/default/playbook_deploy.yml -i VM_IP_OR_FQDN, -u ubuntu --private-key private.pem`.  

## License

MIT
