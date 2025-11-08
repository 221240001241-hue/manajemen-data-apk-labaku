# SSD (Software System Design) - Labaku Aplikasi Pencatatan Keuangan UMKM

## Pendahuluan

Dokumen ini menjelaskan desain sistem perangkat lunak (SSD) untuk aplikasi "Labaku - Aplikasi Pencatatan Keuangan UMKM". Dokumen ini merinci arsitektur, komponen-komponen kunci, alur data, serta pertimbangan teknis lainnya yang akan memandu implementasi aplikasi menggunakan Flutter dan teknologi pendukung lainnya.

## 1. Arsitektur Umum (High-Level Architecture)

Aplikasi Labaku akan mengadopsi arsitektur berbasis klien tunggal (single-client architecture) yang berfokus pada pengalaman offline-first. Data utama akan disimpan secara lokal di perangkat pengguna. Komunikasi dengan backend (jika ada di masa depan untuk sinkronisasi/backup) akan bersifat opsional dan asinkron.

```mermaid
graph TD
    A[Pengguna] --> B[Aplikasi Labaku (Flutter)]
    B --> C[Lapisan UI/Presentation - Widget Tree]
    B --> D[Lapisan Bisnis/Domain - Services, Use Cases]
    B --> E[Lapisan Data - Repository, Drift Database]
    E --> F[Drift (SQLite)]
    E -- (Opsional) --> G[Backend Server / Cloud Storage (API)]
    G --> H[Cloud Database]

    subgraph Aplikasi Labaku
        C
        D
        E
    end
---
classDiagram
    class User {
        +UUID id
        +String username
        +String hashedPassword
        +String email
        +String fullName
        +DateTime registrationDate
        +DateTime lastLoginDate
    }

    class Category {
        +UUID id
        +UUID userId
        +String name
        +CategoryType type
        +bool isDefault
        +DateTime createdAt
        +DateTime updatedAt
    }

    class Transaction {
        +UUID id
        +UUID userId
        +UUID categoryId
        +TransactionType type
        +double amount
        +String description
        +DateTime transactionDate
        +DateTime createdAt
        +DateTime updatedAt
    }

    User "1" *-- "N" Category : has
    User "1" *-- "N" Transaction : records
    Category "1" *-- "N" Transaction : categorizes
---
graph LR
    UserInterface[Widgets/Pages] -->|Uses| Router[go_router]
    UserInterface -->|Interacts with| Providers[Riverpod Providers]
    Providers -->|Invokes| Services[Business Services/Use Cases]
    Services -->|Uses| Repositories[Data Repositories]
    Repositories -->|Queries/Stores| Drift[Drift Database]
    Drift -->|Manages| SQLite[SQLite File]

    Freezed[Freezed Models] --> Services
    Freezed --> Repositories
    Freezed --> Drift

    fpdart[fpdart Utilities] --> Services
    fpdart --> Repositories

    Logger[Logger] --> Services
    Logger --> Repositories
---
graph LR
    UserInterface[Widgets/Pages] -->|Uses| Router[go_router]
    UserInterface -->|Interacts with| Providers[Riverpod Providers]
    Providers -->|Invokes| Services[Business Services/Use Cases]
    Services -->|Uses| Repositories[Data Repositories]
    Repositories -->|Queries/Stores| Drift[Drift Database]
    Drift -->|Manages| SQLite[SQLite File]

    Freezed[Freezed Models] --> Services
    Freezed --> Repositories
    Freezed --> Drift

    fpdart[fpdart Utilities] --> Services
    fpdart --> Repositories

    Logger[Logger] --> Services
    Logger --> Repositories
