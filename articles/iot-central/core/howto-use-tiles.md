---
title: Csempék használata az Azure IoT Central alkalmazás-irányítópulton | Microsoft Docs
description: A Builder használatával megtudhatja, hogyan használhatók a csempék az alkalmazás-irányítópultokon, az eszközökön beállított irányítópultokon és az eszközök irányítópultján.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952627"
---
# <a name="how-to-use-tiles"></a>Csempék használata
A csempéket használhatja az alkalmazás-irányítópultok, az eszközök irányítópultok és az eszközök beállított irányítópultjának testreszabásához. Több csempét is hozzáadhat egy irányítópulthoz, és testreszabhatja ezeket a csempéket az alkalmazásával kapcsolatos információk megjelenítéséhez. Átméretezheti a csempéket, és testre is szabhatja az elrendezést bármely irányítópulton. Az alábbi képernyőképen az alkalmazás irányítópultja látható, amely különböző csempéket mutat be.

![Alkalmazás irányítópultja](media/howto-use-tiles/image1a.png)


Az alábbi táblázat összefoglalja a csempék használatát az Azure IoT Centralban:

 
| Csempe | Irányítópult | Leírás
| ----------- | ------- | ------- |
| Hivatkozás | Alkalmazások és eszközök beállítása irányítópultok |A hivatkozások csempéi a fejléc és a Leírás szövegét megjelenítő csempék. Egy hivatkozás csempével engedélyezheti, hogy a felhasználó navigáljon az alkalmazáshoz kapcsolódó URL-címhez. |
| Kép | Alkalmazások és eszközök beállítása irányítópultok |A képcsempék egy egyéni rendszerképet jelenítenek meg, és megadhatók. Egy képcsempe használatával grafikus elemeket adhat hozzá az irányítópulthoz, és opcionálisan engedélyezheti a felhasználónak, hogy az alkalmazáshoz kapcsolódó URL-címet navigáljon.|
| Címke | Alkalmazás-irányítópultok |A felirat csempék egyéni szöveget jelenítenek meg az irányítópulton. Megadhatja a szöveg méretét. A címke csempével fontos információkat adhat hozzá az irányítópulthoz, például a leírásokat, a kapcsolattartási adatokat vagy a súgót.|
| Térkép | Alkalmazások és eszközök beállítása irányítópultok |A Térkép csempéi az eszközök helyét és állapotát jelenítik meg. Megjelenítheti például az eszköz helyét, illetve azt, hogy a ventilátor be van-e kapcsolva.|
| Vonalas diagram | Alkalmazás-és eszköz-irányítópultok |A vonalas diagram csempéi egy adott időszakra vonatkozóan egy adott eszköz összesített mértékének diagramját jelenítik meg. Megjeleníthet például egy olyan diagramot, amely egy eszköz átlagos hőmérsékletét és terhelését jeleníti meg az elmúlt órában|
| Oszlopdiagram | Alkalmazás-és eszköz-irányítópultok |A oszlopdiagram csempéi egy adott időszakra vonatkozó összesített mérési diagramot jelenítenek meg. Megjeleníthet például egy olyan sávdiagram, amely az adott eszköz átlagos hőmérsékletét és terhelését jeleníti meg az elmúlt órában |
| Események előzményei | Alkalmazás-és eszköz-irányítópultok |Az esemény előzményei csempe egy adott időszakon belül megjeleníti az adott eszköz eseményeit. A segítségével például megjelenítheti az adott eszközre vonatkozó összes hőmérséklet-változást az elmúlt órában. |
| Korábbi állapotok | Alkalmazás-és eszköz-irányítópultok |Az állapot előzményei csempe egy adott időszakra vonatkozó mérési értékeket jeleníti meg. Használhatja például az eszköz hőmérsékleti értékének megjelenítésére az elmúlt órában.|
| KPI | Alkalmazás-és eszköz-irányítópultok | A KPI csempe egy adott időszak összesített telemetria vagy az esemény mértékét jeleníti meg. Használhatja például az eszköz utolsó órájában elérhető maximális hőmérséklet megjelenítését.|
| Utolsó ismert érték | Alkalmazás-és eszköz-irányítópultok |Az utolsó ismert érték csempe a telemetria vagy az állapot mérésének legújabb értékét jeleníti meg. Ezzel a csempével például megjelenítheti az eszközök hőmérsékletének, nyomásának és nedvességtartalmának legutóbbi méréseit.|
| Eszköz beállított rácsa | Alkalmazások és eszközök beállítása irányítópultok | Az eszköz beállított rácsa az eszközre vonatkozó információkat jeleníti meg. Az eszköz set Grid csempe használatával megjelenítheti az eszközön lévő összes eszköz nevét, helyét és modelljét.|


Ha többet szeretne megtudni arról, hogyan konfigurálhatja az irányítópultot az Azure IoT Central alkalmazásban, tekintse meg a [csempék hozzáadása az irányítópulthoz](./howto-add-tiles-to-your-dashboard.md)című témakört.
