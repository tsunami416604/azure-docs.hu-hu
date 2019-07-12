---
title: Az Azure Backup figyelési riasztás – gyakori kérdések
description: 'Gyakori kérdésekre adott válaszok: Az Azure Backup Monitoring riasztás'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: bb684f65539b4429862b2dce0e378d8f659d2975
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705030"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Az Azure Backup figyelési riasztás – gyakori kérdések
Ez a cikk az Azure figyelési riasztással kapcsolatos gyakori kérdésekre ad választ.

## <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Hogyan tudom ellenőrizni, ha a jelentési adatok a tárfiókba kerülnek egy megkezdődött?
Nyissa meg a konfigurált tárfiók, és válassza ki a tárolók. Ha a container insights-logs-azurebackupreport bejegyzést, azt jelzi, hogy vonatkozó adatokról szóló jelentéseket indult tárfiókba.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Mi az a gyakoriságát, adatleküldés egy storage-fiókot és az Azure Backup-tartalomcsomaghoz a Power bi-ban?
  A felhasználók nap 0 vesz igénybe körülbelül 24 óra adatok leküldése egy storage-fiókot. A kezdeti leküldéses befejezése után az adatok frissülnek a gyakoriságú, az alábbi ábrán látható.

  * Kapcsolódó adatok **feladatok**, **riasztások**, **biztonsági másolati elemek**, **tárolók**, **védett kiszolgálók**, és  **Házirendek** át lett helyezve egy ügyfél tárfiókja, amikor azt a naplózására akkor kerül sor.

  * Kapcsolódó adatok **tárolási** át lett helyezve egy ügyfél tárfiókja 24 óránként.

       ![Az Azure Backup-jelentések adatok leküldéses gyakorisága](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * A Power BI rendelkezik egy [az ütemezett frissítés naponta egyszer](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Az adatok manuális frissítés a tartalomcsomag a Power BI is végrehajthatja.

### <a name="how-long-can-i-retain-reports"></a>Mennyi ideig őrizheti meg a jelentéseket?
Amikor konfigurál egy tárfiókot, kiválaszthatja a jelentés adatai olyan megőrzési időszakot a storage-fiókban. 6\. a következő lépés a [jelentések tárfiók konfigurálása](backup-azure-configure-reports.md#configure-storage-account-for-reports) szakaszban. Emellett képes [elemzése az Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) , és mentse őket a hosszabb megőrzési időt, az igényei szerint.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>A jelentésekben lévő adatok után a tárfiók konfigurálható látható?
 Storage-fiók beállítása után létrehozott összes adatok a storage-fiók át lett helyezve, és jelentésekben érhető el. Folyamatban lévő feladatok rendszer nem küldi le a jelentéskészítéshez. Miután a feladat befejeződik, vagy nem sikerül, a jelentések zajlik.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Ha már konfigurálta a tárfiókot,-jelentések megtekintéséhez, módosítható a konfigurációt egy másik tárfiók használatára?
Igen, módosíthatja a konfigurációt, hogy egy másik tárfiók mutasson. Az újonnan beállított storage-fiókot használja, amíg csatlakozik az Azure Backup-tartalomcsomaghoz. Miután egy másik tárfiók van konfigurálva, az új adatok folyamatok ezt a tárfiókot. Régebbi adatokat (mielőtt módosítaná a konfigurációt) továbbra is a korábbi tárfiókban maradnak.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Jelentések tekinthetem tárolók és -előfizetések között?
Igen, cross-tároló jelentések megtekintéséhez a különböző tárolók ugyanazt a tárfiókot is konfigurálhatók. Konfigurálhatja a tárolók esetében ugyanazt a tárfiókot is, előfizetések között. Ezt követően használhatja ezt a tárfiókot, csatlakozik az Azure Backup-tartalomcsomaghoz a Power BI-jelentések megtekintése közben. A kiválasztott tárfiók és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Mennyi időt vesz igénybe az Azure backup-ügynök feladatot állapot tükrözik a portálon?
Az Azure Portalon az Azure Backup szolgáltatás ügynöke feladat állapotát tükröző legfeljebb 15 perc is eltarthat.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Amikor egy biztonsági mentési feladat sikertelen volt, mennyi ideig tart kell riasztást küldenie?
Riasztást hoz létre az Azure biztonsági mentési hiba 20 perc belül.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Van egy esetet, ahol egy e-mailt nem küldhetők, ha értesítések be vannak állítva?
Igen. Az alábbi esetekben nem kap értesítést.

* Ha az értesítések óránkénti vannak konfigurálva, és a egy riasztás kiadása és feloldása egy órán belül
* Amikor egy feladat meg lett szakítva
* Ha egy második biztonsági mentési feladat sikertelen lesz, mert folyamatban van az eredeti biztonsági mentési feladat

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Mennyi időt vesz igénybe az Azure backup-ügynök feladatot állapot tükrözik a portálon?
Az Azure Portalon az Azure Backup szolgáltatás ügynöke feladat állapotát tükröző legfeljebb 15 perc is eltarthat.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Amikor egy biztonsági mentési feladat sikertelen volt, mennyi ideig tart kell riasztást küldenie?
Riasztást hoz létre az Azure biztonsági mentési hiba 20 perc belül.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Van egy esetet, ahol egy e-mailt nem küldhetők, ha értesítések be vannak állítva?
Igen. Az alábbi esetekben nem kap értesítést.

* Ha az értesítések óránkénti vannak konfigurálva, és a egy riasztás kiadása és feloldása egy órán belül
* Amikor egy feladat meg lett szakítva
* Ha egy második biztonsági mentési feladat sikertelen lesz, mert folyamatban van az eredeti biztonsági mentési feladat

## <a name="next-steps"></a>További lépések

Olvassa el a gyakori kérdések:

- [Gyakori kérdések](backup-azure-vm-backup-faq.md) Azure virtuális gép biztonsági mentéseinek kapcsolatban.
- [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) tudnivalók az Azure Backup-ügynök
