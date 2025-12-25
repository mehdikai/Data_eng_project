# Real-Time Data Streaming Pipeline

![Project Banner](https://img.shields.io/badge/Data_Engineering-Real--Time_Streaming-blue)
![Tech Stack](https://img.shields.io/badge/Stack-Kafka%20%7C%20Spark%20%7C%20Cassandra-orange)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker)
![Python](https://img.shields.io/badge/Python-3.9-3776AB?logo=python)

A production-ready, end-to-end real-time data streaming pipeline that processes user data from ingestion to visualization. Built with modern data engineering tools and optimized to run on resource-constrained environments (8GB RAM).

## 📊 Project Overview

This project demonstrates a complete streaming analytics platform that:
- Ingests user data from an external API
- Streams data through Apache Kafka
- Processes streams with Apache Spark
- Stores processed data in Cassandra
- Visualizes real-time analytics with Grafana

**Live Results:** 941+ users processed | 10+ countries | Real-time dashboards

## 🏗️ Architecture

![System Architecture](https://github.com/)

### Data Flow:
1. **Data Ingestion:** Airflow orchestrates API calls to fetch random user data
2. **Streaming:** Data is published to Kafka topic `users_created`
3. **Processing:** Spark Streaming consumes, transforms, and enriches data
4. **Storage:** Processed records stored in Cassandra database
5. **Visualization:** Grafana queries Cassandra for real-time dashboards

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Orchestration** | Apache Airflow 2.6.0 | Workflow management and scheduling |
| **Message Broker** | Apache Kafka 7.4.0 | Real-time data streaming |
| **Stream Processing** | Apache Spark 3.4.1 | Distributed data processing |
| **Database** | Apache Cassandra 5.0 | Scalable NoSQL storage |
| **Visualization** | Grafana 10.2.0 | Real-time analytics dashboards |
| **Metadata DB** | PostgreSQL 14.0 | Airflow metadata storage |
| **Coordination** | Apache Zookeeper 7.4.0 | Distributed coordination |
| **Schema Registry** | Confluent Schema Registry 7.4.0 | Schema management |
| **Containerization** | Docker & Docker Compose | Deployment and orchestration |

## ✨ Key Features

- ✅ **Real-Time Processing:** Sub-second latency from ingestion to visualization
- ✅ **Custom Transformations:** 
  - Country extraction from address strings
  - Date parsing (year, month, day extraction)
  - Data enrichment and validation
- ✅ **Scalable Architecture:** Horizontally scalable components
- ✅ **Resource Optimized:** Runs efficiently on 8GB RAM
- ✅ **Fault Tolerant:** Checkpointing and error handling
- ✅ **Live Dashboards:** Auto-refreshing Grafana visualizations
- ✅ **One-Command Deployment:** Fully containerized with Docker Compose

## 📦 Project Structure

```
.
├── dags/
│   └── kafka_stream.py          # Airflow DAG for data ingestion
├── script/
│   └── entrypoint.sh            # Airflow initialization script
├── spark_stream.py              # Spark streaming application
├── docker-compose.yml           # Container orchestration
├── requirements.txt             # Python dependencies
└── README.md                    # Project documentation
```

## 🚀 Getting Started

### Prerequisites

- Docker Desktop or Docker Engine (20.10+)
- Docker Compose (2.0+)
- Minimum 8GB RAM
- 20GB free disk space

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/mehdikai/Data_eng_project.git
cd Data_eng_project
```

2. **Create virtual environment**
```bash
sudo apt install python3-venv
python3 -m venv venv
source venv/bin/activate
```

3. **Start the infrastructure**
```bash
docker-compose up -d
```

4. **Wait for services to initialize** (2-3 minutes)
```bash
# Check service health
docker-compose ps
```

### Running the Pipeline

1. **Access Airflow UI**
   - URL: `http://localhost:8080`
   - Username: `admin`
   - Password: `admin`
   - Enable and trigger the `user_automation` DAG

2. **Start Spark Streaming**
```bash
python spark_stream.py
```

3. **Access Grafana**
   - URL: `http://localhost:3000`
   - Username: `admin`
   - Password: `admin`

4. **Configure Grafana Data Source**
   - Go to: Configuration → Data Sources → Add data source
   - Select: Apache Cassandra
   - Configure:
     ```
     Host: cassandra:9042
     Keyspace: spark_streams
     Username: cassandra
     Password: cassandra
     Consistency: ONE
     ```

## 📊 Dashboard Panels

The Grafana dashboard includes:

1. **Total Users Count** - Real-time user registration count
2. **Gender Distribution** - Male vs Female ratio (Pie Chart)
3. **Top 10 Countries** - Geographic distribution (Horizontal Bar)
4. **Recently Registered Users** - Latest user profiles (Table)
5. **Monthly Registration Trend** - Time-series analysis (Line Chart)
6. **Email Providers** - Popular email domains (Bar Chart)

## 🔧 Configuration

### Kafka Topics
- `users_created` - Main data stream topic

### Cassandra Schema
```sql
CREATE KEYSPACE spark_streams 
WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '1'};

CREATE TABLE spark_streams.created_users (
    id TEXT PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    gender TEXT,
    address TEXT,
    post_code TEXT,
    email TEXT,
    username TEXT,
    registered_date TEXT,
    phone TEXT,
    picture TEXT,
    year TEXT,
    month TEXT,
    day TEXT,
    country TEXT
);
```

### Resource Configuration

Optimized for 8GB RAM:
- Spark Worker: 1GB memory, 2 cores
- Cassandra: 512MB heap
- Kafka: Default settings
- Control Center: Disabled (saves 1-1.5GB)

## 🎯 Data Transformations

The Spark streaming application performs:

1. **Country Extraction**
   - Parses address field
   - Extracts country (last component after comma)
   - Example: "123 Main St, New York, USA" → "USA"

2. **Date Parsing**
   - Converts ISO timestamp to date
   - Extracts year, month, day as separate fields
   - Enables time-series analysis

3. **Data Validation**
   - Schema enforcement
   - Null handling
   - Error logging

## 📈 Performance Metrics

- **Throughput:** ~1000 records/minute
- **Latency:** < 2 seconds (end-to-end)
- **Resource Usage:** ~6-7GB RAM total
- **Uptime:** Continuous streaming (tested 24+ hours)

## 🧪 Testing

### Verify Data Flow

1. **Check Kafka topic**
```bash
docker exec -it broker kafka-console-consumer \
  --bootstrap-server localhost:9092 \
  --topic users_created --from-beginning
```

2. **Query Cassandra**
```bash
docker exec -it cassandra cqlsh -e \
  "SELECT COUNT(*) FROM spark_streams.created_users;"
```

3. **View Spark logs**
```bash
# Check Spark streaming output
tail -f /opt/spark/logs/*
```

## 🐛 Troubleshooting

### Common Issues

**Services not starting:**
```bash
# Check logs
docker-compose logs <service_name>

# Restart specific service
docker-compose restart <service_name>
```

**Cassandra connection issues:**
```bash
# Verify Cassandra is ready
docker exec -it cassandra nodetool status
```

**Out of memory errors:**
```bash
# Reduce worker memory in docker-compose.yml
SPARK_WORKER_MEMORY: 512m
```

**Port conflicts:**
```bash
# Check port availability
netstat -tulpn | grep <port>
```

## 🎓 Learning Outcomes

This project demonstrates:
- Event-driven architecture design
- Stream processing patterns
- Distributed systems coordination
- Resource optimization techniques
- Real-time data visualization
- Container orchestration
- Schema evolution strategies
- Data transformation pipelines

## 🔮 Future Enhancements

- [ ] Add data quality monitoring with Great Expectations
- [ ] Implement dead letter queue for failed records
- [ ] Add Prometheus + Grafana for infrastructure monitoring
- [ ] Set up CI/CD pipeline with GitHub Actions
- [ ] Add data lake layer (MinIO/S3) for long-term storage
- [ ] Implement authentication and security (SASL/SSL)
- [ ] Scale to multi-node Kafka and Cassandra clusters
- [ ] Add machine learning predictions on streaming data

## 📚 References

- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Apache Spark Streaming Guide](https://spark.apache.org/docs/latest/streaming-programming-guide.html)
- [Cassandra Data Modeling](https://cassandra.apache.org/doc/latest/data_modeling/)
- [Grafana Tutorials](https://grafana.com/tutorials/)
- [Apache Airflow Guide](https://airflow.apache.org/docs/)

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 👤 Author

**Your Name**
- LinkedIn: [Elmehdi Elkaissouni](https://www.linkedin.com/in/elmehdi-elkaissouni-234172264/)
- GitHub: [@mehdikai](https://github.com/mehdikai)
- Email: elkaissounielmehdi@gmail.com

## 🙏 Acknowledgments

- [randomuser.me](https://randomuser.me/) for providing the free API
- [Confluent Platform](https://www.confluent.io/) for Kafka ecosystem
- [Apache Software Foundation](https://apache.org/) for open-source tools
- Data Engineering community for inspiration and support

---

⭐ **If you found this project helpful, please give it a star!** ⭐

**Built with ❤️ by EL Mehdi**
