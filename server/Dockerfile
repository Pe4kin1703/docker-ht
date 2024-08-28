FROM alpine:latest

RUN apk add --no-cache netcat-openbsd

COPY ./ /app/
RUN chmod +x /app/server.sh

WORKDIR /app

CMD ["sh", "/app/server.sh"]