---
sidebar_label: 'Installation'
---

# Installation

The Asysco AMT ACS Integration requires an OpCon license that includes the ACS Agent type.

The software can be downloaded using the OpCon Web Installer (OWI).

Once the software is downloaded, the contents can be extracted from the .zip file and placed in the **ProgramData\SAM\plugins** directory of the OpCon server or the 
**ProgramData\Relay\plugins** directory of the Relay component.

The ACS component within SMANetCom or Relay detects the Asysco AMT dll and registers the integration with OpCon.

Once registered with opCon the Asysco AMT connection can be configured and tasks can be defined.

If the files already exist in the **plugins** directory, the service must be stopped before the files can be replaced.
 
