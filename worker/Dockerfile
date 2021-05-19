FROM maven:3.6.1-jdk-8-alpine

WORKDIR /app

COPY . .

RUN mvn package -DskipTests && \
    mv target/worker-jar-with-dependencies.jar /run/worker.jar && \
    rm -rf *

CMD java -jar /run/worker.jar