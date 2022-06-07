# Dojo CI / CD Rezel

Welcome to this CI/CD Dojo! Your goal is to setup a complete CI/CD pipeline for a simple web application, and learn the maximum during this session.

That's why it is essential that:

- You experiment and try things that may break. Don't simply copy paste everything from StackOverflow until it passes the test !

- But also keep focus on your goal: have a working application, piece by piece. Don't block at the start for a detail.

- To keep moving or learn more, please _andon_! It means then whenever you have a question, a doubt, a feedback, call someone from the staff, we'll love to help you.

Here is the link to the [presentation](https://docs.google.com/presentation/d/1tgaKprcIsQu3uvg0TnJ6cbMpC9tycFUBqhAS2uD_h2k/edit?usp=sharing).

## The app

This project is a simple Node.js + express app for demonstrating testing and code coverage.
[Jest](https://facebook.github.io/jest/) and
[Supertest](https://github.com/visionmedia/supertest) are used for testing.
Jest is also used for mocking functions and measuring code coverage.
Note that this app only focuses on server-side JavaScript testing.

## Prerequisites

- Git ([how to install](https://www.atlassian.com/git/tutorials/install-git))
- A [Gitlab account](https://gitlab.com/)

  - It must be linked to an ssh key you own
  - [Create an ssh key](https://docs.gitlab.com/ee/user/ssh.html#generate-an-ssh-key-pair)
  - [Add the key to your Gitlab account](https://docs.gitlab.com/ee/user/ssh.html#add-an-ssh-key-to-your-gitlab-account)

- Node.js - [https://nodejs.org/](https://nodejs.org/)
- Alternative : NVM (Node Version Manager, permet de facilement avoir la bonne version de Node) - [https://github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm)

## Getting Started

- Ask a staff member to add you to the dojo's [Gitlab Group](https://gitlab.com/padok-team/dojo/dojo-ci-cd-rezel) as a Maintainer
- Create a new blank Gitlab project into the group with the name `prenom-nom`
  - Uncheck the README creation
- Clone your created repository and the [template](https://gitlab.com/padok-team/dojo/dojo-ci-cd-rezel/template) repository
- Copy all the content of the `template` repository into your newly created repository

  - Do not copy the `.git` directory ! It should already be there when you cloned the repository.

- Your directory should look like :
  ```
  ├── app
  ├── e2e_tests
  ├── .eslintrc
  ├── EXERCISES.md
  ├── .git
  ├── .gitignore
  ├── helm
  ├── package.json
  ├── package-lock.json
  └── README.md
  ```
- Run these commands at the root directory of your new repository :

```bash
# TESTER CES COMMANDES
git switch -c main
git add .
git commit -m "Initial commit"
git push -u origin main
```

## Local environment

### Running the app

- Install dependencies with `npm install`
- Run server with `npm start` and go here:
  [http://localhost:3000/](http://localhost:3000/), it should respond with a HTTP 200 and an "OK" text
- Going into [http://localhost:3000/app](http://localhost:3000/app) will show you an app where you can search for pictures using the Flickr API

### Running tests

- Run linter : `npm run lint`
- Run unit and integration tests: `npm test`
- Run end-to-end tests: `npm run test:e2e`

If you see some errors, it's normal ;)

### Exercises

[Go here !](/EXERCISES.md)
