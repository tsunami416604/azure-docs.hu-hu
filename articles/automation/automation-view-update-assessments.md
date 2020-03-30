---
title: Az Azure Update Management frissítési felmérésének megtekintése
description: Ez a cikk a frissítési központi telepítések frissítési felmérései megtekintését ismerteti.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278414"
---
# <a name="view-azure-update-management-update-assessments"></a>Az Azure Update Management frissítési felmérésének megtekintése

Az Azure Automation-fiókban válassza **a Frissítéskezelés lehetőséget** a gépek állapotának megtekintéséhez.

Ez a nézet a gépekről, a hiányzó frissítésekről, a központi telepítések frissítéséről és az ütemezett frissítési központi telepítésekről nyújt tájékoztatást. A **MEGFELELŐSÉG** oszlopban láthatja a gép legutóbbi értékelésének idejét. Az **UPDATE AGENT READINESS** oszlopban láthatja a frissítési ügynök állapotát. Ha probléma merül fel, válassza ki a hivatkozást a hibaelhárítási dokumentációhoz, amely segíthet a probléma megoldásában.

Ha olyan naplókeresést szeretne futtatni, amely a gépről, frissítésről vagy központi telepítésről ad vissza információkat, válassza ki a megfelelő elemet a listában. Megnyílik **a Naplókeresés** ablaktábla a kijelölt elem lekérdezésével:

![A kezelés frissítése alapértelmezett nézet](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza **a Hiányzó frissítések** lehetőséget a gépekről hiányzó frissítések listájának megtekintéséhez. Minden frissítés megjelenik, és kiválasztható. A frissítést igénylő gépek számáról, az operációs rendszer részleteiről és a további információkra mutató hivatkozásról tájékoztató információk jelennek meg. A **Napló keresési** ablaktábla a frissítésekkel kapcsolatos további részleteket is megjelenít.

![Hiányzó frissítések](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Frissítési besorolások

Az alábbi táblázatok az Update Management támogatott frissítési besorolásait sorolják fel, az egyes besorolásokhoz egy-egy definícióval.

### <a name="windows"></a>Windows

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy adott probléma frissítése, amely egy kritikus, nem biztonsággal kapcsolatos hibát szüntet meg.        |
|Biztonsági frissítések     | Termékspecifikus, biztonsággal kapcsolatos probléma frissítése.        |
|Kumulatív frissítések     | A könnyű telepítés érdekében összecsomagolt gyorsjavítások összesítő készlete.        |
|Funkciócsomagok     | A termékkiadáson kívül terjesztett új termékfunkciók.        |
|Szervizcsomagok     | Az alkalmazásra alkalmazott gyorsjavítások összesítő készlete.        |
|Definíciófrissítések     | Vírus- vagy más definíciós fájlok frissítése.        |
|Eszközök     | Olyan segédprogram vagy szolgáltatás, amely egy vagy több feladat elvégzését segíti.        |
|Frissítések     | Jelenleg telepített alkalmazás vagy fájl frissítése.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Frissítések egy adott problémára vagy egy termékspecifikus, biztonsággal kapcsolatos problémára.         |
|Egyéb frissítések     | Minden más olyan frissítés, amely nem kritikus jellegű, vagy nem biztonsági frissítés.        |

Linux esetén az Update Management különbséget tud tenni a kritikus frissítések és a biztonsági frissítések között a felhőben, miközben értékelési adatokat jelenít meg. (Ez a részletesség a felhőben az adatok gazdagítása miatt lehetséges.) A javításhoz az Update Management a számítógépen elérhető besorolási adatokra támaszkodik. Más disztribúciókkal ellentétben a CentOS nem rendelkezik ezekkel az információkkal a termék RTM verzióiban. Ha a Következő parancs biztonsági adatainak visszaadására van konfigurálva a CentOS-gépek, az Update Management a besorolások alapján javíthat:

```bash
sudo yum -q --security check-update
```

Jelenleg nincs támogatott módszer a natív besorolási adatok rendelkezésre állásának engedélyezésére a CentOS rendszeren. Jelenleg csak a legjobb támogatást nyújtjuk azoknak az ügyfeleknek, akik önállóan engedélyezték ezt a funkciót.

A Red Hat Enterprise 6-os verziójának frissítéseinek besorolásához telepítenie kell a yum-security bővítményt. A Red Hat Enterprise Linux 7-en a plugin már része a yumnak, nincs szükség a telepítésre. További információt a Red Hat [következő tudásbáziscikkében](https://access.redhat.com/solutions/10021)talál.

## <a name="next-steps"></a>További lépések

A frissítési értékelések megtekintése után ütemezheti a frissítések központi telepítését az [Azure-beli virtuális gépek frissítéseinek és javításainak kezelésécímű](automation-tutorial-update-management.md)lépések ben leírt lépésekkel.
