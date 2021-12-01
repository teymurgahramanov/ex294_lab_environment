#### configure vim
```echo "set ts=2 sw=2 expandtab" >> ~/.vimrc```

#### ansible.cfg
Use ```ansible-config``` dump to see possible parameters for ansible.cfg

#### adhoc
```ansible webservers -m user -a "name=foo state=present"```

#### set password in user module
```"{{ 123456 | password_hash('sha512') }}"```

#### access custom facts
```ansible_local.(file name in /etc/ansible/facts.d/ on managed node).section.parameter```

#### when host in group
```when: inventory_hostname in groups['database']```

#### jinja2 for loop
```
{% for host in groups['all'] %}
{{ host }}
{% endfor %}
```
#### special/magic variables
- hostvars - Access host variables or gathered facts\
```{{ hostvars['test.example.com']['ansible_facts']['distribution'] }}```
- groups - Enumerate hosts in groups
```
{% for host in groups['app_servers'] %}
   {{ hostvars[host]['ansible_facts']['eth0']['ipv4']['address'] }}
{% endfor %}
```
inventory_hostname - Hostname in inventory