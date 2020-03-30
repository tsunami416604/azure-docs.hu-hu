---
title: Diagnosztikai beállítások használata a Recovery Services-tárolókhoz
description: A régi és az új diagnosztikai események azure backup-hoz való használatát ismertető cikk
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e3919d120e5f741af6cd30dd27e5a1dfa2b06cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136939"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Diagnosztikai beállítások használata Recovery Services-tárolókhoz

Az Azure Backup diagnosztikai eseményeket küld, amelyek összegyűjthetők és felhasználhatók elemzés, riasztás és jelentéskészítés céljából. 

A helyreállítási szolgáltatások trezordiagnosztikai beállításait az Azure Portalon keresztül konfigurálhatja, ha a tárolóra navigál, és a **Diagnosztikai beállítások** menüelemre kattint. A **+ Diagnosztikai beállítás hozzáadása** elemre kattintva egy vagy több diagnosztikai eseményt küldhet egy tárfiókba, az Eseményközpontba vagy a Log Analytics (LA) munkaterületre.

![Diagnosztikai beállítások panel](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Az Azure biztonsági mentést lehetővé tőinek elérhető diagnosztikai eseményei

Az Azure Backup a következő diagnosztikai eseményeket biztosítja, amelyek mindegyike részletes adatokat biztosít a biztonsági mentéssel kapcsolatos összetevők adott készletéről:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupTárhely

[Adatmodell az Azure biztonsági mentésdiagnosztikai eseményeihez](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Ezek az események adatait lehet küldeni egy tárfiók, LA munkaterület, vagy egy Event Hub. Ha ezeket az adatokat la-munkaterületre küldi, ki kell választania az **Erőforrás-specifikus** kapcsolót a **Diagnosztika beállítása** képernyőn (további információt az alábbi szakaszokban talál).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Diagnosztikai beállítások használata a Log Analytics szolgáltatással (LA)

Az Azure Log Analytics-ütemtervhez igazodva az Azure Backup most már lehetővé teszi, hogy a tárolódiagnosztikai adatokat dedikált LA-táblákbiztonsági célokra küldje el. Ezeket [erőforrás-specifikus tábláknak nevezzük.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)

A trezor diagnosztikai adatainak la-be való elküldése:

1.  Keresse meg a trezort, és kattintson a **Diagnosztikai beállítások gombra.** Kattintson **a + Diagnosztikai beállítás hozzáadása gombra.**
2.  Adjon nevet a Diagnosztika beállításnak.
3.  Jelölje be a **Küldés a Log Analytics szolgáltatásba** jelölőnégyzetet, és válasszon egy Log Analytics-munkaterületet.
4.  Válassza **az Erőforrás-specifikus lehetőséget** a váltóban, és ellenőrizze a következő hat eseményt : **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**és **AddonAzureBackupStorage**.
5.  Kattintson a **Mentés gombra.**

![Erőforrás-specifikus mód](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Amint az adatok beáramlanak az LA-munkaterületre, az egyes eseményekhez dedikált táblák jönnek létre a munkaterületen. Ezen táblák bármelyikét közvetlenül lekérdezheti, és szükség esetén egyesítheti vagy egyesítheti ezeket a táblákat.

> [!IMPORTANT]
> A fenti hat esemény, nevezetesen a CoreAzureBackup, az AddonAzureBackupAlerts, az AddonAzureBackupEdInstance, az AddonAzureBackupJobs, az AddonAzureBackupPolicy és az AddonAzureBackupStorage **csak** erőforrás-specifikus módban támogatott. **Kérjük, vegye figyelembe, hogy ha az Azure diagnosztikai módban megpróbál adatokat küldeni a hat eseményhez, nem fog nak adatok at a LA-munkaterületre.**

## <a name="legacy-event"></a>Örökölt esemény

Hagyományosan a tároló összes biztonsági mentéssel kapcsolatos diagnosztikai adata egyetlen eseményben található, amelyet "AzureBackupReport" nevű esemény tartalmaz. A fent leírt hat esemény lényegében az AzureBackupReport ban található összes adat lebontása. 

Jelenleg továbbra is támogatjuk az AzureBackupReport eseményt a visszamenőleges kompatibilitás érdekében, azokban az esetekben, amikor a felhasználók már rendelkeznek egyéni lekérdezésekkel ezen az eseményen, például egyéni naplóriasztások, egyéni vizualizációk stb. Azonban **azt javasoljuk, hogy az új eseményekre való mielőbb idot,** mivel ez sokkal könnyebbé teszi az adatok használatát a naplólekérdezésekben, jobb felderíthetőséget biztosít a sémák és azok szerkezete, javítja a teljesítményt mind a betöltési késés, mind a lekérdezési idő között. **Az Azure Diagnosztika mód használatának támogatása végül fokozatosan megszűnik, és így az új események kiválasztása segíthet elkerülni az összetett áttelepítéseket egy későbbi időpontban.**

Az Azure Backup beépített szabályzatával új diagnosztikai beállítást adhat hozzá a 6 új eseményhez a megadott hatókör összes tárolójával kapcsolatban: [A Vault diagnosztikai beállításainak konfigurálása nagy méretekben](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

Dönthet úgy, hogy külön diagnosztikai beállításokat hoz létre az AzureBackupReport és a hat új esemény, amíg az összes egyéni lekérdezések az új táblák ból származó adatok használatára. Az alábbi képen egy olyan tároló látható, amelynek két diagnosztikai beállítása van. Az első **beállítás1 az** AzureBackupReport-esemény adatait küldi egy LA-munkaterületre AzureDiagnostics módban. A második beállítás, **a Setting2** a hat új Azure Backup-esemény adatait küldi el egy LA-munkaterületre erőforrás-specifikus módban.

![Két beállítás](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Az AzureBackupReport esemény **csak** Azure diagnosztikai módban támogatott. **Kérjük, vegye figyelembe, hogy ha erőforrás-specifikus módban próbál adatokat küldeni az eseményhez, nem fog adatáramlani a LA-munkaterületre.**

> [!NOTE]
> Az Azure Diagnostics / Resource Specific kapcsoló csak akkor jelenik meg, ha a felhasználó ellenőrzi a **Küldés a Log Analytics szolgáltatásba**című fájlt. Adatok küldése egy tárfiókba vagy egy Event Hub, a felhasználó egyszerűen válassza ki a kívánt célt, és ellenőrizze a kívánt eseményeket, anélkül, hogy további bemenetek. Ismét ajánlott, hogy ne válassza ki az örökölt esemény AzureBackupReport, előrehaladva.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Az Azure-webhely-helyreállítási eseményeket küldő felhasználók a Log Analytics (LA) szolgáltatásba

Az Azure Backup és az Azure site recovery események ugyanattól a Recovery Services Vaultból kerülnek elküldésre. Mivel az Azure Site Recovery jelenleg nincs beszervezve az erőforrás-specifikus táblákba, az Azure Site Recovery Events la-be küldeni kívánt felhasználók **csak** az Azure Diagnosztikai mód használatára irányulnak (lásd az alábbi képet). **Ha az Azure site recovery-eseményekhez erőforrás-specifikus módot választ, a rendszer megakadályozza a szükséges adatok küldését az LA-munkaterületre.**

![Hely-helyreállítási események](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Összefoglalva a fenti árnyalatok:

* Ha már rendelkezik LA diagnosztika beállítása az Azure Diagnostics és a írt egyéni lekérdezések tetején, tartsa meg a beállítást **érintetlenül,** amíg át nem telepíti a lekérdezéseket az új események adatainak felhasználásához.
* Ha új táblákba is szeretne beszállni (az ajánlott módon), hozzon létre egy **új** diagnosztikai beállítást, válassza az **Erőforrás-specifikus** lehetőséget, és válassza ki a fent megadott hat új eseményt.
* Ha jelenleg az Azure Site Recovery események et küld la, **ne** válassza erőforrás-specifikus mód ezeket az eseményeket, különben az ilyen események adatai nem áramlik a LA-munkaterületre. Ehelyett hozzon létre egy **további diagnosztikai beállítást,** válassza az **Azure Diagnosztika**lehetőséget, és válassza ki a megfelelő Azure Site Recovery-eseményeket.

Az alábbi képen egy példa egy felhasználó három diagnosztikai beállítások at a tároló. Az első **beállítás1 nevű** adatokat küld az AzureBackupReport eseményegy LA-munkaterület i AzureDiagnostics módban. A második beállítás, **a Setting2** adatokat küld a hat új Azure Backup-eseményről egy LA-munkaterületre erőforrás-specifikus módban. A harmadik beállítás, **a Setting3,** adatokat küld az Azure Site Recovery események egy LA-munkaterület i Azure diagnosztikai módban.

![Három beállítás](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>További lépések

[Ismerje meg a diagnosztikai események naplóelemzési adatmodelljét](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
