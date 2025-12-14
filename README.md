# C2 Framework Evasion Toolkit

## Project Overview

The **C2 Framework Evasion Toolkit** is a specialized set of modules designed to enhance the stealth and resilience of Command and Control (C2) operations against modern endpoint detection and response (EDR) and network security solutions. This project focuses on two primary pillars of evasion: **Polymorphic Payload Generation** for signature-based defense bypass, and **Network Stealth** via advanced Domain Fronting techniques.

This toolkit is intended for authorized Red Team operations, penetration testing, and security research purposes only.

## Key Features

| Feature | Component | Description | Evasion Focus |
| :--- | :--- | :--- | :--- |
| **Polymorphic Payload Generation** | `GoLang-Payload-Generator` | Generates highly-obfuscated, fileless payloads. Utilizes Go's cross-compilation capabilities to produce small, statically-linked binaries. | Signature-based EDR, Static Analysis |
| **In-Memory Execution** | `CSharp-Implant` | The core agent is a C# assembly designed for in-memory execution. It avoids writing artifacts to disk and uses techniques like unhooking and API hashing. | Behavioral EDR, Disk Forensics |
| **Domain Fronting** | `README.md` (Concept) | Outlines the configuration and methodology for using Content Delivery Networks (CDNs) to obscure the true C2 server IP address, making network traffic appear benign. | Network Firewalls, Traffic Analysis |
| **Network Stealth** | `CSharp-Implant` | Implements custom communication protocols (e.g., HTTPS with custom headers) to blend in with normal enterprise traffic. | Network Intrusion Detection Systems (NIDS) |

## Architecture

The toolkit follows a two-stage architecture:

1.  **Payload Generation (GoLang):** A Red Team operator uses the Go-based generator to create a custom, obfuscated C# implant binary. This generator handles the initial stage of evasion.
2.  **Implant Execution (C#):** The C# implant is executed in-memory on the target system. It establishes a covert channel back to the C2 server using the pre-configured Domain Fronting mechanism.

### Technical Deep Dive: EDR Evasion

The `CSharp-Implant` incorporates several advanced techniques to bypass EDR solutions:

*   **API Hashing:** Instead of directly calling sensitive Windows API functions (e.g., `CreateRemoteThread`, `VirtualAllocEx`), the implant resolves their addresses at runtime by hashing the function names in memory. This bypasses simple API hook checks.
*   **Indirect Syscalls (Conceptual):** The design allows for the integration of indirect syscalls to completely bypass user-land hooks placed by EDRs, executing kernel-level functions directly.
*   **Junk Code Insertion:** The Go generator inserts random, non-functional code blocks into the C# binary to change its signature and confuse automated analysis tools.

## Setup and Usage Guide

### Prerequisites

*   GoLang (latest stable version) for the payload generator.
*   .NET Framework (4.0 or higher) for the C# implant compilation.
*   A configured C2 server (e.g., Cobalt Strike, Metasploit, or a custom server) with a Domain Fronting setup (e.g., using AWS CloudFront or Azure Front Door).

### 1. Configure Domain Fronting

The C2 server must be configured behind a CDN. The implant will connect to the CDN's domain, but use a specific `Host` header to route the traffic to the C2 server's actual domain.

*   **CDN Domain (Front Domain):** `https://example.cloudfront.net` (Benign-looking domain)
*   **C2 Domain (Host Header):** `c2.redteam-op.com` (Actual C2 server)

### 2. Generate the Payload

Navigate to the `src/GoLang-Payload-Generator` directory and run the generator.

\`\`\`bash
# Build the generator
go build -o payload_gen main.go

# Run the generator
./payload_gen --c2-host "c2.redteam-op.com" --front-domain "example.cloudfront.net" --output "implant.exe"
\`\`\`

This command compiles the C# source code into a binary, applies obfuscation and polymorphism, and embeds the necessary C2 configuration.

### 3. Deploy and Execute

The generated `implant.exe` is a highly evasive binary. It should be deployed using standard red team initial access vectors. Upon execution, the implant will:

1.  Perform EDR unhooking checks.
2.  Resolve API functions via hashing.
3.  Establish a secure, domain-fronted connection to the C2 server.

## Source Code Examples

Detailed source code for the GoLang generator and the C# implant can be found in the respective `src` subdirectories.

*   **GoLang Payload Generator:** Focuses on code obfuscation and embedding the C# payload.
*   **C# Implant:** Focuses on in-memory execution, API hashing, and custom network communication.

## Architecture Diagram

*(The architecture diagram will be generated and included in the next step, visually representing the flow from payload generation to C2 communication.)*

## Technologies Used

*   **GoLang:** Payload Generation, Obfuscation
*   **C#:** In-Memory Implant Development
*   **EDR Evasion:** API Hashing, Process Hollowing (Conceptual)
*   **Network Stealth:** Domain Fronting, Custom HTTPS Communication
*   **Security Research:** Adversarial Simulation, Red Teaming
