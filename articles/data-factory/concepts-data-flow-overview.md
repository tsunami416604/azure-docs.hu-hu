---
title: Azure Data Factory leképezési adatfolyam áttekintése
description: Az adatfolyamatok leképezésének áttekintése Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123929"
---
# <a name="what-are-mapping-data-flows"></a>Mik azok a leképezési adatfolyamok?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az adatfolyamatok leképezése a Azure Data Factoryban vizuálisan tervezett adatátalakítást végez. Az adatforgalom lehetővé teszi, hogy az adatmérnökök programkód írása nélkül fejlesszenek grafikus Adatátalakítási logikát. Az eredményül kapott adatfolyamatok a kibővített Azure Databricks-fürtöket használó Azure Data Factory folyamatokon belüli tevékenységként lesznek végrehajtva.

Azure Data Factory adatáramlás célja, hogy teljes körű vizuális élményt nyújtson, amely nem igényel kódolást. Az adatfolyamatok a saját végrehajtási fürtön lesznek végrehajtva a kibővített adatfeldolgozáshoz. Azure Data Factory kezeli az összes kód fordítását, az elérési út optimalizálását és az adatfolyam-feladatok végrehajtását.

Első lépésként hozza létre az adatfolyamatokat hibakeresési módban, hogy interaktív módon érvényesítse az átalakítási logikát. Ezután vegyen fel egy adatfolyam-tevékenységet a folyamatba, hogy végrehajtsa és tesztelje az adatfolyamot a folyamat hibakeresése során, vagy használja a folyamat "trigger most" műveletét, hogy tesztelje az adatfolyamot egy folyamat tevékenységből.

Ezután az adatfolyam-tevékenységeket az adatáramlási tevékenységet végrehajtó Azure Data Factory folyamatok használatával ütemezheti és figyelheti.

Az adatfolyam-tervezési felületen a hibakeresési mód váltógomb lehetővé teszi az adatátalakítások interaktív kiépítését. A hibakeresési mód adatelőkészítési és adatelőnézeti környezetet biztosít az adatfolyamok készítéséhez.

## <a name="begin-building-your-data-flow-logical-graph"></a>Az adatfolyam logikai gráfjának kiépítése

Hozzon létre egy új adatfolyam létrehozásához az adatfolyamatok létrehozásának első lépéseit a gyári erőforrások + bejelentkezés területén.

![új adatfolyam](media/data-flow/newdataflow2.png "új adatfolyam")

Először konfigurálja a forrás-átalakítást, majd adja hozzá az adatátalakítást minden további lépéshez a + jel használatával. A logikai gráf kiépítésekor válthat a gráfok és a konfigurációs üzemmódok között a "diagram megjelenítése" és a "Graph elrejtése" gomb használatával.

![Gráf megjelenítése](media/data-flow/showg.png "Gráf megjelenítése")

## <a name="configure-transformation-logic"></a>Átalakítási logika konfigurálása

![Gráf elrejtése](media/data-flow/hideg.png "Gráf elrejtése")

A gráf elrejtése lehetővé teszi, hogy később navigáljon az átalakítási csomópontokon.

![Navigáljon](media/data-flow/showhide.png "navigáljon")

## <a name="next-steps"></a>További lépések

* [Kezdés forrás-átalakítással](data-flow-source.md)
