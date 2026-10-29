FROM node:26-alpine3.23
WORKDIR /app
COPY . .
RUN npm install
ENV MONGO_URI='mongodb://localhost:27017/travelmemory'
ENV PORT=3001
EXPOSE 3001
CMD [ "node" , "index.js" ]
