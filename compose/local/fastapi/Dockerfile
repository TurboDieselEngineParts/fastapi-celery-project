FROM python:3.11-slim-bookworm

ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

RUN apt-get update \
  && apt-get install -y build-essential libpq-dev telnet netcat-openbsd \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /app

# Copy only files needed for installing dependencies -- helps cache pip layer
COPY ./pyproject.toml /app/
# If you have a `uv.lock` file, copy it as well:
# COPY ./uv.lock /app/

# Install dependencies (if you have a lock file, use it)
RUN uv pip install --system -r pyproject.toml
# or, with lockfile:
# RUN uv pip install -r uv.lock

# Copy the rest of your app files/scripts
COPY ./compose/local/fastapi/entrypoint.sh /entrypoint.sh
RUN sed -i 's/\r$//g' /entrypoint.sh && chmod +x /entrypoint.sh

COPY ./compose/local/fastapi/start.sh /start.sh
RUN sed -i 's/\r$//g' /start.sh && chmod +x /start.sh

COPY ./compose/local/fastapi/celery/worker/start.sh /start-celeryworker
RUN sed -i 's/\r$//g' /start-celeryworker && chmod +x /start-celeryworker

COPY ./compose/local/fastapi/celery/beat/start.sh /start-celerybeat
RUN sed -i 's/\r$//g' /start-celerybeat && chmod +x /start-celerybeat

COPY ./compose/local/fastapi/celery/flower/start.sh /start-flower
RUN sed -i 's/\r$//g' /start-flower && chmod +x /start-flower

ENTRYPOINT ["/entrypoint.sh"]
