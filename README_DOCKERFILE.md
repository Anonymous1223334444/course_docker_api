` FROM python:3.9-alpine3.13 `
## Cette ligne spécifie l'image de base à partir de laquelle construire l'image Docker. Ici, l'image est basée sur Python 3.9 avec Alpine Linux 3.13, qui est une version légère de Linux.

` WORKDIR /app `
## Définit le répertoire de travail dans le conteneur. Toutes les instructions suivantes seront exécutées dans ce répertoire, ce qui est ici /app.

` LABEL maintainer="someone.com" `
## Ajoute un label au conteneur, ici spécifiant l'adresse e-mail du mainteneur de l'image Docker. C'est une bonne pratique pour la documentation et la gestion des images.

` ENV PYTHONUNBUFFERED 1 `
## Définit une variable d'environnement PYTHONUNBUFFERED à 1. Cela configure Python pour qu'il n'utilise pas le buffering pour les flux de sortie, ce qui est souvent utile pour les journaux en temps réel.

` COPY ./requirements.txt /tmp/requirements.txt `
` COPY ./requirements.dev.txt /tmp/requirements.dev.txt `
` COPY ./app /app `
## Copie les fichiers requirements.txt et requirements.dev.txt de ton répertoire local vers le répertoire /tmp dans le conteneur.
## Copie le répertoire app de ton répertoire local vers le répertoire /app dans le conteneur.

` ARG DEV=false `
## Définit un argument de construction DEV avec une valeur par défaut false. Cet argument peut être utilisé pour activer ou désactiver certaines fonctionnalités lors de la construction de l'image.

` RUN python -m venv /py && \ `
    ` /py/bin/pip install --upgrade pip && \ `
    ` apk add --update --no-cache postgresql-client && \ `
    ` apk add --update --no-cache --virtual .tmp-build-deps \ `
    `     build-base postgresql-dev musl-dev && \ `
    ` /py/bin/pip install -r /tmp/requirements.txt && \ `
    ` if [ $DEV = "true" ]; \ `
    `     then /py/bin/pip install -r /tmp/requirements.dev.txt; \ `
    ` fi && \ `
    ` rm -rf /tmp && \ `
    ` apk del .tmp-build-deps && \ `
    ` adduser \ `
    `    --disabled-password \ `
    `    --no-create-home \ `
    `    django-user `
## python -m venv /py: Crée un environnement virtuel Python dans le répertoire /py.
## /py/bin/pip install --upgrade pip: Met à jour pip à la dernière version dans l'environnement virtuel.
## apk add --update --no-cache postgresql-client: Installe le client PostgreSQL sur l'image, nécessaire pour interagir avec des bases de données PostgreSQL.
## apk add --update --no-cache --virtual .tmp-build-deps build-base postgresql-dev musl-dev: Installe les paquets nécessaires pour la construction, tels que build-base (compilateur C), postgresql-dev (bibliothèques de développement pour PostgreSQL), et musl-dev (bibliothèques de développement C pour Alpine). L'option --virtual .tmp-build-deps crée un groupe de dépendances temporaires pour faciliter leur suppression ultérieure.
## /py/bin/pip install -r /tmp/requirements.txt: Installe les dépendances Python spécifiées dans requirements.txt.
## if [ $DEV = "true" ]; then /py/bin/pip install -r /tmp/requirements.dev.txt; fi: Si l'argument DEV est égal à "true", installe les dépendances supplémentaires pour le développement spécifiées dans requirements.dev.txt.
## rm -rf /tmp: Supprime les fichiers temporaires copiés dans /tmp pour réduire la taille de l'image.
## apk del .tmp-build-deps: Supprime les paquets de construction temporaires qui ne sont plus nécessaires.
## adduser --disabled-password --no-create-home django-user: Crée un utilisateur django-user sans mot de passe et sans répertoire personnel, ce qui est une bonne pratique pour la sécurité.

` EXPOSE 8000 `
## Indique que le conteneur écoute sur le port 8000. Cela n'ouvre pas réellement le port mais sert de documentation pour savoir quel port l'application est censée utiliser.

` ENV PATH="/py/bin:$PATH" `
## Ajoute le répertoire des exécutables de l'environnement virtuel (/py/bin) au PATH système, ce qui permet d'exécuter les commandes Python et pip depuis n'importe quel répertoire.

` USER django-user `
## Définit l'utilisateur qui exécutera les commandes suivantes dans le conteneur comme étant django-user. Cela aide à améliorer la sécurité en évitant d'exécuter des processus en tant que root.

## En résumé, ce Dockerfile crée une image légère et sécurisée pour une application Python, en configurant un environnement virtuel, en installant les dépendances nécessaires, et en configurant les paramètres pour le bon fonctionnement de l'application.