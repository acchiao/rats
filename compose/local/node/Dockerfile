# syntax=docker/dockerfile:1
FROM node:21-bullseye-slim

ARG APP_HOME=/app
WORKDIR ${APP_HOME}

COPY ./package.json ./package-lock.json ${APP_HOME}/

RUN npm install \
  && npm cache clean --force

ENV PATH ./node_modules/.bin/:$PATH
