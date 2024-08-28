FROM alpine:latest

RUN apk add --no-cache netcat-openbsd

COPY ./ /app/
RUN chmod +x /app/client.sh

WORKDIR /app

ENTRYPOINT ["sh", "/app/client.sh"]
CMD ["tail", "-f", "/dev/null"]
