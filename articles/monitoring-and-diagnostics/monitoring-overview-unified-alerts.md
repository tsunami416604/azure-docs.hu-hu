---
title: "Az új riasztások (előzetes verzió) felületet Azure figyelőben felfedezése |} Microsoft Docs"
description: "Hogyan szerzői, megtekintése és egyszerűbb riasztások kezelése az Azure elérhetővé válnak az új egyszerű és méretezhető riasztások tapasztalatai ismertetése"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 316dcd53509897a6efc387749ca6f9ec268cb7ac
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Fedezze fel az új riasztások (előzetes verzió) felületet Azure a Teljesítményfigyelőben

## <a name="overview"></a>Áttekintés
 Az Azure-ban élmény rendelkezik egy friss riasztások keresse meg és frissített funkcióit. Az új felhasználói élmény érhető el a **riasztások (előzetes verzió)** lapon az Azure-figyelő. Az alábbiakban néhány az új riasztások (előzetes verzió) felületet használatának előnyei:

 - **Fired riasztások és a riasztás szabályok** - Alerts(Preview) élmény a riasztási szabályok (a riasztást kiváltó definition), és megkülönböztetett forgalomosztályból következik be riasztásokat (a riasztási szabály indítási példánya), ezért a működési és konfigurációs nézetek elkülönül egymástól.
 - **A metrika és napló riasztások szerzői élmény egyesített** – szerzői élmény útmutatók a felhasználó a riasztási szabályt, amely megfelelő a következőket a küldjön riasztást Önnek felderítéséhez egyszerűbbé teszi konfigurálásának lépésein mentén új riasztások (előzetes verzió).
 - **Nézet indította Naplóelemzési riasztásait az Azure portálon** -élmény a figyelmeztetések (előzetes verzió), akkor most is lásd: az előfizetésében Naplóelemzési riasztások következik be.  
 - **Egyesített jelentéskészítő környezetét tevékenység napló riasztások** -hozhatók létre közvetlenül a napló tevékenységriasztásokat **figyelő** > **riasztások (előzetes verzió)**. Korábban is létrehozhatók ezeket csak a **figyelő** > **tevékenységnapló**.

A következő szakaszok ismertetik, részletesebben, az új felület működése.

## <a name="taxonomy"></a>Besorolás
A riasztást szabály és a riasztás indította objektumok szétválasztásának egységes a szerzői műveletekhez élmény különböző riasztástípusok között közben a következő fogalmak használja a Alerts(preview) élményt.

- **Célerőforrás** -cél lehet az Azure-erőforrásokkal. Cél erőforráson hatókör és jelek érhető el, mert így határozza meg. Példa célok: a virtuális gép, egy tárfiókot, a virtuálisgép-méretezési csoport, Naplóelemzési munkaterület vagy megoldás.

- **Feltételek** – feltételek jel kombinációja és logika alkalmazza a cél erőforráson. Példák: Százalékos CPU > 70 % kiszolgáló válaszideje > 4 ms, a napló eredményszám lekérdezése a > 100 stb. 

- **Jel** - jelzi a célerőforrás által kibocsátott, és több típusúak lehetnek. Ez az előzetes kiadás támogatja **metrika**, **tevékenységnapló**, **Application Insights** és **napló** jel típusként.

- **Logikai** -felhasználó által meghatározott logikát, ellenőrizze, hogy a jel belül-e a várt tartományon és-értékek.  
 
- **A művelet** -egy adott hívás a fogadó (például e-mail címet vagy a webhook URL-CÍMÉT a könyvelési) értesítést küldeni. Értesítések általában elindítható több művelet. A riasztási típusokat, ebben az előzetesben támogatási művelet csoportok támogatott.  
 
- **Riasztási szabály** -volna a riasztást kiváltó feltétel definíciója. Ebben az előzetes verzióban a riasztások a riasztási szabály rögzíti a cél és a feltételek. A riasztási szabály egy engedélyezett vagy letiltott állapotban lehet.
 
- **Riasztás indította** -jön létre, amikor a riasztási szabály engedélyezett lép működésbe. A égetett riasztási objektum Fired vagy bizonytalan állapotban lehet.

    > [!NOTE]
    > Ez eltér az aktuális riasztások élmény, ahol a riasztás a szabály és a égetett riasztás és ezért figyelmeztetés, aktív vagy le van tiltva állapota lehet.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Egy helyen riasztások megtekintése és kezelése
A riasztások (előzetes verzió) élmény célja a egyetlen hely, az összes Azure-riasztások megtekintése és kezelése. Az alábbi alszakaszok az új felület minden egyes képernyőjén feladatait ismertetik.

### <a name="alerts-preview-overview-page"></a>Riasztások (előzetes verzió) – áttekintés oldalra
**Monitor - riasztás (előzetes verzió)** – áttekintés oldalra a égetett riasztások összesített összegzését jeleníti meg, és teljes konfigurált/engedélyezve van a riasztási szabályok. Emellett az összes égetett riasztás listáját jeleníti meg. Az előfizetések, illetve a szűrő paramétereit frissíti az összesítések, és a riasztások listája következik be.

> [!NOTE]
> Riasztások (előzetes verzió) látható égetett riasztások korlátozódnak támogatott metrika és napló riasztások; Az Azure a figyelő akkor jelez régebbi Azure riasztások lévőket is beleértve tűz riasztások száma

 ![riasztások – előzetes verzió-áttekintése](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>A riasztási szabályok kezelése
**Monitor - riasztás (előzetes verzió) > szabályok** egy egyetlen lap minden riasztási szabályok kezelése az Azure-előfizetések között. Felsorolja az összes riasztási szabályok (engedélyezett vagy tiltott) és a tároló erőforrásait, az erőforráscsoportok, a szabály neve vagy a állapota alapján rendezhető. A riasztási szabályok is le van tiltva vagy engedélyezhető vagy szerkeszteni ezen a lapon.  

 ![riasztások – előzetes verzió-szabályok](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Jelentéskészítő környezetét minden figyelési forrás egy riasztás
A riasztásokban (előzetes verzió) tapasztal, riasztásokat függetlenül a figyelőszolgáltatás egységes módon hozhat létre, vagy a típus azt. Az összes riasztás következik be, és a kapcsolódó részletes adatok találhatók egylapos.  
 
Riasztás szerzői egy három lépéses feladat ahol a felhasználó először választja ki egy cél a riasztást, a jobb oldali jel kiválasztásával, és majd megadja az alkalmazandó a jel a riasztási szabály részeként logika követ. Az egyszerűsített létrehozási folyamat már nem szükséges tudni, hogy a figyelési forrás- vagy egy Azure-erőforrás kiválasztása előtt támogatott jelek a felhasználó. A közös szerzői élmény automatikusan kijelölt tároló-erőforrás alapján elérhető jelek listájának szűrése, és végig is vezeti riasztási logika létrehozása

További létrehozásával, a következő riasztástípusok [Itt](monitor-alerts-unified-usage.md).
- Metrika riasztások (néven aktuális élmény közel valós idejű metrika riasztások)
- Napló riasztások (Naplóelemzési)
- Napló riasztások (tevékenységi naplóit)
- Napló riasztások (Application Insights)

 

## <a name="alert-types-supported-in-this-preview"></a>Ebben az előzetes verzióban támogatott riasztástípusok


| **Jel típusa** | **Forrás figyelése** | **Leírás** | 
|-------------|----------------|-------------|
| Metrika | Az Azure-figyelő | Nevű [ **közel valós idejű metrika riasztások** ](monitoring-near-real-time-metric-alerts.md) aktuális élmény, a metrika a riasztások metrika feltételek kiértékelése 1 perces gyakorisággal támogatja, és lehetővé teszik a több metrika szabályok. A támogatott erőforrástípusai listáját [Itt](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for). Más metrika riasztások meghatározott [Itt](monitoring-overview-alerts.md#alerts-in-different-azure-services) nem támogatottak a riasztások (előzetes verzió) élményt nyújt.|
| Logs  | Log Analytics | Értesítéseket, illetve automatikus műveleteket futtatott metrika és/vagy esemény adatok napló keresési lekérdezés meghatározott feltételeknek eleget.|
| Logs  | Tevékenységnaplók | Ez a kategória tartalmazza a rekordok összes Create, frissítés, és törli a kiválasztott célkiszolgáló (erőforrás és az erőforrások csoport/előfizetést) keresztül végrehajtott műveleteket. |
| Logs  | Állapotfigyelő szolgáltatás naplók | Nem támogatott (előzetes verzió) riasztások élményt nyújt.   |
| Logs  | Application Insights | Ez a kategória naplók és az alkalmazás teljesítményének részleteit tartalmazza. Analytics query használatával végrehajtott – az alkalmazásadatok alapján műveletek feltételeinek megadása. |
| Metrika | Application Insights | Nem támogatott (előzetes verzió) riasztások élményt nyújt. |
| Rendelkezésreállás figyelésére szolgáló tesztek | Application Insights | Nem támogatott (előzetes verzió) riasztások élményt nyújt. |


## <a name="next-steps"></a>További lépések
- [Az új riasztások (előzetes verzió) felület használata létrehozása, megtekintése és riasztások kezelése](monitor-alerts-unified-usage.md)
- [További tudnivalók a napló riasztásokat a riasztások (előzetes verzió) élmény](monitor-alerts-unified-log.md)
- [További tudnivalók a metrika riasztásokat a riasztások (előzetes verzió) élmény](monitoring-near-real-time-metric-alerts.md)
- [További tudnivalók a tevékenység napló riasztásokat a riasztások (előzetes verzió) élmény](monitoring-activity-log-alerts-new-experience.md)
