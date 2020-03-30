---
title: Riasztások vizsgálata az Azure Sentinel használatával| Microsoft dokumentumok
description: Az oktatóanyag ból megtudhatja, hogyan vizsgálhatja meg a riasztásokat az Azure Sentinel segítségével.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585203"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Oktatóanyag: A fenyegetések észlelése nem a beépített


> [!IMPORTANT]
> A beépített fenyegetésészlelés jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure Sentinelhez, értesítést szeretne kapni, ha valami gyanús történik. Ehhez az Azure Sentinel beépített sablonokat biztosít. Ezeket a sablonokat a Microsoft biztonsági szakértőiből és elemzőiből álló csapata tervezte ismert fenyegetések, gyakori támadási vektorok és gyanús tevékenységeszkalációs láncok alapján. A sablonok engedélyezése után automatikusan megkeresnek minden olyan tevékenységet, amely gyanúsnak tűnik a környezetben. A sablonok közül sok testre szabható, hogy az Igényeinek megfelelően keressen vagy szűrje ki a tevékenységeket. Az ezek a sablonok által létrehozott riasztások olyan incidenseket hoznak létre, amelyeket a környezetben rendelhet hozzá és vizsgálhat meg.

Ez az oktatóanyag segít a fenyegetések észlelésének az Azure Sentinel használatával:

> [!div class="checklist"]
> * Használatra használható észlelések használata
> * A fenyegetésekre adott válaszok automatizálása

## <a name="about-out-of-the-box-detections"></a>A beépített észlelések ismertetése

Az összes beépített észlelés megtekintéséhez nyissa meg az **Analytics,** majd a **Szabálysablonok című elemet.** Ez a lap tartalmazza az Azure Sentinel beépített szabályait.

   ![Az Azure Sentinel használatával beépített észlelések segítségével kereshetfenyegetéseket](media/tutorial-detect-built-in/view-oob-detections.png)

A következő sablontípusok érhetők el:

- **Microsoft biztonság** – A Microsoft biztonsági sablonjai valós időben automatikusan létrehozzák az Azure Sentinel-incidenseket a más Microsoft biztonsági megoldásokban létrehozott riasztásokból. A Microsoft biztonsági szabályai sablonként is használhatók új szabályok hasonló logikával történő létrehozásához. A biztonsági szabályokról további információt az [Incidensek automatikus létrehozása a Microsoft biztonsági riasztásaiból című témakörben talál.](create-incidents-from-alerts.md)
- **Fusion** – A Fusion technológia alapján az Azure Sentinel fejlett többfokozatú támadásészlelése skálázható gépi tanulási algoritmusokat használ, amelyek számos alacsony hűségű riasztást és eseményt több terméken keresztül hi-fi és perelhető incidensekké képesek összekapcsolni. A fusion alapértelmezés szerint engedélyezve van. Mivel a logika rejtett, ezt nem használhatja sablonként egynél több szabály létrehozására.
- **Gépi tanulási viselkedéselemzés** – Ezek a sablonok saját Microsoft gépi tanulási algoritmusokon alapulnak, így nem láthatja a működésük belső logikáját, és mikor futnak. Mivel a logika rejtett, ezt nem használhatja sablonként egynél több szabály létrehozására.
-   **Ütemezett** – Az ütemezett analitikus szabályok a Microsoft biztonsági szakértői által írt ütemezett lekérdezések. Megtekintheti a lekérdezéslogikát, és módosíthatja azt. Az ütemezett szabályok sablonként való használatával hasonló logikával új szabályokat hozhat létre.

## <a name="use-out-of-the-box-detections"></a>Használatra használható észlelések használata

1. A beépített sablon használatához kattintson a **Szabály létrehozása** gombra egy új aktív szabály létrehozásához az adott sablon alapján. Minden bejegyzés rendelkezik az automatikusan ellenőrzött szükséges adatforrások listájával, ami a **Szabály létrehozása** letiltását eredményezheti.
  
   ![Az Azure Sentinel használatával beépített észlelések segítségével kereshetfenyegetéseket](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Ezzel megnyitja a szabálykészítő varázslót a kijelölt sablon alapján. Minden részlet automatikusan kivan töltve, és **az ütemezett szabályok** vagy a Microsoft biztonsági **szabályai**esetében testreszabhatja a logikát, hogy jobban megfeleljen a szervezetnek, vagy további szabályokat hozhat létre a beépített sablon alapján. Miután követte a szabálykészítő varázsló lépéseit, és befejezte a sablonon alapuló szabály létrehozását, az új szabály megjelenik az **Aktív szabályok** lapon.

A varázsló mezőiről további információt az [Oktatóanyag: Egyéni analitikus szabályok létrehozása a gyanús fenyegetések észleléséhez című](tutorial-detect-threats-custom.md)témakörben talál.



## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan kezdheti el észlelni a fenyegetéseket az Azure Sentinel használatával. 

A fenyegetésekre adott válaszok automatizálásáról [az Automatikus fenyegetésre adott válaszok beállítása az Azure Sentinelben című dokumentumban.](tutorial-respond-threats-playbook.md)

