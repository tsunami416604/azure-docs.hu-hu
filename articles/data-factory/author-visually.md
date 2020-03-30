---
title: Vizuális szerzői
description: Ismerje meg, hogyan használhatja a vizuális szerzői lehetőségeket az Azure Data Factoryban
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: 09d4055ba98da2dd87efc9421402f2827a87ba16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440935"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Vizuális szerzői jog az Azure Data Factoryban

Az Azure Data Factory felhasználói felület (UX) lehetővé teszi, hogy vizuálisan az adatok-előállító erőforrásait anélkül, hogy bármilyen kódot írni. A tevékenységeket áthúzhatja egy folyamatvászonra, tesztfuttatásokat hajthat végre, iteratív módon debugot, valamint telepítheti és figyelheti a folyamatfuttatásokat.

Jelenleg az Azure Data Factory UX csak a Microsoft Edge és a Google Chrome támogatja.

## <a name="authoring-canvas"></a>Szerzői vászon

A **szerzői vászon**megnyitásához kattintson a ceruza ikonra. 

![Szerzői vászon](media/author-visually/authoring-canvas.png)

Itt fogja megalkotni a folyamatokat, tevékenységeket, adatkészleteket, összekapcsolt szolgáltatásokat, adatfolyamokat, eseményindítókat és integrációs futtatásokat, amelyek a gyárat alkotják. A folyamat létrehozásának megkezdéséhez a szerzői vászon használatával az [Adatok másolása a másolási tevékenység használatával című témakörben található.](tutorial-copy-data-portal.md) 

Az alapértelmezett vizuális szerzői élmény közvetlenül a Data Factory szolgáltatással működik. Az Azure Repos Git- vagy GitHub-integráció is támogatott, hogy lehetővé tegye a forrásvezérlést és az együttműködést az adatfeldolgozó folyamatokon végzett munkához. Ha többet szeretne megtudni a szerzői élmények közötti különbségekről, olvassa el a Forrásvezérlés az Azure Data Factory ban című [témakört.](source-control.md)

## <a name="expressions-and-functions"></a>Kifejezések és függvények

Kifejezések és függvények statikus értékek helyett számos tulajdonság megadására használható az Azure Data Factoryban.

Ha egy tulajdonságértékhez kifejezést szeretne megadni, válassza **a Dinamikus tartalom hozzáadása** lehetőséget, vagy kattintson az Alt + **P** gombra, miközben a mezőre fókuszál.

![Dinamikus tartalom hozzáadása](media/author-visually/dynamic-content-1.png)

Ezzel megnyitja a **Data Factory Expression Builder,** ahol hozhat létre kifejezéseket a támogatott rendszerváltozók, tevékenység kimenet, funkciók és a felhasználó által megadott változók vagy paraméterek. 

![Kifejezésszerkesztő](media/author-visually/dynamic-content-2.png)

A kifejezés nyelvéről további információt az [Azure Data Factory kifejezések és függvények](control-flow-expression-language-functions.md)című témakörében talál.

## <a name="provide-feedback"></a>Visszajelzés küldése

Válassza **a Visszajelzés** lehetőséget a szolgáltatásokkal kapcsolatos megjegyzések hez vagy az eszközrel kapcsolatos problémákról való értesítéshez:

![Visszajelzés](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>További lépések

A folyamatok figyeléséről és kezeléséről a [Folyamatok figyelése és kezelése programozott módon.](monitor-programmatically.md)
