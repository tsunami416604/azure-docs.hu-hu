---
title: Az új riasztások felületet Azure figyelőben felfedezése |} Microsoft Docs
description: Hogyan szerzői, megtekintése és egyszerűbb riasztások kezelése az Azure elérhetővé válnak az új egyszerű és méretezhető riasztások tapasztalatai ismertetése
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: bc9d788367ab14751f9f9158ac88149dc420368a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="explore-the-alerts-experience-in-azure-monitor"></a>Megismerheti a riasztások élményt Azure figyelője

## <a name="overview"></a>Áttekintés
 Az Azure-ban élmény rendelkezik egy friss riasztások keresse meg és frissített funkcióit. Az új felhasználói élmény érhető el a **riasztások** lapon az Azure-figyelő. Az alábbiakban néhány az új riasztások felületet (klasszikus) riasztások élmény képest használatának előnyei:

 - **Fired riasztások és a riasztás szabályok** - az új riasztások felületet, a riasztási szabályok (a riasztást kiváltó definition), és megkülönböztetett forgalomosztályból következik be riasztásokat (a riasztási szabály indítási példánya), ezért a működési és konfigurációs nézetek elkülönül egymástól.
 - **A metrika, tevékenységnapló és napló riasztások szerzői élmény egyesített** – az új riasztások szerzői élmény útmutatók a felhasználó mentén a riasztási szabályt, amely megfelelő a következőket a küldjön riasztást Önnek felderítéséhez egyszerűbbé teszi konfigurálásának lépésein.
 - **Nézet indította Naplóelemzési riasztásait az Azure portálon** – a riasztások élmény, akkor most is lásd: az előfizetésében Naplóelemzési riasztások következik be.  
 

A következő szakaszok ismertetik, részletesebben, az új felület működése.

## <a name="taxonomy"></a>Besorolás
A riasztást szabály és a riasztás indította objektumok szétválasztásának egységes a szerzői műveletekhez élmény különböző riasztástípusok között közben a következő fogalmak használja a riasztások élményt.

- **Célerőforrás** -cél lehet az Azure-erőforrásokkal. Cél erőforráson hatókör és jelek érhető el, mert így határozza meg. Példa célok: a virtuális gép, egy tárfiókot, a virtuálisgép-méretezési csoport, a Naplóelemzési munkaterület vagy az Application Insights-erőforrás.

- **Feltételek** – feltételek jel kombinációja és logika alkalmazza a cél erőforráson. Példák: Százalékos CPU > 70 % kiszolgáló válaszideje > 4 ms, a napló eredményszám lekérdezése a > 100 stb. 

- **Jel** - jelzi a célerőforrás által kibocsátott, és több típusúak lehetnek. Ez az előzetes kiadás támogatja **metrika**, **tevékenységnapló**, **Application Insights** és **napló** jel típusként.

- **Logikai** -felhasználó által meghatározott logikát, ellenőrizze, hogy a jel belül-e a várt tartományon és-értékek.  
 
- **A művelet** -egy adott hívás a fogadó (például e-mail címet vagy a webhook URL-CÍMÉT a könyvelési) értesítést küldeni. Értesítések általában elindítható több művelet. A riasztási típusokat, ebben az előzetesben támogatási művelet csoportok támogatott.  
 
- **Riasztási szabály** -volna a riasztást kiváltó feltétel definíciója. Ebben az előzetes verzióban a riasztások a riasztási szabály rögzíti a cél és a feltételek. A riasztási szabály egy engedélyezett vagy letiltott állapotban lehet.
 
    > [!NOTE]
    > Ez eltér a riasztások (klasszikus) élmény, ahol a riasztás a szabály és a égetett riasztás és ezért figyelmeztetés, aktív vagy le van tiltva állapota lehet.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Egy helyen riasztások megtekintése és kezelése
A riasztások (előzetes verzió) élmény célja a egyetlen hely, az összes Azure-riasztások megtekintése és kezelése. Az alábbi alszakaszok az új felület minden egyes képernyőjén feladatait ismertetik.

### <a name="alerts-preview-overview-page"></a>Riasztások (előzetes verzió) – áttekintés oldalra
**Monitor - riasztás (előzetes verzió)** – áttekintés oldalra a égetett riasztások összesített összegzését jeleníti meg, és teljes konfigurált/engedélyezve van a riasztási szabályok. Emellett az összes égetett riasztás listáját jeleníti meg. Az előfizetések, illetve a szűrő paramétereit frissíti az összesítések, és a riasztások listája következik be.

> [!NOTE]
> Riasztások látható égetett riasztások korlátozódnak támogatott metrika és activitylog riasztások; Az Azure figyelő áttekintése látható régebbi Azure riasztások lévőket is beleértve tűz riasztások száma

 ![riasztások – áttekintés](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>A riasztási szabályok kezelése
**Monitor - riasztás (előzetes verzió) > szabályok** egy egyetlen lap minden riasztási szabályok kezelése az Azure-előfizetések között. Felsorolja az összes riasztási szabályok (engedélyezett vagy tiltott) és a tároló erőforrásait, az erőforráscsoportok, a szabály neve vagy a állapota alapján rendezhető. A riasztási szabályok is le van tiltva vagy engedélyezhető vagy szerkeszteni ezen a lapon.  

 ![riasztások-szabályok](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Jelentéskészítő környezetét minden figyelési forrás egy riasztás
Az új riasztások élményt nyújt a riasztásokat függetlenül a figyelőszolgáltatás egységes módon hozhat létre, vagy a típus azt. Az összes riasztás következik be, és a kapcsolódó részletes adatok találhatók egylapos.  
 
Riasztás szerzői egy három lépéses feladat ahol a felhasználó először választja ki egy cél a riasztást, a jobb oldali jel kiválasztásával, és majd megadja az alkalmazandó a jel a riasztási szabály részeként logika követ. Az egyszerűsített létrehozási folyamat már nem szükséges tudni, hogy a figyelési forrás- vagy egy Azure-erőforrás kiválasztása előtt támogatott jelek a felhasználó. A közös szerzői élmény automatikusan kijelölt tároló-erőforrás alapján elérhető jelek listájának szűrése, és végig is vezeti riasztási logika létrehozása

További létrehozásával, a következő riasztástípusok [Itt](monitor-alerts-unified-usage.md).
- Metrika riasztások (néven aktuális élmény közel valós idejű metrika riasztások)
- Napló riasztások (Naplóelemzési)
- Napló riasztások (tevékenységi naplóit)
- Napló riasztások (Application Insights)

 

## <a name="alert-types-supported-in-this-preview"></a>Ebben az előzetes verzióban támogatott riasztástípusok


| **Jel típusa** | **Forrás figyelése** | **Leírás** | 
|-------------|----------------|-------------|
| Metrika | Az Azure-figyelő | Nevű [ **közel valós idejű metrika riasztások**](monitoring-near-real-time-metric-alerts.md), ezek a riasztások metrika metrika feltételek kiértékelése 1 perces gyakorisággal támogatja, és lehetővé teszik a több metrika szabályok. A támogatott erőforrástípusai listáját [Itt](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). Régebbi metrika riasztások meghatározott [Itt](monitoring-overview-alerts.md#alerts-in-different-azure-services) nem támogatottak az új riasztások élmény. Megtalálja azokat a riasztásokat (klasszikus)|
| Logs  | Log Analytics | Értesítéseket, illetve automatikus műveleteket futtatott metrika és/vagy esemény adatok napló keresési lekérdezés meghatározott feltételeknek eleget.|
| Tevékenységnapló | Tevékenységnaplók | Ez a kategória tartalmazza a rekordok összes Create, frissítés, és törli a kiválasztott célkiszolgáló (erőforrás és az erőforrások csoport/előfizetést) keresztül végrehajtott műveleteket. |
| Logs  | Állapotfigyelő szolgáltatás naplók | Nem támogatott a riasztások élményt nyújt.   |
| Logs  | Application Insights | Ez a kategória naplók és az alkalmazás teljesítményének részleteit tartalmazza. Analytics query használatával végrehajtott – az alkalmazásadatok alapján műveletek feltételeinek megadása. |
| Metrika | Application Insights | Nem támogatott a riasztások élményt nyújt. Megkeresi azokat a riasztásokat (klasszikus) |
| Rendelkezésreállás figyelésére szolgáló tesztek | Application Insights | Nem támogatott a riasztások élményt nyújt. Megkeresi azokat a riasztásokat (klasszikus) |


## <a name="next-steps"></a>További lépések
- [Az új riasztások felület használata létrehozása, megtekintése és riasztások kezelése](monitor-alerts-unified-usage.md)
- [További tudnivalók a napló riasztásokat a riasztások élmény](monitor-alerts-unified-log.md)
- [További tudnivalók a metrika riasztásokat a riasztások élmény](monitoring-near-real-time-metric-alerts.md)
- [További tudnivalók a tevékenység napló riasztásokat a riasztások élmény](monitoring-activity-log-alerts-new-experience.md)
