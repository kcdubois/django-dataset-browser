FROM python:3.8-slim-buster as builder

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

WORKDIR /wheels

RUN apt-get update && apt-get install -y \
    build-essential gcc python3-dev musl-dev \
    postgresql-server-dev-all libintl-perl gettext

RUN pip install pipenv
COPY Pipfile Pipfile.lock ./
RUN pipenv lock -r > requirements.txt

RUN pip wheel -r requirements.txt


FROM python:3.8-slim-buster

COPY --from=builder /wheels /wheels
RUN pip install --no-index -r /wheels/requirements.txt -f /wheels

RUN rm -rf /wheels

COPY entrypoint.py /usr/bin/
WORKDIR /app
COPY app .

ENTRYPOINT ["entrypoint.py"]
CMD ["gunicorn", "-b 0.0.0.0:8000", "-w 3", "app.wsgi"]