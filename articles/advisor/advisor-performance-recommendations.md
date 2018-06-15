---
title: Az Azure Advisor teljesítmény javaslatok |} Microsoft Docs
description: Az Advisor segítségével az Azure-környezetekhez teljesítményének optimalizálásához.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 00abb5aafc6f3aec2e2dd7326a307bee74d97cc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149355"
---
# <a name="advisor-performance-recommendations"></a>Teljesítmény javaslatokat biztosít

Az Azure teljesítménye javaslatokat biztosít a sebesség és az üzleti szempontból kritikus fontosságú alkalmazások válaszképességének javítása érdekében. Letölthető teljesítmény javaslatok Advisor a **teljesítmény** az Advisor irányítópult.

## <a name="improve-database-performance-with-sql-db-advisor"></a>Az SQL DB Advisor adatbázis teljesítményének növelése

Az Advisor egy egységes, összevont nézetének összes Azure-erőforrások javaslatokat biztosít. SQL Database Advisor lapján kapcsolja a javaslatok az SQL Azure adatbázis teljesítményének növelése integrálható. SQL Database Advisor segédprogramot a használati előzmények elemzésével az SQL Azure-adatbázisok teljesítményét értékeli. Lehetőséget kínál a javaslatok, amelyek az adatbázis átlagos munkaterhelésre fut a legalkalmasabb. 

> [!NOTE]
> Ahhoz, hogy a javaslatok, egy adatbázisnak rendelkeznie kell egy hét használati kapcsolatos, és a hét belül kell konzisztens tevékenységet észleltünk a fiókjában. SQL Database Advisor segédprogramot a lekérdezés konzisztens mintára mint a véletlenszerű felszakadásáig tevékenység könnyebben optimalizálható.

SQL Database Advisor kapcsolatos további információkért lásd: [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Redis gyorsítótár teljesítményének és megbízhatóságának növelése

Az Advisor azonosítja a Redis Cache példány ahol teljesítményt hátrányosan érintheti által magas memóriahasználat, a kiszolgáló terhelését, a hálózati sávszélesség vagy a nagyszámú ügyfél kapcsolatok. Az Advisor is gyakorlati tanácsokat ajánlásokat lehetséges problémák elkerülése érdekében. További információ a Redis Cache javaslatok: [Redis gyorsítótár Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>App Service-teljesítmény és megbízhatóság javítása

Azure Advisor integrálja a alkalmazásszolgáltatások felhasználói élmény javítása és felderítésére vonatkozó platform képességei ajánlott eljárásait. Például App Services ajánlások a következők:
* Ahol memória vagy a Processzor-erőforrások elfogytak a megoldás beállítások app futtatókörnyezetek által példányok észlelése.
* Ahol helymegosztást erőforrások – például webes alkalmazásokat és adatbázisokat példányok észlelésének javíthatja a teljesítményt és az alacsonyabb költségek. 

App Service szolgáltatások javaslatok kapcsolatos további információkért lásd: [ajánlott eljárások az Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Teljesítmény javaslatok az Advisor elérése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **teljesítmény** fülre.

## <a name="next-steps"></a>További lépések

Az Advisor-javaslatokra kapcsolatos további információkért lásd:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Költség javaslatokat biztosít](advisor-performance-recommendations.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-high-availability-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)

