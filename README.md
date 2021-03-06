Role Name
=========

Installs and configures Gerrit Code Review...https://www.gerritcodereview.com/

Requirements
------------
Install all Ansible role requirements.
```
sudo ansible-galaxy install -r requirements.yml -f
```

Vagrant
-------
Spin up Environment under Vagrant to test.
```
vagrant up
```

Usage
-----

###### Non-Vagrant
Login to WebUI using defined owncloud_admin_user and owncloud_admin_pass vars (http://iporhostname:8080)

###### Vagrant
Login to WebUI using defined owncloud_admin_user and owncloud_admin_pass vars (http://127.0.0.1:8080)


Role Variables
--------------

```
---
# defaults file for ansible-gerrit
gerrit_account_info:
  name: gerrit  #defines username
  comment: Gerrit User  #defines username comment
  home: '{{ gerrit_site_dir }}'  #defines username home directory
  group: gerrit  #defines usernames groupname
gerrit_allow_remote_admin: false  #defines if gerrit should allow remote admin capabilities
gerrit_auth_type: OAUTH  #defines authorization type...OPENID, LDAP, LDAP_BIND...
gerrit_canonical_web_url: http://{{ ansible_hostname }}.{{ pri_domain_name }}:{{ gerrit_http_listen_port }}/
gerrit_dl_info:
  - url: https://gerrit-releases.storage.googleapis.com
    filename: 'gerrit-{{ gerrit_version }}.war'
gerrit_gitweb_integration: false  #defines is gerrit should be integrated with gitweb...gitweb is not controlled via gerrit using this method...
gerrit_http_listen_url: http://127.0.0.1
gerrit_http_listen_port: 8081
gerrit_init: "java -jar {{ gerrit_install_dir }}/gerrit.war init --batch --no-auto-start -d {{ gerrit_site_dir }} --migrate-to-note-db --install-plugin codemirror-editor --install-plugin commit-message-length-validator --install-plugin delete-project --install-plugin download-commands --install-plugin gitiles --install-plugin hooks --install-plugin plugin-manager --install-plugin replication --install-plugin reviewnotes --install-plugin singleusergroup --install-plugin webhooks"
gerrit_install_dir: /opt/gerrit
gerrit_install_plugins: false  #setting to false for now until plugin install method works...
gerrit_install_plugins_compiled: false
gerrit_java_home: /usr/lib/jvm/java-11-openjdk-amd64
gerrit_ldap_info:
  - server: 'ldap://dc01.{{ pri_domain_name }}'
    accountbase: 'DC=example,DC=org'
    accountpattern: '(&(objectClass=person)(userPrincipalName=${username}))'
    accountscope: 'sub'
    groupbase: 'DC=example,DC=org'
    accountfullname: 'displayName'
    accountmemberfield: 'memberOf'
    accountemailaddress: 'mail'
    accountsshusername: '${sAMAccountName.toLowerCase}'
    referral: 'follow'
gerrit_plugins_compiled:
  - name: admin-console.jar
  - name: metrics-reporter-prometheus.jar
  - name: gerrit-oauth-provider.jar
  - name: slack-integration.jar
gerrit_oauth_provider_google: false
gerrit_plugins: []
gerrit_replication_enabled: false  #defines if replication should be enabled or not
gerrit_replication_info:
  authgroup: 'Gitlab Replication'  #define the gerrit group to execute replication as..This group needs to be created in gerrit as well. and perms defined as below.
  name: 'gitlab'  #define name to assign to replication definition
  owner: 'infra'  #define username or groupname...if collaborating use groupname
  ssh_config_info:  #defines configuration of ~/.ssh/config....THIS WILL OVERWRITE WHAT IS THERE
    - host: 'gitlab.{{ pri_domain_name }}'
      user: '{{ gerrit_account_info.name }}'
      identityfile: '{{ gerrit_site_dir }}/.ssh/id_rsa'
      stricthostkeychecking: false
  threads: '3'  #defines the number of threads to allocate to replication
  timeout: '30'  #define the replication timeout
  url: 'git@gitlab.{{ pri_domain_name }}'  #define remote url
       ##Gitlab Replication allowed read to refs/* in in Project nameofproject (ex. test-replication)
       ##Gitlab Replication denied read to refs/* in all projects
       ##Group Name: Gitlab Replication
gerrit_service_name: gerrit
gerrit_site_dir: /var/gerrit
gerrit_smtp_server: 'smtp.{{ pri_domain_name }}'
gerrit_sshd_listen_port: 29418
gerrit_vagrant_install: false  #defines if deploying within a Vagrant environment
gerrit_version: 3.1.3
gitweb_cgi_path: /usr/share/gitweb/gitweb.cgi
pri_domain_name: example.org
```

Dependencies
------------

None

Example Playbook
----------------
```
---
- name: Installs Gerrit Code Review
  hosts: gerrit-servers
  sudo: true
  roles:
    - ansible-role-gerrit
  tasks:
```

Notes
-----
Portions of this role have been borrowed from https://github.com/kbrebanov/ansible-gerrit

License
-------

BSD

Author Information
------------------

Donovan Francesco.
- donovan.francesco [at] gmail.com
