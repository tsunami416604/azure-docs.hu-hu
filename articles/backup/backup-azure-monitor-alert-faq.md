---
title: Riasztások figyelése és jelentések – gyakori kérdések
description: Ebben a cikkben a Azure Backup figyelési riasztással és Azure Backup jelentésekkel kapcsolatos gyakori kérdésekre adott válaszokat talál.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 9cf7bf49d29b5faa9811a591b45179fe83c1d483
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172929"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup figyelési riasztás – gyakori kérdések

Ez a cikk az Azure figyelési riasztásával kapcsolatos gyakori kérdésekre ad választ.

## <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Hogyan ellenőrizze, hogy megkezdődött-e a jelentéskészítési adatforgalom egy Storage-fiókba?

Lépjen a konfigurált Storage-fiókra, és válassza a tárolók lehetőséget. Ha a tároló tartalmaz egy bejegyzést az elemzésekhez – naplók – azurebackupreport, azt jelzi, hogy a jelentéskészítési adat elindult.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Milyen gyakorisággal történik az adatküldés a Storage-fiókba és a Azure Backup a Content Pack csomagba Power BI?

  A 0. nap felhasználói számára körülbelül 24 órát vesz igénybe az adatgyűjtés egy Storage-fiókba. A kezdeti leküldés befejezése után az Adatfrissítés az alábbi ábrán látható gyakorisággal történik.

* A feladatokkal, **riasztásokkal**, **biztonsági másolati elemekkel** **, tárolókkal,** **védett kiszolgálókkal**és **házirendekkel** kapcsolatos adatokat a rendszer az ügyfél-Storage-fiókba küldi, a naplózott módon.

* A **tárterülettel kapcsolatos adattárolást** 24 óránként küldi el a rendszer az ügyfél Storage-fiókjába.

    ![Azure Backup jelentés leküldéses gyakorisága](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* A Power BI [naponta egyszer ütemezett frissítést](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)tartalmaz. A Content Pack Power BIban lévő adatok manuális frissítését végezheti el.

### <a name="how-long-can-i-retain-reports"></a>Mennyi ideig tarthatom a jelentéseket?

A Storage-fiók konfigurálásakor kiválaszthatja a jelentési adatok megőrzési idejét a Storage-fiókban. Kövesse a [Storage-fiók konfigurálása a jelentések számára](backup-azure-configure-reports.md#configure-storage-account-for-reports) szakasz 6. lépését. Emellett az [Excelben is elemezheti a jelentéseket](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) , és az igényeinek megfelelően mentheti azokat hosszabb megőrzési időtartamra.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Megjelenik az összes adataim a jelentésekben a Storage-fiók konfigurálása után?

 A Storage-fiók konfigurálása után létrehozott összes információ a Storage-fiókba kerül, és elérhetővé válik a jelentésekben. A folyamatban lévő feladatok nem továbbítódnak a jelentéskészítéshez. Miután a feladatok befejeződik vagy sikertelenek lesznek, a rendszer elküldi a jelentéseknek.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Ha már konfiguráltam a Storage-fiókot a jelentések megtekintéséhez, módosíthatom a konfigurációt egy másik Storage-fiók használatára?

Igen, úgy módosíthatja a konfigurációt, hogy egy másik Storage-fiókra mutasson. Használja az újonnan konfigurált Storage-fiókot, amikor csatlakozik a Azure Backup Content Pack csomaghoz. Egy másik Storage-fiók konfigurálása után a rendszer a Storage-fiókban lévő új adatfolyamatokat is elvégezte. A régebbi adatmennyiség (a konfiguráció módosítása előtt) továbbra is a régebbi Storage-fiókban marad.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Megtekinthetem a jelentéseket a tárolók és az előfizetések között?

Igen, ugyanazt a Storage-fiókot több tárolón is konfigurálhatja a több-tár közötti jelentések megtekintéséhez. Ugyanezt a Storage-fiókot is konfigurálhatja a tárakhoz az előfizetések között. Ezt követően használhatja ezt a Storage-fiókot, amikor a Power BI Azure Backup-csomaghoz csatlakozik a jelentések megtekintéséhez. A kiválasztott Storage-fióknak ugyanabban a régióban kell lennie, mint a Recovery Services-tárolónak.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Mennyi ideig tart az Azure Backup-ügynök feladata, hogy tükrözze a portálon?

A Azure Portal akár 15 percet is igénybe vehet, hogy tükrözze az Azure Backup Agent-feladatok állapotát.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Ha a biztonsági mentési feladatok meghiúsulnak, mennyi ideig tart a riasztások előléptetése?

Az Azure Backup hibája 20 percen belül riasztást vált ki.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Van olyan eset, amikor a rendszer nem küld e-mailt, ha az értesítések konfigurálva vannak?

Igen. A következő helyzetekben az értesítéseket a rendszer nem küldi el.

* Ha a rendszer óránként konfigurálja az értesítéseket, és az órán belül riasztást vált ki és oldott fel
* Feladat megszakításakor
* Ha egy második biztonsági mentési művelet meghiúsul, mert az eredeti biztonsági mentési feladata folyamatban van

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Mennyi ideig tart az Azure Backup-ügynök feladata, hogy tükrözze a portálon?

A Azure Portal akár 15 percet is igénybe vehet, hogy tükrözze az Azure Backup Agent-feladatok állapotát.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Ha a biztonsági mentési feladatok meghiúsulnak, mennyi ideig tart a riasztások előléptetése?

Az Azure Backup hibája 20 percen belül riasztást vált ki.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Van olyan eset, amikor a rendszer nem küld e-mailt, ha az értesítések konfigurálva vannak?

Igen. A következő helyzetekben a rendszer nem küld értesítéseket:

* Ha a rendszer óránként konfigurálja az értesítéseket, és az órán belül riasztást vált ki és oldott fel
* Feladat megszakításakor
* Ha egy második biztonsági mentési művelet meghiúsul, mert az eredeti biztonsági mentési feladata folyamatban van

## <a name="next-steps"></a>Következő lépések

További gyakori kérdések:

* [Gyakori kérdések](backup-azure-vm-backup-faq.md) az Azure-beli virtuális gépek biztonsági mentéséről.
* Az Azure Backup-ügynökkel kapcsolatos [Gyakori kérdések](backup-azure-file-folder-backup-faq.md)
