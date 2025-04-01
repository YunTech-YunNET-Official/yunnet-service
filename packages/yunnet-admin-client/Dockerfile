FROM node:22-alpine AS base

FROM base AS builder
ARG BUILD_DATE
ARG BUILD_BRANCH
ARG BUILD_SHA
ARG NODE_OPTIONS

ENV LANG=en_US.UTF-8
ENV NODE_OPTIONS=${NODE_OPTIONS}
RUN apk add --no-cache libc6-compat
WORKDIR /app
ADD . .
RUN corepack enable
RUN yarn install
RUN yarn yunnet-admin-client install
RUN yarn yunnet-admin-client build

FROM base AS runner
ENV NODE_ENV=production

WORKDIR /app

COPY --from=builder /app/packages/yunnet-admin-client/.next /app/packages/yunnet-admin-client/.next
COPY --from=builder /app/packages/yunnet-admin-client/public /app/packages/yunnet-admin-client/public
COPY --from=builder /app/packages/yunnet-admin-client/package.json /app/packages/yunnet-admin-client/package.json
COPY --from=builder /app/packages/yunnet-admin-client/next.config.ts /app/packages/yunnet-admin-client/next.config.ts
COPY --from=builder /app/package.json /app/package.json
COPY --from=builder /app/yarn.lock /app/yarn.lock


RUN corepack enable
RUN yarn workspaces focus --production yunnet-admin-client


EXPOSE 3000

CMD ["yarn", "yunnet-admin-client", "start"]
