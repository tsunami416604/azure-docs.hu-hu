---
title: Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg, hogyan alkalmazza a foglalási kedvezményt az a kiosztott átviteli sebesség (RU/s) az Azure Cosmos DB-ben.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 8c5e9c8437c516a5e6d1d97494b21bfc8e90d88e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960260"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt az Azure Cosmos DB

Miután egy Azure Cosmos DB szolgáltatás számára fenntartott kapacitást vásárol, a rendszer automatikusan alkalmazza a foglalási kedvezményt az Azure Cosmos DB-erőforrásokat, amelyek megfelelnek a attribútumok és a Foglalás mennyiségét. Egy foglalás tartalmazza az Azure Cosmos DB-erőforrásokat az adatátviteli teljesítmény, és nem fedi le a szoftvert, hálózatkezelési, tárolási, illetve az előre meghatározott tároló díjak.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Azure Cosmos DB-fiókokhoz alkalmazza a foglalási kedvezményt

Rendszer alkalmazza a foglalási kedvezményt [kiosztott átviteli sebesség](../cosmos-db/request-units.md) kérelemegység / óra órán keresztül alapon second(RU/s) tekintetében. Más Cosmos DB-erőforrások, amelyek megfelelnek a Foglalás attribútumok automatikusan alkalmazza a teljes órát nem futtató Azure Cosmos DB-erőforrások, a foglalási kedvezményt. Azure Cosmos DB-erőforrásokat, amelyek egy időben futnak a kedvezményeket is alkalmazhat. Ha nem rendelkezik, amely megfelel a Foglalás attribútumok vagy teljes órát futtató Cosmos DB-erőforrásokat, a teljes előnyeit, a foglalási kedvezményt az adott órában nem kap.

* A kedvezmény vannak rétegzett, ami azt jelenti, hogy a foglalást magasabb kérelemegységgel adja meg a nagyobb kedvezmények.  
* A foglalásvásárlás kedvezmények vonatkoznak egyenértékű, igény szerinti regionális díjszabás aránya az összes régióban. Foglalási kedvezményt arányok az egyes régiókban lásd [régiónként a foglalási kedvezményt](#reservation-discount-per-region) című szakaszát.

## <a name="reservation-discount-per-region"></a>Foglalási kedvezményt régiónként
Az Azure Cosmos DB átviteli sebesség a költségek az előfizetéssel vagy a regisztrált/fiók hatókörében óra órán keresztül alapon alkalmazza a foglalási kedvezményt. A foglalási kedvezményt a következő arány a különböző régiókban lévő használatának mérését vonatkozik:

|Fogyasztásmérő leírása  |Régió |Arány  |
|---------|---------|---------|
|Az Azure Cosmos DB - RU/s/óra 100 - óceáni térség délkeleti régiója  |  Ázsia és a Csendes-óceáni térség délkeleti régiója    |   1      |
|Az Azure Cosmos DB - RU/s/óra 100 - óceáni térség keleti régiója |   Ázsia és a Csendes-óceáni térség keleti régiója   |    1     |
|Az Azure Cosmos DB - RU/s/óra 100 – Észak-Európa|  Észak-Európa       |    1     |
|Az Azure Cosmos DB - RU/s/óra 100 – Korea déli régiója|    Korea déli régiója     |     1    |
|Az Azure Cosmos DB - RU/s/óra 100 – Nyugat-Európa|    Nyugat-Európa     |      1   |
|Az Azure Cosmos DB - RU/s/óra 100 – Korea középső régiója|   Korea középső régiója    |       1  |
|Az Azure Cosmos DB - RU/s/óra 100 – Egyesült Királyság déli régiója|   Az Egyesült Királyság déli régiója      |     1    |
|Az Azure Cosmos DB - RU/s/óra 100 – Egyesült Királyság nyugati régiója|   Az Egyesült Királyság nyugati régiója      |    1     |
|Az Azure Cosmos DB - RU/s/óra 100 – Egyesült Királyság északi régiója |   Egyesült Királyság északi régiója    |     1    |
|Az Azure Cosmos DB - RU/s/óra 100 – Egyesült Királyság 2. déli régiója|   Egyesült Királyság 2. déli régiója      |     1    |
|Az Azure Cosmos DB - RU/s/óra 100 – USA 2. keleti régiója|  USA 2. keleti régiója     |     1    |
|Az Azure Cosmos DB - RU/s/óra 100 – USA északi középső régiója|   USA északi középső régiója      |     1    |
|Az Azure Cosmos DB használatával – 100 RU/s/óra – USA nyugati régiója|   USA nyugati régiója      |     1    |
|Az Azure Cosmos DB - RU/s/óra 100 – USA középső régiója| USA középső régiója        |     1    |
|Az Azure Cosmos DB - RU/s/óra 100 – USA 2. nyugati régiója|   USA 2. nyugati régiója      |      1   |
|Az Azure Cosmos DB - RU/s/óra 100 – USA nyugati középső régiója|   USA nyugati középső régiója      |       1  |
|Az Azure Cosmos DB használatával – 100 RU/s/óra – USA keleti régiója|   USA keleti régiója      |  1       |
|Az Azure Cosmos DB - RU/s/óra 100 - SA északi régiója|     SA északi régiója    |   1      |
|Az Azure Cosmos DB - RU/s/óra 100 - Dél-amerikai nyugati régiója |    Dél-amerikai nyugati régiója      |    1     |
|Az Azure Cosmos DB használatával – 100 RU/s/óra – Dél-India|    Dél-India     |    1.0375    |
|Az Azure Cosmos DB - RU/s/óra 100 - hitelesítésszolgáltató keleti régiója|   Kelet-Kanada      |    1.1      |
|Az Azure Cosmos DB - RU/s/óra 100 – kelet-japán|   Kelet-Japán      |    1,125     |
|Az Azure Cosmos DB - RU/s/óra 100 – Nyugat-japán|     Nyugat-Japán    |   1,125       |
|Az Azure Cosmos DB használatával – 100 RU/s/óra – Nyugat-India|     Nyugat-India    |    1.1375     |
|Az Azure Cosmos DB használatával – 100 RU/s/óra – közép-India|    Közép-India     |  1.1375       |
|Az Azure Cosmos DB - RU/s/óra 100 – Ausztrália keleti régiója|     Ausztrália keleti régiója    |   1,15       |
|Az Azure Cosmos DB - RU/s/óra 100 - hitelesítésszolgáltató közép-India|  Közép-Kanada       |   1.2       |
|Az Azure Cosmos DB - RU/s/óra 100 – közép-Franciaország|   Közép-Franciaország      |    1,25      |
|Az Azure Cosmos DB - RU/s/óra 100 – Dél-Brazília|  Dél-Brazília       |   1.5      |
|Az Azure Cosmos DB - RU/s/óra 100 – Ausztrália középső régiója|   Ausztrália középső régiója      |   1.5      |
|Az Azure Cosmos DB - RU/s/óra 100 – Ausztrália 2. középső régiója| Ausztrália 2. középső régiója        |    1.5     |
|Az Azure Cosmos DB - RU/s/óra 100 – Dél-Franciaország|    Dél-Franciaország     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Forgatókönyvek, amelyek megmutatják, hogyan kell alkalmazni a foglalási kedvezményt

Vegye figyelembe a foglalást az alábbi követelményeket:

* Átviteli sebességgel: 50 000 RU/s  
* Régiók: 2. 

Ebben az esetben az összes igény szerinti szolgáltatások díjait számlán egy teljes RU/s felhasználásra 100 000 óránként 100 RU/s-fogyasztásmérő két ezekben a régiókban, 500 mennyiség vonatkoznak. 

**1. forgatókönyv**

Például ha az Azure Cosmos DB-telepítések az "USA északi középső régiója" és "USA nyugati régiója" régióban van szüksége, és minden egyes régió van 50 000 RU/s átviteli sebesség használatalapú. A foglalásvásárlás 100 000 RU/s teljesen lenne elosztása az igény szerinti szolgáltatások díjait számlán.

A Foglalás által biztosított kedvezményeket számítja ki, hogy (átviteli fogyasztás * reservation_discount_ratio_for_that_region). Az "USA északi középső régiója" és "USA nyugati régiója" régióban a foglalási kedvezményt arány az "1". Így az összes kedvezményes RU/s a 100 000 RU/s (Ez az érték számítja ki, hogy: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s), és nem kell fizetnie a további díjakat a normál használatalapú díjakat. 

|Fogyasztásmérő leírása | Régió |Átviteli sebesség fogyasztás (RU/s) |RU/s alkalmazza a foglalási kedvezményt |
|---------|---------|---------|---------|
|Az Azure Cosmos DB - RU/s/óra 100 – USA északi középső régiója  |   USA északi középső régiója  | 50,000  | 50,000  |
|Az Azure Cosmos DB használatával – 100 RU/s/óra – USA nyugati régiója  |  USA nyugati régiója   |  50,000  |  50,000 |

**2. forgatókönyv**

Például ha az Azure Cosmos DB központi telepítések "Ausztrália 2. középső régiója" és "Dél-Franciaország" régióban van szüksége, és minden egyes régió van 50 000 RU/s átviteli sebesség használatalapú. A foglalásvásárlás 100 000 RU/s alkalmazható a következők lennének (feltéve, hogy Ausztrália 2. középső régiója használati először lett kedvezményes):

|Fogyasztásmérő leírása | Régió |Átviteli sebesség fogyasztás (RU/s) |RU/s alkalmazza a foglalási kedvezményt |
|---------|---------|---------|---------|
|Az Azure Cosmos DB - RU/s/óra 100 – Ausztrália 2. középső régiója  |  Ausztrália 2. középső régiója   |  50,000  |  50,000   |
|Az Azure Cosmos DB - RU/s/óra 100 – Dél-Franciaország  |  Dél-Franciaország   |  50,000 |  15,384  |

75 000 RU/s számlázható használat (vagy normalizált használati) "Ausztrália 2. középső régiója" régióban használat 50 000 egység felel meg. Ez az érték számítja ki, hogy (átviteli fogyasztás * reservation_discount_ratio_for_that_region) 75 000 RU/s, amely egyenlő (Ez az érték számítja ki, hogy: 50 000 * 1.5-ös = 75 000 RU/s) számlázható vagy normalizált használatának. 

100 000 RU/s foglalásvásárlás lenne eltolás "Ausztrália 2. középső régiója" a 75 000 RU/s, és 25 000 RU/s hagyja a "Dél-Franciaország" régiónak. A maradék 25 000 RU/s, a foglalási kedvezményt 15,384 RU/s (Ez az érték számítja ki, hogy: 25 000 / 1.625 = 15,384 RU/s) "Dél-Franciaország" régió vonatkozik. A fennmaradó 34,616 RU/mp "Dél-Franciaország" régióban a normál használatalapú díjszabás szerint számoljuk el. 

Az Azure számlázási rendszer fog hozzárendelni a Foglalás számlázási kedvezményei az első példányhoz, která je zpracována, amely megfelel a Foglalás konfigurációt (például, Ausztrália 2. középső régiója ebben az esetben).

Ismertetése és használati jelentések számlázási megtekintheti az alkalmazást az Azure foglalások: [ismertetése Azure foglalás használatának](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mik az Azure-foglalásokat?](../billing/billing-save-compute-costs-reservations.md)  
* [Fizessen elő az Azure Cosmos DB-erőforrásokat, hogy az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md)  
* [Az Azure Reservations kezelése](../billing/billing-manage-reserved-vm-instance.md)  
* [A használatalapú fizetéses előfizetést foglalás használati adatai](../billing/billing-understand-reserved-instance-usage.md)  
* [A nagyvállalati beléptetés foglalás használati adatai](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [CSP-előfizetésekben foglalás használati adatai](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

