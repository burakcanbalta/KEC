# KEC Card Access Devices

## 1) Introduction: KEC and EKDS Overview

**KEC (Card Access Device)** is a specialized smart card terminal used in Turkey’s **Electronic Identity Verification System (EKDS)** to perform secure, on-site authentication with the new national ID cards. It contains a **GEM (Secure Element Module)** that enables controlled access to sensitive areas of the ID card (such as personal messages or biometric-protected zones) that are inaccessible to standard readers.

**EKDS** leverages the cryptographic and optional **biometric** features of the ID card for secure authentication and transaction logging. The ecosystem follows national standards:

- **TS 13582** — General overview  
- **TS 13583** — Interfaces and features  
- **TS 13584** — Security features  
- **TS 13585** — KEC application software requirements  

KEC embedded firmware has been evaluated against the **Common Criteria “KEC Firmware Protection Profile (v1.0)”**.  
Primary deployment contexts include GSM operator branches, land registry/notary services, healthcare institutions, and other regulated sectors.

---

## 2) Core Components and Protocols

**Hardware:**  
Smart card reader module (ISO 7816 / NFC), **Secure Element (GEM)**, secure clock (RTC), tamper sensors, PIN pad/display, optional fingerprint sensor, USB/serial/Ethernet interfaces.

**Firmware & Software:**  
Secure boot, signed firmware updates, KEC application layer, PC/SC & CCID drivers, management/monitoring agents.

**Cryptography & Protocols:**  
**PACE/EAC**-based access control (BSI TR‑03110), PKI, TLS/mTLS, APDU-based command/response with strict access rules.

**Biometrics:**  
Compliant with ISO/IEC 30107 for Presentation Attack Detection (PAD), supporting liveness detection for fingerprints or facial recognition based on policy.

---

## 3) Attack Surface Map

1. **Hardware:** Tamper sensors, debug interfaces (UART/JTAG), bootloader security.  
2. **Firmware:** Secure boot enforcement, update verification, key management.  
3. **Secure Element:** PIN/key provisioning, key storage protection.  
4. **Card Interaction (APDU/State Machine):** PACE/EAC implementation and state transitions.  
5. **Host & Drivers:** PC/SC, CCID, PKCS#11, SDK libraries.  
6. **Network & Protocols:** TLS/mTLS, certificate validation, time synchronization.  
7. **Backend Services:** API security, session binding, audit logging.  
8. **User Interface & Processes:** Personal message verification, operator actions.  
9. **Supply Chain & Lifecycle:** Manufacturing, shipping, deployment, decommissioning.

---

## 4) Vulnerability Classes by Lifecycle Stage

### 4.1 Design Phase
- Weak cryptographic policies, outdated algorithms.  
- Missing mutual authentication between KEC and backend.  
- Inadequate secure clock/RTC protection.  
**Mitigation:** Use modern cryptography, align with TR‑03110, enforce data minimization.

### 4.2 Manufacturing & Provisioning
- Insecure key injection and PIN loading processes.  
- Poor calibration of tamper protection.  
**Mitigation:** HSM-managed key provisioning, four-eyes principle, secure logging.

### 4.3 Logistics & Storage
- Supply chain risks (hardware implants, malicious firmware).  
**Mitigation:** Chain-of-custody controls, random acceptance testing.

### 4.4 Deployment & Integration
- Default credentials, open services.  
- Missing mTLS or certificate pinning.  
**Mitigation:** mTLS + pinning, locked-down kiosk mode, MDM enforcement.

### 4.5 Operational
- Unsigned or downgradeable firmware updates.  
- APDU state machine errors.  
**Mitigation:** Anti-rollback enforcement, signed logging, PAD compliance.

### 4.6 Maintenance
- Weak authentication for remote management.  
**Mitigation:** Time-bound access, secure logging.

### 4.7 Decommissioning
- Failure to zeroize sensitive keys.  
**Mitigation:** Secure erase and physical destruction.

---

## 5) Attack Vectors and Common Weaknesses

### 5.1 Physical/Hardware
- Tamper bypass, fault injection, side-channel attacks.  
**Defense:** Epoxy filling, shielding, zeroization triggers.

### 5.2 Firmware
- Missing secure boot validation, parser vulnerabilities.  
**Defense:** Signed, versioned updates, fuzz testing.

### 5.3 Secure Element
- Weak PIN/key loading policies, RNG weaknesses.  
**Defense:** Certified SE, rate-limiting, lockout mechanisms.

### 5.4 Protocol-Level (PACE/EAC)
- Downgrade attacks, incomplete terminal authorization checks.  
**Defense:** TR‑03110 compliance testing, negative testing.

### 5.5 Host & Drivers
- DLL hijacking, unsigned drivers.  
**Defense:** Code-signing enforcement, application whitelisting.

### 5.6 Network Security
- Missing mTLS, poor certificate validation.  
**Defense:** TLS hardening, certificate pinning, short-lived certs.

### 5.7 Backend
- Weak transaction binding, replay attack risk.  
**Defense:** Nonce and timestamp enforcement, signed logs.

### 5.8 User Interface & Social Engineering
- Ignored personal message screens, weak PAD.  
**Defense:** Operator training, dual-channel verification.

---

## 6) Example Threat Scenarios (High-Level)

1. **Weak TLS Validation → MITM → Transaction Manipulation**  
Mitigation: mTLS, pinning, signed transaction binding.

2. **Unsigned/Downgraded Firmware → Security Bypass**  
Mitigation: Secure boot, version counters, anti-rollback.

3. **APDU State Machine Flaw → Unauthorized Data Access**  
Mitigation: Formal verification, TR‑03110 protocol testing.

4. **Host SDK Exploit → Privilege Escalation**  
Mitigation: Least privilege, EDR monitoring, sandboxing.

5. **PAD Deficiency → Biometric Spoofing**  
Mitigation: ISO/IEC 30107-3 certified PAD solutions.

---

## 7) Authorized Security Testing Plan

- **Preconditions:** Written authorization, defined scope, isolated lab environment.  
- **Steps:** Threat modeling → Configuration review → Network/TLS audit → Protocol compliance → Firmware update verification → Driver/SDK security tests → Privacy impact review → PAD certification checks.  
- **Boundaries:** No physical destruction, no personal data access, no live production impact.

---

## 8) Hardening Checklist

- Secure Boot + anti-rollback + closed debug interfaces.  
- Modern cryptography; keys stored in SE/HSM.  
- Signed, version-controlled updates.  
- Strict mTLS + certificate pinning.  
- Application whitelisting on host systems.  
- TR‑03110 protocol compliance testing.  
- PAD certification for biometric modules.  
- Signed, privacy-compliant logging.

---

## 9) Indicators of Compromise (IoC)

- TLS or certificate chain errors.  
- Unexpected firmware version changes.  
- Anomalous APDU sequences.  
- Spikes in failed PAD attempts.  
- Unusual IP sources in management interfaces.

---

## 10) References

- **TSE TS 13582‑13585** (National KEC standards)  
- **TÜBİTAK BİLGEM OKTEM** laboratory test scopes  
- **Common Criteria — KEC Firmware Protection Profile v1.0**  
- **BSI TR‑03110** (PACE/EAC protocols)  
- **ISO/IEC 30107‑3** (Biometric PAD evaluation)  
- **NVİ EKDS** definitions and public use cases
