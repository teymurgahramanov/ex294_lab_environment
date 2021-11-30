if there is no ansible.cfg
- Create ansible.cfg, set ANSIBLE_CONFIG environment variable, use ansible-config dump to see 

adhoc example:
- ```ansible webservers -m user -a "name=foo state=present"```

use setup module to get facts

 “{{ user_password | password_hash(‘sha512’) }}”

custom fact structure

ansible_local.(file name in /etc/ansible/facts.d/ on managed node).section.parameter

check host in group
when: inventory_hostname in groups['database']
