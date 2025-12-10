# Movie Booking System

A comprehensive movie ticket booking platform built with Spring Boot, featuring real-time seat availability, booking management, and payment integration.

## 🎯 Project Overview

This RESTful API-based movie booking system enables users to browse movies, check show timings, book seats, and process payments. The system handles concurrent booking requests with proper locking mechanisms to prevent double-booking scenarios.

## ✨ Key Features

- **Movie Management**: Browse movies, view details, showtimes, and theaters
- **Real-Time Seat Availability**: Check available seats with instant updates
- **Secure Booking System**: Pessimistic locking to prevent race conditions during high-traffic bookings
- **Payment Processing**: Integrated payment workflow with transaction management
- **Booking History**: Track user bookings and transaction history
- **RESTful API Design**: Clean, intuitive endpoints following REST principles
- **Robust Error Handling**: Comprehensive exception handling and validation

## 🏗️ System Architecture

```
┌──────────────┐
│   Client     │
│ (Postman/UI) │
└──────┬───────┘
       │
       │ HTTP Requests
       │
┌──────▼───────────────────────────────────┐
│         Spring Boot Application          │
│  ┌────────────────────────────────────┐  │
│  │      REST Controllers              │  │
│  │  - MovieController                 │  │
│  │  - BookingController               │  │
│  │  - PaymentController               │  │
│  └────────────┬───────────────────────┘  │
│               │                           │
│  ┌────────────▼───────────────────────┐  │
│  │      Service Layer                 │  │
│  │  - Business Logic                  │  │
│  │  - Transaction Management          │  │
│  │  - Locking Mechanisms              │  │
│  └────────────┬───────────────────────┘  │
│               │                           │
│  ┌────────────▼───────────────────────┐  │
│  │      Repository Layer (JPA)        │  │
│  │  - Data Access Objects             │  │
│  └────────────┬───────────────────────┘  │
└───────────────┼───────────────────────────┘
                │
        ┌───────▼────────┐
        │  MySQL Database│
        │  - Movies      │
        │  - Shows       │
        │  - Bookings    │
        │  - Payments    │
        └────────────────┘
```

## 🛠️ Technology Stack

- **Backend**: Spring Boot 2.7+
- **ORM**: Spring Data JPA / Hibernate
- **Database**: MySQL 8.0
- **Build Tool**: Maven
- **Testing**: JUnit 5, Mockito
- **API Testing**: Postman
- **Validation**: Spring Boot Validation
- **Logging**: SLF4J with Logback

## 📋 Prerequisites

- Java 11 or higher
- Maven 3.6+
- MySQL 8.0+
- Postman (for API testing)
- IDE (IntelliJ IDEA / Eclipse / VS Code)

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/DeepaliVerma011/Movie_Booking.git
cd Movie_Booking
```

### 2. Setup Database
```sql
-- Create database
CREATE DATABASE movie_booking;

-- Use the database
USE movie_booking;

-- Tables will be auto-created by Hibernate
```

### 3. Configure Database Connection
Update `src/main/resources/application.properties`:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/movie_booking
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### 4. Build the Project
```bash
mvn clean install
```

### 5. Run the Application
```bash
mvn spring-boot:run
```

The application will start on `http://localhost:8080`

## 📡 API Endpoints

### Movie Endpoints

#### Get All Movies
```http
GET /api/movies
```
**Response:**
```json
[
  {
    "id": 1,
    "title": "Inception",
    "description": "A mind-bending thriller",
    "genre": "Sci-Fi",
    "duration": 148,
    "rating": "PG-13",
    "releaseDate": "2010-07-16"
  }
]
```

#### Get Movie by ID
```http
GET /api/movies/{movieId}
```

#### Add New Movie (Admin)
```http
POST /api/movies
Content-Type: application/json

{
  "title": "The Matrix",
  "description": "A computer hacker learns about the true nature of reality",
  "genre": "Sci-Fi",
  "duration": 136,
  "rating": "R",
  "releaseDate": "1999-03-31"
}
```

#### Update Movie (Admin)
```http
PUT /api/movies/{movieId}
Content-Type: application/json

{
  "title": "The Matrix Reloaded",
  "description": "Updated description",
  "genre": "Sci-Fi",
  "duration": 138,
  "rating": "R"
}
```

#### Delete Movie (Admin)
```http
DELETE /api/movies/{movieId}
```

### Show Endpoints

#### Get All Shows for a Movie
```http
GET /api/shows/movie/{movieId}
```

#### Get Show by ID
```http
GET /api/shows/{showId}
```

#### Create Show (Admin)
```http
POST /api/shows
Content-Type: application/json

{
  "movieId": 1,
  "theaterId": 1,
  "showTime": "2024-12-15T18:30:00",
  "price": 250.00,
  "totalSeats": 100,
  "availableSeats": 100
}
```

### Booking Endpoints

#### Create Booking
```http
POST /api/bookings
Content-Type: application/json

{
  "userId": 1,
  "showId": 1,
  "numberOfSeats": 2,
  "seatNumbers": ["A1", "A2"]
}
```

**Response:**
```json
{
  "bookingId": 1,
  "userId": 1,
  "showId": 1,
  "numberOfSeats": 2,
  "seatNumbers": ["A1", "A2"],
  "totalAmount": 500.00,
  "bookingStatus": "CONFIRMED",
  "bookingTime": "2024-12-11T14:30:00"
}
```

#### Get Booking by ID
```http
GET /api/bookings/{bookingId}
```

#### Get User Bookings
```http
GET /api/bookings/user/{userId}
```

#### Cancel Booking
```http
DELETE /api/bookings/{bookingId}
```

### Payment Endpoints

#### Process Payment
```http
POST /api/payments
Content-Type: application/json

{
  "bookingId": 1,
  "amount": 500.00,
  "paymentMethod": "CREDIT_CARD",
  "cardDetails": {
    "cardNumber": "4111111111111111",
    "cvv": "123",
    "expiryDate": "12/25"
  }
}
```

#### Get Payment Status
```http
GET /api/payments/{paymentId}
```

## 📊 Database Schema

### Movies Table
```sql
CREATE TABLE movies (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    genre VARCHAR(100),
    duration INT,
    rating VARCHAR(10),
    release_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Shows Table
```sql
CREATE TABLE shows (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    movie_id BIGINT,
    theater_id BIGINT,
    show_time DATETIME,
    price DECIMAL(10,2),
    total_seats INT,
    available_seats INT,
    FOREIGN KEY (movie_id) REFERENCES movies(id)
);
```

### Bookings Table
```sql
CREATE TABLE bookings (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT,
    show_id BIGINT,
    number_of_seats INT,
    seat_numbers VARCHAR(255),
    total_amount DECIMAL(10,2),
    booking_status VARCHAR(50),
    booking_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (show_id) REFERENCES shows(id)
);
```

### Payments Table
```sql
CREATE TABLE payments (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    booking_id BIGINT,
    amount DECIMAL(10,2),
    payment_method VARCHAR(50),
    payment_status VARCHAR(50),
    transaction_id VARCHAR(255),
    payment_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (booking_id) REFERENCES bookings(id)
);
```

## 🧪 Testing

### Running Tests
```bash
mvn test
```

### Test Coverage
- Unit tests for service layer logic
- Integration tests for repository layer
- Controller tests with MockMvc

### Manual Testing with Postman

1. **Import Postman Collection** (if available in repo)
2. **Test Flow:**
   - Get all movies
   - Get shows for a movie
   - Create a booking
   - Process payment
   - Get booking details

## 📂 Project Structure

```
Movie_Booking/
├── src/
│   ├── main/
│   │   ├── java/com/moviebooking/
│   │   │   ├── controller/
│   │   │   │   ├── MovieController.java
│   │   │   │   ├── BookingController.java
│   │   │   │   └── PaymentController.java
│   │   │   ├── service/
│   │   │   │   ├── MovieService.java
│   │   │   │   ├── BookingService.java
│   │   │   │   └── PaymentService.java
│   │   │   ├── repository/
│   │   │   │   ├── MovieRepository.java
│   │   │   │   ├── BookingRepository.java
│   │   │   │   └── PaymentRepository.java
│   │   │   ├── model/
│   │   │   │   ├── Movie.java
│   │   │   │   ├── Show.java
│   │   │   │   ├── Booking.java
│   │   │   │   └── Payment.java
│   │   │   ├── dto/
│   │   │   ├── exception/
│   │   │   └── config/
│   │   └── resources/
│   │       ├── application.properties
│   │       └── data.sql (optional seed data)
│   └── test/
│       └── java/com/moviebooking/
│           ├── service/
│           ├── controller/
│           └── repository/
├── pom.xml
└── README.md
```

## 🎓 Key Concepts Demonstrated

- **RESTful API Design**: Standard HTTP methods and status codes
- **JPA/Hibernate**: Object-Relational Mapping and database operations
- **Transaction Management**: ACID properties for booking and payment operations
- **Pessimistic Locking**: Preventing race conditions during concurrent bookings
- **Exception Handling**: Global exception handler with custom exceptions
- **DTO Pattern**: Separating API models from database entities
- **Validation**: Input validation using Spring Boot Validation
- **Layered Architecture**: Controller → Service → Repository pattern

## 🔧 Configuration Options

### Application Properties
```properties
# Server Configuration
server.port=8080

# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/movie_booking
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA Configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Logging
logging.level.org.springframework=INFO
logging.level.com.moviebooking=DEBUG
```

## 🚧 Future Enhancements

- [ ] Add user authentication and authorization (Spring Security + JWT)
- [ ] Implement seat layout visualization
- [ ] Add email/SMS notifications for booking confirmations
- [ ] Integrate real payment gateways (Razorpay/Stripe)
- [ ] Add movie recommendations based on user preferences
- [ ] Implement Redis caching for frequently accessed data
- [ ] Add admin dashboard for theater management
- [ ] Create React/Angular frontend
- [ ] Deploy to cloud (AWS/Azure)
- [ ] Add comprehensive API documentation with Swagger/OpenAPI

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📝 License

This project is licensed under the MIT License.

## 👤 Author

**Deepali Verma**
- LinkedIn: [linkedin.com/in/deepaliverma](https://www.linkedin.com/in/deepali-verma-1599241b6/)
- GitHub: [@DeepaliVerma011](https://github.com/DeepaliVerma011)
- Email: deepalivermasde@gmail.com

## 📞 Support

For questions or issues, please:
- Open an issue on GitHub
- Contact via email or LinkedIn

---
⭐ If you find this project useful, please consider giving it a star!
