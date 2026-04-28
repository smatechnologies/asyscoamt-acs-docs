---
title: Installation
description: "Install the AsyscoAMT ACS connector by downloading and placing the connector files in the correct directory on the OpCon server or Relay component."
tags:
  - Procedural
  - System Administrator
  - Installation
sidebar_label: 'Installation'
---

# Installation

**Theme:** Configure | **Audience:** System Administrator

## What is it?

The AsyscoAMT ACS connector is distributed as a .zip archive and installed by placing the extracted files in the OpCon plugins directory. The ACS component automatically detects and registers the connector when the service starts.

Install the AsyscoAMT ACS connector when:

- You are deploying the connector for the first time on an OpCon server or Relay component.
- You are upgrading an existing AsyscoAMT connector to a new version.

The AsyscoAMT ACS Integration requires an OpCon license that includes the ACS Agent type.

To install the AsyscoAMT ACS connector, complete the following steps:

1. Download the software using the OpCon Web Installer (OWI).
2. Extract the contents of the .zip file.
3. Copy the extracted files to the appropriate directory:
   - For an OpCon server: copy to `ProgramData\SAM\plugins`
   - For a Relay component: copy to `ProgramData\Relay\plugins`

**NOTE:** If the files already exist in the `plugins` directory, stop the service before replacing the files.

The ACS component within SMANetCom or Relay detects the AsyscoAMT connector and registers the integration with OpCon. Once registered with OpCon, you can configure the AsyscoAMT connection and define jobs.

## Glossary

**OWI (OpCon Web Installer)** — The web-based download tool used to obtain OpCon software packages, including ACS connectors.

**Plugins directory** — The file system location where ACS connectors are placed so the SMANetCom or Relay component can detect and load them. Located at `ProgramData\SAM\plugins` on an OpCon server or `ProgramData\Relay\plugins` on a Relay component.
