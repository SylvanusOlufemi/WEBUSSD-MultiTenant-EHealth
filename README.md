# 🏥 WEBUSSD Care: Multi-Tenant PHI Exchange API (SDG 3 & 9)
A secure, multi-tenant Flask application using USSD for Protected Health Information (PHI) exchange in low-bandwidth regions (SDG 3 &amp; 9).

## Project Overview

This project is a mission-critical backend service designed to facilitate **Protected Health Information (PHI) exchange** using the widely accessible **USSD (Unstructured Supplementary Service Data)** protocol. This architecture is vital for healthcare delivery in regions with low internet penetration or unreliable data access.

The core complexity lies in providing a **Multi-Tenant, secure service** capable of scaling while guaranteeing data isolation and integrity.

## ✨ Architectural Highlights

| Feature | Technical Implementation | Value Proposition |
| :--- | :--- | :--- |
| **Multi-Tenancy** | Database models (Patients, Visits) are partitioned by a mandatory `tenant_id` (ForeignKey), ensuring **strict data isolation** between hospitals and clinics. | Guarantees compliance and operational security for multiple organizations on a single application instance. |
| **Low-Bandwidth Resilience**| **Flask API** acts as the business logic handler, processing simple text inputs from the USSD gateway and returning structured text menus (`CON`/`END`). | Eliminates dependency on mobile data (3G/4G), enabling workers to access and submit PHI in **remote, off-grid locations**. |
| **Data Security (PHI)**| Health Workers are authenticated at **Level 1** via Phone Number + PIN, and authorization checks are performed on every subsequent request to prevent tenant crossover. | Addresses the fundamental security challenge of protecting sensitive patient data (SDG 3) in a non-web environment. |

## 📊 Key Functionality

The application supports a simple, menu-driven workflow tailored for field use:

1.  **Worker Authentication:** Worker enters a 4-digit PIN against their registered phone number.
2.  **Patient Fetch:** Worker requests a patient record using a human-readable ID (e.g., `PAT101`). The system validates the patient belongs to the worker's *tenant*.
3.  **Vitals Submission:** Worker submits PHI (Blood Pressure, Temperature, Pulse) via a simple delimited string (e.g., `120/80*37.5*72`). This creates an immutable `PatientVisit` record in the database.

## 🐍 Core Technology Stack

* **Backend Framework:** Flask (Python)
* **Database:** SQLAlchemy ORM (SQLite for demo; production-ready for PostgreSQL/MySQL)
* **Protocol:** USSD (via gateway simulation)

## 🚀 Execution Instructions

1.  **Dependencies:** `pip install Flask SQLAlchemy`
2.  **Run:** `python app.py` (This automatically runs the `setup_database()` function to create dummy tenants, workers, and patients).
3.  **Test Endpoint:** Simulate a USSD session request using cURL (replace the phone number with one from the dummy data, e.g., `+2348010000001`):

    ```bash
    # Start Session (Level 0)
    curl -X POST [http://127.0.0.1:5000/ussd](http://127.0.0.1:5000/ussd) -d "phoneNumber=+2348010000001&sessionId=123&text="
    
    # Send PIN (Level 1: PIN 1234)
    curl -X POST [http://127.0.0.1:5000/ussd](http://127.0.0.1:5000/ussd) -d "phoneNumber=+2348010000001&sessionId=123&text=1234"

    # Fetch Record for PAT101 (Level 2 & 3)
    curl -X POST [http://127.0.0.1:5000/ussd](http://127.0.0.1:5000/ussd) -d "phoneNumber=+2348010000001&sessionId=123&text=1234*1*PAT101"
    ```

---
*Active Member: Nigeria Computer Society (NCS ID: 18077)*
