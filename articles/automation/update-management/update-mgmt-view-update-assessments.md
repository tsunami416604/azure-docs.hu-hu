---
title: Azure Automation frissítési felmérések megtekintése
description: Ez a cikk azt ismerteti, hogyan lehet megtekinteni Update Management központi telepítések frissítési értékeléseit.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 92861304a946e357b2b265cd825eceb8e22f7d2d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450122"
---
# <a name="view-update-assessments"></a>Frissítésfelmérések megtekintése

A Update Managementban megtekintheti a gépek adatait, a hiányzó frissítéseket, a frissítések központi telepítését és az ütemezett frissítések központi telepítését.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A Update Managementban megtekintheti a gépek adatait, a hiányzó frissítéseket, a frissítések központi telepítését és az ütemezett frissítések központi telepítését.

[![Update Management alapértelmezett nézet](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

A frissítési felmérés megtekintéséhez hajtsa végre a következő műveleteket.

1. A Azure Portalban navigáljon az **Automation-fiókok** elemre, és válassza ki az Automation-fiókját, amelynek Update Management engedélyezve van a listából.

2. Az Automation-fiókban válassza a bal oldali ablaktábla **frissítés kezelése** elemét.

3. A környezet frissítései a **frissítés kezelése** oldalon találhatók. Ha valamelyik frissítést a rendszer hiányzóként azonosította, a **hiányzó frissítések** lapon megjelenik egy lista.

   A **megfelelőség** oszlopban megtekintheti a gép legutóbbi értékelésének időpontját. Az **ügynök készültségének frissítése** oszlopban láthatja a frissítési ügynök állapotát. Ha probléma merül fel, kattintson a hivatkozásra a hibaelhárítási dokumentációhoz, amely segíthet a probléma megoldásában.

4. Az **információ hivatkozás**alatt válassza ki a frissítésre mutató hivatkozást a támogatási cikk megnyitásához, amely fontos információkat nyújt a frissítésről.

     [![Frissítési állapot megtekintése](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

5. A frissítés gombra kattintva nyissa meg a naplóbeli keresés ablaktáblát. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Módosíthatja a lekérdezést, vagy létrehozhat egy saját lekérdezést a részletes információk megtekintéséhez.

    [![Napló lekérdezési eredményeinek megtekintése](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza a **hiányzó frissítések** lehetőséget a gépekből hiányzó frissítések listájának megtekintéséhez. Minden frissítés szerepel a listáján, és kiválasztható. Információ arról, hogy hány gép szükséges a frissítéshez, az operációs rendszer részleteit, valamint egy hivatkozást a további információkhoz. A naplók keresése ablaktáblán további részleteket is láthat a frissítésekről.

![Hiányzó frissítések](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>A frissítési besorolások használata

Az alábbi táblázat a Update Management támogatott frissítési besorolásait sorolja fel, az egyes besorolások definíciója szerint.

### <a name="windows"></a>Windows

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Frissítések a kritikus, nem biztonsági hibákkal kapcsolatos problémák megoldásához.        |
|Biztonsági frissítések     | A termékre vonatkozó, biztonsággal kapcsolatos problémák frissítései.        |
|Kumulatív frissítések     | Az egyszerű üzembe helyezéshez együtt csomagolt gyorsjavítások készletei.        |
|Funkciócsomagok     | A termék kiadásán kívül terjesztett új termék-szolgáltatások.        |
|Szervizcsomagok     | Egy alkalmazásra alkalmazott gyorsjavítások készletei.        |
|Definíciófrissítések     | Vírus-vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Segédprogramok vagy szolgáltatások, amelyek egy vagy több feladat elvégzésére nyújtanak segítséget.        |
|Frissítések     | A jelenleg telepített alkalmazások vagy fájlok frissítései.        |

### <a name="linux"></a>Linux

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Adott probléma vagy termékspecifikus, biztonsággal kapcsolatos probléma frissítései.         |
|Egyéb frissítések     | Minden egyéb olyan frissítés, amely nem kritikus jellegű, vagy amelyek nem biztonsági frissítések.        |

A Linux esetében Update Management a kritikus frissítések és a biztonsági frissítések a felhőben való megkülönböztetését az értékelési információk megjelenítése során. (Ez a részletesség a Felhőbeli adatgazdagítás miatt lehetséges.) A javításhoz a Update Management a gépen elérhető besorolási értékekre támaszkodik. A többi disztribúciótól eltérően a CentOS nem rendelkezik ezekkel az információkkal a termék RTM-verzióiban. Ha olyan CentOS-számítógépekkel rendelkezik, amelyek a következő parancs biztonsági értékének visszaadására vannak konfigurálva, Update Management besorolások alapján lehet javítani:

```bash
sudo yum -q --security check-update
```

Jelenleg nem támogatott a natív besorolás engedélyezése – az adatelérhetőség a CentOS-ben. Jelenleg csak a lehető legjobb támogatást biztosítja azoknak az ügyfeleknek, akik saját maguk engedélyezték ezt a funkciót.

A Red Hat Enterprise 6-os verziójának frissítéseinek besorolásához telepítenie kell a yum-Security beépülő modult. Red Hat Enterprise Linux 7 rendszeren a beépülő modul már maga is része a yum szolgáltatásnak, nem kell semmit telepítenie. További információkért lásd a Red Hat [Tudásbázis következő cikkét](https://access.redhat.com/solutions/10021).

## <a name="next-steps"></a>További lépések

A folyamat következő fázisában a nem megfelelő gépek [frissítéseinek telepítése](update-mgmt-deploy-updates.md) és a telepítési eredmények áttekintése.
