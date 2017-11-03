---
title: "Az Azure Advisor teljesítmény javaslatok |} Microsoft Docs"
description: "Az Advisor segítségével az Azure-környezetekhez teljesítményének optimalizálásához."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 995a1f37a3fd68b39c14a95d46109c0f7814018d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-performance-recommendations"></a>Teljesítmény javaslatokat biztosít

Az Azure teljesítménye javaslatokat biztosít a sebesség és az üzleti szempontból kritikus fontosságú alkalmazások válaszképességének javítása érdekében. Letölthető teljesítmény javaslatok Advisor a **teljesítmény** az Advisor irányítópult.

![Az Advisor teljesítmény lap](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="improve-database-performance-with-sql-db-advisor"></a>Az SQL DB Advisor adatbázis teljesítményének növelése

Az Advisor egy egységes, összevont nézetének összes Azure-erőforrások javaslatokat biztosít. SQL Database Advisor lapján kapcsolja a javaslatok az SQL Azure adatbázis teljesítményének növelése integrálható. SQL Database Advisor segédprogramot a használati előzmények elemzésével az SQL Azure-adatbázisok teljesítményét értékeli. Lehetőséget kínál a javaslatok, amelyek az adatbázis átlagos munkaterhelésre fut a legalkalmasabb. 

> [!NOTE]
> Ahhoz, hogy a javaslatok, egy adatbázisnak rendelkeznie kell egy hét használati kapcsolatos, és a hét belül kell konzisztens tevékenységet észleltünk a fiókjában. SQL Database Advisor segédprogramot a lekérdezés konzisztens mintára mint a véletlenszerű felszakadásáig tevékenység könnyebben optimalizálható.

SQL Database Advisor kapcsolatos további információkért lásd: [SQL Database Advisor](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![SQL-adatbázissal kapcsolatos javaslatok](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="improve-redis-cache-performance-and-reliability"></a>Redis gyorsítótár teljesítményének és megbízhatóságának növelése

Az Advisor azonosítja a Redis Cache példány ahol teljesítményt hátrányosan érintheti által magas memóriahasználat, a kiszolgáló terhelését, a hálózati sávszélesség vagy a nagyszámú ügyfél kapcsolatok. Az Advisor is gyakorlati tanácsokat ajánlásokat lehetséges problémák elkerülése érdekében. További információ a Redis Cache javaslatok: [Redis gyorsítótár Advisor](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>App Service-teljesítmény és megbízhatóság javítása

Azure Advisor integrálja a alkalmazásszolgáltatások felhasználói élmény javítása és felderítésére vonatkozó platform képességei ajánlott eljárásait. Például App Services ajánlások a következők:
* Ahol memória vagy a Processzor-erőforrások elfogytak a megoldás beállítások app futtatókörnyezetek által példányok észlelése.
* Ahol helymegosztást erőforrások – például webes alkalmazásokat és adatbázisokat példányok észlelésének javíthatja a teljesítményt és az alacsonyabb költségek. 

App Service szolgáltatások javaslatok kapcsolatos további információkért lásd: [ajánlott eljárások az Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![App Services javaslatok](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Teljesítmény javaslatok az Advisor elérése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Kattintson a bal oldali ablaktáblában **további szolgáltatások**.

3. A szolgáltatás menü ablaktáblán alatt **figyelés és felügyelet**, kattintson a **Azure Advisor**.  
 Az Advisor irányítópult jelenik meg.

4. Az Advisor irányítópultján kattintson a **teljesítmény** fülre.

5. Válassza ki az előfizetést, amely javaslatokat kap, és kattintson a kívánt **javaslatok beszerzése**.

> [!NOTE]
> Advisor-javaslatokra szeretne használni, először *az előfizetés regisztrálása* az Advisor szolgáltatásban. Egy előfizetés regisztrálva amikor egy *előfizetés tulajdonosának* elindítja az Advisor irányítópulton, és rákattint a **javaslatok beszerzése** gombra. Ez egy *egyszeri művelet*. Az előfizetés regisztrálása után érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* előfizetés, egy erőforráscsoport vagy egy adott erőforrás.

## <a name="next-steps"></a>Következő lépések

Az Advisor-javaslatokra kapcsolatos további információkért lásd:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Költség javaslatokat biztosít](advisor-performance-recommendations.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-high-availability-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)

