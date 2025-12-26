# ---------- build stage ----------
FROM eclipse-temurin:21-jdk AS builder
WORKDIR /app

# 캐시를 위해 먼저 빌드 설정 파일만 복사
COPY gradlew ./
RUN chmod +x ./gradlew

COPY gradle ./gradle
COPY build.gradle* settings.gradle* ./

# 의존성 캐시 (테스트는 스킵)
RUN ./gradlew --no-daemon dependencies || true

# 소스 복사 후 빌드
COPY src ./src
RUN ./gradlew --no-daemon clean bootJar -x test

# ---------- run stage ----------
FROM eclipse-temurin:21-jre
WORKDIR /app

# 보안: non-root 유저로 실행
RUN useradd -m -u 10001 appuser
USER appuser

# 빌드 산출물 복사
COPY --from=builder /app/build/libs/*.jar app.jar

EXPOSE 8090

# JVM 옵션은 필요시 환경변수로 조절 가능
ENV JAVA_OPTS=""
ENV SPRING_PROFILES_ACTIVE=prod

ENTRYPOINT ["sh","-c","java $JAVA_OPTS -jar /app/app.jar"]
