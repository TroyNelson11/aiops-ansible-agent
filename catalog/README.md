# Workbench Catalog Guide

This directory contains the `mapping.yml` used by both the AI Agent and the Ansible Provisioning Playbook.

## ID Naming Convention
IDs are constructed as: `[image_type]_[size]`
- **Sizes:**
  - `small`: 1 CPU | 2Gi RAM (Introductory labs)
  - `medium`: 2 CPU | 4Gi RAM (Standard coursework)
  - `large`: 4 CPU | 8Gi RAM (Heavy computation)
  - `gpu`: 4 CPU | 16Gi RAM | 1 NVIDIA GPU (Deep Learning)

## Image Streams
We utilize the internal OpenShift registry:
`image-registry.openshift-image-registry.svc:5000/redhat-ods-applications/...`
