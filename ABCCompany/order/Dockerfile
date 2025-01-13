FROM tomcat:9.0-jdk11-openjdk

RUN rm -rf /usr/local/tomcat/webapps/*

COPY target/order-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/order-1.0-SNAPSHOT.jar

ENV SERVER_PORT=0

EXPOSE 8081

CMD ["catalina.sh", "run"]