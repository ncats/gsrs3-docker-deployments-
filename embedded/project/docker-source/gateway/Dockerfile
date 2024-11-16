# Docker mvn build
FROM maven:3.6.3-jdk-11 AS build

COPY . .
RUN mvn clean -U package -Dwith.packaging=jar -DskipTests

ENV TZ=America/New_York
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN mkdir -p /app/data/gateway
RUN mkdir -p /app/data/gateway/conf
RUN mkdir -p /app/data/gateway/classes
WORKDIR /app/data/gateway
EXPOSE 9081
ENTRYPOINT ["java", "-cp", "/target/gateway.jar:classes:conf", "org.springframework.boot.loader.JarLauncher"]

