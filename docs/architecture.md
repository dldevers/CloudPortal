# CloudPortal Architecture

## Purpose

CloudPortal provides a stable operational interface between applications and infrastructure providers.

Applications describe the capacity and operational behavior they require. CloudPortal determines how a provider should satisfy that request.

This separation allows infrastructure implementations to change without requiring application teams to redesign their deployment model.

## Architectural Layers

### Application Layer

The application layer declares desired capacity using Resource Classes.

Applications do not select:

- Individual hosts
- Kubernetes node names
- Cloud instance types
- Provider-specific storage products
- Provider-specific network implementations

### CloudPortal Control Plane

The control plane is responsible for:

- Accepting resource requests
- Validating requests
- Resolving Resource Classes
- Selecting an eligible provider
- Dispatching provider operations
- Tracking desired and observed state
- Reporting operation status
- Exposing operational workflows

### Provider Layer

Providers translate CloudPortal resource requests into provider-specific operations.

Examples may include:

- Kubernetes
- Bare-metal infrastructure
- Virtual-machine platforms
- Amazon EKS
- Azure AKS
- Google GKE

A provider may use any implementation capable of satisfying the requested Resource Class contract.

### Infrastructure Layer

The infrastructure layer contains the actual implementation resources:

- Compute hardware
- Virtual machines
- Kubernetes clusters
- Storage devices
- Network equipment
- Managed cloud services

CloudPortal does not expose these implementation details as the primary application interface.

## Desired-State Flow

A basic CloudPortal operation follows this sequence:

1. An application submits a resource request.
2. CloudPortal validates the request.
3. CloudPortal resolves the requested Resource Classes.
4. CloudPortal selects an eligible provider.
5. The provider translates the request into implementation-specific operations.
6. The provider reconciles the requested state.
7. CloudPortal records and reports observed state.

## Initial Vertical Slice

The first implementation will prove the architecture using one workload and one Kubernetes-backed provider.

The goal is not to build a complete portal immediately. The goal is to prove that an application can request capacity without depending directly on Kubernetes concepts.

## Architectural Boundaries

CloudPortal owns:

- Resource Class definitions
- Provider contracts
- Request validation
- Provider selection
- State reporting
- Operational workflows

Providers own:

- Implementation-specific translation
- Infrastructure API interaction
- Reconciliation details
- Provider-specific health information

Applications own:

- Workload definitions
- Declared capacity requirements
- Application configuration
- Application-level health behavior
