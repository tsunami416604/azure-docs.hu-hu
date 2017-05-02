---
title: "Az Azure Time Series Insights áttekintése | Microsoft Docs"
description: "Bevezetés az új Azure Time Series Insight szolgáltatás használatába idősorozat-adatok elemzésére és IoT-megoldásokhoz"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/25/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Az Azure Time Series Insights áttekintése

Az Azure Time Series Insights egy teljes körűen felügyelt felhőszolgáltatás tárolási, elemzési és megjelenítési összetevőkkel, amelyekkel könnyen dolgozható fel, tárolható, deríthető fel és elemezhető akár több milliárd esemény egyszerre. A Time Series Insights globális áttekintést nyújt az adatokról, így gyorsan ellenőrizheti IoT-megoldásait, és elkerülheti az üzletmenet szempontjából kritikus fontosságú eszközök költséges leállását, mivel a rendszer segít felderíteni a rejtett trendeket és rendellenességeket és elemezni a problémák kiváltó okait majdnem valós időben. A Time Series Insights feldolgozza az eseményközvetítőktől (pl. IoT Huboktól vagy eseményközpontoktól) származó idősorozat-adatokat, indexeli azokat, és egy konfigurálható adatmegőrzési házirend alapján kivonja őket. A felhasználók az adott üzleti forgatókönyvhöz tartozó adatokat egy intuitív felületen vagy REST lekérdezési API-kon keresztül használhatják fel.

![A Time Series Insights áttekintése](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

* IoT-megoldások ellenőrzése és figyelése néhány perc alatt
* Nagy mennyiségű IoT-adat intuitív megjelenítése és elemzése
* Gyorsabb kiváltó okokat kereső elemzések és rendellenességészlelés
* Több eszközt, üzemet és adatot tartalmazó globális nézet készítése

## <a name="key-capabilities-and-benefits"></a>Főbb képességek és előnyök

* **Könnyen használatba vehető**: Az Azure Time Series Insights használatához nem szükséges az adatok előzetes előkészítése, ráadásul rendkívül gyors, így percek alatt csatlakozhat több milliárd Azure IoT Hub- vagy Event Hub-eseményhez. A csatlakozást követően az érzékelők adatai másodpercek alatt megjeleníthetőek és kezelhetőek, így gyorsabban ellenőrizheti IoT-megoldásait. A Time Series Insights rendkívül intuitív és könnyen használható – az adatok kezeléséhez egyáltalán nem szükséges kódolás.  Emellett nem kell megtanulnia egy új programozási nyelvet, mivel a Time Series Insights egy részletes, szabad szöveges lekérdezési felületet biztosít a tapasztalt felhasználók számára, illetve egy néhány kattintással felfedezhető felületet a kezdőknek.

* **Majdnem valós idejű betekintés másodpercek alatt**: A Time Series Insights egy helyről elérhető tárolási, elemzési és megjelenítési összetevőivel jobban kihasználhatja az idősorozat-adataiban rejlő értéket. A Time Series Insights több százmillió érzékelőeseményt képes feldolgozni naponta egy perces késéssel, így gyorsan reagálhat a változásokra. A Time Series Insights részletesebb betekintést nyújt az érzékelőadatokba, mivel lehetővé teszi a trendek és rendellenességek gyors észlelését, így könnyedén végezhet összetettebb, kiváltó okokat kereső elemzéseket, és elkerülheti a költséges állásidőt. A valós idejű és az előzményadatok közötti összefüggések engedélyezése révén a Time Series Insights segít a felhasználóknak felderíteni az adatokban észlelhető rejtett trendeket.

* **Egyéni megoldások készítése**: Az Azure Time Series Insights-adatokat beágyazhatja meglévő alkalmazásaiba, vagy új egyéni megoldásokat hozhat létre a Time Series Insights REST API-jaival.  Ezenkívül a személyre szabott nézetek pillanatok alatt létrehozhatóak és megoszthatóak, így mások is egyszerűen megtekinthetik az Ön által felfedezett összefüggéseket.

* **Méretezhetőség**: A Time Series Insights támogatja az IoT-méretezést. Az előzetes verzió napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. A Time Series Insightsszal a felhasználók közel valós időben jeleníthetik meg és elemezhetik az élő adatstreameket és nagy mennyiségű előzményadatot. A későbbi verziókban a bejövőadat-kezelési és adatmegőrzési kapacitást növelni fogjuk a növekvő nagyvállalati igényeknek megfelelően.

## <a name="taxonomy-of-time-series-insights"></a>A Time Series Insights elnevezési rendszere

* **Környezet**: A környezet egy Azure-erőforrás, amely bejövő adatforgalmi és tárolási kapacitással rendelkezik.  Az ügyfelek az Azure Portalon hozhatnak létre környezeteket a szükséges kapacitással.
* **Eseményforrás**: Az eseményforrás egy eseményközvetítőből, például az Azure Event Hubsból származik.  A Time Series Insights közvetlenül csatlakozik az eseményforrásokhoz, és anélkül dolgozza fel az adatfolyamot, hogy a felhasználóknak akár egy sor kódot is kéne írniuk. A Time Series Insights jelenleg Azure Event Hubs- és Azure IoT Hubs-támogatást biztosít, és a támogatott eseményforrások köre később bővülni fog.
* **Referenciaadat**: A Time Series Insights lehetővé teszi a felhasználók számára, hogy az idősorozat-adatokat referenciaadatokkal kapcsolják össze.  A referenciaadatok lehetnek az eszközökkel kapcsolatos metaadatok vagy egyéb statikus adatok, amelyek viszonylag ritkán változnak. A Time Series Insights összekapcsolja a referenciaadatokat az adatstreamekkel, így a felhasználók majdnem valós időben jeleníthetik meg és elemezhetik őket.

