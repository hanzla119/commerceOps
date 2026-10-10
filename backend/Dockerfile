FROM node:20-alpine AS builder

RUN apk add --no-cache libc6-compat python3 make g++

WORKDIR /app

RUN corepack enable

COPY .yarn ./.yarn
COPY .yarnrc.yml package.json yarn.lock ./

RUN yarn install

COPY . .

RUN yarn build

FROM node:20-alpine AS runner

WORKDIR /app

ENV NODE_ENV=production
ENV PORT=9000

RUN apk add --no-cache libc6-compat curl

RUN addgroup -g 1001 -S nodejs && \
    adduser -S medusa -u 1001 -G nodejs

COPY --chown=medusa:nodejs --from=builder /app/package.json ./
COPY --chown=medusa:nodejs --from=builder /app/tsconfig.json ./
COPY --chown=medusa:nodejs --from=builder /app/node_modules ./node_modules
COPY --chown=medusa:nodejs --from=builder /app/.medusa ./.medusa
COPY --chown=medusa:nodejs --from=builder /app/.medusa/server/public ./public
COPY --chown=medusa:nodejs --from=builder /app/medusa-config.ts ./medusa-config.ts
COPY --chown=medusa:nodejs --from=builder /app/src ./src

USER medusa

EXPOSE 9000

CMD ["yarn", "start"]
