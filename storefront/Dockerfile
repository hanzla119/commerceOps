FROM node:20-alpine AS builder

RUN apk add --no-cache libc6-compat

WORKDIR /app

RUN corepack enable

COPY .yarn ./.yarn
COPY .yarnrc.yml package.json yarn.lock ./

RUN yarn install

COPY . .

ARG NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY=pk_b404919928983245cae4847b1ba20c63585a4b84711e4d3c74e4ba250f01418c
ENV NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY=$NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY
ENV NODE_ENV=production

RUN yarn build

FROM node:20-alpine AS runner

WORKDIR /app

ENV NODE_ENV=production
ENV PORT=8000
ARG NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY=pk_b404919928983245cae4847b1ba20c63585a4b84711e4d3c74e4ba250f01418c
ENV NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY=$NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY

RUN apk add --no-cache libc6-compat curl

RUN addgroup -g 1001 -S nodejs && \
    adduser -S nextjs -u 1001 -G nodejs

COPY --chown=nextjs:nodejs --from=builder /app/package.json ./
COPY --chown=nextjs:nodejs --from=builder /app/node_modules ./node_modules
COPY --chown=nextjs:nodejs --from=builder /app/.next ./.next
COPY --chown=nextjs:nodejs --from=builder /app/public ./public
COPY --chown=nextjs:nodejs --from=builder /app/next.config.js ./next.config.js
COPY --chown=nextjs:nodejs --from=builder /app/check-env-variables.js ./check-env-variables.js

USER nextjs

EXPOSE 8000

CMD ["npx", "next", "start", "-p", "8000"]
