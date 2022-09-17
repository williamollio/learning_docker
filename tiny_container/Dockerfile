FROM alpine:3.10

RUN apk update && apk add nodejs-current npm

WORKDIR /home/src

COPY package-lock.json package.json ./

RUN npm ci

COPY . .

CMD ["node", "index.js"]