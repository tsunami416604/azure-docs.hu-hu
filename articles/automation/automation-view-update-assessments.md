---
title: Az Azure Update Management frissítési felmérések megtekintése
description: Ez a cikk azt ismerteti, hogyan lehet megtekinteni a frissítési központi telepítések frissítési értékeléseit
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377719"
---
# <a name="view-azure-update-management-update-assessments"></a>Az Azure Update Management frissítési felmérések megtekintése

Az Automation-fiókjában válassza a **Update Management** lehetőséget a gépek állapotának megtekintéséhez.

Ez a nézet a gépekre, a hiányzó frissítésekre, a frissítések telepítésére és az ütemezett frissítések telepítésére vonatkozó információkat tartalmaz. A **megfelelőség oszlopban**láthatja, hogy mikor történt a gép utolsó értékelése. A **frissítési ügynök készültségi** oszlopában láthatja, hogy a frissítési ügynök állapota. Ha probléma merül fel, a hivatkozásra kattintva keresse meg a hibaelhárítási dokumentációt, amelyből megtudhatja, hogy milyen lépéseket kell tennie a probléma megoldásához.

Ha olyan naplót szeretne futtatni, amely adatokat ad vissza a gépre, a frissítésre vagy a központi telepítésre vonatkozóan, válassza ki az elemet a listában. Megnyílik a **napló keresése** ablaktábla a kiválasztott elem lekérdezésével:

![Update Management alapértelmezett nézet](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza a **hiányzó frissítések** lehetőséget a gépekből hiányzó frissítések listájának megtekintéséhez. Minden frissítés szerepel a listáján, és kiválasztható. A frissítéshez szükséges gépek számával, az operációs rendszerrel és a további információkra mutató hivatkozással kapcsolatos információk láthatók. A **naplók keresése** panel további részleteket jelenít meg a frissítésekről.

![Hiányzó frissítések](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Frissítési besorolások

Az alábbi táblázat a Update Management frissítési besorolásait sorolja fel, az egyes besorolások definíciója szerint.

### <a name="windows"></a>Windows

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy adott problémára vonatkozó frissítés, amely kritikus, nem biztonsággal kapcsolatos hibára vonatkozik.        |
|Biztonsági frissítések     | Egy termékre vonatkozó, biztonsággal kapcsolatos probléma frissítése.        |
|Kumulatív frissítések     | Az egyszerű üzembe helyezéshez együtt csomagolt gyorsjavítások összesített készlete.        |
|Funkciócsomagok     | A termék kiadásán kívül terjesztett új termék-szolgáltatások.        |
|Szervizcsomagok     | Egy alkalmazásra alkalmazott gyorsjavítások kumulatív készlete.        |
|Definíciófrissítések     | Vírus-vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Olyan segédprogram vagy szolgáltatás, amely egy vagy több feladat elvégzését segíti elő.        |
|Frissítések     | Egy aktuálisan telepített alkalmazás vagy fájl frissítése.        |

### <a name="linux-2"></a>Linux

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Adott probléma vagy termékspecifikus, biztonsággal kapcsolatos probléma frissítései.         |
|Egyéb frissítések     | Minden egyéb olyan frissítés, amely nem kritikus jellegű, vagy nem biztonsági frissítés.        |

A Linux rendszerben a Update Management különbséget tehet a Felhőbeli kritikus és biztonsági frissítések között, miközben a felhőben az adatgazdagítás miatt kiértékeli az értékelést. A javításhoz a Update Management a gépen elérhető besorolási értékekre támaszkodik. A többi disztribúciótól eltérően a CentOS nem rendelkezik ezekkel az információkkal a dobozból. Ha a CentOS-gépek úgy vannak konfigurálva, hogy a következő parancs biztonsági értékeit adja vissza, Update Management a besorolások alapján lesz képes a javításra.

```bash
sudo yum -q --security check-update
```

Jelenleg nincs olyan metódus, amely támogatja a natív besorolást – az adatelérhetőség a CentOS-ben. Jelenleg csak a lehető legjobb támogatást nyújtja azoknak az ügyfeleknek, akik saját maguk engedélyezték ezt.

## <a name="next-steps"></a>Következő lépések

A frissítési felmérések megtekintése után az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című szakaszban ismertetett lépéseket követve ütemezhet egy frissítés központi telepítését.