---
title: Recovery Services-tárolók diagnosztikai beállításainak használata
description: Cikk, amely leírja, hogyan használhatja a Azure Backup korábbi és új diagnosztikai eseményeit
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 875adb82aeeb56b378a84ca01e716c7189abc64f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281102"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services-tárolók diagnosztikai beállításainak használata

A Azure Backup diagnosztikai eseményeket küld, amelyeket elemzés, riasztás és jelentéskészítés céljából gyűjthet és használhat fel. 

A Recovery Services-tároló diagnosztikai beállításait a Azure Portal használatával konfigurálhatja a tárolóban, majd a **diagnosztikai beállítások** menüpontra kattintva. A **+ diagnosztikai beállítások hozzáadása** lehetőségre kattintva egy vagy több diagnosztikai eseményt küldhet egy Storage-fiókba, egy Event hub-ba vagy egy log Analytics (La) munkaterületre.

![Diagnosztikai beállítások panel](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup felhasználók számára elérhető diagnosztikai események

Azure Backup a következő diagnosztikai eseményeket biztosítja, amelyek mindegyike részletes információt nyújt a biztonsági másolatok kapcsolódó összetevőinek egy adott készletéről:
* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage 

[Adatmodell a Azure Backup diagnosztikai eseményeihez](https://aka.ms/diagnosticsdatamodel)

Ezeknek az eseményeknek az adatküldhető egy Storage-fiókba, a LA Workspace-ba vagy egy Event hub-ba. Ha ezeket az adatokat egy LA munkaterületre küldi, ki kell választania az **erőforrás-specifikus** kapcsolót a **diagnosztika beállítása** képernyőn (lásd a további tudnivalókat az alábbi részekben).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Diagnosztikai beállítások használata Log Analytics (LA)

Az Azure Log Analytics ütemtervének összehangolásával Azure Backup mostantól lehetővé teszi a tároló diagnosztikai adatainak küldését dedikált LA táblákba a biztonsági mentéshez. Ezeket [erőforrás-specifikus tábláknak](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)nevezzük.

A tároló diagnosztikai adatait a következőre küldje:
1.  Navigáljon a tárolóhoz, és kattintson a **diagnosztikai beállítások**elemre. Kattintson a **+ diagnosztikai beállítás hozzáadása**elemre.
2.  Adjon nevet a diagnosztikai beállításoknak.
3.  Jelölje be a **küldés log Analyticsre** jelölőnégyzetet, és válasszon ki egy log Analytics munkaterületet.
4.  Válassza ki az **erőforrás-specifikus** elemet a kapcsolóban, és jelölje ki a következő hat eseményt: **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**és **AddonAzureBackupStorage**.
5.  Kattintson a **Mentés** gombra.

![Erőforrás-specifikus mód](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Ha az adatforgalom bekerül a LA munkaterületre, az egyes események dedikált táblái a munkaterületen jönnek létre. A táblák bármelyikét lekérdezheti közvetlenül, és szükség esetén összekapcsolásokat vagy szakszervezeteket is végrehajthat.

> [!IMPORTANT]
> A fenti hat esemény, azaz a CoreAzureBackup, a AddonAzureBackupAlerts, a AddonAzureBackupProtectedInstance, a AddonAzureBackupJobs, a AddonAzureBackupPolicy és a AddonAzureBackupStorage **csak** erőforrás-specifikus módban támogatott. **Vegye figyelembe, hogy ha Azure Diagnostics módban kísérli meg az ilyen hat eseményekre vonatkozó adatküldést, a LA munkaterületre nem kerül be az adatforgalom.**

## <a name="legacy-event"></a>Örökölt esemény

Hagyományosan a tár összes biztonsági másolattal kapcsolatos diagnosztikai adatát egyetlen, "AzureBackupReport" nevű esemény tárolja. A fent ismertetett hat esemény lényegében a AzureBackupReport-ben található összes adathalmaz összetételét képezi. 

Jelenleg továbbra is támogatjuk a AzureBackupReport eseményt a visszamenőleges kompatibilitás érdekében, olyan esetekben, amikor a felhasználók meglévő egyéni lekérdezésekkel rendelkeznek ezen az eseményen, például az egyéni naplózási riasztások, az egyéni vizualizációk stb. Azt javasoljuk azonban, hogy az új eseményeket a tár összes új diagnosztikai beállítására kiválasztva, mivel így sokkal könnyebben használhatók a naplók a napló lekérdezésekben, a sémák és azok struktúrájának jobb észlelése, valamint a teljesítmény növelése mindkét betöltésnél késés és lekérdezési időpontok. A Azure Diagnostics mód használatának támogatása végül fokozatosan megszűnik, így az új események kiválasztásával elkerülhető, hogy egy későbbi időpontban el lehessen kerülni az összetett áttelepítést.

Dönthet úgy, hogy külön diagnosztikai beállításokat hoz létre a AzureBackupReport és a hat új eseményhez, amíg az összes egyéni lekérdezést át nem telepítette az új táblákból származó adatok használatára. Az alábbi képen egy példa látható egy olyan tárolóra, amely két diagnosztikai beállítással rendelkezik. A **Setting1** nevű első beállítás a AzureBackupReport esemény adatokat küld egy La munkaterületre AzureDiagnostics módban. A második beállítás, a nevű **Setting2** a hat új Azure Backup eseményt küldi el egy La munkaterületre erőforrás-specifikus módban.

![Két beállítás](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> A AzureBackupReport esemény **csak** Azure Diagnostics módban támogatott. **Vegye figyelembe, hogy ha az adott eseményhez erőforrás-specifikus módban próbál adatküldést végrehajtani, akkor a LA munkaterületre nem kerül be az adatforgalom.**

> [!NOTE]
> A Azure Diagnostics/erőforráshoz tartozó váltógomb csak akkor jelenik meg, ha a felhasználó ellenőrzi a **küldést a log Analytics**. Ha adatokat szeretne elküldeni egy Storage-fiókba vagy egy Event-központba, a felhasználó egyszerűen kiválaszthatja a kívánt célhelyet, és bármely szükséges eseményt megvizsgálhat további bemenetek nélkül. Azt javasoljuk, hogy ne válassza ki az örökölt esemény AzureBackupReport, amely továbbra is fennáll.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Azure Site Recovery eseményeket küldő felhasználók Log Analytics (LA)

Azure Backup és Azure Site Recovery az eseményeket ugyanabból a Recovery Services-tárból küldik. Mivel a Azure Site Recovery jelenleg nem erőforrás-specifikus táblákra van előkészítve, a Azure Site Recovery eseményeket az LA-be küldeni kívánó felhasználók a **csak** Azure Diagnostics mód használatára vannak irányítva (lásd az alábbi ábrát). **Az erőforrás-specifikus mód kiválasztása a Azure site Recovery eseményekhez megakadályozza, hogy a rendszer elküldje a szükséges adatoknak a La munkaterületre**.

![Események Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

A fenti árnyalatok összegzése:

* Ha már beállította a (z) Azure Diagnostics és az egyéni lekérdezésekkel rendelkező LA Diagnostics szolgáltatást, a beállítás **érintetlen**marad, amíg át nem telepíti a lekérdezéseket az új eseményekről származó adatok használatára.
* Ha az új táblákat is szeretné bevezetni (ajánlott), hozzon létre egy **új** diagnosztikai beállítást, válassza az **erőforrás-specifikus** lehetőséget, és válassza ki a fentiekben megadott hat új eseményt.
* Ha jelenleg Azure Site Recovery eseményeket küld a **La-nek, ne válassza** az erőforrás-specifikus üzemmódot ezekhez az eseményekhez, különben ezek az események nem kerülnek be a La-munkaterületre. Ehelyett hozzon létre egy **további diagnosztikai beállítást**, válassza a **Azure Diagnostics**lehetőséget, majd válassza ki a megfelelő Azure site Recovery eseményeket.

Az alábbi képen egy példa látható egy olyan felhasználóra, aki három diagnosztikai beállítással rendelkezik a tárolóhoz. Az első beállítás, amelynek neve **Setting1** , adatokat küld a AzureBackupReport eseményről egy La munkaterületre AzureDiagnostics módban. A második beállítás, amelynek neve **Setting2** , a hat új Azure Backup eseményről küld adatokat egy erőforrás-specifikus módban lévő La-munkaterületre. A **Setting3**nevű harmadik beállítás Azure Diagnostics módban küld adatokat a Azure site Recovery eseményekről egy La munkaterületre.

![Három beállítás](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Következő lépések

[A diagnosztikai események Log Analytics adatmodelljének megismerése](https://aka.ms/diagnosticsdatamodel)
