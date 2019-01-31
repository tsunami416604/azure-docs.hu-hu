---
title: Az Azure Backup központi jelentéskészítési tartalomcsomag frissítése
description: Az Azure Backup tartalom frissítésekkel kapcsolatos információk csomag a Power bi-ban
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 87bc7ed3bb59266484858f534080fa87a7230981
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293961"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>Az Azure Backup központi jelentéskészítési tartalomcsomag frissítése 

A [Azure Backup tartalomcsomag](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) központi biztonsági mentéssel kapcsolatos jelentések megtekintéséhez használható. A tartalomcsomag a további funkciók hozzáadása és hibajavítás rendszeresen frissül. Ez a cikk bemutatja, hogyan frissítse a tartalomcsomagot. Azt is bemutatja, hogyan lehet elhalasztani a frissítést, és az idő függvényében végzett frissítéseket.

## <a name="get-updates-to-the-content-pack"></a>A tartalomcsomag frissítése

### <a name="get-the-updated-content-pack"></a>A frissített tartalomcsomag beszerzése
Ha még nem végzett módosításokat a tartalomcsomag példányának, automatikusan frissül. Ha módosítja a tartalomcsomagot, a Power BI és az e-mailben értesítést kap értesítést. Ha szeretné, a frissített tartalomcsomag beszerzése saját igényei. 

### <a name="postpone-the-update"></a>A frissítés elhalasztása
Az ajánlott eljárás szerint a rendszer importálja a tartalomcsomagot, egy [egyéni munkaterület](https://youtu.be/26zyOtyHPJM?t=1m57s). Most már szerkesztheti a jelentéseket.
Ahogy korábban említettük Ha módosítja a tartalomcsomagot, megjelenik egy értesítés a Power bi-ban. Ha szeretné, a tartalomcsomag újabb beolvasása. 

## <a name="coming-soon"></a>Hamarosan elérhető
   
Az Azure Backup-tartalomcsomaghoz további számítási feladatok frissül. Számítási feladat például az Azure SQL Database IaaS VM biztonsági mentése és a System Center Data Protection Manager. Ez a támogatás hozzáadása az aktuális támogatása az Azure backup és az Azure virtuális gép biztonsági mentéseinek. Ez a támogatás azt jelenti, hogy megtekintheti és elemezheti a biztonsági mentési adatok egy központi helyen. [Jelentések is testreszabható](https://youtu.be/26zyOtyHPJM) a szervezet igényeinek megfelelően.

Az előre konfigurált jelentéseket, amelyek az Azure Backup-tartalomcsomaghoz kapható változnak. A módosításokat a jelentések kifejezőbb számítási feladatokhoz. A jelentések közelgő beállítva lehetőségekbe itt érhető el.

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

* [Jelentések megosztása a szervezetben](https://youtu.be/26zyOtyHPJM)
* [Az Azure Backup – gyakori kérdések](backup-azure-backup-faq.md)
