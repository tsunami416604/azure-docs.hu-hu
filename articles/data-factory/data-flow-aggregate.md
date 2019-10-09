---
title: Összesített átalakítás a leképezési adatfolyamban – Azure Data Factory | Microsoft Docs
description: Megtudhatja, hogyan összesítheti a Azure Data Factory méretezési adatokat a leképezési adatfolyam összesített átalakításával.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 0201cbdd05cd8aae4afb92b459bf58fb5ff6a142
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026978"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Összesített átalakítás a leképezési adatfolyamban 



Az összesített átalakítás az, ahol meghatározhatja az adatstreamek oszlopainak összesítéseit. A Kifejezésszerkesztő használatával különböző típusú összesítéseket határozhat meg, például a SUM, a MIN, a MAX és a COUNT értéket, amely a meglévő vagy számított oszlopok szerint csoportosítható.

## <a name="group-by"></a>Csoportosítási alapja
Válasszon ki egy meglévő oszlopot, vagy hozzon létre egy új számított oszlopot, amelyet Group By záradékként kíván használni az összesítéshez. Meglévő oszlop használatához válassza ki a kívánt oszlopot a legördülő listából. Új számított oszlop létrehozásához vigye a kurzort a záradék fölé, és kattintson a "számított oszlop" elemre. Ekkor megnyílik az adatfolyam- [kifejezés szerkesztője](concepts-data-flow-expression-builder.md). Miután létrehozta a számított oszlopot, adja meg a kimeneti oszlop nevét a "név" mezőben. Ha további Group By záradékot szeretne hozzáadni, vigye a kurzort egy meglévő záradék fölé, és kattintson a "+" elemre.

![Összesítő transzformációs csoport beállítások alapján](media/data-flow/agg.png "összesített transzformációs csoport szerint")

> [!NOTE]
> Group By záradék használata nem kötelező egy összesített átalakításban.

## <a name="aggregate-column"></a>Összesítő oszlop 
Az összesítési kifejezések létrehozásához válassza az "összesítések" fület. Kiválaszthat egy meglévő oszlopot, és felülírhatja az összesítési értéket, vagy létrehozhat egy új mezőt új névvel. Az összesítési kifejezés az oszlopnév-választó melletti jobb oldali mezőben van megadva. A kifejezés szerkesztéséhez kattintson a szövegmezőre, hogy megnyissa a Kifejezésszerkesztő párbeszédpanelt. További összesítés hozzáadásához vigye a kurzort egy meglévő kifejezés fölé, és kattintson a "+" elemre egy új aggregációs oszlop vagy [oszlop minta](concepts-data-flow-column-pattern.md)létrehozásához.

![Aggregált átalakítás összesített beállításai](media/data-flow/agg2.png "összesített transzformációs összesítő beállítások")

> [!NOTE]
> Minden összesítési kifejezésnek legalább egy összesítő függvényt tartalmaznia kell.

> [!NOTE]
> Hibakeresési módban a Kifejezésszerkesztő nem hozhat létre összesítő függvényekből származó adat-előnézeteket. Az összesített átalakításokhoz tartozó adatelőnézetek megtekintéséhez a Kifejezésszerkesztő bezárásához és az adatelőnézet lapon megtekintheti az adatokat.

## <a name="reconnect-rows-and-columns"></a>Sorok és oszlopok újracsatolása
Az összesített átalakítások szorosan egyenértékűek az SQL aggregált választó lekérdezésekkel. Azokat az oszlopokat, amelyek nem szerepelnek a GROUP BY záradékban, vagy az összesítő függvények nem kerülnek át az összesített átalakítás kimenetére. Ha más oszlopok is szerepelnek az összesített sorok kimenetében, akkor a következők valamelyikét kell megadnia:

* Összesítő függvény használata a további oszlop, például az utolsó () vagy az első () érték befoglalásához
* Csatlakoztassa újra az oszlopokat az összesítéshez a [saját illesztési minta](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)használatával.

## <a name="next-steps"></a>További lépések

* Ablak alapú összesítés definiálása az ablak- [transzformáció](data-flow-window.md) használatával
