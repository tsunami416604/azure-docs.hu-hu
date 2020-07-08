---
title: Vizuális szerzői műveletek
description: Ismerje meg, hogyan használható a Visual authoring a Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 05/15/2020
ms.openlocfilehash: ac51fd63adcc0328ff67f5fbe13dbfdb768f4bfe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343113"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visual authoring in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory felhasználói felületi élmény (UX) segítségével vizuálisan hozhat létre és helyezhet üzembe erőforrásokat az adatai-előállító számára anélkül, hogy kódot kellene írnia. A tevékenységeket áthúzhatja egy csővezeték-vászonra, tesztelheti a iteratív, hibakeresést végezhet, valamint telepítheti és figyelheti a folyamat futtatását.

Jelenleg a Azure Data Factory UX csak a Microsoft Edge és a Google Chrome esetében támogatott.

## <a name="authoring-canvas"></a>Szerzői vászon

A **szerzői műveletek vászon**megnyitásához kattintson a ceruza ikonra. 

![Szerzői vászon](media/author-visually/authoring-canvas.png)

Itt a gyárat alkotó folyamatokat, tevékenységeket, adatkészleteket, társított szolgáltatásokat, adatfolyamatokat, eseményindítókat és integrációs modulokat hozhat létre. A folyamat létrehozásához a szerzői műveletek vászon használatával megtudhatja, hogyan másolhat egy folyamatot a [másolási tevékenység használatával](tutorial-copy-data-portal.md). 

Az alapértelmezett vizualizáció-létrehozási élmény közvetlenül a Data Factory szolgáltatással működik. Az Azure Repos git vagy GitHub integrációja is támogatott, hogy lehetővé váljon a verziókövetés és az együttműködés a saját adatfeldolgozó-folyamatokban. Ha többet szeretne megtudni a szerzői műveletek közötti különbségekről, tekintse meg a [Azure Data Factory a verziókövetés](source-control.md)című témakört.

### <a name="properties-pane"></a>Tulajdonságok panel

A legfelső szintű erőforrások, például folyamatok, adatkészletek és adatfolyamatok esetében a magas szintű tulajdonságok a vászon jobb oldalán található Tulajdonságok ablaktáblán szerkeszthetők. A Tulajdonságok ablaktábla olyan tulajdonságokat tartalmaz, mint például a név, a leírás, a jegyzetek és más magas szintű tulajdonságok. Az alerőforrások (például a folyamat tevékenységei és az adatfolyam-TRANSZFORMÁCIÓK) a vászon alján található panel használatával szerkeszthetők. 

![Szerzői vászon](media/author-visually/properties-pane.png)

A Tulajdonságok ablaktábla csak az erőforrások létrehozásakor nyílik meg alapértelmezés szerint. A szerkesztéshez kattintson a vászon jobb felső sarkában található Tulajdonságok ablaktábla ikonra.

## <a name="management-hub"></a>Felügyeleti központ

A Azure Data Factory UX *felügyelet* lapján elérhető felügyeleti központ egy olyan portál, amely globális felügyeleti műveleteket üzemeltet az Ön adatelőállítójának. Itt kezelheti az adattárakhoz és a külső számításokhoz, a verziókövetés konfigurációjához és az aktiválási beállításokhoz kapcsolódó kapcsolatokat. További információkért tekintse meg a [felügyeleti központ](author-management-hub.md)képességeit.

![Társított szolgáltatások kezelése](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>Kifejezések és függvények

A kifejezések és függvények a statikus értékek helyett használhatók a Azure Data Factory számos tulajdonságának megadásához.

Egy tulajdonság értékének megadásához válassza a **dinamikus tartalom hozzáadása** lehetőséget, vagy kattintson az **ALT + P** gombra a mezőre való fókuszálás közben.

![Dinamikus tartalom hozzáadása](media/author-visually/dynamic-content-1.png)

Ekkor megnyílik a **Data Factory Expression Builder** , amelyen létrehozhat kifejezéseket a támogatott rendszerváltozók, a tevékenységek kimenete, a függvények és a felhasználó által megadott változók vagy paraméterek alapján. 

![Kifejezésszerkesztő](media/author-visually/dynamic-content-2.png)

A kifejezés nyelvével kapcsolatos további információkért lásd: [kifejezések és függvények a Azure Data Factoryban](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Visszajelzés küldése

Válassza ki a **visszajelzéseket** a funkciókkal kapcsolatos megjegyzésekhez, vagy tájékoztassa a Microsoftot az eszközzel kapcsolatos problémákról:

![Visszajelzés](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>További lépések

A folyamatok figyelésével és kezelésével kapcsolatos további információkért lásd a [folyamatok programozott figyelését és](monitor-programmatically.md)felügyeletét ismertető témakört.
