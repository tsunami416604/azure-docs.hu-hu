---
title: Azure Automation frissítési felmérések megtekintése
description: Ez a cikk azt ismerteti, hogyan lehet megtekinteni Update Management központi telepítések frissítési értékeléseit.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 321146364897d46a403bdfd6789fcb219179d88c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830599"
---
# <a name="view-update-assessments"></a>Frissítésfelmérések megtekintése

A Azure Automation fiókjában válassza a **Update Management** lehetőséget a gépek állapotának megtekintéséhez.

Ez a nézet a gépekre, a hiányzó frissítésekre, a frissítések telepítésére és az ütemezett frissítések telepítésére vonatkozó információkat tartalmaz. A **megfelelőség** oszlopban láthatja, hogy mikor történt a gép utolsó értékelése. A **frissítési ügynök készültségi** oszlopában láthatja a frissítési ügynök állapotát. Ha probléma merül fel, kattintson a hivatkozásra a hibaelhárítási dokumentációhoz, amely segíthet a probléma megoldásában.

Ha olyan naplót szeretne futtatni, amely adatokat ad vissza a gépre, a frissítésre vagy a központi telepítésre vonatkozóan, válassza ki a megfelelő elemet a listában. Megnyílik a napló keresése ablaktábla a kiválasztott elem lekérdezésével.

![Update Management alapértelmezett nézet](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza a **hiányzó frissítések** lehetőséget a gépekből hiányzó frissítések listájának megtekintéséhez. Minden frissítés szerepel a listáján, és kiválasztható. Információ arról, hogy hány gép szükséges a frissítéshez, az operációs rendszer részleteit, valamint egy hivatkozást a további információkhoz. A naplók keresése ablaktáblán további részleteket is láthat a frissítésekről.

![Hiányzó frissítések](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>A frissítési besorolások használata

Az alábbi táblázat a Update Management támogatott frissítési besorolásait sorolja fel, az egyes besorolások definíciója szerint.

### <a name="windows"></a>Windows

|Osztályozás  |Description  |
|---------|---------|
|Kritikus frissítések     | Frissítések a kritikus, nem biztonsági hibákkal kapcsolatos problémák megoldásához.        |
|Biztonsági frissítések     | A termékre vonatkozó, biztonsággal kapcsolatos problémák frissítései.        |
|Kumulatív frissítések     | Az egyszerű üzembe helyezéshez együtt csomagolt gyorsjavítások készletei.        |
|Funkciócsomagok     | A termék kiadásán kívül terjesztett új termék-szolgáltatások.        |
|Szervizcsomagok     | Egy alkalmazásra alkalmazott gyorsjavítások készletei.        |
|Definíciófrissítések     | Vírus-vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Segédprogramok vagy szolgáltatások, amelyek egy vagy több feladat elvégzésére nyújtanak segítséget.        |
|Frissítések     | A jelenleg telepített alkalmazások vagy fájlok frissítései.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Osztályozás  |Description  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Adott probléma vagy termékspecifikus, biztonsággal kapcsolatos probléma frissítései.         |
|Egyéb frissítések     | Minden egyéb olyan frissítés, amely nem kritikus jellegű, vagy amelyek nem biztonsági frissítések.        |

A Linux esetében Update Management a kritikus frissítések és a biztonsági frissítések a felhőben való megkülönböztetését az értékelési információk megjelenítése során. (Ez a részletesség a Felhőbeli adatgazdagítás miatt lehetséges.) A javításhoz a Update Management a gépen elérhető besorolási értékekre támaszkodik. A többi disztribúciótól eltérően a CentOS nem rendelkezik ezekkel az információkkal a termék RTM-verzióiban. Ha olyan CentOS-számítógépekkel rendelkezik, amelyek a következő parancs biztonsági értékének visszaadására vannak konfigurálva, Update Management besorolások alapján lehet javítani:

```bash
sudo yum -q --security check-update
```

Jelenleg nem támogatott a natív besorolás engedélyezése – az adatelérhetőség a CentOS-ben. Jelenleg csak a lehető legjobb támogatást biztosítja azoknak az ügyfeleknek, akik saját maguk engedélyezték ezt a funkciót.

A Red Hat Enterprise 6-os verziójának frissítéseinek besorolásához telepítenie kell a yum-Security beépülő modult. Red Hat Enterprise Linux 7 rendszeren a beépülő modul már maga is része a yum szolgáltatásnak, nem kell semmit telepítenie. További információkért lásd a Red Hat [Tudásbázis következő cikkét](https://access.redhat.com/solutions/10021).

## <a name="next-steps"></a>Következő lépések

Általános információk: az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md).
