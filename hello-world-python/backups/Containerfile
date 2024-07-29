FROM registry.access.redhat.com/ubi9/python-311:1-72
RUN pip install flask
COPY src/ /app
WORKDIR /app
EXPOSE 8080
ENTRYPOINT ["python"]
CMD ["app.py"]
