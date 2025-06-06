FROM python:3.12.9-slim

LABEL maintainer="Zipstack Inc."

ENV \
    # Keeps Python from generating .pyc files in the container
    PYTHONDONTWRITEBYTECODE=1 \
    # Set to immediately flush stdout and stderr streams without first buffering
    PYTHONUNBUFFERED=1 \
    # OpenTelemetry configuration
    OTEL_SERVICE_NAME="structure-tool" \
    OTEL_TRACES_EXPORTER=none \
    OTEL_METRICS_EXPORTER=none \
    OTEL_LOGS_EXPORTER=none \
    # Enable context propagation
    OTEL_PROPAGATORS="tracecontext"

# Install dependencies for SDK and prompt service
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    ffmpeg libsm6 libxext6 libmagic-dev poppler-utils \
    libreoffice freetds-dev freetds-bin \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir -U pip
# Set the working directory in the container
WORKDIR /app
COPY requirements.txt /app/
RUN pip install --no-cache-dir -r requirements.txt && \
    pip install --no-cache-dir \
    opentelemetry-distro \
    opentelemetry-exporter-otlp \
    platformdirs>=3.0.0 \
    && opentelemetry-bootstrap -a install
# Copy the contents of your project directory into the container at /app
COPY src /app/src/
WORKDIR /app/src

ENTRYPOINT ["opentelemetry-instrument", "python", "main.py"]
