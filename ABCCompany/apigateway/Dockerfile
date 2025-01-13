FROM tomcat:9.0-jdk11-openjdk

# Remove default web apps to clean up
RUN rm -rf /usr/local/tomcat/webapps/*

# Update and install Maven
RUN apt-get update && apt-get install -y maven && rm -rf /var/lib/apt/lists/*

# Set the working directory
WORKDIR /app

# Copy the pom.xml and download dependencies
COPY ["pom.xml", "./"]
RUN mvn dependency:go-offline

# Copy the entire project and build it
COPY . .
RUN mvn clean package

# Copy the generated JAR to Tomcat's webapps directory
COPY target/apigateway-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/apigateway-1.0-SNAPSHOT.jar

# Set the environment variable for the port
ENV SERVER_PORT=8088

# Expose the port
EXPOSE 8088

# Start Tomcat
CMD ["catalina.sh", "run"]










#FROM tomcat:9.0-jdk11-openjdk
#
#RUN rm -rf /usr/local/tomcat/webapps/*
#
#COPY target/apigateway-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/apigateway-1.0-SNAPSHOT.jar
#
## Use an environment variable for the port
#ENV SERVER_PORT=0
#
#EXPOSE 8088
#
#CMD ["catalina.sh", "run"]