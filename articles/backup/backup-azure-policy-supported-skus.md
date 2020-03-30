---
title: Az Azure Policy támogatott virtuálisgép-termékváltozatai
description: A támogatott vm-termékváltozatokat (a Publisher, a Image Offer és a Image SKU) ismerteti, amelyek et a Biztonsági mentés által biztosított beépített Azure-szabályzatok támogatják
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980113"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Az Azure Policy támogatott virtuálisgép-termékváltozatai

Az Azure Backup egy beépített szabályzatot biztosít (az Azure Policy használatával), amely **egy előfizetésen vagy erőforráscsoporton belül egy adott helyen**rendelhető hozzá az összes Azure-beli virtuális géphez. Ha ez a házirend egy adott hatókörhöz van rendelve, az adott hatókörben létrehozott összes új virtuális gép automatikusan konfigurálva lesz **egy ugyanazon a helyen és előfizetésben lévő meglévő tárolóba**való biztonsági mentéshez. Az alábbi táblázat felsorolja az összes virtuális gép sk-ek támogatja ezt a szabályzatot.

### <a name="supported-vms"></a>**Támogatott virtuális gépek**

**Házirend neve:** Biztonsági másolat konfigurálása egy hely virtuális gépein egy meglévő központi tárolóba ugyanazon a helyen

Kép közzétevője | Kép ajánlat | Kép Termékváltozat
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 adatközpont (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 adatközpont (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 adatközpont (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 adatközpont (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core tárolókkal (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Távoli asztali munkamenetgazda 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Adatközpont (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core tárolókkal (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core tárolókkal (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 adatközpont tárolókkal (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 adatközpont tárolókkal (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Adatközpont-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Minden kép SKUs
MicrosoftSQLServer | SQL2016SP1-WS2016 | Minden kép SKUs
MicrosoftSQLServer | SQL2016-WS2016  | Minden kép SKUs
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Minden kép SKUs
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Minden kép SKUs
MicrosoftSQLServer | SQL2016-WS2012R2 | Minden kép SKUs
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Minden kép SKUs
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Minden kép SKUs
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Minden kép SKUs
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Minden kép SKUs
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Minden kép SKUs
MicrosoftRServer | MLServer-WS2016 | Minden kép SKUs
MicrosoftVisualStudio | VisualStudio | Minden kép SKUs
MicrosoftVisualStudio | Windows | Minden kép SKUs
MicrosoftDynamicsAX | Dynamics  | Pre-Req-AX7-Onebox-U8
microsoft-hirdetések | windows-data-science-vm | Minden kép SKUs
MicrosoftWindowsDesktop | Windows-10 rendszerben | Minden kép SKUs
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-prioritás | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-prioritás | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-szabvány | 12.X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-NAPI-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-NAPI-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-NAPI-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS–LVM | 6.X, 7.X
OpenLogic | CentOS–SRIOV | 6.X, 7.X
felhő | cloudera-centos-os | 7.X
