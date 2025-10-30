

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>invitation-server</artifactId>
  <version>1.0.0</version>
  <name>Invitation App Server</name>
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.5</version>
  </parent>
  <properties>
    <java.version>17</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-j</artifactId>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>


---

⚙️ application.properties

# Server settings
server.port=8080

# MySQL Database configuration
spring.datasource.url=jdbc:mysql://mysql:3306/invitationdb?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# Uploads directory
app.upload.dir=uploads


---

🐋 docker-compose.yml

version: '3.9'
services:
  mysql:
    image: mysql:8.0
    container_name: invitation_mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: invitationdb
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

  app:
    build: .
    container_name: invitation_app
    depends_on:
      - mysql
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/invitationdb?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: root
    volumes:
      - ./uploads:/uploads
      - ./target:/app

volumes:
  mysql_data:


---

🧠 जावा फाइलें (पहले जैसी ही रहेंगी)

Contact, Repository, FileStorageService, और ContactController को पहले वाले संस्करण की तरह रखें — वे MySQL के साथ वैसे ही काम करेंगे।


---

🚀 कैसे चलाएँ

1️⃣ Docker इन्स्टॉल करें

Windows, macOS या Linux पर Docker Desktop इन्स्टॉल करें।

2️⃣ प्रोजेक्ट बिल्ड करें

mvn clean package -DskipTests

3️⃣ Docker से रन करें

docker-compose up --build

सर्वर http://localhost:8080 पर चल जाएगा और MySQL localhost:3306 पर उपलब्ध रहेगा।

4️⃣ टेस्ट करें

curl -X POST http://localhost:8080/api/contacts \
  -F "name=Ram" \
  -F "village=Delhi" \
  -F "familyJson=[\"Ram\",\"Sita\"]" \
  -F "relationsJson=[\"Father\",\"Mother\"]" \
  -F "photo=@/path/to/photo.jpg"

5️⃣ डेटा स्थायी रहेगा

MySQL डेटा mysql_data वॉल्यूम में सेव रहेगा — सर्वर बंद करने पर भी नहीं मिटेगा।


---

✅ अगले कदम

अब आप MySQL GUI (जैसे DBeaver या phpMyAdmin) से डेटा देख सकते हैं।

चाहें तो मैं इसमें JWT Login + Admin Panel या Server-side PDF Download भी जोड़ सकता हूँ।
