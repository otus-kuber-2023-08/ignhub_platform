FROM node:7
RUN useradd -r -u 1001 user
USER user
ADD app.js app.js
ENTRYPOINT ["node", "app.js"]
EXPOSE 8000