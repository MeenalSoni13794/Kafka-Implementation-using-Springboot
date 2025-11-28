# Kafka + Spring Boot Demo

A very simple Spring Boot project that shows how to:

- Produce plain text messages to a Kafka topic
- Produce JSON messages (a `User` object) to a Kafka topic
- Expose REST APIs so you can send messages using Postman / browser

---

## 1. What you need

- **Java**: JDK 17
- **Build tool**: Maven (comes with IntelliJ IDEA or install separately)
- **Kafka**: Apache Kafka + Zookeeper (or Kafka with KRaft mode)
- **Git (optional)**: only needed if you want to push to GitHub
- **IDE**: IntelliJ IDEA / VS Code / Eclipse (your choice)

---

## 2. Project structure (important files)

- `pom.xml` – Maven dependencies (Spring Boot, Spring Kafka, Jackson, etc.)
- `src/main/java/com/example/kafka/Kafka/KafkaProducer.java`  
  Sends **simple String messages** to Kafka.
- `src/main/java/com/example/kafka/Kafka/JsonKafkaProducer.java`  
  Sends **JSON `User` objects** to Kafka.
- `src/main/java/com/example/kafka/Controller/MessageController.java`  
  REST API to send **plain text** messages.
- `src/main/java/com/example/kafka/Controller/JsonMessageController.java`  
  REST API to send **JSON `User`** messages.
- `src/main/java/com/example/kafka/payload/User.java`  
  Simple POJO with `id`, `firstName`, `lastName`.

---

## 3. Configure Kafka connection

Open `src/main/resources/application.properties` (or `application.yml`) and set at least:

spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

# For JSON producer (User object)
spring.kafka.producer.properties.spring.json.trusted.packages=*
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializerTopics used in this project (based on code):

- For plain text: check `KafkaProducer` (e.g. `"javaguides"` or your topic name)
- For JSON: `JsonKafkaProducer` uses topic `"javaguides"` by default

Make sure these topics exist in Kafka (or enable auto‑creation).

---

## 4. Start Kafka

From your Kafka installation directory (paths will vary):

# 1. Start Zookeeper (if using Zookeeper mode)
bin/zookeeper-server-start.sh config/zookeeper.properties

# 2. Start Kafka broker
bin/kafka-server-start.sh config/server.propertiesOn Windows PowerShell, commands are usually:
hell
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
.\bin\windows\kafka-server-start.bat .\config\server.propertiesKeep these terminals **running** while you use the app.

---

## 5. Run the Spring Boot app

From project root (`kafka` folder):

mvn spring-boot:runOr from your IDE, run the main class:

- `com.example.kafka.KafkaApplication`

By default, the app runs on: `http://localhost:8080`

---

## 6. REST APIs

### 6.1 Send plain text message

- **Method**: `GET`
- **URL**: `http://localhost:8080/api/v1/kafka/publish?message=hello%20world`

Example (browser or Postman):

- Open:  
  `http://localhost:8080/api/v1/kafka/publish?message=hello`

What happens:

- `MessageController` receives the request
- Calls `KafkaProducer.sendMessage(message)`
- Message is written to the configured Kafka topic

---

### 6.2 Send JSON `User` object

- **Method**: `POST`
- **URL**: `http://localhost:8080/api/v1/kafka/publish`
- **Body (JSON)**:

{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe"
}Steps in Postman:

1. Set **method** to `POST`
2. URL: `http://localhost:8080/api/v1/kafka/publish`
3. Tab **Body** → select **raw** → choose **JSON**
4. Paste the JSON above
5. Click **Send**

What happens:

- `JsonMessageController.publish()` receives the `User` JSON
- Spring converts JSON → `User` object
- `JsonKafkaProducer.sendMessage(user)` sends it to the JSON topic

---

## 7. How to see messages in Kafka

From Kafka folder, in a new terminal:

# Replace "javaguides" with your topic name
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic javaguides --from-beginningWindows PowerShell:
hell
.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic javaguides --from-beginningYou should see the messages you send from the REST APIs.

---

## 8. Common errors & quick fixes

- **`NoClassDefFoundError: com.fasterxml.jackson.databind.ObjectMapper`**  
  → Add `jackson-databind` dependency in `pom.xml` (already done in this project).

- **`java.net.ConnectException: Connection refused` (Kafka)**  
  → Kafka broker not running or wrong `bootstrap-servers`.  
    Check that Kafka is started and `spring.kafka.bootstrap-servers` is correct.

- **`cannot find symbol: class User`**  
  → Import is missing. Make sure you import:  
    `import com.example.kafka.payload.User;`

If you get any other error, copy the full stack trace and ask for help.

---

## 9. How to push this project to GitHub (short)

From project root (`C:\Users\PC\Downloads\kafka`):

git init
git add .
git commit -m "Initial Kafka + Spring Boot demo"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main(Replace `<your-username>` and `<your-repo>` with your own.)

---

## 10. Next steps / practice ideas

- Create a **Kafka consumer** in this project and log received messages.
- Add new fields to `User` (e.g. `email`) and send them in JSON.
- Deploy this app to a cloud service (Heroku, AWS, etc.) and connect to a remote Kafka.

This project is meant as a **starter**. Feel free to experiment, break things, and learn.
