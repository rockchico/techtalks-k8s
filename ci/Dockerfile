ARG NODE_VERSION=18.15.0
FROM node:${NODE_VERSION}-bullseye-slim AS builder
WORKDIR /app
ENV NODE_ENV=production
# Instalar as dependências
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile --silent
# Faz o build do projeto
COPY . .
RUN yarn global add @nestjs/cli
RUN yarn run build

FROM node:${NODE_VERSION}-bullseye-slim AS runner
WORKDIR /app
ENV NODE_ENV=production
RUN apt-get update && apt-get install -y --no-install-recommends dumb-init && rm -rf /var/lib/apt/lists/*
# Não executar o processo como root
RUN addgroup --system --gid 1001 expressjs
RUN adduser --system --uid 1001 expressjs
USER expressjs
COPY --from=builder /app/package*.json /app/
COPY --from=builder /app/yarn.lock /app/
COPY --from=builder /app/dist/ /app/dist/
COPY --from=builder /app/node_modules/ /app/node_modules/

EXPOSE 3000
CMD ["dumb-init", "node", "dist/main.js"]
