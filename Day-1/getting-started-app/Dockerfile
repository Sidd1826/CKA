FROM node:lts-alpine3.23

WORKDIR /app

COPY . . 

RUN yarn install --production

CMD ["node", "src/index.js"]

EXPOSE 3000