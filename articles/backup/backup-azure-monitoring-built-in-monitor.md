---
title: 'Azure Backup: A figyelő az Azure Backup védett munkaterhelések'
description: Az Azure Backup-alapú számítási feladatokat az Azure portal figyelése
services: backup
author: pvrk
manager: shivamg
keywords: Az Azure Backup; Riasztások;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 50d766eed5800c58d0b0258b2be6637f139c542a
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730970"
---
# <a name="monitoring-azure-backup-workloads"></a>Az Azure Backup figyelési feladatok

Az Azure Backup a biztonsági mentési követelmény és az infrastruktúra topológia (helyszíni vagy Azure) alapján több biztonsági mentési megoldást kínál. Minden olyan biztonsági mentési felhasználó vagy rendszergazda kell látnia, hogy mi történik összes megoldás között, és értesülhet a fontos forgatókönyvet lehetővé tesz a várható. Ez a cikk részletesen a figyelési és értesítési funkciók, az Azure Backup szolgáltatás által biztosított.

## <a name="backup-jobs-in-recovery-services-vault"></a>A helyreállítási tárban lévő biztonsági mentési feladatok

Az Azure Backup beépített figyelési és riasztási funkciókat az Azure Backup által védett munkaterhelések biztosít. A Recovery Services-tároló beállításait, a **figyelés** a témakör a beépített feladatok és riasztások.

![A beépített figyelés RS-tároló](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

A feladatok jönnek létre, amikor elvégzett műveletek, például a konfigurálása a biztonsági mentés, biztonsági mentés, visszaállítás, Törlés biztonsági mentés és stb.

A következő Azure Backup megoldásai feladatok itt látható:

  - Azure-beli virtuális gép biztonsági mentése
  - Az Azure biztonsági mentés
  - Az Azure rendszeren futó munkaterhelés biztonsági például SQL
  - Az Azure Backup ügynöke (MAB)

A System Center Data Protection Manager (SC-DPM), a Microsoft Azure Backup Server (MABS) feladatok nem jelennek meg.

> [!NOTE]
> Az Azure számítási feladatok, például az SQL biztonsági másolatainak Azure virtuális gépeken belül rendelkezik hatalmas biztonsági mentési feladatok számát. Ha például a naplóalapú biztonsági mentések futtathatja, 15 percenként. Ezért az ilyen DB számítási feladatokhoz, kizárólag egyetlen felhasználó által aktivált műveletek jelennek meg. Ütemezett biztonsági mentési műveletek nem jelennek meg.

## <a name="backup-alerts-in-recovery-services-vault"></a>Biztonsági mentési riasztások a Recovery Services-tárolóban

Riasztások olyan elsősorban olyan forgatókönyvek, ahol felhasználó értesítést kap, hogy azok a megfelelő műveleteket végezheti el. A **biztonsági mentési riasztások** szakasz bemutatja az Azure Backup szolgáltatás által létrehozott riasztásokat. Ezek a riasztások a szolgáltatás által definiált és felhasználó egyéni nem minden olyan riasztásokat hozhat létre.

### <a name="alert-scenarios"></a>Riasztási forgatókönyvek
A következő esetekben alertable forgatókönyvek szolgáltatás vannak meghatározva.

  - Biztonsági mentési és visszaállítási hibák
  - Biztonsági mentés sikeres volt, de figyelmeztetésekkel
  - Védelem leállítása és megőrizni az adatokat és leállítása a védelmi adatok törlésével

### <a name="exceptions-when-an-alert-is-not-raised"></a>Ha a riasztás nem jelenik meg, kivételek
Nincsenek néhány kivételtől eltekintve, ha a riasztást a hiba nem hoz létre, azok:

  - A felhasználó által megszakítva explicit módon a futó feladat
  - A feladat sikertelen lesz, mivel egy másik biztonsági mentési feladat van folyamatban (ahhoz itt, mivel csak rá kell Várjon, amíg befejeződik az előző feladat tegyen semmit)
  - A virtuális gép biztonsági mentési feladat sikertelen lesz, mivel a biztonsági másolat az Azure virtuális gép már nem létezik.

A fenti kivételek, a feltétellel, hogy ezeket a műveleteket (elsősorban a felhasználó által aktivált) eredménye megjelenik-e azonnal portal/PS/CLI ügyfeleken lettek kialakítva. A felhasználó, ezért azonnal észleli, és nem kell egy értesítést.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>A következő Azure biztonsági mentési megoldások riasztásai itt látható:

  - Az Azure virtuális gép biztonsági mentéseinek
  - Az Azure biztonsági másolatainak
  - Például az SQL Azure-beli számítási biztonsági
  - Az Azure Backup ügynöke (MAB)

> [!NOTE]
> A System Center Data Protection Manager (SC-DPM), a Microsoft Azure Backup Server (MABS) riasztások nem jelennek meg itt.

### <a name="alert-types"></a>Riasztástípusok
Riasztás súlyossága alapján a riasztások háromféle lehet definiálni:

  - **Kritikus fontosságú**: Alapelvet, bármely biztonsági mentési vagy helyreállítási hiba (ütemezett vagy felhasználó által aktivált) a riasztások generálása vezetne, és akkor jelennek meg a kritikus riasztás, és ártalmas műveletek, például a Törlés biztonsági mentési.
  - **Figyelmeztetés**: Ha a biztonsági mentési művelet sikeres, de néhány figyelmeztetésekkel fejeződött be, hogy szerepelnek figyelmeztető riasztások.
  - **Tájékoztató**: Mai nem tájékoztató generál riasztást az Azure Backup szolgáltatás.

## <a name="notification-for-backup-alerts"></a>Értesítés biztonsági mentésekkel kapcsolatos riasztások

> [!NOTE]
> Értesítési konfigurációja csak az Azure Portalon végezhető el. PS/CLI vagy REST API-t vagy az Azure Resource Manager-sablon támogatási nem támogatott.

Riasztás történik, ha felhasználó értesítést kap. Az Azure Backup egy e-mailben a beépített értesítési mechanizmust biztosít. Egyéni e-mail-címeket vagy terjesztési listák értesítést a rendszer riasztást állít elő egy is megadhat. Azt is beállíthatja az egyes egyedi riasztások értesülni szeretne, vagy hogy szeretne-e csoportosíthatja őket egy óránkénti összefoglaló majd értesítést kaphat.

![R-tároló beépített e-mail-értesítések](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Amikor értesítés van konfigurálva, egy üdvözlő vagy bevezető e-mailt fog kapni. Ezzel megerősíti, hogy az Azure Backup e-maileket küldhet ezeket a címeket, ha egy riasztás akkor jön létre.<br>

Ha gyakoriságát az óránkénti összefoglaló értékre lett beállítva, és riasztást kiváltott és a egy órán belül fog állni, nem lesz a közelgő óránkénti összefoglaló része.

> [!NOTE]
>
* Ha például egy destruktív művelet **állítsa le a védelmi adatok törlésével** van hajtja végre, riasztást hoz létre, és a egy e-mailt küld az előfizetés-tulajdonosokat, rendszergazdák és társrendszergazdák akkor is, ha a helyreállítás szolgáltatáshoz nem állíthatók be értesítéseket tároló.
* A sikeres feladatokra használja az értesítések konfigurálásával [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>További lépések

[Az Azure Monitor használatával az Azure biztonsági mentési számítási feladatok monitorozása](backup-azure-monitoring-use-azuremonitor.md)
