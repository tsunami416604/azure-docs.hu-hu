---
title: Az Azure Spring Cloud metrikáinak ismertetése
description: Ismerje meg, hogyan tekintheti át a metrikákat az Azure Spring Cloud-ban
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607813"
---
# <a name="metrics-for-azure-spring-cloud"></a>Az Azure Spring Cloud mérőszámai

Azure Monitor a metrikák Explorer a Microsoft Azure Portal egy olyan összetevője, amely lehetővé teszi a diagramok ábrázolását, a trendek vizuális korrelációját, valamint a tüskék és a kiugró adatok kivizsgálása mérőszámokban. Az erőforrások állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. Az Azure Spring Cloud két lehetőséget kínál a metrikák megtekintésére: diagramok az **alkalmazás – áttekintés** oldalon és a szolgáltatási szint mérőszámok oldalon.

## <a name="application-overview-page"></a>Alkalmazás – Áttekintés lap

Az egyes alkalmazások **alkalmazás-áttekintő** lapja egy mérőszámokat tartalmazó diagramot jelenít meg, amely lehetővé teszi az alkalmazás gyors állapot-ellenőrzésének elvégzését.  Lépjen az Azure Spring Cloud Service oldalára, és válassza az **alkalmazás-irányítópult**lehetőséget, majd válasszon ki egy alkalmazást a listából.  

5 diagramot biztosítunk percenként frissített metrikákkal a következőkhöz:

* **Http-kiszolgálóhiba: hibák**száma az alkalmazásra irányuló HTTP-kérések esetében.
* **A-ben lévő adat**: az alkalmazás által fogadott bájtok száma.
* **Kimenő adat**: az alkalmazásnak elküldett bájtok száma.
* **Kérelmek**: az alkalmazás által fogadott kérelmek.
* **Átlagos válaszidő**: az alkalmazás átlagos válaszideje.

Kiválaszthat egy időtartományt a diagram 1 óra és 7 nap közötti időtartamára.

## <a name="service-level-metric-queries"></a>Szolgáltatási szintű metrikai lekérdezések

Az Azure Spring Cloud lehetővé teszi különböző alkalmazás-metrikák figyelését. A szolgáltatással kapcsolatos további információkért tekintse át a Azure Monitor mérőszámok megismerését ismertető [útmutatót](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) .

A metrikai adatok áttekintéséhez ki kell választania a metrikát, az **összesítést**és az időtartományt.  Az alábbi fogalmakat alább ismertetjük.

### <a name="aggregation"></a>Összesítés 

Az Azure minden percben lekérdezi és frissíti a metrikákat. Az Azure háromféle módon összesíti az adatokat egy kiválasztott időszakra vonatkozóan:

* **Összesen**: az összes mérőszámot célként megadott kimenetként összesíti.
* **Átlag**: az időszakban az átlagos értéket célként megadott kimenetként használja.
* **Max/Min**: használja a maximális/legkisebb értéket az időszakban célként megadott kimenetként.

### <a name="time-range"></a>Időtartomány

Válasszon ki egy alapértelmezett időtartományt, vagy adja meg a sajátját.

### <a name="modifying-the-granularity-of-your-metric-query"></a>A metrika-lekérdezés részletességének módosítása

Alapértelmezés szerint az Azure összesíti az Azure Spring Cloud Service összes alkalmazásának mérőszámait. Az alkalmazás vagy a példány szintjén található mérőszámok áttekintéséhez használja a Filter függvényt.  
Válassza a **szűrő hozzáadása**lehetőséget, állítsa a tulajdonságot **alkalmazás** értékre, és válassza ki a figyelni kívánt célalkalmazás-alkalmazást. Igény szerint a **felosztás alkalmazása** lehetőséggel külön sorokat rajzolhat a diagramon lévő egyes alkalmazásokhoz.

>[!TIP]
> Saját diagramokat hozhat létre a metrikák lapon, és rögzítheti őket az **irányítópulton**. Először nevezze el a diagramot.  Ezután válassza **a rögzítés az irányítópulton lehetőséget a jobb felső sarokban**. Mostantól a portál **irányítópultján**is megtekintheti az alkalmazását.