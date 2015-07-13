## CKAN Ansible Playbook

This is an ansible playbook for building and minimally deploying [CKAN](http://ckan.org/) on Ubuntu 14.04 host. This playbook tries as much as possible to follow [the recommended way of installing CKAN from source](http://docs.ckan.org/en/latest/maintaining/installing/install-from-source.html) while adhering to [Ansible best practices](http://docs.ansible.com/playbooks_best_practices.html).
After successfully running this playbook, there are some additional tasks which must be run manually such as initializing databases, copying site theme, content e.t.c.

By default, this playbook assumes that you're installing CKAN & its dependencies(solr, postgresql, nginx & apache) on one host. And you'll be using apache web server primarily for python WSGI support via nginx web server on the front(reverse proxy) i.e. port 80 as recommended by CKAN docs.

### Ansible Roles
This playbook is broken down the following several parts/roles, each doing a minimal & specific tasks:
- `apache` role: Installs & configures apache2. Apache provides [modwsgi](https://code.google.com/p/modwsgi/) which allows us to run python applications with WSGI interface. This role also configures apache2 to listen & serve requests on port `8080` since we'll have nginx on the front( port `80`).
- `nginx` role: Installs & configures nginx web server. Additional configuration parameters for proxying, X-* security headers, gzip compression, caching, e.t.c. are also deployed on the remote server.
- `postgresql` role: This role installs & configures postgresql-9.3 server. This role doesn't create/manager postgresql users & or databases, that is done in the ckan role.
- `solr` role: Installs & configures solr-5.2.x. By default, this role creates one solr core - `ckan_default` used by CKAN. Though CKAN doesn't insist/recommend using a specific version of solr, I decided to use the latest version - solr 5.2.1 - and it works fine on test environments.
- `ckan` role: This role builds & installs CKAN from sources using pip. It creates a ckan user who owns all CKAN related files & directories, creates postgresql user & database used by CKAN, configures apache2 vhost for WSGI support & finally creates nginx vhost for proxy passing requests to apache2 WSGI processes.

> **NOTE**<br/>
> `apache`, `nginx` & `ckan` roles can all be grouped in one group – _ckan_ – & usually deployed on one host in production & test environments whereas `postgresql` & `solr` roles are in separate groups – _db_ & _solr_ respectively – and usually deployed on separate hosts too.
