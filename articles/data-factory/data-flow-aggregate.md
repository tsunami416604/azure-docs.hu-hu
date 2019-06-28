---
title: Leképezés adatfolyam - Azure Data Factory az Adatátalakítási összesített |} A Microsoft Docs
description: Ismerje meg, hogyan összesíti a leképezési adatok Flow összesített átalakítása az Azure Data Factory a nagy mennyiségű adat.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312117"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Az adatfolyam-leképezés összesített átalakítása 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az összesített átalakítás, ahol meghatározhatja a data-adatfolyamok összesítések az oszlopok. A Kifejezésszerkesztő segítségével meghatározhatja a különböző típusú összesítés, mint például a SUM, MIN, MAX és száma, amely a meglévő vagy számított oszlop szerint csoportosíthatók.

## <a name="group-by"></a>Csoportosítási szempont
Válasszon ki egy meglévő oszlopot, vagy hozzon létre egy új számított oszlop használata csoportként záradék által az összesítés. Egy létező oszlop használatához a legördülő listából válassza ki a kívánt oszlop. Új számított oszlop létrehozása, a záradék fölé, majd kattintson a "Számított oszlop". Ekkor megnyílik a [Data Flow Kifejezésszerkesztő](concepts-data-flow-expression-builder.md). Miután létrehozta a számított oszlop, adja meg a kimeneti oszlop neve "Néven" mező alatti. Szeretne hozzáadni egy további olyan csoport by záradékban, ha egy meglévő záradék mutasson, és kattintson a "+".

![Összesített átalakítási csoport beállításai szerint](media/data-flow/agg.png "összesített átalakítási csoport beállításai szerint")

> [!NOTE]
> Egy csoport by záradékban nem egy összesített átalakításában szerepel a kötelező megadni.

## <a name="aggregate-column"></a>Összesített oszlop 
Válassza a "Összesítések" lapot összesítő kifejezések felépítéséhez létre. Válasszon egy meglévő oszlopára, és az az érték felülírása az összesítést, vagy létrehoz egy új mezőt egy új. Az összesítő kifejezésben a jobb oldali mezőbe az Oszlopválasztó neve mellett is meg kell adni. A kifejezés szerkesztéséhez kattintson a szövegmezőbe írja be a Kifejezésszerkesztő megnyitása. Adjon hozzá további összesítést, vigye a kurzort egy meglévő kifejezést, majd kattintson a "+" összesítés új oszlop létrehozása, vagy [oszlop minta](concepts-data-flow-column-pattern.md).

![Aggregált összesítő átalakítási beállítások](media/data-flow/agg2.png "aggregált összesítő átalakítási beállítások")

> [!NOTE]
> Minden egyes összesítő kifejezés tartalmaznia kell legalább egy összesítő függvény.

> [!NOTE]
> Hibakeresési módban a Kifejezésszerkesztő adatelőnézetek az aggregátumfüggvények nem tud létrehozni. Összesített átalakítások adatok előnézetének megtekintéséhez zárja be a Kifejezésszerkesztő és megtekinteni az adatokat az "Adatok előnézet" lapján.

## <a name="next-steps"></a>További lépések

-Meghatározhatja a Windows-alapú összesítést használja a [ablak átalakítása](data-flow-window.md)