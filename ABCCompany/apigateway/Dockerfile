FROM tomcat:9.0-jdk11-openjdk

RUN rm -rf /usr/local/tomcat/webapps/*

WORKDIR /app

COPY ["pom.xml", "./"]
COPY .mvn .mvn
COPY mvnw .

RUN chmod +x mvnw

RUN ./mvnw dependency:go-offline

COPY . .

RUN ./mvnw clean package

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