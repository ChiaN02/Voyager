# Voyager

*Large-Scale and Multi-Structured Databases Project — Academic Year 2025/2026*

A travel journaling, discovery and recommendation platform for **travellers** and **hotel hosts**, built with **Spring Boot**, **MongoDB** and **Neo4j**. The data model and the architecture are designed for a Big Data context: NoSQL databases, a distributed setup and **BASE** semantics.

**Team:** Susanna Baldo, Chiara Nasoni, Vittoria Vellutini

## 📄 Documents

- [`Voyager_Report.pdf`](Voyager_Report.pdf): full project report (design, database structure, queries, performance and fault tests)
- [`Voyager_Presentation.pdf`](Voyager_Presentation.pdf): presentation slides

## 🔎 What it does

The application has two kinds of users:

1. **Travellers**: keep a journal of their trips, set their travel preferences and get recommendations for cities and hotels, based both on their own history and on travellers with similar habits. They can also find the hotels with the best quality-to-price ratio and see their travel habits, behavioural segment and trip frequency.
2. **Hosts**: add, update and delete their hotels, and analyse them: visibility gap, seasonal concentration, missing facilities and typical guest profile.

Everyone can search cities and hotels, see the top attractions of a city and find similar cities.

The dataset (about 90 MB) combines two public Kaggle datasets on European cities and hotels, plus 1000 travellers and 1000 hosts generated with Faker.

## 🗄️ Data model

| Database | Role |
|---|---|
| **MongoDB** (replica set) | Main store: cities, hotels, hosts, travellers and their trips |
| **Neo4j** | Graph of travellers, trips, hotels, cities and attractions, used for recommendations and similarity queries |

MongoDB is accessed with two consistency levels:

- **Strong** (hosts and travellers): reads from the primary, read and write concern `majority`
- **Fast** (cities and hotels): reads from the secondaries, write concern `w:1`, so data is eventually consistent

Every change in MongoDB is propagated to the Neo4j graph, and statistics on hotels and trips are updated asynchronously.

## 📁 Project structure

```
Voyager/src/main/java/it/unipi/Voyager/
  controller/        REST endpoints (auth, cities, hotels, hosts, travellers, trips)
  service/           Business logic and analytics queries
  repository/        MongoDB repositories (fast, strong) and Neo4j repositories
  model/             MongoDB documents and Neo4j nodes and relationships
  config/            Database setup, consistency policies, data ingestion, Neo4j sync, security
Voyager/src/main/resources/   Configuration and initial JSON datasets
Voyager/voyager_snapshot/     MongoDB dump of the dataset
Voyager_Report.pdf            Project report
Voyager_Presentation.pdf      Presentation slides
```

## 🚀 Getting started

Requirements: Java 21, a MongoDB replica set and Neo4j with the APOC plugin.

1. Restore the dataset:
   ```bash
   mongorestore --uri "mongodb://localhost:27017" Voyager/voyager_snapshot
   ```
2. Set the MongoDB and Neo4j connections in `Voyager/src/main/resources/application.properties`.
3. Start the application:
   ```bash
   cd Voyager
   ./mvnw spring-boot:run
   ```
4. The API documentation is available at `http://localhost:8080/swagger-ui.html`.
