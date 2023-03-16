<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->

# Session 5:  Practice presentation

In this session we will finish the tutorials pending from Session 4: Container Orchestrators, particularly Docker-Compose Case Study #2 and Kubernetes. Also, we will be presenting the first assignment for the practical part of the course.  

```
version: '3'

services:
  prometheus:
    container_name: node-prom
    image: prom/prometheus:latest
    ports:
      - 9090:9090
    depends_on:
      - apache
      - grafana
  apache:
    container_name: apache
    image: httpd
    ports:
      - 8080:80
  grafana:
    image: grafana/grafana-oss
    ports:
      - 3000:3000

```



