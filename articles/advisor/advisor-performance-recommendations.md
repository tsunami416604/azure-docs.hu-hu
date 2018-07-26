---
title: Az Azure Advisor-teljesítményajánlások |} A Microsoft Docs
description: Az Advisor használatával az Azure-környezetek teljesítményének optimalizálásához.
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
ms.openlocfilehash: 3331c795cbb1c45820d4c86d287ef57b54f0ae6b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247642"
---
# <a name="advisor-performance-recommendations"></a>Advisor-teljesítményajánlások

Az Azure Advisor-teljesítményajánlások sebesség és az üzleti szempontból kritikus fontosságú alkalmazások válaszképességét javítása érdekében. Advisor javaslatainak teljesítménnyel kapcsolatos javaslatok is kap a **teljesítmény** az Advisor irányítópult lapon.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>A Traffic Manager-profilt, hogy gyorsabban átadása a kifogástalan állapotú végpontok DNS idejének csökkentése

[Élettartam (TTL) beállításainak idő](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) a Traffic Manager-profilok lehetővé teszik, hogy adja meg, hogyan lehet gyorsan végpontok váltani, ha a megadott végpont nem válaszol, a lekérdezéseket. Csökkenti az élettartam-értékek, az azt jelenti, hogy az ügyfelek gyorsabban működő végpontok lesznek átirányítva.

Az Azure Advisor azonosítja a Traffic Manager-profilok konfigurált hosszabb TTL és 20 másodperc és 60 másodperc, attól függően, hogy az élettartam konfigurálása javasolja a profilhoz van beállítva [gyors feladatátvétel](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Adatbázis-teljesítmény javítása az SQL DB Advisor szolgáltatással

Az Advisor-javaslatok az Azure-erőforrások egységes, összevont nézetének biztosít. Integrálható az SQL Database Advisor viszi, az SQL Azure adatbázis teljesítményének javítására vonatkozó javaslatokat. Az SQL Database Advisor az SQL Azure-adatbázisok teljesítményét értékeli a használati előzmények elemzésével. Javaslatok, amelyek a leginkább kihasználni az adatbázis jellemző számítási feladatot futtat majd kínál. 

> [!NOTE]
> Javaslatokat beolvasni egy adatbázisnak rendelkeznie kell egy hét használati kapcsolatban, és a hét belül kell lennie konzisztens tevékenységet észleltünk a fiókjában. Az SQL Database Advisor a lekérdezés konzisztens minták, mint a tevékenység véletlenszerű csúcsforgalomra könnyebben optimalizálható.

Az SQL Database Advisor kapcsolatos további információkért lásd: [az SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Redis gyorsítótár teljesítményének és megbízhatóságának javítása

Az Advisor azonosítja, ahol teljesítményét negatívan befolyásolhatja a magas memóriahasználat, a kiszolgáló terhelését, a hálózati sávszélesség vagy a ügyfélkapcsolatok nagy számú Redis Cache-példányokban. Az Advisor is gyakorlati tanácsokat javaslatok kapcsolatos lehetséges problémák elkerülése érdekében. További információ a Redis Cache javaslatok: [a Redis Cache Advisor](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Az App Service teljesítményének és megbízhatóságának javítása

Az Azure Advisor integrálja, ajánlott eljárásait az App Services élmény javítása és a megfelelő platform képességei felderítése. Példák az App Services ajánlások a következők:
* Ahol memória vagy a Processzor-erőforrások elfogytak, kockázatcsökkentési lehetőségek alkalmazás modulok által példányok felismerése.
* Észlelését, ahol helymegosztást erőforrások, például a web apps és az adatbázisok példányai javíthatja a teljesítményt és alacsony költségek mellett. 

App Services javaslatok kapcsolatos további információkért lásd: [ajánlott eljárások az Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Az Advisor teljesítményajánlásainak elérése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **teljesítmény** fülre.

## <a name="next-steps"></a>További lépések

Az Advisor-javaslatok kapcsolatos további információkért lásd:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor díjakkal kapcsolatos ajánlások](advisor-performance-recommendations.md)
* [Az Advisor magas rendelkezésre állás – javaslatok](advisor-high-availability-recommendations.md)
* [Az Advisor biztonsági javaslatok](advisor-security-recommendations.md)

