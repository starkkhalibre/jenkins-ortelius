FROM python:3.12-slim

WORKDIR /app

RUN pip install flask

COPY ./Docker/html /app/html/

COPY ./Docker/app.py /app/app.py

CMD ["python", "app.py"]
