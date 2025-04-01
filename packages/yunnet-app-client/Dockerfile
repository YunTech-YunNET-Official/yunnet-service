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
RUN yarn yunnet-app-client install
RUN yarn yunnet-app-client build

FROM base AS runner
ENV NODE_ENV=production

WORKDIR /app

COPY --from=builder /app/packages/yunnet-app-client/.next /app/packages/yunnet-app-client/.next
COPY --from=builder /app/packages/yunnet-app-client/public /app/packages/yunnet-app-client/public
COPY --from=builder /app/packages/yunnet-app-client/package.json /app/packages/yunnet-app-client/package.json
COPY --from=builder /app/packages/yunnet-app-client/next.config.ts /app/packages/yunnet-app-client/next.config.ts
COPY --from=builder /app/package.json /app/package.json
COPY --from=builder /app/yarn.lock /app/yarn.lock


RUN corepack enable
RUN yarn workspaces focus --production yunnet-app-client


EXPOSE 3000

CMD ["yarn", "yunnet-app-client", "start"]
