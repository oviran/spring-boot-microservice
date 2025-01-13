FROM tomcat:9.0-jdk11-openjdk

RUN rm -rf /usr/local/tomcat/webapps/*

COPY target/base-1.0-SNAPSHOT.jar /usr/local/tomcat/webapps/base-1.0-SNAPSHOT.jar

EXPOSE 8083

CMD ["catalina.sh", "run"]