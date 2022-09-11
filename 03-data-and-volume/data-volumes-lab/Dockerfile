FROM node:18

ARG DEFAULT_PORT=80

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

ENV PORT $DEFAULT_PORT

EXPOSE $PORT

VOLUME ["/app/feedback"]

# CMD ["node", "server.js"]
# to use nodemon instead of naked node.js
CMD [ "npm" , "start" ]


