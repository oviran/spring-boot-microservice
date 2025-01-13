FROM tomcat:9.0-jdk11-openjdk

RUN rm -rf /usr/local/tomcat/webapps/*

# Set the working directory
WORKDIR /app

# Copy the parent POM into the appropriate directory
COPY ../parent-project/pom.xml /app/parent-project/pom.xml

# Copy the child project POM
COPY pom.xml /app/

# Install dependencies using Maven
RUN apt-get update && apt-get install -y maven && rm -rf /var/lib/apt/lists/*
RUN mvn dependency:go-offline

# Copy the rest of the project files
COPY . .

# Build the project
RUN mvn clean package

# Copy the generated JAR to the Tomcat webapps directory
COPY target/apigateway-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/apigateway-1.0-SNAPSHOT.jar

ENV SERVER_PORT=8088

EXPOSE 8088

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