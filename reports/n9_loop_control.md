# Loop Control

Loop control in Ansible refers to a set of options and attributes that allow you to control and customize how loops are executed within Ansible playbooks. Loop control options provide fine-grained control over loop iterations and can be used to adjust the behavior of tasks that involve loops. These options are defined within the loop_control dictionary within a loop in Ansible playbooks.

The key loop control options include:

- loop
  Specifies the list of items over which the loop iterates.

```
---
- name: Example Playbook with Register
  hosts: webserver
  become: yes
  tasks:
    - name: install required softwares
      package:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - ntp
        - wget
        - telnet
      register: softare_installation_output

    - name: show softare_installation_output
      debug:
        msg: "{{ item.results }}"
      loop: "{{ softare_installation_output.results }}"
```

- loop_var
  Defines the name of the loop variable that holds the current item in each iteration.

```
---
- name: Example Playbook with Register
  hosts: webserver
  become: yes
  tasks:
    - name: install required softwares
      package:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - ntp
        - wget
        - telnet
      register: softare_installation_output

    - name: show softare_installation_output
      debug:
        msg: "{{ software.results }}"
      loop: "{{ softare_installation_output.results }}"
      loop_control:
        loop_var: software
```

- label
  Assigns a label to the loop to distinguish it from other loops within the playbook.

```
---
- name: Example Playbook with Register
  hosts: webserver
  become: yes
  tasks:
    - name: install required softwares
      package:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - ntp
        - wget
        - telnet
      register: softare_installation_output

    - name: show softare_installation_output
      debug:
        msg: "{{ software.results }}"
      loop: "{{ softare_installation_output.results }}"
      loop_control:
        loop_var: software
        label: "software_installation_status"
```

- index_var
  Allows you to create a loop variable that keeps track of the current iteration index.

```
---
- name: Example Playbook with Register
  hosts: webserver
  become: yes
  tasks:
    - name: install required softwares
      package:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - ntp
        - wget
        - telnet
      register: softare_installation_output

    - name: show softare_installation_output
      debug:
        msg: "item position is : {{ idx }} , {{ software.results }}"
      loop: "{{ softare_installation_output.results }}"
      loop_control:
        loop_var: software
        label: "{{ inventory_hostname }}"
        index_var: idx
```

- pause
  Specifies the duration (in seconds) to pause between iterations of the loop. Useful when you want to keep some wait interval between instaling each software.

```
---
- name: Example Playbook with Register
  hosts: webserver
  become: yes
  tasks:
    - name: install required softwares
      package:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - ntp
        - wget
        - telnet
      loop_control:
        pause: 5
      register: softare_installation_output

    - name: show softare_installation_output
      debug:
        msg: "item position is : {{ idx }} , {{ software.results }}"
      loop: "{{ softare_installation_output.results }}"
      loop_control:
        loop_var: software
        label: "{{ inventory_hostname }}"
        index_var: idx
        pause: 10
```

- until
  Defines a condition that, when met, causes the loop to exit early before all items have been processed. Useful in checking if a website is up etc.

These loop control options provide flexibility and control, allowing you to fine-tune loop behavior, handle complex scenarios, and improve the efficiency of playbook execution. Loop control is particularly useful when you need to customize the behavior of loops based on specific requirements in your Ansible playbooks.
