FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# Menggunakan node server.js langsung lebih aman
CMD ["node", "server.js"]