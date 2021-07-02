FROM python:3.8-slim-buster as base

RUN apt-get update \
  && apt-get install -y --no-install-recommends libpq-dev curl \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*
  # You can add additional steps to the build by appending commands down here using the
  # format `&& <command>`. Remember to add a `\` at the end of LOC 12.
  # WARNING: Changes to this file may cause unexpected behaviors when building the app.
  # Change it at your own risk.

WORKDIR /opt/webapp

COPY Pipfil* ./
RUN pip3 install --no-cache-dir cookiecutter -q 'pipenv==2018.11.26' \
    && pipenv install --deploy --system

FROM base as release

WORKDIR /opt/webapp

# Run the image as a non-root user
RUN adduser --disabled-password --gecos "" django
USER django

# Run the web server on port $PORT
# (Done early to avoid running it on every code change)
CMD waitress-serve --port=$PORT d20210629_1_dev_22671.wsgi:application

COPY . .
COPY --from=base /usr/local/lib/python3.8/site-packages/ /usr/local/lib/python3.8/site-packages/
COPY --from=base /usr/local/bin/ /usr/local/bin/

RUN python3 manage.py collectstatic --no-input
