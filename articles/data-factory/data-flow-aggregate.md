---
title: Az Azure Data Factory-folyamat összesített átalakítását leképezése
description: Az Azure Data Factory folyamat összesített átalakítását
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 3793c49da32845d559d73faa25c571d3f86e062f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271818"
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
