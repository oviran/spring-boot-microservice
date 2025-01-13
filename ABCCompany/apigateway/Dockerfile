# Use a Tomcat base image with JDK 11
FROM tomcat:9.0-jdk11-openjdk

# Remove the default webapps to clean up the environment
RUN rm -rf /usr/local/tomcat/webapps/*

# Set the working directory
WORKDIR /app

# Copy the Maven project files to the container
COPY ["pom.xml", "./"]

# Copy the Maven wrapper if present, otherwise ensure Maven is available
COPY .mvn .mvn
COPY mvnw .

# Download Maven dependencies
RUN ./mvnw dependency:go-offline

# Copy the rest of the project files
COPY . .

# Build the Maven project
RUN ./mvnw clean package

# Copy the generated JAR to the Tomcat webapps directory
COPY target/apigateway-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/apigateway-1.0-SNAPSHOT.jar

# Set an environment variable for the server port
ENV SERVER_PORT=8088

# Expose the port the application will run on
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