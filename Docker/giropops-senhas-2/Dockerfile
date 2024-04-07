FROM python:3.9.19-alpine3.19

WORKDIR /app

COPY . .
RUN python -m venv /app/venv
RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 5000

CMD flask run --host=0.0.0.0

