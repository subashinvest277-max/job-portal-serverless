FROM python:3.11-slim

WORKDIR /app

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    gcc \
    default-libmysqlclient-dev \
    libpq-dev \
    pkg-config \
    curl \
    gnupg \
    findutils && \
    rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt-get install -y nodejs

COPY requirement.txt .

RUN pip install --upgrade pip
RUN pip install -r requirement.txt
RUN pip install gunicorn reportlab razorpay

COPY . /app

WORKDIR /app/frontend

RUN npm install
RUN npm run build

RUN npm install -g serve

RUN mkdir -p /app/static_frontend

RUN cp -r /app/frontend/dist/* /app/static_frontend/

WORKDIR /app

EXPOSE 8000
EXPOSE 3000

RUN echo '#!/bin/bash\n\
gunicorn --bind 0.0.0.0:8000 jobportal.wsgi:application &\n\
serve -s /app/static_frontend -l 3000 &\n\
wait' > /app/start.sh && chmod +x /app/start.sh

CMD ["/app/start.sh"]
