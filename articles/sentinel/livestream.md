---
title: Használja a vadászat Livestream az Azure Sentinel fenyegetések észlelése | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan használhatja a vadászat livestream az Azure Sentinel az adatok nyomon követéséhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582126"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Az Azure Sentinel ben a vadászat élő közvetítésével észlelheti a fenyegetéseket

> [!IMPORTANT]
> Az Azure Sentinel élő közvetítésének levadászása jelenleg nyilvános előzetes verzióban érhető el, és fokozatosan jelenik meg a bérlők számára.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)


A vadászatélő közvetítéssel interaktív munkameneteket hozhat létre, amelyek lehetővé teszik az újonnan létrehozott lekérdezések események bekövetkezéseként történő tesztelését, az egyezést követő munkamenetek értesítéseinek lefolytatását, és szükség esetén vizsgálatok indítását. A Log Analytics-lekérdezések használatával gyorsan létrehozhat élőközvetítési munkamenetet.

- **Újonnan létrehozott lekérdezések tesztelése események bekövetkeztekor**
    
    A lekérdezéseket az eseményekre aktívan alkalmazott aktuális szabályok ütközése nélkül tesztelheti és módosíthatja. Miután meggyőződött arról, hogy ezek az új lekérdezések a várt módon működnek, egyszerűen előléptetheti őket egyéni riasztási szabályokká egy olyan beállítás kiválasztásával, amely a munkamenetet riasztásra emeli.

- **Értesítés a fenyegetések bekövetkezéséről**
    
    Összehasonlíthatja a fenyegetés adatcsatornáit az összesített naplóadatokkal, és értesítést kaphat egyezés ről. A fenyegetési adatcsatornák olyan folyamatos adatfolyamok, amelyek potenciális vagy aktuális fenyegetésekhez kapcsolódnak, így az értesítés potenciális fenyegetést jelezhet a szervezet számára. Hozzon létre egy élőközvetítési munkamenetet egyéni riasztási szabály helyett, ha értesítést szeretne kapni egy lehetséges problémáról anélkül, hogy az egyéni riasztási szabály fenntartásának általános költségei lennének.

- **Vizsgálatok indítása**
    
    Ha van egy aktív vizsgálat, amely magában foglalja az eszköz, például egy gazdagép vagy felhasználó, megtekintheti az adott tevékenység (vagy bármely) tevékenység a naplóadatok, ahogy az adott eszköz. Értesítést kaphat, ha ez a tevékenység bekövetkezik.


## <a name="create-a-livestream-session"></a>Élőközvetítési munkamenet létrehozása

Élő közvetítési munkamenetet létrehozhat egy meglévő vadászlekérdezésből, vagy létrehozhatja a munkamenetet a semmiből.

1. Az Azure Portalon keresse meg a **Sentinel** > **fenyegetéskezelési** > **vadászat .**

2. Élőközvetítéses munkamenet létrehozása vadászati lekérdezésből:
    
    1. A **Lekérdezések** lapon keresse meg a használni hozandó vadászati lekérdezést.
    2. Kattintson a jobb gombbal a lekérdezésre, és válassza **a Hozzáadás az élő közvetítéshez parancsot.** Példa:
    
    > [!div class="mx-imgBorder"]
    > ![Livestream munkamenet létrehozása az Azure Sentinel vadászati lekérdezésből](./media/livestream/livestream-from-query.png)

3. Élőközvetítéses munkamenet létrehozása teljesen újjától kezdve: 
    
    1. Az Élő közvetítés lap **kijelölése**
    2. Válassza **az Ugrás az élő közvetítéshez**lehetőséget.
    
4. Az **Élő közvetítés** ablaktáblán:
    
    - Ha lekérdezésből indította el az élő közvetítést, tekintse át a lekérdezést, és hajtsa végre a végrehajtani kívánt módosításokat.
    - Ha teljesen újonnan indította el az élő közvetítést, hozza létre a lekérdezést. 

5. Válassza a **parancssáv Lejátszás** parancsát.
    
    A parancssáv alatti állapotsor azt jelzi, hogy az élőfolyam-munkamenet fut vagy szünetel. A következő példában a munkamenet fut:
    
    > [!div class="mx-imgBorder"]
    > ![élőközvetítéses munkamenet létrehozása az Azure Sentinel vadászatából](./media/livestream/livestream-session.png)

6. Válassza a **Parancssáv Mentés parancsát.**
    
    Ha nem **választja a Szünet lehetőséget,** a munkamenet addig folytatódik, amíg ki nem jelentkezik az Azure Portalról.

## <a name="view-your-livestream-sessions"></a>Az élőközvetítéses munkamenetek megtekintése

1. Az Azure Portalon keresse meg a **Sentinel** > **fenyegetéskezelés** > **vadászatélő** > **közvetítése** lapot.

2. Jelölje ki a megtekinteni vagy szerkesztni kívánt élőfolyam-munkamenetet. Példa:
    
    > [!div class="mx-imgBorder"]
    > ![élőközvetítéses munkamenet létrehozása az Azure Sentinel vadászati lekérdezéséből](./media/livestream/livestream-tab.png)
    
    Megnyílik a kiválasztott élőközvetítési munkamenet lejátszása, szüneteltetése, szerkesztése és így tovább.

## <a name="receive-notifications-when-new-events-occur"></a>Értesítések fogadása új események ről

Mivel az új események élőközvetítési értesítései az Azure Portal-értesítéseket használják, ezeket az értesítéseket az Azure Portal használatakor láthatja. Példa:

![Az Azure Portal értesítése az élő közvetítéshez](./media/livestream/notification.png)

Válassza ki az értesítést az **Élőáram** ablaktábla megnyitásához.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Élőközvetítési munkamenet riasztásra való felfelé ítése

Az élő közvetítésmunkamenetet új riasztássá léptetheti, ha az Elevate lehetőséget választja a megfelelő élőközvetítési munkamenet parancssávjáról **való riasztáshoz:**

> [!div class="mx-imgBorder"]
> ![Az élőközvetítésmunkamenet figyelmeztetésre való felfelé ítése](./media/livestream/elevate-to-alert.png)

Ez a művelet megnyitja a szabálykészítő varázslót, amely előre ki van töltve az élőközvetítési munkamenethez társított lekérdezéssel.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a vadászat élő közvetítés az Azure Sentinel. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:

- [Proaktív vadászat a fenyegetésekre](hunting.md)
- [Automatikus vadászati kampányok jegyzetfüzetei használata](notebooks.md)
