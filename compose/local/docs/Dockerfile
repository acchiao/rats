# syntax=docker/dockerfile:1
FROM python:3.11.6-slim-bullseye as python


# Python build stage
FROM python as python-build-stage

ENV PYTHONDONTWRITEBYTECODE 1

RUN apt-get update && apt-get install --no-install-recommends --yes \
  # dependencies for building Python packages
  build-essential \
  # psycopg2 dependencies
  libpq-dev \
  # cleaning up unused files
  && apt-get purge --yes --auto-remove --option APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*

# Requirements are installed here to ensure they will be cached.
COPY ./requirements /requirements

# create python dependency wheels
RUN pip wheel --no-cache-dir --wheel-dir /usr/src/app/wheels \
  --requirement /requirements/local.txt \
  --requirement /requirements/production.txt \
  && rm -rf /requirements


# Python 'run' stage
FROM python as python-run-stage

ARG BUILD_ENVIRONMENT
ENV PYTHONUNBUFFERED 1
ENV PYTHONDONTWRITEBYTECODE 1

RUN apt-get update && apt-get install --no-install-recommends --yes \
  # To run the Makefile
  make \
  # psycopg2 dependencies
  libpq-dev \
  # Translations dependencies
  gettext \
  # Uncomment below lines to enable Sphinx output to latex and pdf
  # texlive-latex-recommended \
  # texlive-fonts-recommended \
  # texlive-latex-extra \
  # latexmk \
  # cleaning up unused files
  && apt-get purge --yes --auto-remove --option APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*

# copy python dependency wheels from python-build-stage
COPY --from=python-build-stage /usr/src/app/wheels /wheels

# use wheels to install python dependencies
RUN pip install --no-cache-dir /wheels/* \
  && rm -rf /wheels

COPY ./compose/local/docs/start /start-docs
RUN sed -i 's/\r$//g' /start-docs
RUN chmod +x /start-docs

WORKDIR /docs
