---
title: Az Azure Data Factory-folyamat összesített átalakítását leképezése
description: Az Azure Data Factory folyamat összesített átalakítását
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467377"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Az Azure Data Factory-folyamat összesített átalakítását leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az összesített átalakítás, ahol meghatározhatja a data-adatfolyamok összesítések az oszlopok. A Kifejezésszerkesztőben határozza meg a különböző típusú összesítést (például SUM, MIN, MAX, száma stb.), és hozzon létre egy új mezőt a kimenetben, amely tartalmazza az ezen összesítések választható csoportosítási mezőkkel.

![Átalakítási beállítások összesített](media/data-flow/agg.png "1 összesítése")

## <a name="group-by"></a>Csoportosítási szempont
(Nem kötelező) Válassza ki az összesítés Group by záradékban, és a egy létező oszlop nevére vagy egy új nevet. Használat "oszlop hozzáadása" Adjon hozzá további group by záradékot, és kattintson a a szöveg, indítsa el a Kifejezésszerkesztő csak egy létező oszlop, oszlopok vagy a csoportosítási kifejezések kombinációját kiválaszthatja az oszlop neve melletti jelölőnégyzetbe.

## <a name="the-aggregate-column-tab"></a>Az összesített oszlop lapon 
(Kötelező) Válassza ki az összesített oszlop lapon az összesítő kifejezések felépítéséhez létre. Válasszon egy létező oszlop értékét felülírja az összesítést, vagy hozzon létre egy új mezőt az új névre, az összesítés. A kifejezés, amely az összesítés használni kívánt kell megadni a jobb oldali mezőbe az Oszlopválasztó neve mellett. A szövegmező kattint nyílik meg, a Kifejezésszerkesztőt.

![Átalakítási beállítások összesített](media/data-flow/agg2.png "gyűjtő")

## <a name="data-preview-in-expression-builder"></a>A Kifejezésszerkesztő Adatelőnézet

Hibakeresési módban a Kifejezésszerkesztő adatelőnézetek az aggregátumfüggvények nem tud létrehozni. Összesített átalakítások adatok előnézetének megtekintéséhez zárja be a Kifejezésszerkesztő, és megtekintheti az adatprofil az adatfolyam-tervezőből.
