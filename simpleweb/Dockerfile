# FROM alpine
FROM node:14-alpine

#Change the working directory inside the container, so the following commands will be executed there
WORKDIR /usr/app
#Copy the context of build into the container, so npm has access to package.json and index.js
#We copy the package.json to the container so npm install work. This only will execute if the package change so the npm install will be needed.
COPY ./package.json ./
RUN npm install
#we copy the rest of our project, so we can use cache untill the last step instead of running npm install each time we create a image.
COPY ./ ./

CMD ["npm", "start"]