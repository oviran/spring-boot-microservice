FROM tomcat:9.0-jdk11-openjdk

RUN rm -rf /usr/local/tomcat/webapps/*

COPY target/inventory-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/inventory-1.0-SNAPSHOT.jar

# Use an environment variable for the port
ENV SERVER_PORT=0

EXPOSE 8085

CMD ["catalina.sh", "run"]