FROM node:26-alpine3.23
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
ENV REACT_APP_BACKEND_URL=http://localhost:3000
CMD [ "npm" , "start" ]
