---
title: Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg, hogyan alkalmazza a foglalási kedvezményt az a kiosztott átviteli sebesség (RU/s) az Azure Cosmos DB-ben.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: cwatson
ms.reviewer: sngun
ms.openlocfilehash: 5b15b5f8188f2077b3e9cb17ab3794e881a4deb3
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353432"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt az Azure Cosmos DB

Miután egy Azure Cosmos DB szolgáltatás számára fenntartott kapacitást vásárol, a rendszer automatikusan alkalmazza a foglalási kedvezményt az Azure Cosmos DB-erőforrásokat, amelyek megfelelnek a attribútumok és a Foglalás mennyiségét. Egy foglalást az Azure Cosmos DB-erőforrásokat az adatátviteli teljesítmény ismerteti. Nem fedi le a szoftvereket, a hálózat, tárolás, vagy előre definiált tároló díjak.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Azure Cosmos DB-fiókokhoz alkalmazza a foglalási kedvezményt

Rendszer alkalmazza a foglalási kedvezményt [kiosztott átviteli sebesség](../cosmos-db/request-units.md) tekintetében kérelemegység / másodperc (RU/s) a óra – óra alapon. Más Cosmos DB-erőforrások, amelyek megfelelnek a Foglalás attribútumok automatikusan alkalmazza a teljes órát nem futtató Azure Cosmos DB-erőforrások, a foglalási kedvezményt. Azure Cosmos DB-erőforrásokat, amelyek egy időben futnak a kedvezményeket is alkalmazhat. Ha nem rendelkezik, amely megfelel a Foglalás attribútumok vagy teljes órát futtató Cosmos DB-erőforrásokat, a teljes előnyeit, a foglalási kedvezményt az adott órában nem kap.

A kedvezmény számítógépen rétegzett. Magasabb kérelemegységgel foglalások nagyobb kedvezmények adja meg. 

A foglalásvásárlás kedvezmények vonatkoznak egyenértékű, igény szerinti regionális díjszabás aránya az összes régióban. A foglalási kedvezményt arányok minden régióban, tekintse meg a [régiónként a foglalási kedvezményt](#reservation-discount-per-region) című szakaszát.

## <a name="reservation-discount-per-region"></a>Foglalási kedvezményt régiónként
Azure Cosmos DB átviteli költségek óra órán keresztül alkalmazza a foglalási kedvezményt. Az egyetlen előfizetés vagy a regisztrált/fiók hatóköre alkalmazva. A foglalási kedvezményt a következő arányok a különböző régiókban lévő használatának mérését vonatkozik:

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
* Régiók: 2 

Ebben az esetben az összes igény szerinti szolgáltatások díjait számlán 500 mennyiség 100 RU/s mérő e két régióban vannak. A teljes RU/s használat 100 000 óránként. 

**1. forgatókönyv**

Tegyük fel, hogy módosítania kell az Azure Cosmos DB központi telepítések, az USA északi középső régiója és USA nyugati régióban. Minden egyes régió 50 000 RU/s átviteli sebesség használatalapú rendelkezik. A foglalásvásárlás 100 000 RU/s teljesen lenne elosztása az igény szerinti szolgáltatások díjait számlán.

A kedvezményt, amely lefed egy foglalást számítja ki, hogy: átviteli fogyasztás * reservation_discount_ratio_for_that_region. A foglalási kedvezményt arány az USA északi középső régiója és USA nyugati régió esetében az 1. Így az összes kedvezményes RU/s 100 000. Ez az érték számítja ki, hogy: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. Nem kell fizetnie a további díjakat a normál használatalapú díjakat. 

|Fogyasztásmérő leírása | Régió |Átviteli sebesség fogyasztás (RU/s) |RU/s alkalmazza a foglalási kedvezményt |
|---------|---------|---------|---------|
|Az Azure Cosmos DB - RU/s/óra 100 – USA északi középső régiója  |   USA északi középső régiója  | 50,000  | 50,000  |
|Az Azure Cosmos DB használatával – 100 RU/s/óra – USA nyugati régiója  |  USA nyugati régiója   |  50,000  |  50,000 |

**2. forgatókönyv**

Tegyük fel, hogy módosítania kell az Azure Cosmos DB központi telepítések, Ausztrália 2. középső régiója és Dél-Franciaország régiókban. Minden egyes régió 50 000 RU/s átviteli sebesség használatalapú rendelkezik. A foglalásvásárlás 100 000 RU/s alkalmazható a következők lennének (feltéve, hogy Ausztrália 2. középső régiója használati először lett kedvezményes):

|Fogyasztásmérő leírása | Régió |Átviteli sebesség fogyasztás (RU/s) |RU/s alkalmazza a foglalási kedvezményt |
|---------|---------|---------|---------|
|Az Azure Cosmos DB - RU/s/óra 100 – Ausztrália 2. középső régiója  |  Ausztrália 2. középső régiója   |  50,000  |  50,000   |
|Az Azure Cosmos DB - RU/s/óra 100 – Dél-Franciaország  |  Dél-Franciaország   |  50,000 |  15,384  |

Ausztrália 2. középső régiója régióban 50 000 egység használati 75 000 RU/s számlázható használat (vagy normalizált használati) felel meg. Ez az érték számítja ki, hogy: átviteli fogyasztás * reservation_discount_ratio_for_that_region. A számítási 75 000 RU/s számlázható vagy normalizált használat eredménye. Ez az érték számítja ki, hogy: 50 000 * 1.5-ös = 75 000 RU/s.

A foglalásvásárlás 100 000 RU/s lenne eltolás Ausztrália 2. középső régiója az 75 000 RU/s. 25 000 RU/s, hagyja a Dél-Franciaország régió. A maradék 25 000 RU/s, az a foglalási kedvezményt 15,384 RU/s vonatkozik a Dél-Franciaország régió. A kedvezmény értékét számítja ki, hogy: 25 000 / 1.625 = 15,384 RU/s. A fennmaradó 34,616 RU/s a Dél-Franciaország régió a normál használatalapú díjszabás szerint számoljuk el. 

Az Azure számlázási rendszer fog hozzárendelni a Foglalás számlázási kedvezményei az első példányhoz, amelyek feldolgozása, és, amely megfelel a Foglalás konfigurációját. Ha például Ausztrália 2. középső régiója ebben az esetben.

Ismertetése és használati jelentések számlázási megtekintheti az alkalmazást az Azure foglalások: [ismertetése Azure foglalás használatának](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>További lépések

Azure foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mik az Azure-foglalásokat?](../billing/billing-save-compute-costs-reservations.md)  
* [Fizessen elő az Azure Cosmos DB-erőforrásokat, hogy az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)  
* [Az Azure Reservations kezelése](../billing/billing-manage-reserved-vm-instance.md)  
* [A használatalapú fizetéses előfizetést foglalás használati adatai](../billing/billing-understand-reserved-instance-usage.md)  
* [A nagyvállalati beléptetés foglalás használati adatai](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [CSP-előfizetésekben foglalás használati adatai](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

