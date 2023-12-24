FROM node:16-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

# Define environment variables
ENV MYSQL_HOST=db
ENV MYSQL_USER=training
ENV MYSQL_PASSWORD=training_pw
ENV MYSQL_DATABASE=user

CMD ["node", "app.js"]
