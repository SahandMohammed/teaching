# Stage 1: deps — install production-only dependencies
FROM node:22-alpine AS deps
WORKDIR /app
RUN corepack enable && corepack prepare pnpm@latest --activate
COPY package.json pnpm-lock.yaml* ./
# --prod skips devDependencies (nodemon, etc.) to keep the image lean
RUN pnpm i --frozen-lockfile --prod

# Stage 2: runner
FROM node:22-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 expressjs

# Copy deps first, then source — chown so expressjs user can read them
COPY --from=deps --chown=expressjs:nodejs /app/node_modules ./node_modules
COPY --chown=expressjs:nodejs . .

USER expressjs
EXPOSE 3000
ENV PORT=3000
CMD ["node", "index.js"]
