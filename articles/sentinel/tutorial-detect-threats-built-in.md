---
title: Riasztások vizsgálata az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a beépített Azure Threat Detection-sablonokat, amelyek értesítik, ha valamilyen gyanús esemény történik.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d73337c25c812363b7a542bf42372ca3baa10e8
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605408"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Oktatóanyag: fenyegetések észlelése – beépített


> [!IMPORTANT]
> A beépített fenyegetések észlelése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md)   Az Azure Sentinel szolgáltatáshoz, értesítést szeretne kapni, ha valamilyen gyanús esemény történik. Az Azure Sentinel ezért beépített, beépített sablonokat tartalmaz, amelyek segítenek a fenyegetések észlelési szabályainak létrehozásában. Ezeket a sablonokat az ismert fenyegetések, a gyakori támadási vektorok és a gyanús tevékenység-eszkalációs láncok alapján a Microsoft biztonsági szakértői és elemzői alkották. Az ezekből a sablonokból létrehozott szabályok automatikusan megkeresik a környezetet minden olyan tevékenység esetében, amely gyanúsnak tűnik. A sablonok többsége testreszabható a tevékenységek keresésére, vagy az igényeinek megfelelő szűrésre. Az ezekkel a szabályokkal létrehozott riasztások olyan incidenseket hoznak létre, amelyeket a környezetben rendelhet hozzá és vizsgálhat meg.

Ez az oktatóanyag segítséget nyújt a fenyegetések észleléséhez az Azure Sentinel használatával:

> [!div class="checklist"]
> * Beépített veszélyforrások észlelése
> * Fenyegetési válaszok automatizálása

## <a name="about-out-of-the-box-detections"></a>A beépített észlelések ismertetése

Az összes beépített észlelés megtekintéséhez lépjen az **elemzés** , majd a **szabály sablonok**elemre. Ezen a lapon az Azure Sentinel beépített szabályai láthatók.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="A fenyegetések felderítése az Azure Sentinel használatával beépített észlelésekkel":::

A következő sablon típusok érhetők el:

- **Microsoft-Biztonság**
   
   A Microsoft biztonsági sablonjai valós időben hoznak létre automatikusan Azure Sentinel-incidenseket a más Microsoft biztonsági megoldásokban létrehozott riasztásokból. A Microsoft biztonsági szabályai sablonként használhatók a hasonló logikával rendelkező új szabályok létrehozásához. További információ a biztonsági szabályokról: [incidensek automatikus létrehozása a Microsoft biztonsági értesítéseiből](create-incidents-from-alerts.md).

- **Fúziós** 

    A fúziós technológián alapuló, fejlett, többszintű támadások észlelése az Azure Sentinel-ben skálázható gépi tanulási algoritmusokat használ, amelyek számos kis-és nagyszámú riasztást és eseményt kezelhetnek több termékben, amelyek magas szintű és gyakorlati incidensekkel rendelkeznek A Fusion alapértelmezés szerint engedélyezve van. Mivel a logika rejtett, ezért nem szabható testre, csak egy szabályt hozhat létre ezzel a sablonnal.

- **Gépi tanulás – viselkedési elemzés**

    Ezek a sablonok a Microsoft gépi tanulási algoritmusai alapján működnek, így nem látja a belső logikát a működésük és a futtatásuk során. Mivel a logika rejtett, ezért nem szabható testre, csak egyetlen szabályt hozhat létre minden ilyen típusú sablonnal.

- **Ütemezett**

    Az ütemezett elemzési szabályok a Microsoft biztonsági szakértői által írt beépített lekérdezéseken alapulnak. Láthatja a lekérdezési logikát, és módosíthatja azt. Az ütemezett szabályok sablont használhatja, és testreszabhatja a lekérdezési logikát és az ütemezési beállításokat új szabályok létrehozásához.

## <a name="use-out-of-the-box-detections"></a>Beépített észlelések használata

1. Ha beépített sablont szeretne használni, kattintson a sablon nevére, majd kattintson a **szabály létrehozása** gombra a részletek ablaktáblán a sablon alapján létrehozott új aktív szabály létrehozásához. Minden sablon rendelkezik a szükséges adatforrások listájával. A sablon megnyitásakor az adatforrások automatikusan bekerülnek a rendelkezésre állásba. Ha rendelkezésre állási probléma merül fel, előfordulhat, hogy a **szabály létrehozása** gomb le van tiltva, vagy erre figyelmeztető üzenet jelenik meg.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Észlelési szabály előnézet panelje":::
 
1. A **szabály létrehozása** gombra kattintva megnyílik a szabály létrehozása varázsló a kiválasztott sablon alapján. Az összes adat kitöltése, valamint az **ütemezett** vagy a **Microsoft biztonsági** sablonjaival testreszabhatja a logikát és az egyéb szabályok beállításait, hogy jobban megfeleljen az igényeinek. Ezt a folyamatot megismételve további szabályokat hozhat létre a beépített sablon alapján. A szabály létrehozása varázsló lépéseinek befejezését követően a sablon alapján elkészült egy szabály létrehozása. Az új szabályok az **aktív szabályok** lapon fognak megjelenni.

    További információ a szabályok testreszabásáról a szabály létrehozása varázslóban [: oktatóanyag: egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan kezdheti el a fenyegetések észlelését az Azure Sentinel használatával. 

Ha szeretné megtudni, hogyan automatizálhatja a fenyegetésekre adott válaszokat, [állítson be automatizált fenyegetési válaszokat az Azure sentinelben](tutorial-respond-threats-playbook.md).

