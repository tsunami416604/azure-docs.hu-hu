---
title: 'Azure Backup: A figyelő az Azure Backup védett munkaterhelések'
description: Az Azure Backup-alapú számítási feladatokat az Azure portal figyelése
services: backup
author: pvrk
manager: shivamg
keywords: Az Azure Backup; Riasztások;
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 2513f2e7a2e82a541fa5638b70d0543192c84765
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885353"
---
# <a name="monitoring-azure-backup-workloads"></a>Az Azure Backup figyelési feladatok

Az Azure Backup a biztonsági mentési követelmény és az infrastruktúra topológia (helyszíni vagy Azure) alapján több biztonsági mentési megoldást kínál. Bármely biztonsági mentési felhasználó/rendszergazda kell látnia, hogy mi történik összes megoldás között, és értesülhet a fontos forgatókönyvet lehetővé tesz a várható. Ez a cikk részletesen a figyelési és értesítési funkciók, az Azure Backup szolgáltatás által biztosított.

## <a name="backup-jobs-in-recovery-services-vault"></a>A helyreállítási tárban lévő biztonsági mentési feladatok

Az Azure Backup beépített figyelési és riasztási funkciókat az Azure Backup által védett munkaterhelések biztosít. A Recovery Services-tároló beállításait a "figyelés" szakaszban biztosít beépített feladatok és riasztások.

![A beépített figyelés RS-tároló](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

A feladatok jönnek létre, ha például a biztonsági mentés konfigurálása, biztonsági mentési művelet, a visszaállításhoz, delete stb. biztonsági mentési műveleteket.
A következő Azure Backup megoldásai feladatok Itt jelennek meg.

- Az Azure virtuális gép biztonsági mentéseinek
- Az Azure biztonsági másolatainak
- Például az SQL Azure-beli számítási biztonsági
- Az Azure Backup ügynöke (MAB)

A System Center Data Protection Manager (SC-DPM), a Microsoft Azure Backup Server (MABS) feladatok nem jelennek meg itt.

> [!NOTE]
> Például az SQL biztonsági másolatainak Azure virtuális gépeken belül az Azure számítási feladatokhoz érdemes olyan hatalmas biztonsági mentési feladatok számát. Ha például a naplóalapú biztonsági mentések futtathatja, 15 percenként. Ezért az ilyen DB számítási feladatokhoz, kizárólag egyetlen felhasználó által aktivált műveletek jelennek meg. Ütemezett biztonsági mentési műveletek nem jelennek meg.

## <a name="backup-alerts-in-recovery-services-vault"></a>Biztonsági mentési riasztások a Recovery Services-tárolóban

Riasztások olyan elsősorban olyan forgatókönyvek, ahol vásárlói kell, hogy a megfelelő reagálhasson értesítést kaphat. A "Biztonsági mentési riasztások" szakasz az Azure Backup szolgáltatás által létrehozott riasztásokat mutatja. Ezek a riasztások a szolgáltatás által definiált és felhasználó egyéni nem minden olyan riasztásokat hozhat létre. A következő esetekben szolgáltatás által meghatározott alertable forgatókönyvek szerint

- Biztonsági mentési és visszaállítási hibák
- Biztonsági mentés sikeres volt, de figyelmeztetésekkel
- Védelem leállítása és megőrizni az adatokat és leállítása a védelmi adatok törlésével

Ha a riasztás nem jelenik meg a hiba, nincsenek néhány kivétellel.

- A felhasználó által megszakítva explicit módon a futó feladat
- A feladat sikertelen lesz, mivel egy másik biztonsági mentési feladat van folyamatban (ahhoz itt, mivel csak rá kell Várjon, amíg befejeződik az előző feladat tegyen semmit)
- A virtuális gép biztonsági mentési feladat sikertelen lesz, mivel a biztonsági másolat az Azure virtuális gép már nem létezik.

A fenti kivételek, a feltétellel, hogy ezeket a műveleteket (elsősorban a felhasználó által aktivált) eredménye megjelenik-e azonnal portal/PS/CLI ügyfeleken lettek kialakítva. A felhasználó, ezért azonnal észleli, és nem kell egy értesítést.

A következő Azure biztonsági mentési megoldások riasztásai Itt jelennek meg.

- Az Azure virtuális gép biztonsági mentéseinek
- Az Azure biztonsági másolatainak
- Például az SQL Azure-beli számítási biztonsági
- Az Azure Backup ügynöke (MAB)

> [!NOTE]
> A System Center Data Protection Manager (SC-DPM), a Microsoft Azure Backup Server (MABS) riasztások nem jelennek meg itt.

Riasztás súlyossága alapján a riasztások háromféle lehet definiálni:

- **Kritikus:** Elvileg minden biztonsági mentési vagy helyreállítási hiba (ütemezett vagy felhasználó által aktivált) a riasztások generálása vezetne, és akkor jelennek meg a kritikus riasztás. Felülíró műveletek, például a törlés backup lenne is generálhat kritikus riasztások.
- **Figyelmeztetés**: Ha a biztonsági mentési művelet sikeresen befejeződött, de néhány figyelmeztetésekkel fejeződött be, akkor jelennek meg figyelmeztető riasztások.
- **Tájékoztató**: Mai nem tájékoztató generál riasztást az Azure Backup szolgáltatás.

### <a name="notification-for-backup-alerts"></a>Értesítés biztonsági mentésekkel kapcsolatos riasztások

> [!NOTE]
> Értesítési konfigurációja csak az Azure Portalon végezhető el. PS/CLI vagy REST API-t vagy az Azure Resource Manager-sablon támogatása még nincs megadva.

Riasztás történik, ha az ügyfél kell értesítést kaphat. Az Azure Backup egy e-mailben a beépített értesítési mechanizmust biztosít. Egy egyéni e-mail-címeket vagy terjesztési listák riasztást kap értesítést is megadhat. Azt is beállíthatja az egyes egyedi riasztások értesülni szeretne, vagy hogy szeretne-e csoportosíthatja őket egy óránkénti összefoglaló majd értesítést kaphat.

![R-tároló beépített e-mail-értesítések](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Értesítések konfigurálása, ha egy üdvözli a bevezető e-mailt küldünk, amely megerősíti, hogy az Azure Backup e-maileket küldhet ezeket a címeket, ha egy riasztás akkor jön létre. Ha a gyakoriság az óránkénti összefoglaló úgy állították be, és riasztást kiváltott és a egy órán belül fog állni, nem lesz a közelgő óránkénti összefoglaló része.

> [!NOTE]
> Egy destruktív művelet, például a "stop protection adatok törlésével" hajtja végre, ha egy riasztás akkor jön létre, és a egy e-mailt küld az előfizetés-tulajdonosokat, rendszergazdák és társrendszergazdák még akkor is, ha az értesítések nincsenek konfigurálva a RS-tároló.

## <a name="next-steps"></a>További lépések

[Az Azure Monitor használatával az Azure biztonsági mentési számítási feladatok monitorozása](backup-azure-monitoring-use-azuremonitor.md)