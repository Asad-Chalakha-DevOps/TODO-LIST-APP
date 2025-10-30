FROM node:14
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
# we can expose the code here also but we didnt this was a demo so i define the port in the manifest only the port of the backend is 3500 
COPY . .
CMD ["node", "index.js"] 
