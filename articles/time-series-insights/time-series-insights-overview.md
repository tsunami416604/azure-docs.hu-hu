---
title: "Az Azure Time Series Insights áttekintése | Microsoft Docs"
description: "Bevezetés az idősorozat-adatok elemzéséhez és IoT-megoldásokhoz készült új Azure Time Series Insight szolgáltatás használatába"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: abd66208ab7ac30831f3f1eddb2891ed7bcd3995
ms.contentlocale: hu-hu
ms.lasthandoff: 07/21/2017

---

# <a name="what-is-azure-time-series-insights"></a>Mi az Azure Time Series Insights?

Az Azure Time Series Insights egy felügyelt felhőszolgáltatás tárolási, elemzési és megjelenítési összetevőkkel, amelyekkel könnyen dolgozható fel, tárolható, deríthető fel és elemezhető akár több milliárd esemény egyszerre. A Time Series Insights globális áttekintést nyújt az adatokról, így gyorsan ellenőrizheti IoT-megoldásait, és elkerülheti az eszközök költséges leállását, mivel a rendszer segít a rejtett trendek és rendellenességek felderítésében, valamint a problémák kiváltó okainak közel valós idejű elemzésében. A Time Series Insights feldolgozza az eseményközvetítőktől (pl. IoT Huboktól vagy eseményközpontoktól) származó idősorozat-adatokat, indexeli azokat, és egy konfigurálható adatmegőrzési házirend alapján kivonja őket. A felhasználók az adatokat egy intuitív felületen vagy REST lekérdezési API-kon keresztül használhatják fel.

![A Time Series Insights áttekintése](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

* IoT-megoldások monitorozása és ellenőrzése néhány perc alatt.
* Nagy mennyiségű IoT-adat vizualizációja és elemzése.
* Gyorsabb kiváltó okokat kereső elemzések és rendellenességészlelés
* Több eszközt, üzemet és adatot tartalmazó globális nézet készítése.

## <a name="capabilities-and-benefits"></a>Képességek és előnyök

* **Könnyen használatba vehető**: Az Azure Time Series Insights használatához nem szükséges az adatok előzetes előkészítése, ráadásul rendkívül gyors. Percek alatt csatlakozhat több milliárd Azure IoT Hub- vagy Event Hub-eseményhez. A csatlakozás után másodpercek alatt megjelenítheti és kezelheti az érzékelők adatait, így gyorsan ellenőrizheti IoT-megoldásait. A Time Series Insights rendkívül könnyen használható; az adatok kezeléséhez egyáltalán nem szükséges kódolás.  Nem kell megtanulnia egy új programozási nyelvet; a Time Series Insights részletes, szabad szöveges lekérdezési felületet biztosít a tapasztalt felhasználóknak, illetve egy bárki által néhány kattintással felfedezhető felületet kínál.

* **Közel valós idejű elemzés**: A Time Series Insights több százmillió érzékelőeseményt képes feldolgozni naponta egy perces késéssel, így gyorsan reagálhat a változásokra. A Time Series Insights segítségével részletesebb betekintést nyerhet az érzékelőadatokba, mivel lehetővé teszi a trendek és rendellenességek gyors észlelését, az összetett, kiváltó okokat kereső elemzések elvégzését és a költséges leállási idő elkerülését. A valós idejű és az előzményadatok összevetésének biztosításával a Time Series Insights segít felderíteni az adataiban észlelhető rejtett trendeket.

* **Egyéni megoldások készítése**: Az Azure Time Series Insights-adatokat beágyazhatja meglévő alkalmazásaiba, vagy új egyéni megoldásokat hozhat létre a Time Series Insights REST API-jaival. Személyre szabott nézeteket hozhat létre és oszthat meg, hogy mások is megtekinthessék a felfedezett összefüggéseket.

* **Méretezhetőség**: A Time Series Insights támogatja a nagy mennyiségű IoT-adat kezelését. Az előzetes verzió napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. Közel valós időben jelenítheti meg és elemezheti az élő adatstreameket és nagy mennyiségű előzményadatot. A későbbi verziókban a bejövőadat-kezelési és adatmegőrzési kapacitást növelni fogjuk a növekvő nagyvállalati igényeknek megfelelően.

## <a name="time-series-insights-glossary"></a>Time Series Insights – Szószedet

* **Környezet**: A környezet egy Azure-erőforrás, amely bejövő adatforgalmi és tárolási kapacitással rendelkezik.  Az ügyfelek az Azure Portalon hozhatnak létre környezeteket a szükséges kapacitással.
* **Eseményforrás**: Az eseményforrás egy eseményközvetítőből, például az Azure Event Hubsból származik.  A Time Series Insights közvetlenül csatlakozik az eseményforrásokhoz, és kód írása nélkül dolgozza fel az adatstreamet. A Time Series Insights jelenleg az Azure Event Hubs és Azure IoT Hubs forrásokat támogatja.
* **Referenciaadat**: A Time Series Insights lehetővé teszi a felhasználók számára, hogy az idősorozat-adatokat referenciaadatokkal kapcsolják össze.  A referenciaadatok lehetnek az eszközökkel kapcsolatos metaadatok vagy egyéb statikus adatok, amelyek viszonylag ritkán változnak. A Time Series Insights összekapcsolja a referenciaadatokat az adatstreamekkel, így a felhasználók majdnem valós időben jeleníthetik meg és elemezhetik őket.

