# Laravel with Gitpod <!-- omit in toc -->

- [Setups](#setups)
  - [PHP serve and Gitpod](#php-serve-and-gitpod)

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
