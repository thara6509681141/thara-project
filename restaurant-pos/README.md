# restaurant-pos

## Overview
This project is a **web-based Point of Sale (POS) system** designed for restaurant management. Built with **Spring Boot** for the backend and **MySQL** as the database, this system helps streamline ordering, inventory management, and sales tracking. It includes a user interface for restaurant employees to place orders and track sales, as well as admin features for managing menu items and employee profiles.

## Features

- **Employee Login**: Allows employees to log in to the POS system by entering their Work ID and password. If login is successful, the system records the employee’s ID and start time; otherwise, it prompts for re-entry.

- **Add Food Item to Order**: Employees can add a food item to the order. The system checks the stock; if available, the item is added to the order list, but if out of stock, an "Out of Stock" message is displayed.

- **Calculate Total Amount**: Calculates the total amount for an order by summing the prices of selected food items and promotions. If a membership ID is provided, the system applies a discount based on the membership level, then displays the final amount due.

- **Calculate Discount**: Determines the total discount for an order based on the selected items and promotions. The discount is then applied to the order's total amount.

- **Process Payment**: After selecting items and any applicable discounts, the employee enters the payment method, dine-in or takeout option, and the amount paid. The system records these details in the receipt, calculates any change due, and finalizes the transaction.

- **Print Receipt**: Generates a printable receipt by formatting all transaction details for easy printing.

- **Register New Member**: Allows customers to register as members by providing their name, password, ID number, phone number, and birth date. The system validates the information and creates a new member record if all details are correct.

- **Update Member Information**: Enables employees to update member details based on the provided member ID, phone number, or ID number, along with the member’s password. The system only saves valid updates.

- **Add Member Points**: Awards loyalty points to members based on their order total, calculated at 10% of the amount spent, and updates their points balance.

- **Restock Food Inventory**: Allows employees to restock food items by entering the item name and quantity. The system increases the stock level for the specified item accordingly.

- **Reduce Food Inventory**: Automatically reduces the stock level for each purchased item based on the quantities ordered.

- **Add Promotion**: Enables the addition of new promotions, including a promotion code, applicable items, promotional price, and expiration date. This promotion is then saved in the system for employee and customer use.

- **Display Promotions**: Lists all active promotions when requested by an employee and automatically removes any expired promotions.

- **Search Promotion**: Allows employees to search for a promotion by entering its code, displaying the details if the promotion is active.

- **Delete Promotion**: Removes a promotion by its code; if the system cannot locate the code, it displays a "Promotion not found" message.

## Installation Guide

### Prerequisites
- **MySQL**: Ensure MySQL is installed and running.
- **Java Development Kit (JDK)**: Install JDK 8 or higher.
- **Maven**: Install Maven to build and run the application.

### Database Setup
1. **Create the MySQL Database**:
   Open your MySQL CLI or Workbench, then create a new database:
   ```sql
   CREATE DATABASE restaurant_database;
   ```

1. **Create all Tables**:
   Open your MySQL CLI or Workbench, then create a new tables:
   ```sql
   CREATE TABLE Invoice (
        InvoiceNo INT(10) PRIMARY KEY AUTO_INCREMENT,
        Payment DOUBLE(10,2) NOT NULL,
        PaymentMethod VARCHAR(255) NOT NULL,
        DateTime DATETIME NOT NULL,
        TotalDiscount DOUBLE(10,2),
        NetPrice DOUBLE(10,2) NOT NULL,
        IsTakeHome BIT(1) NOT NULL,
        MemberID VARCHAR(10),
        i_change DOUBLE(10,2) NOT NULL
    );

    CREATE TABLE Member ( 
        m_id VARCHAR(10) PRIMARY KEY,
        m_password VARCHAR(255) NOT NULL,
        m_rank INT(10) NOT NULL,
        m_citizenId VARCHAR(13) UNIQUE NOT NULL,
        m_name VARCHAR(255) NOT NULL,
        m_points INT(10) NOT NULL,
        m_enroll DATETIME NOT NULL,
        m_birthdate DATE NOT NULL
    );

    CREATE TABLE Member_tel (
        m_id VARCHAR(10),
        m_tel VARCHAR(10),
        PRIMARY KEY (m_tel),
        FOREIGN KEY (m_id) REFERENCES Member(m_id)
    );

    CREATE TABLE InvoiceHaveMember (
        InvoiceNo INT(10),
        m_id VARCHAR(10),
        FOREIGN KEY (InvoiceNo) REFERENCES Invoice(InvoiceNo),
        FOREIGN KEY (m_id) REFERENCES Member(m_id)
    );

    CREATE TABLE Menu (
        unit VARCHAR(255) NOT NULL,
        foodname VARCHAR(255) PRIMARY KEY,
        amount INT(10) NOT NULL,
        price DOUBLE(5,2) NOT NULL
    );

    CREATE TABLE Promotion (
        Promotion_Name VARCHAR(255) NOT NULL,
        Promotion_Price DOUBLE(5,2) NOT NULL,
        Promotion_Code VARCHAR(20) PRIMARY KEY,
        Promotion_Expire DATETIME NOT NULL
    );

    CREATE TABLE OrderMenu (
        InvoiceNo INT(10),
        foodname VARCHAR(255),
        m_amount INT(10),
        FOREIGN KEY (InvoiceNo) REFERENCES Invoice(InvoiceNo),
        FOREIGN KEY (foodname) REFERENCES Menu(foodname)
    );
    CREATE TABLE OrderPromotion (
        InvoiceNo INT(10),
        Promotion_Code VARCHAR(20),
        p_amount INT(10),
        FOREIGN KEY (InvoiceNo) REFERENCES Invoice(InvoiceNo),
        FOREIGN KEY (Promotion_Code) REFERENCES Promotion(Promotion_Code)
    );

    CREATE TABLE MenuHavePromotion (
        foodname VARCHAR(255),
        Promotion_Code VARCHAR(20),
        Amount INT(10),
        FOREIGN KEY (foodname) REFERENCES Menu(foodname),
        FOREIGN KEY (Promotion_Code) REFERENCES Promotion(Promotion_Code)
    );

    CREATE TABLE Seller (
        s_workid VARCHAR(10) PRIMARY KEY,
        s_name VARCHAR(255) NOT NULL,
        s_password VARCHAR(255) NOT NULL,
        s_citizenID VARCHAR(13) NOT NULL UNIQUE,
        s_address TEXT NOT NULL,
        s_startDate DATETIME NOT NULL
    );

    CREATE TABLE Seller_tel (
        s_workid VARCHAR(10),
        s_tel VARCHAR(10),
        PRIMARY KEY (s_tel),
        FOREIGN KEY (s_workid) REFERENCES Seller(s_workid)
    );

    CREATE TABLE Checkin (
        s_workid VARCHAR(10),
        Date DATE,
        TimeIn TIME,
        TimeOut TIME,
        PRIMARY KEY (s_workid, Date),
        FOREIGN KEY (s_workid) REFERENCES Seller(s_workid)
    );
   ```

3. **Configure the `application.properties`**:
   Update `application.properties` in `src/main/resources/` with your database credentials and configuration:

   ```properties
   ## Spring DATASOURCE (DataSourceAutoConfiguration & DataSourceProperties)
   spring.datasource.url = jdbc:mysql://localhost:3306/restaurant_database
   spring.datasource.username = root
   spring.datasource.password = your_password_here

   ## Hibernate Properties
   spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQLDialect
   spring.jpa.hibernate.ddl-auto = update
   spring.jpa.show-sql = true
   spring.jpa.properties.hibernate.format_sql = true
   ```

### Running the Application
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/restaurant-pos-system.git
   cd restaurant-pos-system
   ```

2. **Build and Run**:
   Run the following command to start the application:
   ```bash
   ./mvnw spring-boot:run
   ```

3. **Access the Application**:
   Open your browser and navigate to `http://localhost:8080`.

## Technologies Used

### Infrastructure:
- **Operating System**: Ubuntu Server 24.04 LTS
- **Environment**: Docker 24.0.7
- **Cloud Service**: Azure Portal

### Frontend:
- **Frontend Framework**: Vanilla JavaScript

### Backend:
- **Backend Framework**: Spring Boot
- **Diagramming Tool**: Draw.io


### Database Designer and SQL Designer:
- **DBMS**: MySQL
- **Database Diagramming Tool**: Draw.io

### UX/UI Designer:
- **Design Tool**: Figma

### Project Management:
- **Version Control**: GitHub
- **Project Management Tool**: Notion, Figma, Google Drive
- **Communication Tool**: Line
- **Concurrent Tool**: Microsoft Teams

### Documentation:
- **Document Tool**: Microsoft Word
- **Presentation Tool**: Canva

### Additional Tools:
- **CI/CD**: Jenkins or GitLab CI (Optional for continuous integration/deployment)
- **Testing**: JUnit for backend testing, Postman for API testing
- **Containerization**: Docker for environment management
- **Code Quality**: SonarQube or ESLint for code quality checks
- **API Documentation**: Swagger for API documentation
- **Logging & Monitoring**: ELK Stack (Elasticsearch, Logstash, Kibana) for logs and Prometheus/Grafana for monitoring

## License
This project is licensed under the MIT License. See the LICENSE file for more details.

## Acknowledgements
- **Spring Boot Documentation**: For guidance on building REST APIs.
- **Figma Community**: For design resources and inspirations.
- **MySQL Documentation**: For database setup and configurations.

## Team Members

- **Kittithara Sutthaphirom** – Scrum Master, Product Owner, Configuration Manager
- **Chayatorn Prukrattananapa** – Nanny
- **Thapanee Chaiprapha** – UX/UI Designer
- **Teeraphat Siritham** – Database Designer, SQL Designer
- **Praphon Khaosaard** – Database Designer, SQL Designer
- **Pongsatorn Sripli** – Frontend Developer
- **Phuripat Kongsakban** – Infrastructure Manager
- **Ananyata Thangthong** – Nanny
- **Thara Sritharadol** – Backend Developer, Architect

## Screenshots


