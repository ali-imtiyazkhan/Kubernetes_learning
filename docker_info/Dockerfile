# Use the official lightweight Bun image
FROM oven/bun:1.1-alpine AS base

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy lockfile and package.json first to leverage Docker cache
COPY package.json bun.lock ./

# Install dependencies (frozen-lockfile ensures reproducibility)
RUN bun install --frozen-lockfile

# Copy the rest of the application source code
COPY . .

# Expose the port the app runs on
EXPOSE 3000

# Run the app
CMD ["bun", "run", "index.ts"]
