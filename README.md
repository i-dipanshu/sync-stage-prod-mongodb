## Overview

This project involves the synchronization of MongoDB ReplicaSets between production and staging environments deployed as Kubernetes (K8s) stateful sets. It also included a Jenkins Job for manual syncing of staging database.

## Features

- Automated daily synchronization of data from staging to production.
- Manual triggering of data synchronization through a Jenkins job.
- Secure and reliable data transfer using K8s cronjobs.
- Scalable and easily configurable within a Kubernetes cluster.

## Prerequisites

- A Kubernetes cluster with Mongodb ReplicaSets configured.
- Jenkins for manual synchronization triggers.
- Google Cloud Storage for database backups.

## Usage

1. Create the K8s cronjob for automated synchronization.

2. Create a Jenkins job for manual synchronization triggers.

3. Customize the schedule and settings to fit your specific requirements.

## Configuration

You can modify the synchronization schedule, settings, and other parameters in the provided K8s cronjob and Jenkins job configurations to suit your needs.