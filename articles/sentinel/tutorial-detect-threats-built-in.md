---
title: Riasztások vizsgálata az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan vizsgálja ki a riasztásokat az Azure Sentinel használatával.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77585203"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Oktatóanyag: fenyegetések észlelése – beépített


> [!IMPORTANT]
> A beépített fenyegetések észlelése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure Sentinel szolgáltatáshoz, értesítést szeretne kapni, ha valamilyen gyanús esemény történik. Ennek lehetővé tételéhez az Azure Sentinel beépített sablonokat is biztosít Önnek a rendelkezésére. Ezeket a sablonokat az ismert fenyegetések, a gyakori támadási vektorok és a gyanús tevékenység-eszkalációs láncok alapján a Microsoft biztonsági szakértői és elemzői alkották. A sablonok engedélyezése után a rendszer automatikusan megkeresi azokat a tevékenységeket, amelyek gyanúsak a környezetében. Számos sablon testreszabható a tevékenységek kereséséhez, illetve kiszűréséhez az igényeinek megfelelően. A sablonok által generált riasztások olyan incidenseket hoznak létre, amelyeket a környezetben rendelhet hozzá és vizsgálhat meg.

Ez az oktatóanyag segítséget nyújt a fenyegetések észleléséhez az Azure Sentinel használatával:

> [!div class="checklist"]
> * Beépített észlelések használata
> * Fenyegetési válaszok automatizálása

## <a name="about-out-of-the-box-detections"></a>A beépített észlelések ismertetése

Az összes beépített észlelés megtekintéséhez lépjen az **elemzés** , majd a **szabály sablonok**elemre. Ezen a lapon az Azure Sentinel beépített szabályai láthatók.

   ![A fenyegetések felderítése az Azure Sentinel használatával beépített észlelésekkel](media/tutorial-detect-built-in/view-oob-detections.png)

A következő sablon típusok érhetők el:

- **Microsoft Security** – a Microsoft biztonsági sablonjai valós időben hoznak létre automatikusan Azure Sentinel-incidenseket a más Microsoft biztonsági megoldásokban létrehozott riasztásokból. A Microsoft biztonsági szabályai sablonként használhatók a hasonló logikával rendelkező új szabályok létrehozásához. További információ a biztonsági szabályokról: [incidensek automatikus létrehozása a Microsoft biztonsági értesítéseiből](create-incidents-from-alerts.md).
- **Fúzió –** fúziós technológián alapuló, fejlett, többszintű támadás észlelése az Azure sentinelben skálázható gépi tanulási algoritmusokat használ, amelyek számos kis hűségű riasztást és eseményt kezelhetnek több termékre, és így magas szintű és gyakorlati incidenseket jelenthetnek. A Fusion alapértelmezés szerint engedélyezve van. Mivel a logika rejtett, nem használhatja ezt sablonként egynél több szabály létrehozásához.
- **Machine learning – viselkedés-elemzés** – ezek a sablonok a saját Microsoft Machine learning-algoritmusokon alapulnak, így nem láthatók a működésük és a futtatásaik belső logikája. Mivel a logika rejtett, nem használhatja ezt sablonként egynél több szabály létrehozásához.
-   **Ütemezett** – az ütemezett elemzési szabályok a Microsoft biztonsági szakértői által írt ütemezett lekérdezések. Láthatja a lekérdezési logikát, és módosíthatja azt. Az ütemezett szabályokat sablonként használva új szabályokat hozhat létre hasonló logikával.

## <a name="use-out-of-the-box-detections"></a>Beépített észlelések használata

1. Ha beépített sablont szeretne használni, kattintson a **szabály létrehozása** elemre egy új, a sablon alapján létrehozott aktív szabály létrehozásához. Mindegyik bejegyzés rendelkezik az automatikusan ellenőrzött szükséges adatforrások listájával, és ez a **létrehozási szabály** letiltását eredményezheti.
  
   ![A fenyegetések felderítése az Azure Sentinel használatával beépített észlelésekkel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Ekkor megnyílik a szabály létrehozása varázsló a kiválasztott sablon alapján. Az összes adat kitöltése, valamint az **ütemezett szabályok** vagy a **Microsoft biztonsági szabályok**esetében testreszabhatja a logikát, hogy jobban megfeleljen a szervezetének, vagy további szabályokat hozzon létre a beépített sablon alapján. A szabály létrehozása varázsló lépéseit követve és a sablon alapján létrehozott szabály létrehozása után az új szabály megjelenik az **aktív szabályok** lapon.

A varázsló mezőivel kapcsolatos további információkért lásd [: oktatóanyag: egyéni analitikus szabályok létrehozása a gyanús fenyegetések észleléséhez](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>További lépések
Ebből az oktatóanyagból megtudhatta, hogyan kezdheti el a fenyegetések észlelését az Azure Sentinel használatával. 

Ha szeretné megtudni, hogyan automatizálhatja a fenyegetésekre adott válaszokat, [állítson be automatizált fenyegetési válaszokat az Azure sentinelben](tutorial-respond-threats-playbook.md).

