# DockerSpawner

Enables [JupyterHub](https://github.com/jupyter/jupyterhub) to spawn
user servers in docker containers.

## Setting up the spawner

Install dependencies:

    pip install -r requirements.txt

Install to the system:

    python setup.py install

### DockerSpawner

For basic, temporary notebook servers, tell JupyterHub to use DockerSpawner by
adding the following to your `jupyterhub_config.py`:

    c.JupyterHub.spawner_class = 'dockerspawner.DockerSpawner'

There is a complete example in [examples/oauth](examples/oauth) for
using GitHub OAuth to authenticate users, and spawn containers with docker.

### SystemUserSpawner

If you want to spawn notebook servers for users that correspond to system users,
you can use the SystemUserSpawner instead. Add the following to your
`jupyterhub_config.py`:

    c.JupyterHub.spawner_class = 'dockerspawner.SystemUserSpawner'

Additionally, you will need to specify what the user ids are for the system
users. For example, if you had users `foo` and `bar`:

    c.SystemUserSpawner.user_ids = dict(
        foo=1001,
        bar=1002
    )

(You can find out what id a user is by running the command `id -u <username>`).

The SystemUserSpawner will also need to know where the user home directories
are on the host. By default, it expects them to be in `/home/<username>`, but if
you want to change this, you'll need to further modify the
`jupyterhub_config.py`. For example, the following will look for a user's home
directory on the host system at `/volumes/user/<username>`:

    c.SystemUserSpawner.host_homedir_format_string = '/volumes/user/{username}'

For a full example of how `SystemUserSpawner` is used, see the
[compmodels-jupyterhub](https://github.com/jhamrick/compmodels-jupyterhub)
repository (this additionally runs the JupyterHub server within a docker
container, and authenticates users using GitHub OAuth).

## Building the docker images

### Single user notebook server

Build the `jupyter/singleuser` container with:

    docker build -t jupyter/singleuser singleuser

or use `docker pull jupyter/singleuser` to download it from [Docker
Hub](https://registry.hub.docker.com/u/jupyter/singleuser/).

### System user notebook server

Or the `jupyter/systemuser` container with:

    docker build -t jupyter/systemuser systemuser

or use `docker pull jupyter/systemuser` to download it from [Docker
Hub](https://registry.hub.docker.com/u/jupyter/systemuser/).

