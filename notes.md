if there is no ansible.cfg
- Create ansible.cfg, set ANSIBLE_CONFIG environment variable, use ansible-config dump to see 

adhoc example:
- ```ansible webservers -m user -a "name=foo state=present"```

use setup module to get facts

 “{{ user_password | password_hash(‘sha512’) }}”
