# Handle permissions on linux

By default, the target `frankenphp_dev` of the `php` service runs with the user root.
Après l'installation, l'ensemble des fichiers vont appartenir à root, ce qui n'est pas très pratique sous linux.

Pour corriger et lancer le service `php` en tant que user `nonroot` avec le même user que l'utilisateur sur l'hôte, 
corriger d'abord les droits sur l'ensemble des fichiers.

```
docker compose down -v
chown -R $(id -u):$(id -g) .
```

Puis builder le service `php` avec l'UID / GID de l'utilisateur connecté sur l'hôte. Trois solutions au choix : 

**A chaque build du service**

```
USER_UID=$(id -u) USER_GID=$(id -g) docker compose build php
```

**En fixant les UID / GID dans le fichier compose.override.yaml**

```diff
args:
-    USER_UID: ${USER_UID:-0}
-    USER_GID: ${USER_GID:-0}
+    USER_UID: ${USER_UID:-1000}
+    USER_GID: ${USER_GID:-1000}
```

*On peut envisager de versionner le fichier compose.override.yaml en tant que compose.override.yaml.dist pour permettre
la personnalisation de l'UID / GID de chaque développeur.*

Puis `docker compose build php`

**En ajoutant USER_UID / USER_GID** au fichier .env**

```

```

## Editing Permissions on Linux

If you work on Linux and cannot edit some of the project files right after
the first installation, you can run the following command
to set yourself as owner of the project files that were created by the Docker container:

```console
docker compose run --rm php chown -R $(id -u):$(id -g) .
```
