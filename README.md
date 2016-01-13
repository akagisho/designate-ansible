# designate-ansible

designate-ansible is a provisioner of OpenStack [Designate](http://docs.openstack.org/developer/designate/).

## Requirements

* Vagrant 1.5+
* Ansible

## Setup

### Start VMs

Clone this repository.

    $ git clone https://github.com/akagisho/designate-ansible.git
    $ cd designate-ansible

Start VMs.

    $ vagrant up

### Execute Ansible playbook

    $ vagrant provision
        OR
    $ ansible-playbook -i inventories/development site.yml

### Register servers on Designate

    $ curl -X POST -i -H 'Accept: application/json' -H 'Content-Type: application/json' \
        -d '{"name": "ns1.example.com."}' \
        http://10.200.19.40:9001/v1/servers
    $ curl -X POST -i -H 'Accept: application/json' -H 'Content-Type: application/json' \
        -d '{"name": "ns2.example.com."}' \
        http://10.200.19.40:9001/v1/servers

## API examples

### Create domain

    $ curl -X POST -i -H 'Accept: application/json' -H 'Content-Type: application/json' \
        -d '{
          "name": "example.net.",
          "email": "hostmaster@example.net"
        }' http://10.200.19.40:9001/v1/domains

### Add record

    $ curl -X POST -i -H 'Accept: application/json' -H 'Content-Type: application/json' \
        -d '{
          "name": "www.example.net.",
          "type": "A",
          "data": "192.168.1.1"
        }' http://10.200.19.40:9001/v1/domains/bc9d25dd-76b6-499d-9492-d157a7b86683/records

## Confirm records

    $ for i in 10.200.19.41 10.200.19.42; do \
        dig +norec +noques +nostats +nocmd @$i example.net. ns; done
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 44680
    ;; flags: qr aa; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0
    
    ;; ANSWER SECTION:
    example.net.        3600    IN  NS  ns1.example.com.
    example.net.        3600    IN  NS  ns2.example.com.
    
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 28476
    ;; flags: qr aa; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0
    
    ;; ANSWER SECTION:
    example.net.        3600    IN  NS  ns1.example.com.
    example.net.        3600    IN  NS  ns2.example.com.

    $ for i in 10.200.19.41 10.200.19.42; do \
        dig @$i www.example.net. a +short; done
    192.168.1.1
    192.168.1.1
