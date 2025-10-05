ARG build_for=linux/amd64

# Please do not upgrade beyond python3.10.7 currently AS dbt-spark does not support
FROM --platform=$build_for python:3.10.7-slim-bullseye AS base

ARG dbt_core_version=1.10.6
ARG dbt_spark_version=1.9.3
ARG dbt_bigquery_version=1.10.0
ARG dbt_snowflake_version=1.10.0
ARG dbt_redshift_version=1.9.5
ARG dbt_databricks_version=1.10.6
ARG dbt_postgres_version=1.9.1

# special case args
ARG dbt_third_party

# System setup
RUN apt-get update \
  && apt-get dist-upgrade -y \
  && apt-get install -y --no-install-recommends \
    git \
    ssh-client \
    software-properties-common \
    make \
    build-essential \
    ca-certificates \
    libpq-dev \
  && apt-get clean \
  && rm -rf \
    /var/lib/apt/lists/* \
    /tmp/* \
    /var/tmp/*

ENV PYTHONIOENCODING=utf-8
ENV LANG=C.UTF-8

RUN python -m pip install --upgrade pip setuptools wheel --no-cache-dir

WORKDIR /usr/app/dbt/
ENTRYPOINT ["dbt"]

##
# dbt-core
##
FROM base AS dbt-core
RUN python -m pip install --no-cache-dir "dbt-core==${dbt_core_version}"

##
# dbt-postgres
##
FROM base AS dbt-postgres
RUN python -m pip install --no-cache-dir "dbt-postgres==${dbt_postgres_version}"


##
# dbt-redshift
##
FROM base AS dbt-redshift
RUN python -m pip install --no-cache-dir "dbt-redshift==${dbt_redshift_version}"


##
# dbt-bigquery
##
FROM base AS dbt-bigquery
RUN python -m pip install --no-cache-dir "dbt-bigquery==${dbt_bigquery_version}"


##
# dbt-snowflake
##
FROM base AS dbt-snowflake
RUN python -m pip install --no-cache-dir "dbt-snowflake==${dbt_snowflake_version}"

##
# dbt-spark
##
FROM base AS dbt-spark
RUN apt-get update \
  && apt-get dist-upgrade -y \
  && apt-get install -y --no-install-recommends \
    python-dev \
    libsasl2-dev \
    gcc \
    unixodbc-dev \
  && apt-get clean \
  && rm -rf \
    /var/lib/apt/lists/* \
    /tmp/* \
    /var/tmp/*
RUN python -m pip install --no-cache-dir "dbt-spark==${dbt_spark_version}"


##
# dbt-databricks
##
FROM base AS dbt-databricks
RUN python -m pip install --no-cache-dir "dbt-databricks==${dbt_databricks_version}"

##
# dbt-third-party
##
FROM dbt-core AS dbt-third-party
RUN python -m pip install --no-cache-dir "${dbt_third_party}"

##
# dbt-all
##
FROM base AS dbt-all
RUN apt-get update \
  && apt-get dist-upgrade -y \
  && apt-get install -y --no-install-recommends \
    python-dev \
    libsasl2-dev \
    gcc \
    unixodbc-dev \
  && apt-get clean \
  && rm -rf \
    /var/lib/apt/lists/* \
    /tmp/* \
    /var/tmp/*
  RUN python -m pip install --no-cache-dir "dbt-redshift==${dbt_redshift_version}"
  RUN python -m pip install --no-cache-dir "dbt-bigquery==${dbt_bigquery_version}"
  RUN python -m pip install --no-cache-dir "dbt-snowflake==${dbt_snowflake_version}"
  RUN python -m pip install --no-cache-dir "dbt-databricks==${dbt_databricks_version}"
  RUN python -m pip install --no-cache-dir "dbt-spark==${dbt_spark_version}"
  RUN python -m pip install --no-cache-dir "dbt-postgres==${dbt_postgres_version}"
