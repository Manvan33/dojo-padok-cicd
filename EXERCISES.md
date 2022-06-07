# Exercises

Do not hesitate to read the [docs](https://docs.gitlab.com/ee/ci/) and to andon a staff member to the rescue if you are blocked at any step !

## 1 - Lint

### Why

Linting is the process of running a program that will analyse code for potential errors.

### What

- Create a `.gitlab-ci.yml` file at the root of your repository
  - It's the file where you will define all of your CI/CD !
  - Add this snippet into the file :
  ```yaml
  variables:
    MY_NAME: prenom-nom # Change it accordingly, please respect the formatting !
  ```
- Use the image `766369807176.dkr.ecr.us-east-1.amazonaws.com/node:10-alpine`
- Add a `lint` stage
- Add a `lint` job :
  - that runs on the `lint` stage
  - with a `script` section that installs the app dependencies and runs the linting process
- Add the `kubernetes` and `rezel` tags to your job (and all subsequent jobs)
  - Can you guess what this means ?
- Commit your code and push it to your main branch, it should run a pipeline with one job in the CI/CD > Pipelines section of your Gitlab repository, go ahead and see it running !
- Watch out for the logs of the job, analyze them if it's failing !

<details>
<summary>Hint 1</summary>

You can define global parameters for all your jobs, that's what you'll want to do for the `image` and `stages` parameters.

</details>

<details>
<summary>Hint 2</summary>

How do you run the linting process ? Look at the README !

</details>

<details>
<summary>Hint 3</summary>

Your job should have a `stage`, a `script` and a `tags` section.

</details>

<details>
<summary>Hint 4</summary>

You don't want to use `npm install` in CI/CD jobs, but rather this [command](https://docs.npmjs.com/cli/v8/commands/npm-ci).

</details>

<details>
<summary>Solution</summary>

```yaml
variables:
  MY_NAME: prenom-nom

image: 766369807176.dkr.ecr.us-east-1.amazonaws.com/node:10-alpine

stages:
  - lint

lint:
  stage: lint
  script:
    - npm ci
    - npm run lint
  tags:
    - kubernetes
    - rezel
```

</details>

### Checks (before you move on)

- [ ] I have a `.gitlab-ci.yml` file at the root of my repo
- [ ] I can run a pipeline with a `lint` job in it
- [ ] I can make the `lint` job successfully pass

## 2 - Test

Now that your code has successfully passed the linting process (great job !), we need to test it !

### Why

Continuously testing your application for potential errors is important in the development process. Rigorous testing can prevent terrible bugs from going into production.

### What

- Add a `test` stage
- Add a `test` job :
  - that runs on the `test` stage
  - with a `script` section that installs the app dependencies and runs the testing process
- Don't forget the tags !
- Commit, push and repair the job if it's failing.
- When the job becomes successful, you can move on.

</details>

<details>
<summary>Hint 1</summary>

How do you run the testing process ? Look at the README !

</details>

<details>
<summary>Hint 2</summary>

Your job should look very similar to the linting job.

</details>

<details>
<summary>Solution</summary>

```yaml
stages:
  - lint
  - test

[...]

test:
  stage: test
  script:
    - npm ci
    - npm test
  tags:
    - kubernetes
    - rezel
```

</details>

### Checks

- [ ] I can run a pipeline with a `test` job in it
- [ ] I can make the `test` job successfully pass

## 3 - End-to-end tests

Now that our unit tests are OK, we need to run the end-to-end tests on our app to ensure that it works in real conditions.

### Why

End-to-end testing is the processus of automating the test of real user case scenarios. An example could be, for a shopping site :

- I can log in to my account
- I can put an item in my shopping cart
- I can purchase the items in my shopping cart

### What

- Add a `test-e2e` stage
- Add a `test-e2e` job :
  - that runs on the `test-e2e` stage
  - with a `script` section that installs the app dependencies and runs the end to end testing process
- Don't forget the tags !
- Commit, push and repair the job if it's failing.

<details>
<summary>Hint 1</summary>

How do you run the end-to-end testing process ? Look at the README !

</details>

<details>
<summary>Hint 2</summary>

Your job should look very similar to the testing job.

</details>

<details>
<summary>Solution</summary>

```yaml
stages:
  - lint
  - test
  - test-e2e

[...]

test-e2e:
  stage: test-e2e
  script:
    - npm ci
    - npm run test:e2e
  tags:
    - kubernetes
    - rezel
```

</details>

### Checks

- [ ] I can run a pipeline with a `test-e2e` job in it
- [ ] I can make the `test-e2e` job successfully pass

**Congratulations, you finished the CI part !** Your app is fully tested and ready to be deployed.

Now, you can take a break, or get going on the CD !

<!-- TODO: add an optional dockerfile creation step -->

## 3.5 - (OPTIONAL) Creating a Dockerfile

_This step is optional, you can directly go to the next step where a Dockerfile is given to you._

The first step towards the CD is to make sure our application is able to run in a Kubernetes cluster. To do so, we need to use Docker, and Dockerize the app.

This step will be less guided as it's optional, but go ahead and read this [documentation](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/) which will help you a lot !

You'll need to have Docker installed on your machine : [installation docs](https://docs.docker.com/get-docker/).

### Checks

- [ ] I can build the Docker image on my machine
- [ ] The Docker image exposes the port 3000
- [ ] I can run it and access the app on `localhost:3000`

## 4 - Build and release

After having fully tested our application, we want to deploy it so that our customers can interact with it. To do this, we will use a few new tools : [Docker](https://www.docker.com/), the [AWS CLI](https://aws.amazon.com/fr/cli/), [Kubernetes](https://kubernetes.io/fr/) and [Helm](https://helm.sh/). Don't worry, you won't have to understand everything to finish this part ! And you won't have to install these tools on your machine.

### Why

We want to run this application in a Kubernetes cluster. This involves two previous steps : building a Docker image of the app and releasing it to a Docker repository.

Building a Docker image of the app will make it runnable on almost any system, which is perfect to package and distribute your application easily.

### What

- BEFORE THAT : change the `stage` variable in your previous jobs to `test`. This will allow them to run in parallel and accelerate the whole process !

- Use your own Dockerfile or use the one given here :

  <details>
  <summary>Dockerfile</summary>

  ```docker
  FROM node:10-alpine

  WORKDIR /dojo-express-app

  COPY package*.json ./

  RUN npm ci --only=production

  COPY . .

  EXPOSE 3000

  CMD ["node", "app/server.js"]
  ```

  </details>

- Add a new `build-and-release` stage
- Add a new `build-and-release` job :

  - That runs on the `build-and-release` stage
  - Using the `docker:20.10.16` image
  - Using the following variables :

  ```yaml
  # These are variables to instruct the GitlabCI job on how to use Docker on the Kubernetes node on which it is running. See https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#docker-in-docker-with-tls-enabled-in-kubernetes
  variables:
    DOCKER_HOST: tcp://docker:2376
    DOCKER_TLS_CERTDIR: '/certs'
    DOCKER_TLS_VERIFY: 1
    DOCKER_CERT_PATH: '$DOCKER_TLS_CERTDIR/client'
    # This variable is an exception, as it will take the value of the shortened current commit SHA, see https://docs.gitlab.com/ee/ci/variables/predefined_variables.html
    APP_VERSION: ${CI_COMMIT_SHORT_SHA}
  ```

  - Using the the following service :

  ```yaml
  # This allows the CI jobs to access Docker on the node on which it is running.
  services:
    - docker:20.10.16-dind
  ```

- There must be a `before_script` section which installs the AWS CLI with the `apk` CLI (package manager for the Alpine Linux distribution)
- The script should:

  - Build the Docker image and tag it as `dojo-express-app:${MY_NAME}.{APP_VERSION}`
  - Tag the image with `766369807176.dkr.ecr.us-east-1.amazonaws.com/dojo-express-app:${MY_NAME}.${APP_VERSION}`
  - Log in to the private AWS Docker repository by running the command `aws ecr get-login-password | docker login --username AWS --password-stdin 766369807176.dkr.ecr.us-east-1.amazonaws.com`
  - Push the newly tagged image

- Do not forget the tags !

This step is a bit more complicated than what we just saw, do not hesitate to _andon_ !

<details>
<summary>Hint 1</summary>

Use the `apk` command to install the AWS CLI : `apk update && apk add --no-cache aws-cli`

</details>

<details>
<summary>Hint 2</summary>

Building an image is done by using the `docker build` command, and using the `-t` argument to tag it.

</details>

<details>
<summary>Hint 3</summary>

Tagging an existing image is done by using the `docker tag` command.

</details>

<details>
<summary>Hint 4</summary>

Pushing the image into the registry is done by using the `docker push` command.

</details>

<details>
<summary>Solution</summary>

```yaml
stages:
  - lint
  - test
  - test-e2e
  - build-and-release

[...]

build-and-release:
  stage: build-and-release
  image: docker:20.10.16
  services:
    - docker:20.10.16-dind
  variables:
    DOCKER_HOST: tcp://docker:2376
    DOCKER_TLS_CERTDIR: '/certs'
    DOCKER_TLS_VERIFY: 1
    DOCKER_CERT_PATH: '$DOCKER_TLS_CERTDIR/client'
    APP_VERSION: ${CI_COMMIT_SHORT_SHA}
  before_script:
    - apk update && apk add --no-cache aws-cli
  script:
    - docker build . -t dojo-express-app:${MY_NAME}.${APP_VERSION}
    - docker tag dojo-express-app:${MY_NAME}.${APP_VERSION} 766369807176.dkr.ecr.us-east-1.amazonaws.com/dojo-express-app:${MY_NAME}.${APP_VERSION}
    - aws ecr get-login-password | docker login --username AWS --password-stdin 766369807176.dkr.ecr.us-east-1.amazonaws.com
    - docker push 766369807176.dkr.ecr.us-east-1.amazonaws.com/dojo-express-app:${MY_NAME}.${APP_VERSION}
  tags:
    - kubernetes
    - rezel
```

</details>

### Checks

- [ ] I can run a pipeline with a `build-and-release` job in it
- [ ] I can make the `build-and-release` job successfully pass
- [ ] I can see my image in the private Docker repository (ask a staff member)

## 5 - Deploy

Congratulations, you just finished the hardest step of this dojo ! Now let's finally deploy our app in the cluster.

### Why

We want our users to access our app, so we need to deploy it.

### What

For this step, we will use [Helm](https://helm.sh/), which is a Kubernetes application packaging tool. It uses templating to render Kubernetes resources from values that you can customize. Go and see the [/helm/dojo-express-app/](/helm/dojo-express-app/) directory to see what it is. Don't hesitate to ask questions to a staff member if something is unclear.

- Modify the `myName` value in the [/helm/dojo-express-app/values.yaml](/helm/dojo-express-app/values.yaml) to `prenom-nom`
- Add a new `deploy` stage
- Add a new `deploy` job :

  - Using the `deploy` stage
  - Using the `alpine/helm:3.9.0` image
  - With the same `APP_VERSION` variable used earlier

- The script section of the job should :
  - Use the `helm upgrade` command ([docs](https://helm.sh/docs/helm/helm_upgrade/))
  - With RELEASE being `dojo-express-app` and CHART being `helm/dojo-express-app`
  - With the `--install`, `--create-namespace=true` arguments
  - With the `--namespace` argument with the MY_NAME variable
  - It must override the `imageTag` value, which is in [/helm/dojo-express-app/values.yaml](/helm/dojo-express-app/values.yaml) with the `APP_VERSION` variable using the `--set` argument

- Do not forget the tags !

<details>
<summary>Hint 1</summary>

No hint ! This should be pretty straightforward.

</details>

<details>
<summary>Solution</summary>

```yaml
stages:
  - lint
  - test
  - test-e2e
  - build-and-release
  - deploy

[...]

deploy:
  stage: deploy
  image: alpine/helm:3.9.0
  variables:
    APP_VERSION: ${CI_COMMIT_SHORT_SHA}
  script:
    - helm upgrade --install dojo-express-app helm/dojo-express-app -n ${MY_NAME} --create-namespace=true --set imageTag=${APP_VERSION}
  tags:
    - kubernetes
    - rezel
```

</details>

### Checks

- [ ] I can run a pipeline with a `deploy` job in it
- [ ] I can make the `deploy` job successfully pass
- [ ] I can see my application in the MY_NAME namespace running in the cluster (ask a staff member)
- [ ] I can access my app on the http://MY_NAME.dojo-rezel.aws.padok.cloud domain

Congratulations ! You finished the dojo ! 🎉 👏

You can ask a staff member to review your code if you want and ask any questions. You can keep on going if you want with these bonus steps, or help your colleagues if they are struggling !

## (BONUS) 6 - Going further

If you made it this far, congratulations again ! Here are some ideas of stuff you can do to explore more aspects of the GitLabCI :

- Make some modifications in the code, and redeploy your app to see them live !

- In the [/package.json](/package.json), there are some commands that runs `prettier` checks and coverage tests, add them to your CI/CD !

- In all your CI jobs, you need to install the dependencies which (three times !), find a solution to make your pipeline install the dependencies only once and make your tests use them (TIP : use artifacts).
