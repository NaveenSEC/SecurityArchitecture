## Security Architecture
- This include the Security Concepts, Designs, and Features

### Diagnostics with the UDS Service 29 “Authentication”
#### ISO14229 
- **ISO14229**: Unified Diagnostic Services (UDS), specifying the application layer requirements for automotive electronic control unit (ECU) diagnostics
- **Client-Server Model:** Operates with an external diagnostic tester as the client and the vehicle ECU as the server
- **OSI Layer Position:** Covers the Application Layer (Layer 7) and works alongside Session Layer (Layer 5) specifications
- **Consolidation:** Merges older legacy diagnostic protocols like KWP2000 (ISO 14230) and Diagnostics on CAN (ISO 15765) into a single manufacturer-independent standard

#### Core Functions & Services
- **Diagnostic Sessions:** Manages operational states (e.g., Default, Programming, and Extended Diagnostic Sessions)
- **Security Access:** Protects sensitive functions (like firmware flashing or calibration) via a seed-and-key authentication challenge
- **Fault Memory (DTCs):** Reads and clears Diagnostic Trouble Codes using services like 0x19 and 0x14
- **Data Read/Write:** Requests real-time sensor values, identification data, and parameter updates via Data Identifiers (DIDs)
- **Routine Control & Flashing:** Starts or stops onboard routines, controls I/O actuators, and handles memory uploads/downloads for firmware updates

#### Key Parts of the ISO 14229 Family
- **ISO 14229-1:** Application layer requirements (updated to Edition 4 in June 2026).
- **ISO 14229-2:** Session layer services and configuration.
- **ISO 14229-3 to -8:** Specialized implementation requirements for CAN (DoCAN), FlexRay, LIN, and Internet Protocol (DoIP)

<img width="1127" height="894" alt="image" src="https://github.com/user-attachments/assets/3fd24895-2531-4e82-a11a-19dcb3a55f9e" />
<img width="424" height="441" alt="image" src="https://github.com/user-attachments/assets/acd35abb-f35d-4b2a-9ea6-9f96dc6c6b23" />

### Motivation for 0x29
- Formely, diagnostics security was depended on Service 27 “Security Access”
- **Service 27 is breachable and it weakans the ECU**, and the vehicle eventually. But it came to an end, with Service 29 “Authentication”

### Why this replaced Service 0x27
- Service 0x27 - Seed-and-key scheme, usually with one secret algorithm or key shared across a whole ECU family
- Once someone reverse-engineers it from one ECU or one leaked tool, every vehicle with that ECU is open
- It has no notion of who the
  - tester is,
  - no roles,
  - no expiry,
  - no revocation and
  - no session keys
- Service 0x29 fixes each of these
- APCE in particular gives individual identities, scoped rights and revocable trust, and either approach can bootstrap session keys for protecting the traffic that follows

### Choices: APCE Vs ACR
  - APCE suits large tool fleets, aftermarket or independent workshop access, and anywhere you need roles or revocation
  - ACR suits closed environments such as end-of-line production, a single OEM tool, or ECUs too constrained for certificate parsing. Use the asymmetric flavour unless the symmetric keys are diversified per ECU.

### What is the Authentication Service?
- Service 29 in the Unified Diagnostic Services (UDS) protocol, known as “Authentication,” was added in a 2020 update to the standard
- Provides a more modern approach to authentication compared to the Security Access (0x27) service
- Service includes **bidirectional authentication with PKI-based Certificate Exchange**, offering a standardized method for secure communication and access control within automotive electronic systems
- Crucial during
  - Downloading
  - Uploading routines and data
  - Reading from specific memory in a server, where improper handling could harm vehicle components or compromise compliance standards
  - Prevent data security breaches when retrieving data from a server
  - **Service offers two security approaches**
  
 #### Two Security approaches
 - PKI Certificate Exchange
   - Based on **PKI certificate exchange using asymmetric cryptography**, employing
     - **Card Verifiable Certificate (CVC) (ISO 7816–8)** - **Relation to ISO/IEC 7816-8**
       -  Lightweight digital certificate designed specifically to be parsed and verified by resource-constrained devices, such as smart cards and ePassports, which have limited memory and processing power.
       -  Unlike heavy X.509 certificates that use complex ASN.1 parsing and dynamic trees, CVCs use a **straightforward Type–Length–Value (TLV) structure with fixed-length or max-length fields in a strictly defined order**.
       -  Compact Size: Much smaller footprint than standard X.509 certificates
       -  Fast Parsing: Fixed TLV fields allow chips to validate certificates without holding large nested structures in RAM.
     - **X.509 (ISO/IEC 9594–8, RFC 5280, RFC 5755, IEEE 1609.2) formats**.
 - Challenge-response procedure without PKI certificates
   - Utilizes a **challenge-response procedure without PKI certificates**, either through **asymmetric cryptography with software authentication tokens** or **symmetric cryptography**.

#### High-Level Concept - Service 29 security concepts
  <img width="720" height="394" alt="image" src="https://github.com/user-attachments/assets/fa618a3d-93a4-4805-82f1-1cb2990272a1" />

#### Low-Level Concept - Service 29 security concepts - Big picture: why Service 0x29 replaced 0x27
  <img width="712" height="597" alt="image" src="https://github.com/user-attachments/assets/7a5653fd-ec68-41c0-826a-1e0b18d2fdcc" />

### PKI Certificate Exchange
#### Authentication with PKI Certificate Exchange (APCE)
- PKI Challenge-response procedure without PKI certificates
- Several functions for authentication and certificate management
- Ending an authenticated state?
- Unidirectional and bidirectional authentication
- Proof of ownership (POWN) transmission
- Independent certificate transfer
- **Key prerequisites:** Client and server having respective certificates and private keys.
- **Key Response = Proof of Ownership**

<img width="720" height="846" alt="image" src="https://github.com/user-attachments/assets/c202ed14-5052-4d80-9d5f-8a41ffae1ae9" />

<img width="2040" height="3378" alt="uds_0x29_acr_ecdsa_p256_sequence" src="https://github.com/user-attachments/assets/4d9c6a75-a683-4f9d-abb6-4ee2af173908" />

The authentication process, depending on unidirectional or bidirectional needs, involves a series of steps including challenge creation, certificate verification, ephemeral key pair generation for session key establishment, and proof of ownership calculation

#### Authentication with Challenge-Response (ACR)
- Asymmetric cryptography with software authentication tokens

##### Bidirectional ACR exchange

<img width="662" height="475" alt="image" src="https://github.com/user-attachments/assets/6ee6ba24-eea9-4545-8722-56edf7b91d76" />



####### Symmetric cryptography

### Authentication with PKI Certificate Exchange (APCE) - Unidirectional Mode + Session Keys
<img width="2040" height="4212" alt="APCE_UNI_Session_keys" src="https://github.com/user-attachments/assets/c80d028f-586a-4619-8e8d-718f32cbb1ce" />

### Authentication with PKI Certificate Exchange (APCE) - Bidirectional Mode + Session Keys
<img width="2040" height="4272" alt="APCE_BI_Session_Keys" src="https://github.com/user-attachments/assets/c4abc454-c790-476f-9dad-534b19dc8e68" />

### Authentication with Challenge-Response (ACR) - Bidirectional Mode + Aymmetric
<img width="2040" height="3798" alt="ACR_BI_Asymmetric" src="https://github.com/user-attachments/assets/971e032a-9967-46e0-bc9a-c9cc9546deb4" />

### Authentication with Challenge-Response (ACR) - Unidirectional Mode + Symmetric
<img width="2040" height="3798" alt="ACR_Uni_Symmetric" src="https://github.com/user-attachments/assets/871fdd47-1e12-4a86-b5d7-0fa9275f522f" />

### Authentication with Challenge-Response (ACR) - Unidirectional Mode + Aymmetric
<img width="2040" height="4386" alt="ACR_BI_Symmetric" src="https://github.com/user-attachments/assets/390a6995-16bd-4742-96dd-e47698649088" />

### UDS_0x29_Variants_Comparison
<img width="3188" height="2340" alt="UDS_0x29_Variants_Comparison" src="https://github.com/user-attachments/assets/1a66256c-f0ea-4212-8db1-ff54d4757461" />

### What I learned
- UDS Service - 0x27 is Weak and Vulnerable
- UDS Service - 0x29 Authentication Service is more robust
- Types
  - Authentication with PKI Certificate Exchange (APCE) - Unidirectional or Bi-directional mode
    - Asymmetric
    - Types of Certificates
      - CVC ISO 7816-8
      - X.509 (ISO/IEC 9594–8, RFC 5280, RFC 5755, IEEE 1609.2)
  - Authentication with Challenge-Response (ACR) - Unidirectional or Bi-directional mode
    - Asymmetric
    - Symmetric
- Key Response = Proof of Ownership

### Reference
https://medium.com/@mkklyci/implementing-advanced-secure-diagnostics-with-uds-authentication-service-29h-9b1b9ec762ec



   

 

  

  
