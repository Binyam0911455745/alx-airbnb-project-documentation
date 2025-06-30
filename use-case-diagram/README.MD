# Online Accommodation Booking System

---

## Overview

This project outlines the design of an online accommodation booking system, covering key functionalities from user management to property listings, bookings, payments, and reviews. This `README.md` details the initial steps taken to design the system's **Use Case Diagram** using Draw.io, based on identified actors and their interactions with the system.

## Design Phase: Use Case Diagram

The first part of our solution involved designing a comprehensive Use Case Diagram. This diagram visually represents the interactions between users (actors) and the system, illustrating the different functionalities (use cases) provided by the booking platform.

### Actors Identified

The following primary actors have been identified for the system:

* **Guest**: Individuals who search for, view, book, and review properties.
* **Host**: Users who list their properties, manage bookings, and receive payouts.
* **Admin**: System administrators responsible for managing users, listings, bookings, and payments.
* **Payment Gateway**: An external system crucial for processing all financial transactions.

### Key Use Cases

Based on the system's features, the following key use cases have been defined and are represented in the Use Case Diagram:

#### User Management
* **Register Account**: Allows both Guests and Hosts to create new accounts.
* **Log In**: Enables all actors to access their respective accounts.
* **Manage Profile**: Allows Guests, Hosts, and Admins to update their personal information.

#### Property Listings Management
* **Add Listing**: Hosts can add new properties for rent.
* **Edit Listing**: Hosts can modify details of their existing properties.
* **Delete Listing**: Hosts can remove their properties from the system.

#### Search and Filtering
* **Search Properties**: Guests can search for accommodations based on various criteria.
* **Filter Properties**: Guests can refine their search results using filters.
* **View Property Details**: Guests can view detailed information about a specific property.

#### Booking Management
* **Book Property**: Guests can reserve an accommodation.
* **Cancel Booking**: Guests can cancel an existing booking.
* **View Booking Status**: Guests and Hosts can check the status of their bookings.

#### Payment Integration
* **Process Payment**: Handles the financial transaction for bookings (involving the Payment Gateway).
* **Receive Payout**: Hosts receive funds for their bookings.

#### Reviews and Ratings
* **Leave Review**: Guests can submit reviews and ratings for properties they've stayed in.
* **Respond to Review**: Hosts can respond to reviews left on their properties.

#### Notifications System
* **Receive Notification**: Users receive alerts and updates from the system.

#### Admin Dashboard
* **Manage Users**: Admin can oversee and modify user accounts.
* **Manage Listings**: Admin can manage all property listings.
* **Manage Bookings**: Admin can manage all bookings within the system.
* **Manage Payments**: Admin can oversee and manage payment processes.

---

## How to Access the Diagram

The Use Case Diagram for this project was designed using **Draw.io**. You can typically find the `.drawio` file (or an exported image format like PNG/SVG) within the project's documentation or design assets folder.

---

## Next Steps

This initial design phase provides a solid foundation for the system's development. Future steps will involve delving into more detailed design aspects, such as class diagrams, sequence diagrams, and ultimately, implementation.
