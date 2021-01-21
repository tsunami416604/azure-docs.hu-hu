---
title: Ismerkedés a huzavona adatforgalmával Azure Data Factory
description: Oktatóanyag az Azure Data Factory huzavona-adatfolyam használatával történő előkészítéséhez
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f32739b06920f6b20dc87b8e1fbd2884c323a859
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634130"
---
# <a name="prepare-data-with-data-wrangling"></a>Adatfeldolgozás az huzavona

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A huzavona-ben tárolt adatmennyiség lehetővé teszi, hogy natív módon hozzon létre egy interaktív Power Query Mash-UPS-t az ADF-ben, majd hajtsa végre a méretezést egy ADF-folyamaton belül.

> [!NOTE]
> Az ADF-ben lévő Power Query körülhatárolása jelenleg nyilvános előzetes verzióban avilable

## <a name="create-a-power-query-activity"></a>Power Query tevékenység létrehozása

A Azure Data Factory kétféleképpen hozhat létre Power Query. Az egyik módszer, ha a plusz ikonra kattint, és kiválasztja az **adatfolyamot** a gyári erőforrások ablaktáblán.

> [!NOTE]
> Korábban a huzavona funkció az adatfolyam munkafolyamatában található. Most létrehozza az adatait huzavona mash-up-ból ```New > Power query```

![A gyári erőforrások ablaktáblán látható Power Query bemutató képernyőkép.](media/data-flow/power-query-wrangling.png)

A másik módszer a folyamat vászonjának tevékenységek ablaktábláján található. Nyissa meg a **Power Query** harmonikát, és húzza a **Power Query** tevékenységet a vászonra.

![Képernyőkép a huzavona-adatfolyam lehetőségről.](media/data-flow/power-query-activity.png)

## <a name="author-a-wrangling-data-flow"></a>Huzavona-adatfolyam készítése

Adja hozzá a Power Query mash-up **forrás-adatkészletét** . Válasszon egy meglévő adatkészletet, vagy hozzon létre egy újat. Kiválaszthat egy fogadó adatkészletet is. Kiválaszthat egy vagy több forrás adatkészletet, de egyszerre csak egy fogadót lehet engedélyezni. A fogadó adatkészlet kiválasztása nem kötelező, de legalább egy forrás adatkészletet kötelező megadni.

![Huzavona](media/wrangling-data-flow/tutorial4.png)

Kattintson a **Létrehozás** elemre a Power Query online adategyesítés-szerkesztő megnyitásához.

![Képernyőfelvétel: a Create (létrehozás) gombra kattintva megnyílik a Power Query online mashup-szerkesztő.](media/wrangling-data-flow/tutorial5.png)

A huzavona-adatfolyamok létrehozása kód nélküli adatelőkészítéssel. Az elérhető függvények listáját lásd: [transzformációs függvények](wrangling-functions.md). Az ADF egy adatfolyam-parancsfájlba fordítja le a ```M``` szkriptet, így az ADF-adatfolyam Spark-környezettel méretezhető Power Query.

![Képernyőkép, amely a huzavona-adatfolyam készítésének folyamatát mutatja be.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Huzavona-adatfolyam futtatása és figyelése

Egy huzavona-adatfolyam hibakeresési folyamatának végrehajtásához kattintson a folyamat vásznon a **hibakeresés** elemre. Miután közzétette az adatfolyamot, a **trigger most** végrehajtja az utolsó közzétett folyamat igény szerinti futtatását. Az huzavona-adatfolyamatok az összes meglévő Azure Data Factory eseményindítóval ütemezhetők.

![Képernyőkép, amely bemutatja, hogyan adhat hozzá egy huzavona-adatfolyamot.](media/wrangling-data-flow/tutorial3.png)

Lépjen a **figyelés** lapra, és jelenítse meg az aktivált huzavona adatfolyam-tevékenységek kimenetét.

![Képernyőkép, amely egy aktivált huzavona adatfolyam-tevékenység kimenetét jeleníti meg.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre leképezési](tutorial-data-flow.md)adatfolyamot.
