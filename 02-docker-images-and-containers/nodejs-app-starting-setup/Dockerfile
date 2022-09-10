FROM node

WORKDIR /app

# a little opimization install  before copy entire code
# to be cache when source code is changed
COPY package.json /app

RUN npm install

# COPY <local> <container>
# in container we have workdir = /app
# thus, /app = ./
# COPY . ./
COPY . /app

EXPOSE 80

CMD ["node", "server.js"]
