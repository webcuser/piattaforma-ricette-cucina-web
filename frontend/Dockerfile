FROM node:18

# Set working directory
WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy app files
COPY . .

# Expose port and start application
EXPOSE 8080
CMD ["npm", "run", "serve"]
