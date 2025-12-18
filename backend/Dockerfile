# 使用 Python 3.12 作为基础镜像（onnxruntime支持3.10-3.13）
FROM python:3.12-slim

# 设置工作目录
WORKDIR /app

# 安装系统依赖
RUN apt-get update && apt-get install -y \
    curl \
    && rm -rf /var/lib/apt/lists/*

# 安装 uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv
RUN chmod +x /usr/local/bin/uv

# 复制项目配置文件
COPY pyproject.toml ./
COPY uv.lock* ./

# 安装 Python 依赖，指定使用当前Python版本
RUN uv sync --python $(which python3)

# 复制后端代码
COPY backend/ ./backend/

# 创建必要的目录
RUN mkdir -p /app/backend/instance /app/uploads

ENV PYTHONPATH=/app
ENV FLASK_APP=backend/app.py

# 暴露端口
EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD ["sh", "-c", "curl -f http://localhost:${PORT:-5000}/health || exit 1"]

# 启动应用
CMD ["uv", "run", "--directory", "backend", "python", "app.py"]

