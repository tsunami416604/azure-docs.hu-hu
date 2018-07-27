---
title: Az Azure biztonsági mentési központi jelentéskészítési tartalomcsomag frissítése
description: Az Azure Backup-Tartalomcsomaghoz a Power bi-ban frissítésekkel kapcsolatos információk
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265670"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>Az Azure biztonsági mentési központi jelentéskészítési tartalomcsomag frissítése 

[Az Azure Backup-Tartalomcsomaghoz](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) segítségével központilag megtekintheti a jelentések biztonsági mentéséről. A tartalomcsomag további funkciókat rendszeresen frissül, és kijavítja a hibát. Ez a cikk végigvezeti a lépéseket, frissítse a Tartalomcsomagot, a frissítést, és a idővel végzett frissítések késleltetése.

## <a name="how-to-get-updates-to-the-content-pack"></a>A tartalomcsomaghoz frissítések beszerzése

### <a name="to-get-the-updated-content-pack"></a>A frissített tartalomcsomag letöltése
Rendszer nem végzett módosításokat, ha automatikusan frissíti a tartalomcsomagot a példányát. Abban az esetben, ha a tartalomcsomag módosult, értesítést a powerbi-ban, és közel azonos e-mail-értesítést fog kapni. Ha szeretné, megfelelően a felhasználók kényelme érdekében a frissített tartalomcsomag beszerzése. 

### <a name="to-delay-the-update"></a>A frissítés késleltetése
Az ajánlott eljárás szerint a rendszer importálja a tartalomcsomagot, egy [egyéni munkaterület](https://youtu.be/26zyOtyHPJM?t=1m57s). Most már a jelentések szerkesztési képességének.
Ahogy említettük, ha a tartalomcsomag módosul, megjelenik egy értesítés, a powerbi-ban. Ha szeretné, a tartalomcsomag újabb beolvasása. 

## <a name="coming-soon"></a>Hamarosan
   
Az Azure biztonsági mentési tartalomcsomag frissítése az aktuális támogatási MAB és Azure virtuális gép biztonsági mentése mellett támogatja a további számítási feladatok, az SQL IaaS VM biztonsági mentése, valamint a SC DPM, folyamatban van. Ez azt jelenti, hogy Ön hamarosan megtekintheti és elemezheti az adatokat a biztonsági mentési adatok egy központi helyen. A [jelentések testre is szabható](https://youtu.be/26zyOtyHPJM) a szervezet igényeinek megfelelően.

Ahhoz, hogy a jelentések kifejezőbb számítási feladatok, a következőre változik az Azure Backup-Tartalomcsomaghoz előre konfigurált jelentések készletét. A következő jelentések t lehetőségekbe itt érhető el:

### <a name="summary"></a>Összegzés
   
![Összegzés](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Számlázás

![Számlázás](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Megfelelőség

![Megfelelőség](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Biztonsági másolati elemek
![BackupItems](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Riasztások

![Riasztások](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Feladatok

![Feladatok](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>További lépések:

* [A szervezet között megosztást jelentések](https://youtu.be/26zyOtyHPJM)
* [Az Azure Backup – gyakori kérdések](backup-azure-backup-faq.md)
