FROM python:3.6-buster AS wheels

ARG DEV_BUILD

WORKDIR /home/superset

RUN apt-get update -y
RUN apt-get install -y libsasl2-dev

COPY requirements.txt .
COPY requirements-dev.txt .
COPY contrib/docker/requirements-extra.txt .

RUN pip install --upgrade setuptools pip \
        && pip wheel --wheel-dir=/wheels \
        -r requirements.txt -r requirements-extra.txt

RUN pip wheel --wheel-dir=/wheels -r requirements-dev.txt;


FROM python:3.6-slim-buster

ARG DEV_BUILD

RUN useradd --user-group --create-home --no-log-init --shell /bin/bash superset

# Configure environment
ENV LANG=C.UTF-8 \
        LC_ALL=C.UTF-8

RUN apt-get update -y

# Install git as it is used for requirements from Github
RUN apt-get install -y git
# Install superset dependencies
RUN apt-get install -y libsasl2-dev libsasl2-2 libsasl2-modules-gssapi-mit libmariadb3

RUN apt-get install -y curl && \
        curl -sL https://deb.nodesource.com/setup_10.x | bash - \
        && apt-get install -y nodejs vim less postgresql-client redis-tools;

WORKDIR /home/superset

COPY --from=wheels /wheels /wheels

COPY requirements.txt .
COPY requirements-dev.txt .
COPY contrib/docker/requirements-extra.txt .

RUN pip install --upgrade setuptools pip \
        && pip install --no-index --find-links=/wheels \
        -r requirements.txt -r requirements-extra.txt

RUN pip install --no-index --find-links=/wheels -r requirements-dev.txt;

RUN rm -rf /wheels

ENV PATH=/home/superset/superset/bin:$PATH \
        PYTHONPATH=/home/superset/:$PYTHONPATH \
        SUPERSET_CONFIG_PATH=/home/superset/superset/superset_config.py

USER superset

ENTRYPOINT ["/entrypoint.sh"]

HEALTHCHECK CMD ["curl", "-f", "http://localhost:8088/health"]

EXPOSE 8088
