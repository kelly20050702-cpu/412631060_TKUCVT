FROM python:3.12-slim
RUN useradd -u 1000 appuser
WORKDIR /app
COPY requirements.txt .
# 強制使用 --no-cache-dir，這能大幅降低因網路暫停導致的錯誤
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
RUN chown -R 1000:1000 /app
USER 1000
# 明確指定 Python 搜尋路徑，確保 Flask 能被找到
ENV PYTHONPATH=/usr/local/lib/python3.12/site-packages
CMD ["python", "app.py"]
