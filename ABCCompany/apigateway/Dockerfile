# Start from the official Tomcat image
FROM tomcat:9.0-jdk11-openjdk

# Remove default web apps to clean the image
RUN rm -rf /usr/local/tomcat/webapps/*

# Set the working directory inside the container
WORKDIR /app

# Copy the Maven wrapper files and pom.xml
COPY ["pom.xml", "./"]
COPY .mvn .mvn
COPY mvnw .

# Make the Maven wrapper executable
RUN chmod +x mvnw

# Fetch project dependencies while offline (this ensures that Maven can resolve dependencies before the actual build)
RUN ./mvnw dependency:go-offline

# Copy the rest of the application files into the container
COPY . .


RUN ./mvnw clean install -DskipTests


RUN ./mvnw clean package -DskipTests


COPY target/apigateway-1.0-SNAPSHOT.war /usr/local/tomcat/webapps/apigateway-1.0-SNAPSHOT.war


ENV SERVER_PORT=8088
EXPOSE 8088

# Start the Tomcat server with the WAR file
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