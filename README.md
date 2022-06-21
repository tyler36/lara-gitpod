# Laravel with Gitpod <!-- omit in toc -->

- [Setups](#setups)
  - [PHP serve and Gitpod](#php-serve-and-gitpod)
  - [DDEV and Gitpod](#ddev-and-gitpod)

## Setups

### PHP serve and Gitpod

- `.gitpod.yml` to serve Laravel via Laravel serve

    ```yml
    # .gitpod.yml
    ports:
      # Laravel server
      - port: 8000
        onOpen: open-preview

    tasks:
      - init: >
          cp .env.example .env &&
          sed -i "s|APP_URL=|APP_URL=${GITPOD_WORKSPACE_URL}|g" .env &&
          sed -i "s|APP_URL=https://|APP_URL=https://8000-|g" .env &&
          composer install &&
          php artisan key:generate
        command: >
          php -S 0.0.0.0:8000 -t public
    ```

### DDEV and Gitpod

- Create a new DDEV config

    ```shell
    ddev config
    ```

- `.gitpod.yml` to serve Laravel via DDEV

    ```yml
    # .gitpod.yml
    image: drupalpod/drupalpod-gitpod-base:20220322

    ports:
      # Used by ddev - local db clients
      - port: 3306
        onOpen: ignore
      # Used by MailHog
      - port: 8026
        onOpen: ignore
      # Used by phpMyAdmin
      - port: 8037
        onOpen: ignore
      # Direct-connect ddev-webserver port that is the main port
      - port: 8080
        onOpen: ignore
      # Ignore host https port
      - port: 8443
        onOpen: ignore
      # xdebug port
      - port: 9003
        onOpen: ignore

    tasks:
      - init: |
          cp .env.example .env
          sed -i "s|APP_URL=|APP_URL=${GITPOD_WORKSPACE_URL}|g" .env
          sed -i "s|APP_URL=https://|APP_URL=https://8000-|g" .env
          ddev start -y
          ddev composer install
          ddev exec "cat .env | sed  -E 's/DB_(HOST|DATABASE|USERNAME|PASSWORD)=(.*)/DB_\1=db/g' > .env"
          ddev artisan key:generate
        command: |
          ddev start -y
          gp await-port 8080 && gp preview $(gp url 8080)

    github:
      prebuilds:
        # enable for the master/default branch (defaults to true)
        master: true
        # enable for all branches in this repo (defaults to false)
        # branches: true
        # enable for pull requests coming from this repo (defaults to true)
        pullRequests: true
        # enable for pull requests coming from forks (defaults to false)
        # pullRequestsFromForks: false
        # add a check to pull requests (defaults to true)
        addCheck: true
        # add a "Review in Gitpod" button as a comment to pull requests (defaults to false)
        addComment: false
        # add a "Review in Gitpod" button to the pull request's description (defaults to false)
        addBadge: true
        # add a label once the prebuild is ready to pull requests (defaults to false)
        addLabel: true
    ```
