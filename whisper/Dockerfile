FROM ubuntu:22.04 as builder

RUN apt-get update && apt-get install -y \
    build-essential \
    git \
    cmake \
    libopenblas-dev \
    wget \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
RUN git clone https://github.com/ggerganov/whisper.cpp && \
    cd whisper.cpp && \
    make

RUN if [ ! -f "whisper.cpp/server" ]; then \
      echo "ERROR: server binary not built!"; \
      exit 1; \
    fi

# Этап 2: Финальный образ
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    libopenblas-base \
    && rm -rf /var/lib/apt/lists/*

# Копируем ВЕСЬ каталог
COPY --from=builder /app/whisper.cpp /app/whisper.cpp

# Автоматическая загрузка модели
RUN mkdir -p /app/whisper.cpp/models && \
    wget -O /app/whisper.cpp/models/ggml-base.bin \
    https://huggingface.co/ggerganov/whisper.cpp/resolve/main/ggml-base.bin

WORKDIR /app/whisper.cpp
EXPOSE 8080

CMD ["./server", "-ht", "-t", "4"]