---
title: Az Azure Spring Cloud metrikáinak ismertetése
description: Ismerje meg, hogyan tekintheti át a metrikákat az Azure Spring Cloud-ban
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039125"
---
# <a name="metrics-for-azure-spring-cloud"></a>Az Azure Spring Cloud mérőszámai

Azure Monitor a metrikák Explorer a Microsoft Azure Portal egy olyan összetevője, amely lehetővé teszi a diagramok ábrázolását, a trendek vizuális korrelációját, valamint a tüskék és a kiugró adatok kivizsgálása mérőszámokban. Az erőforrások állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. Az Azure Spring Cloud két lehetőséget kínál a metrikák megtekintésére: diagramok az **alkalmazás – áttekintés** oldalon és a szolgáltatási szint mérőszámok oldalon.

## <a name="application-overview-page"></a>Alkalmazás – Áttekintés lap

Az egyes alkalmazások **alkalmazás-áttekintő** lapja egy mérőszámokat tartalmazó diagramot jelenít meg, amely lehetővé teszi az alkalmazás gyors állapot-ellenőrzésének elvégzését.  Lépjen az Azure Spring Cloud Service oldalára, és válassza az **alkalmazás-irányítópult**lehetőséget, majd válasszon ki egy alkalmazást a listából.  

5 diagramot biztosítunk percenként frissített metrikákkal a következőkhöz:

* **Http-kiszolgálói hibák**: Az alkalmazáshoz tartozó HTTP-kérelmek száma.
* **A-ben tárolt adatértékek**: Az alkalmazás által fogadott bájtok száma.
* **Kimenő adatvesztés**: Az alkalmazásnak eljuttatott bájtok száma.
* **Kérelmek**: Az alkalmazás által fogadott kérelmek.
* **Átlagos válaszidő**: Az alkalmazás átlagos válaszideje.

Kiválaszthat egy időtartományt a diagram 1 óra és 7 nap közötti időtartamára.

## <a name="service-level-metric-queries"></a>Szolgáltatási szintű metrikai lekérdezések

Az Azure Spring Cloud lehetővé teszi különböző alkalmazás-metrikák figyelését. A szolgáltatással kapcsolatos további információkért tekintse át a Azure Monitor mérőszámok megismerését ismertető [útmutatót](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) .

A metrikai adatok áttekintéséhez ki kell választania a metrikát, az **összesítést**és az időtartományt.  Az alábbi fogalmakat alább ismertetjük.

### <a name="aggregation"></a>Összesítés 

Az Azure minden percben lekérdezi és frissíti a metrikákat. Az Azure háromféle módon összesíti az adatokat egy kiválasztott időszakra vonatkozóan:

* **Összesen**: Az összes metrika összegzése célként megadott kimenetként.
* **Átlag**: Használja az időszak átlagos értékét célként megadott kimenetként.
* **Maximum/perc**: A maximális/minimális értéket adja meg az időszakban célként megadott kimenetként.

### <a name="time-range"></a>Időtartomány

Válasszon ki egy alapértelmezett időtartományt, vagy adja meg a sajátját.

### <a name="modifying-the-granularity-of-your-metric-query"></a>A metrika-lekérdezés részletességének módosítása

Alapértelmezés szerint az Azure összesíti az Azure Spring Cloud Service összes alkalmazásának mérőszámait. Az alkalmazás vagy a példány szintjén található mérőszámok áttekintéséhez használja a Filter függvényt.  
Válassza a **szűrő hozzáadása**lehetőséget, állítsa a tulajdonságot **alkalmazás** értékre, és válassza ki a figyelni kívánt célalkalmazás-alkalmazást. Igény szerint a **felosztás alkalmazása** lehetőséggel külön sorokat rajzolhat a diagramon lévő egyes alkalmazásokhoz.

>[!TIP]
> Saját diagramokat hozhat létre a metrikák lapon, és rögzítheti őket az **irányítópulton**. Először nevezze el a diagramot.  Ezután válassza **a rögzítés az irányítópulton lehetőséget a jobb felső sarokban**. Mostantól a portál **irányítópultján**is megtekintheti az alkalmazását.