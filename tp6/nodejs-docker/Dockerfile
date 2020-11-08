
FROM node:13.12.0-alpine AS build

WORKDIR /app

COPY ./ ./

RUN npm install --only=production


FROM node:13.12.0-alpine AS release  

WORKDIR /app

COPY --from=build /app ./

EXPOSE 3000

CMD ["npm", "start"]