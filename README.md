Docker is a container platform that allows you to build, test, and deploy applications quickly. 

Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime. 

Using Docker, you can quickly deploy and scale applications into any environment and know your code will run.

Here we are deploying a simple node app using docker. lately, we will see how to reduce the image size with the help of a multi-stage Dockerfile.

Prerequisites:

Ubuntu o/s platform (I am using AWS EC2 ubuntu instance)

Docker installed on our o/s.

the node js application with package file (if package file does not exist then you should create using run command "npm init -y")

make sure the inbound rule for port 3000 is open.

I used Ec2 Instance

![AWS](https://github.com/Tripti-TD/nodejs-app/assets/128075759/8785933e-05f6-4508-9bf6-f70c24878c72)

 Dockerfile uses the official Node.js Docker image as the base, copies your application files into the image, installs dependencies, exposes port 3000, and specifies the command to run your Node.js server.

Open a terminal, and clone the GitHub repo:
![clone repo](https://github.com/Tripti-TD/nodejs-app/assets/128075759/5249b410-3c30-4fec-b4cf-e2406a8152e9)

navigate to your project directory:
And run the following command to build your Docker image:

![build dockerfile](https://github.com/Tripti-TD/nodejs-app/assets/128075759/2468af4e-604c-4130-8c18-36341002e0da)ocker build

This command will build a Docker image with the tag my-nodejs-app.
you can now see the image.

![Dockerimage](https://github.com/Tripti-TD/nodejs-app/assets/128075759/67702e3c-e27c-48c3-a5e0-3fcb15188abf)

Run the Docker container: 
![container](https://github.com/Tripti-TD/nodejs-app/assets/128075759/2c0da6b2-7c05-4e47-bca5-b2bf0f00b1f0)

This command will start a Docker container based on your image, and it will forward port 3000 from the container to port 3000 on your EC2.

Now, your Node.js application is Dockerized, and you can access it in your web browser at http://public-ip:3000

![Page](https://github.com/Tripti-TD/nodejs-app/assets/128075759/1b01e964-a7b3-435a-a87b-e1945a86d8e2)

You can see the image size is 911 MB isn't it so much, so to reduce the size of this image we use a multistage Dockerfile.

With multi-stage Dockerfiles, you can also share data between build stages. This way, you can build the application in one stage and copy only the necessary components that the application needs to run to the final image, resulting in smaller and more optimized Docker images.

first, we need to deallocate port 3000.

Multistage Dockerfile

# Stage 1: Build the application
FROM node:14 AS builder

WORKDIR /usr/src/app

# Copy package.json to the working directory
COPY package*.json ./

# Install dependencies
RUN npm install --only=production

# Copy application source code
COPY . .

# Stage 2: Create the production image
FROM node:14-alpine

WORKDIR /usr/src/app

# Copy only necessary files from the previous stage
COPY --from=builder /usr/src/app .

# Expose port 3000
EXPOSE 3000

# Command to run the application
CMD ["node", "server.js"]


In this Dockerfile:

Stage 1 (Builder): We use the node:14 image as the base image to build the application. We copy package.json install dependencies (excluding development dependencies), and copy the application source code.

Stage 2 (Production): We use a lighter-weight node:14-alpine image as the base image for the production stage. We copy only the necessary files from the builder stage, namely the built application and the dependencies installed earlier. This reduces the size of the final image.

To build the Docker image, run:

docker build . -t new-node-js-app

![new build](https://github.com/Tripti-TD/nodejs-app/assets/128075759/0a879504-3459-4429-9b38-c799e2cfaee3)

new container from multistage dockerfile

![newcontainer](https://github.com/Tripti-TD/nodejs-app/assets/128075759/a010f634-106b-4387-a187-2a64b859bd0b)

Then browser at http://public-ip:3000

![newpage](https://github.com/Tripti-TD/nodejs-app/assets/128075759/d5a3b0dc-3ae2-48d2-846b-91583798da7d)

So what have we achieved? We reduced the docker image by 77%.

![difference](https://github.com/Tripti-TD/nodejs-app/assets/128075759/4e6c754f-f53a-4a68-8d05-ae9123ed9195)

So we do our project let clean of them.

![cleaning](https://github.com/Tripti-TD/nodejs-app/assets/128075759/d8fd32ba-ecd7-49ae-a0b7-6e7cad5fbf5f)

Thanks for reading, happy learning.

Tripti Dewangan
