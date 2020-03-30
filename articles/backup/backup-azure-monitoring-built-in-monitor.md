---
title: Az Azure Backup által védett számítási feladatok figyelése
description: Ebben a cikkben az Azure Backup-számítási feladatok figyelési és értesítési képességeit az Azure Portalon keresztül megismerhet.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294922"
---
# <a name="monitoring-azure-backup-workloads"></a>Az Azure Backup számítási feladatainak figyelése

Az Azure Backup több biztonsági mentési megoldást biztosít a biztonsági mentési követelmény és az infrastruktúra-topológia (helyszíni és azure) alapján. Minden biztonsági mentési felhasználónak vagy rendszergazdának látnia kell, hogy mi történik az összes megoldásban, és várhatóan értesítést kell kapnia a fontos forgatókönyvekben. Ez a cikk részletezi az Azure Backup szolgáltatás által biztosított figyelési és értesítési lehetőségeket.

## <a name="backup-jobs-in-recovery-services-vault"></a>Biztonsági mentési feladatok a helyreállítási szolgáltatások tárolójában

Az Azure Backup beépített figyelési és riasztási lehetőségeket biztosít az Azure Backup által védett számítási feladatokhoz. A Recovery Services-tároló beállításaiban a **figyelési** szakasz beépített feladatokat és riasztásokat biztosít.

![RS-tároló beépített figyelése](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

A feladatok akkor jönnek létre, amikor olyan műveleteket hajtanak végre, mint a biztonsági mentés, a biztonsági mentés, a visszaállítás, a biztonsági másolat törlése és így tovább.

Az alábbi Azure Backup-megoldások feladatai itt láthatók:

- Azure-beli virtuális gép biztonsági mentése
- Az Azure-fájl biztonsági mentése
- Az Azure-munkaterhelés biztonsági mentése, például az SQL és az SAP HANA
- Azure Backup ügynök (MAB)

A System Center Data Protection Manager (SC-DPM), a Microsoft Azure Backup Server (MABS) nem jelenik meg.

> [!NOTE]
> Az Azure-számítási feladatok, például az SQL és az SAP HANA biztonsági mentések az Azure virtuális gépeken belül hatalmas számú biztonsági mentési feladatok. A naplóbiztonsági mentések például 15 percenként futtathatók. Ezért az ilyen DB-munkaterhelések esetében csak a felhasználó által aktivált műveletek jelennek meg. Az ütemezett biztonsági mentési műveletek NEM jelennek meg.

## <a name="backup-alerts-in-recovery-services-vault"></a>Biztonsági mentési riasztások a Helyreállítási szolgáltatások tárolójában

A riasztások elsősorban olyan forgatókönyvek, ahol a felhasználók értesítést kapnak, hogy megtehessék a megfelelő lépéseket. A **Biztonsági mentési riasztások** szakasz az Azure Backup szolgáltatás által létrehozott riasztásokat jeleníti meg. Ezeket a riasztásokat a szolgáltatás határozza meg, és a felhasználó nem hozhat létre egyéni riasztásokat.

### <a name="alert-scenarios"></a>Riasztási forgatókönyvek

A következő forgatókönyvek a szolgáltatás riasztási forgatókönyvként vannak definiálva.

- Biztonsági mentési/visszaállítási hibák
- A biztonsági mentés sikeresen befejeződött az Azure Backup ügynök (MAB) esetében, de figyelmeztetésekkel
- Stop védelem az adatok megőrzésével/Stop védelem a törlési adatokkal

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Az alábbi Azure Backup-megoldásokból származó riasztások itt láthatók

- Azure-beli virtuális gép biztonsági mentései
- Azure File biztonsági mentései
- Azure-beli számítási feladatok biztonsági mentései, például az SQL, az SAP HANA
- Azure Backup ügynök (MAB)

> [!NOTE]
> A System Center Data Protection Manager (SC-DPM), a Microsoft Azure Backup Server (MABS) nem jelenik meg itt.

### <a name="consolidated-alerts"></a>Összevont riasztások

Az Azure számítási feladatok biztonsági mentési megoldások, például az SQL és az SAP HANA, napló biztonsági mentések nagyon gyakran (legfeljebb 15 percenként a szabályzat szerint). Így az is lehetséges, hogy a napló biztonsági mentési hibák is nagyon gyakoriak (legfeljebb 15 percenként). Ebben a forgatókönyvben a végfelhasználó lesz túlterheltek, ha minden hiba előfordulása riasztást kap. Így a rendszer riasztást küld az első előfordulásra, és ha a későbbi hibák ugyanazon kiváltó ok miatt vannak, akkor a további riasztások nem jönnek létre. Az első riasztás frissül a hibaszámmal. De ha a riasztásinaktiválta a felhasználó, a következő előfordulás a következő esemény egy másik riasztást, és ez lesz az adott esemény első riasztásaként. Az Azure Backup így hajt végre riasztási összesítést az SQL és az SAP HANA biztonsági mentések esetén.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Kivételek, ha nem jelenik meg riasztás

Kevés kivétel van, ha hiba esetén nem jelenik meg riasztás. Ezek a következők:

- A felhasználó explicit módon megszakította a futó feladatot
- A feladat nem sikerül, mert egy másik biztonsági mentési feladat van folyamatban (semmi sem jár itt, mivel csak meg kell várni az előző feladat befejezéséhez)
- A virtuális gép biztonsági mentési feladata sikertelen, mert a biztonsági mentést készítő Azure Virtuális gép már nem létezik
- [Összevont riasztások](#consolidated-alerts)

A fenti kivételek abból a feltétellel, hogy ezek a műveletek (elsősorban a felhasználó által aktivált) azonnal megjelenik a portál/PS/CLI ügyfelek. Így a felhasználó azonnal tudatában van, és nem kell értesítést.

### <a name="alert-types"></a>Riasztástípusok

A riasztás súlyossága alapján a riasztások három típusban határozhatók meg:

- **Kritikus**: Elvileg bármilyen biztonsági mentési vagy helyreállítási hiba (ütemezett vagy felhasználó által aktivált) riasztás generálásához vezetne, és kritikus riasztásként és roncsolásos műveletekként, például biztonsági mentés törlése ként jelenik meg.
- **Figyelmeztetés:** Ha a biztonsági mentési művelet sikeres, de kevés figyelmeztetéssel, figyelmeztetésként jelenik meg.
- **Információs:** A mai napig az Azure Backup szolgáltatás nem hoz létre tájékoztató riasztást.

## <a name="notification-for-backup-alerts"></a>Értesítés a biztonsági mentési riasztásokról

> [!NOTE]
> Az értesítés konfigurálása csak az Azure Portalon keresztül végezhető el. A PS/CLI/REST API/Azure Resource Manager Template támogatása nem támogatott.

A riasztás kihirdetése után a felhasználók értesítést kapnak. Az Azure Backup e-mailben beépített értesítési mechanizmust biztosít. Megadhatja az egyes e-mail címeket vagy terjesztési listákat, amelyeket a riasztás létrehozásakor értesíteni kell. Azt is megadhatja, hogy értesítést kapjon-e minden egyes riasztásról, vagy óránkénti kivonatba csoportosítsa őket, majd értesítést kapjon.

![RS Vault beépített e-mail értesítés](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Az értesítés konfigurálása után üdvözlő vagy bevezető e-mailt kap. Ez megerősíti, hogy az Azure Backup küldhet e-maileket ezekre a címekre, ha egy riasztás t.<br>

Ha a gyakoriság óránkénti kivonatra volt állítva, és egy órán belül riasztást adtak ki és oldottak fel, az nem lesz része a közelgő óránkénti kivonatnak.

> [!NOTE]
>
> - Ha egy romboló művelet, például **a stop védelem törlési adatokkal** történik, riasztást kap, és egy e-mailt küld az előfizetés-tulajdonosok, a rendszergazdák és a társ-rendszergazdák akkor is, ha az értesítések nincsenek konfigurálva a Szolgáltatás helyreállítása tároló.
> - A sikeres feladatok értesítésének konfigurálásához használja a [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)szolgáltatást.

## <a name="inactivating-alerts"></a>Riasztások inaktiválása

Aktív riasztás inaktiválásához/feloldásához kattintson az inaktiválni kívánt értesítésnek megfelelő listaelemre. Ekkor megnyílik egy képernyő, amely részletes információkat jelenít meg a riasztásról, felül egy "Inaktiválás" gombbal. Erre a gombra kattintva a riasztás állapota "Inaktív" lesz. A riasztást úgy is inaktiválhatja, hogy a jobb gombbal a riasztásnak megfelelő listaelemre kattint, és az "Inactivate" lehetőséget választja.

![RS Vault riasztásinaktiválás](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>További lépések

[Az Azure biztonsági mentési számítási feladatainak figyelése az Azure Monitor használatával](backup-azure-monitoring-use-azuremonitor.md)
