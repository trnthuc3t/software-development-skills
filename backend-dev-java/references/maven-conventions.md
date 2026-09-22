# MAVEN CONVENTION — Java/Spring Boot (Thức)

## 1. Toạ độ artifact
- `groupId` = `com.thuc.<tên dự án>` (khớp gốc package).
- `artifactId` = `<tên dự án>-<tên service>` (ví dụ `billing-invoice`).
- `version` = SemVer: `MAJOR.MINOR.PATCH` (`-SNAPSHOT` khi đang phát triển).
- Bố cục chuẩn Maven: `src/main/java`, `src/main/resources`, `src/test/java`, `src/test/resources`.

## 2. Single-module (mặc định mỗi service)
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" ...>
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.x</version>
    <relativePath/>
  </parent>

  <groupId>com.thuc.billing</groupId>
  <artifactId>billing-invoice</artifactId>
  <version>1.0.0-SNAPSHOT</version>

  <properties>
    <java.version>21</java.version>
    <maven.compiler.release>21</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency><groupId>org.springframework.boot</groupId><artifactId>spring-boot-starter-web</artifactId></dependency>
    <dependency><groupId>org.springframework.boot</groupId><artifactId>spring-boot-starter-validation</artifactId></dependency>
    <dependency><groupId>org.springframework.boot</groupId><artifactId>spring-boot-starter-data-jpa</artifactId></dependency>
    <dependency><groupId>org.springframework.boot</groupId><artifactId>spring-boot-starter-actuator</artifactId></dependency>
    <dependency><groupId>org.springframework.boot</groupId><artifactId>spring-boot-starter-security</artifactId></dependency>
    <dependency><groupId>org.springdoc</groupId><artifactId>springdoc-openapi-starter-webmvc-ui</artifactId><version>2.x</version></dependency>
    <dependency><groupId>org.flywaydb</groupId><artifactId>flyway-core</artifactId></dependency>
    <dependency><groupId>org.projectlombok</groupId><artifactId>lombok</artifactId><optional>true</optional></dependency>
    <dependency><groupId>org.mapstruct</groupId><artifactId>mapstruct</artifactId><version>1.6.x</version></dependency>
    <dependency><groupId>io.github.resilience4j</groupId><artifactId>resilience4j-spring-boot3</artifactId></dependency>
    <!-- test -->
    <dependency><groupId>org.springframework.boot</groupId><artifactId>spring-boot-starter-test</artifactId><scope>test</scope></dependency>
    <dependency><groupId>org.testcontainers</groupId><artifactId>junit-jupiter</artifactId><scope>test</scope></dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin><groupId>org.springframework.boot</groupId><artifactId>spring-boot-maven-plugin</artifactId></plugin>
      <plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-compiler-plugin</artifactId>
        <configuration><annotationProcessorPaths>
          <path><groupId>org.projectlombok</groupId><artifactId>lombok</artifactId></path>
          <path><groupId>org.mapstruct</groupId><artifactId>mapstruct-processor</artifactId><version>1.6.x</version></path>
        </annotationProcessorPaths></configuration>
      </plugin>
      <plugin><groupId>org.jacoco</groupId><artifactId>jacoco-maven-plugin</artifactId></plugin>          <!-- độ phủ -->
      <plugin><groupId>com.diffplug.spotless</groupId><artifactId>spotless-maven-plugin</artifactId></plugin> <!-- format -->
      <plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-enforcer-plugin</artifactId></plugin> <!-- chốt java/maven version -->
    </plugins>
  </build>
</project>
```
> Lưu ý thứ tự annotation processor: **Lombok trước MapStruct** để MapStruct thấy getter/setter Lombok sinh ra.

## 3. Multi-module (khi monorepo/monolith module hoá)
```
<dự án>-parent/                 (pom packaging=pom, quản lý version tập trung ở <dependencyManagement>)
├── <dự án>-domain/             (thuần Java, ít phụ thuộc Spring)
├── <dự án>-application/        (use case)
├── <dự án>-infrastructure/     (JPA, client, adapter out)
└── <dự án>-bootstrap/          (module chạy: @SpringBootApplication, wiring, config)
```
- Parent giữ `<dependencyManagement>` + `<pluginManagement>`; module con **không** khai version lẻ.
- Chỉ module `bootstrap` đóng gói chạy được (`spring-boot-maven-plugin`).

## 4. Lệnh chuẩn
- `mvn clean verify` — build + test + coverage + lint (dùng ở CI, là "cửa" trước handover).
- `mvn spring-boot:run -Dspring-boot.run.profiles=dev` — chạy local.
- `mvn -DskipTests package` — chỉ khi đóng gói nhanh (không dùng để bỏ test trước handover).
- Không phát hành `-SNAPSHOT` lên môi trường UAT/prod.

## 5. Quy ước phụ thuộc
- Ưu tiên **starter** của Spring Boot; để `spring-boot-starter-parent`/BOM **quản version**, không ghim tay.
- Không thêm thư viện trùng chức năng; rà `mvn dependency:tree` khi nghi đụng version.
- Quét lỗ hổng phụ thuộc (SCA) theo B5; không đưa thư viện lỗi thời/không rõ nguồn.
