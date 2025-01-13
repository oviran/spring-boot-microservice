FROM tomcat:9.0-jdk11-openjdk

RUN rm -rf /usr/local/tomcat/webapps/*

COPY target/discoveryserver-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/discoveryserver-1.0-SNAPSHOT.jar

EXPOSE 8761

CMD ["catalina.sh", "run"]