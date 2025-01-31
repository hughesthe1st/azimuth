# Launching
The app can be launched on different data and models, which are defined in a config file.
Different config files are readily available in the repo, under `config/`.

!!! tldr "TLDR to launch the app locally"
    Assuming you already installed `poetry` and `yarn` (See
    [:material-link: Initial Setup](setup.md)), you can run the app by performing:
    ```bash
    make local_configs  # modify all configs for local usage
    make CFG_PATH={path to config file} launch.local  # Start the backend.
    ```

    `{path to config file}` should be the path in `local_configs`. Ex for clinc dummy: `local_configs/development/clinc_dummy/conf.json`

    In another terminal:
    ```bash
    cd webapp && yarn start # Starts the frontend.
    ```



## Launching locally
When debugging, it is easier to launch the back end and the front end separately, as shown in the next two sections.
However, it is also possible to launch both together using `Docker`, as shown in the third section below.
It will just take longer, and does not allow for fast debugging.

### Back End
* Config files need to be regenerated for local development.
  ```
  make local_configs
  ```
  This will create a new folder `/local_configs` under the project root and will perform the necessary config updates to run locally.
* You can then launch the back end using:
  ```
  make CFG_PATH={path to config file} launch.local
  ```
     - For `{path to config file}`, use the path to the file in `local_configs` rather than `configs`. With `clinc_dummy`, this will be:
  ```
  make CFG_PATH=local_configs/development/clinc_dummy/conf.json launch.local
  ```

    ??? fail "Error in the Back End?"
        If you get an error while launching the back end, common causes can be that the `poetry`
        has new dependencies or the configs were changed. Be sure to run:
        ```bash
        poetry install
        make local_configs
        ```

    ??? fail "If you get `No module named '_lzma'`..."

        You may be missing the `xz` library (https://tukaani.org/xz/).
        That would result in a `ModuleNotFoundError: No module named '_lzma'` when you try to run locally.
        This may be corrected by using homebrew to install it.
        The following instructions are inspired from [those](https://gist.github.com/iandanforth/f3ac42b0963bcbfdf56bb446e9f40a33).
        ```shell
        brew install xz
        pyenv uninstall 3.8.9  # If you already had it - otherwise directly go to the steps
        pyenv install 3.8.9  # Reinstall (now with the lzma lib available)
        pyenv local 3.8.9  # Set this version to always run in this directory
        ```

From this point, the back end will launch and compute the start-up task.

* You can consult the openapi documentation at `localhost:8091/docs`. From there, you can consult the API documentation and try out the different endpoints. This can be useful for debugging.
* Note that the back end will not reload automatically based on code changes.

!!! warning "Cleaning the Cache"
    If you make changes to back-end modules that result in different module responses, you will need to delete the cache, using:
    ```
    make clean
    ```
    As soon as you have an error in the start-up task, a good reflex is to use this command.
    Optionally, you can clean a single project by supplying the project name as an argument.
    ```
    make TARGET=CLINC clean
    ```
    will delete all cache folders beginning with `CLINC`.

### Front End
You can then launch the front end using:
```
cd webapp
yarn        # Only needed when dependencies were updated.
yarn start
```

* The front-end app will launch and automatically connect to the back-end API, assuming you launched it already.
* The front end will hot reload automatically based on code changes.

??? fail "Error in the Front End?"
    If you get an error while launching the front end, make sure that the dependencies were updated using `yarn`.

### Launch Using Docker
* Docker compose will build the images and connect them, using the following command.
  ```
  make CFG_PATH=/config/examples/.../conf.json compose
  ```
  _Note that the path starts with `/config` as we mount `./config:/config`._
