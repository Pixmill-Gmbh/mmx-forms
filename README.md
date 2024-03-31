Develop Forms for MODX 3
---

> This extra is part of **MMX** initiative - the **M**odern **M**OD**X** approach.

### Nota Bene!

This repository is only for developers! It does not contain [mmxForms][mmx-forms] itself, 
only developer container and scripts.

This is why you need to clone this repository with `--recurse-submodules` command:
```sh
git clone --recurse-submodules https://github.com/Pixmill-Gmbh/mmx-forms.git ./mmxForms
cd ./mmxForms
```

### Run Docker containers

Create `.env` file and start containers:
```sh
cp .env.dist .env
docker compose up --build -d
```

### Install MODX

MODX installation is fully automatized with script `./run-install.sh`, which will exec a number of commands in
PHP-FPM container. 

It was tested only in MacOS, so if you use Windows, please read source code and try to run this commands manually with 
Docker Desktop interface.

After successful installation you can log in to manager `http://127.0.0.1:8080/manager`:
- username `admin` 
- password `adminpassword`

### The logic of work

- Main source code of mmxForms located in the `mmx-forms` submodule.
- This directory is mounted under the root of PHP Docker container as `/mmx-forms`.
- This path specified as custom repository in MODX `composer.json`:
```json
"repositories": {
    "mmx-forms": {
        "type": "path",
        "url": "/mmx-forms"
    }
}
```
- When we run `composer require mmx-forms` it is symlinked from local directory to `/modx/core/vendor/mmx-forms`.
- When we run `composer exec mmx-forms install` it make 2 more symlinks:
  - `/modx/core/vendor/mmx-forms/core` to `/modx/core/components/mmx-forms`
  - `/modx/core/vendor/mmx-forms/assets/dist` to `/modx/assets/components/mmx-forms`

When you change something in the `mmx-forms` subdirectory, it is immediately reflected on the MODX website, without 
any additional actions.

Enjoy the developing process!

### Develop frontend

mmxForms extra is located in `/manager/?a=home&namespace=mmx-forms#/` and uses built files from `mmx-forms/assets/dist`
by default.

You can use Vite dev-server with HMR by deleting `mmx-forms/assets/dist/manifest.json` and reloading the page.
This way mmxForms plugin will connect to `node` container and port `9090`.

### Build frontend

When you are done making changes, you will need to build frontend using a script `./run-build.sh`.

It will generate new `manifest.json`.

### PhpStorm Xdebug

Go to **PHP** -> **Servers** section:

- `name` = ModxDocker
- `host` = 127.0.0.1
- `port` = 8080
- `debugger` = Xdebug
- Check `Use path mapping`
  - /Users/.../path/to/mmxForms/mmx-forms -> `/mmx-forms`
  - /Users/.../path/to/mmxForms/modx -> `/modx`

Now you can use `Start Listening for PHP Debug Connections` button.

### Emails

You can debug emails with [mailhog container][MailHog]. You need to change MODX system settings:
- `mail_use_smtp` = yes
- `mail_smtp_hosts` = mailhog
- `mail_smtp_port` = 1025

Then you can use the MailHog interface at `http://127.0.0.1:8090`. 

[mmx-forms]: https://github.com/bezumkin/mmx-forms
[mailhog]: https://github.com/mailhog/MailHog