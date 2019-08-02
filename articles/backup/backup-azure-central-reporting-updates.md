---
title: A Azure Backup központi jelentési tartalomkezelő csomag frissítése
description: Információk a Azure Backup Content Pack frissítéseiről Power BI
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 4e217148db42e10e8fe2046cbd062f0708011e96
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689318"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>A Azure Backup központi jelentési tartalomkezelő csomag frissítése 

A [Azure Backup Content Pack](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) használatával megtekintheti a központi biztonsági mentéssel kapcsolatos jelentéseket. A Content Pack frissítése rendszeresen megtörténik a további funkciók hozzáadásához és a hibák javításához. Ebből a cikkből megtudhatja, hogyan frissítheti a Content Pack csomagot. Emellett azt is bemutatja, hogyan elhalaszthatja a frissítést, és megtekintheti a frissítések időbeli változásait.

## <a name="get-updates-to-the-content-pack"></a>A Content Pack frissítéseinek beolvasása

### <a name="get-the-updated-content-pack"></a>A frissített tartalomkezelő csomag beszerzése
Ha nem módosította a Content Pack másolatát, az automatikusan frissül. A Content Pack változásakor értesítést kap Power BI és egy e-mailes értesítést. Megadhatja, hogy a frissített Content Pack legyen az Ön kényelme. 

### <a name="postpone-the-update"></a>A frissítés elhalasztása
Az ajánlott eljárás az, ha a Content Pack csomagot egy [Egyéni](https://youtu.be/26zyOtyHPJM?t=1m57s)munkaterületre importálja. Most már szerkesztheti a jelentéseket.
Ahogy korábban említettük, a Content Pack változásakor megjelenik egy értesítés a Power BI. Később is kiválaszthatja, hogy melyik csomagot szeretné beszerezni. 

## <a name="coming-soon"></a>Hamarosan
   
A Azure Backup Content Pack a további számítási feladatok támogatásához frissül. A számítási feladatok közé tartozik Azure SQL Database a IaaS VM Backup és a System Center Data Protection Manager. Ez a támogatás hozzáadja az Azure Backup és az Azure virtuális gépek biztonsági mentésének aktuális támogatását. Ez a támogatás azt jelenti, hogy az összes biztonsági mentési adatait egyetlen központi helyen tekintheti meg és elemezheti. A [jelentések emellett testreszabhatók](https://youtu.be/26zyOtyHPJM) a szervezet igényeinek megfelelően.

A Azure Backup Content Pack csomaghoz tartozó előre konfigurált jelentések változnak. A módosítások a munkaterhelések között jobban értelmezik a jelentéseket. Itt találja a közelgő jelentések betekintését.

### <a name="summary"></a>Összegzés
   
![Összegzés](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Számlázás

![Számlázás](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Megfelelőség

![Megfelelőség](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Biztonsági másolati elemek
![Biztonsági másolati elemek](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Riasztások

![Riasztások](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Feladatok

![Feladatok](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>További lépések

* [Jelentések megosztása a szervezeten belül](https://youtu.be/26zyOtyHPJM)
* [Azure Backup GYIK](backup-azure-backup-faq.md)
