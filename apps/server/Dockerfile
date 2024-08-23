FROM node:20.5.0-alpine3.18

WORKDIR /usr/src/apps
EXPOSE 3000
COPY apps/server .

ENV DOCKERIZE_VERSION=v0.6.1
RUN apk update --no-cache \
    && apk add --no-cache wget \
    && wget -O /usr/local/bin/dumb-init https://github.com/Yelp/dumb-init/releases/download/v1.2.5/dumb-init_1.2.5_x86_64\
    && chmod +x /usr/local/bin/dumb-init \
    && wget -O - https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz | tar xzf - -C /usr/local/bin \
    && apk del wget

RUN npm install -g pnpm \
&& pnpm install \
&& pnpm run build 

ENV NODE_ENV=production
ENV NEW_RELIC_DISTRIBUTED_TRACING_ENABLED=true \
    NEW_RELIC_LOG=stdout \
    NEW_RELIC_NO_CONFIG_FILE=true


ENTRYPOINT ["dumb-init", "--"]
CMD ["sh", "-c", "dockerize -wait tcp://$DB_HOST:$DB_PORT && npx sequelize db:migrate && node dist/server.js"]