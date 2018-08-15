docker_webservices
=========

[PROVISIONAL] `docker_webservices` is a role that automates the deployment of Dockerized Web Apps (or Web Services) and the generation of SSL Certificates with Caddy.

Requirements
------------

Ansible 2.4 (because of `geerlingguy.docker` role)

`docker` Python library (needed by `docker_container` and `docker_image` modules). [Example installation](https://github.com/geerlingguy/ansible-role-docker#use-with-ansible-and-docker-python-library).


Role Variables
--------------

`docker_webservices` accepts an array of objects as variable: `docker_web_services`. The variable has the following structure:

```yaml
docker_web_services:
  - name: service_name
    build:
      name: ""
      repo: ""
      context_path: ""
      dockerfile: ""
    caddy:
      host: ""
      port: ""
      mail: ""
    container_opts:
      env: {}
```

- `name`: `String` Name of the service. Used as part of the container name and as fallback when building an image.
- `build`: `Optional` `Object` Provide this to instruct `docker_webservices` to perform an image build (Fetching a repo containing the Dockerfile)
  * `name`: `Optional` `String` The name of the image to build. Falls back to the service `name` declared above
  * `repo`: `String` Repo from wich a clone / checkout is performed (Your Dockerfile is in here)
  * `context_path`: `String` Path (relative to rebo base) to use as context for docker build
  * `dockerfile`: `String` Path (relative to rebo base) pointing to the Dockerfile to use for docker build
- `caddy`: `Object` Configuration related to `caddy-gen`
  * `host`: `String` Space separated hostnames that Caddy should generate certs for and proxy requests for to this container (i.e. "my.host.com my1.host.com")
  * `port`: `String` The port Caddy should proxy requests to
  * `mail`: `String` Must be a valid email in order to enable TLS
- `container_ops`: `Object` The plan is to accept arbitrary instructions for the `docker_container` module here. Currently accepts only `env`.
  * `env`: `Object` Key - Value pairs that will be pass as-is to `docker_container`'s `env`.


Dependencies
------------

- geerlingguy.pip
- geerlingguy.docker
- panagiks.caddy_gen


Example Playbook
----------------


```yaml
- hosts: servers
  vars:
    docker_web_services:
      - name: my_service
        build:
          name: "image_name"
          repo: https://github.com/handle/repo
          context_path: path/
          dockerfile: path/service.dockerfile
        caddy:
          host: "my.host.com my1.host.com"
          port: "1234"
          mail: "a_mail@provicer.sth"
        container_opts:
          env:
            ENV_VAR_NAME: ENV_VAR_VAL
  roles:
    - { role: panagiks.docker_webservices }
```

License
-------

MIT
