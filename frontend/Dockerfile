# syntax=docker/dockerfile:1.4

FROM node:lts AS development

ENV CI=true
ENV PORT=3000

WORKDIR /code
RUN npm config set cache /cache/npm/frontend
COPY package.json /code/package.json
COPY package-lock.json /code/package-lock.json
RUN --mount=type=cache,target=/cache/npm/frontend npm ci
COPY . /code

CMD [ "npm", "run", "start" ]

FROM development AS builder

RUN yarn run build

FROM development as dev-envs
RUN <<EOF
apt-get update
apt-get install -y --no-install-recommends git
EOF

RUN <<EOF
useradd -s /bin/bash -m vscode
groupadd docker
usermod -aG docker vscode
EOF
# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /
CMD [ "npm", "run", "start" ]

FROM nginx:1.13-alpine

COPY --from=builder /code/build /usr/share/nginx/html
