---
title: A foglalási kedvezmény alkalmazása az Azure Cosmos DB-re | Microsoft Docs
description: Megtudhatja, hogy van alkalmazva a foglalási kedvezmény a kiosztott átviteli sebességre (RU/s) az Azure Cosmos DB-ben.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: d5a13e4466234d73bafe8dbe76cae92955cf64bd
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "60370747"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>A foglalási kedvezmény alkalmazása az Azure Cosmos DB-re

A fenntartott Azure Cosmos DB-kapacitás megvásárlása után a foglalási kedvezmény automatikusan alkalmazva lesz a foglalás attribútumaival és mennyiségével egyező Azure Cosmos DB-erőforrásokra. A foglalások fedezik az Azure Cosmos DB-erőforrások számára kiosztott átviteli sebességet. Nem fedezik azonban a szoftverek, a hálózatkezelés, a tárolás és az előre megadott tárolók költségeit.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Tehát ha egy bizonyos órában nem rendelkezik egyező erőforrásokkal, akkor arra az órára elveszíti a foglalási mennyiséget. A lefoglalt de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Az Azure Cosmos DB-fiókokra alkalmazott foglalási kedvezmény

A rendszer a [kiosztott átviteli sebességre](../cosmos-db/request-units.md) (másodpercenkénti kérelemegységek (RU/s)) alkalmazza a foglalási kedvezményt óránkénti alapon. Azon Azure Cosmos DB-erőforrások esetében, amelyek nem futnak egy teljes órán át, a rendszer automatikusan a foglalási attribútumokkal egyező egyéb Cosmos DB-erőforrásokra alkalmazza a foglalási kedvezményt. A kedvezmény párhuzamosan futó Azure Cosmos DB-erőforrásokra is alkalmazható. Ha nem rendelkezik olyan, a foglalási attribútumokkal egyező Cosmos DB-erőforrásokkal, amelyek egy teljes órán át futnak, akkor arra az órára nem kapja meg a foglalási kedvezménnyel járó teljes előnyt.

A kedvezmények szintekre oszlanak. A több kérelemegységgel rendelkező foglalásokra nagyobb kedvezmények érvényesek.

A foglalás megvásárlásakor a rendszer a regionális, igény szerinti díjszabással egyenlő arányban alkalmazza a kedvezményeket az összes régióra. Az egyes régiók foglalási kedvezményeinek arányaiért tekintse meg a cikk [Foglalási kedvezmények régiónként](#reservation-discount-per-region) című szakaszát.

## <a name="reservation-discount-per-region"></a>Foglalási kedvezmények régiónként
A foglalási kedvezmény óránkénti alapon lesz alkalmazva az Azure Cosmos DB-beli átviteli sebesség költségeire. A kedvezmény alkalmazása egy önálló előfizetés vagy a regisztrált fiók hatókörében történik. A foglalási kedvezmény a különböző régiókban a következő arányban érvényes a fogyasztásmérő szerinti használatra:

|Fogyasztásmérő leírása  |Régió |Arány  |
|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/óra – Ázsia és a Csendes-óceáni térség délkeleti régiója  |  Ázsia és a Csendes-óceáni térség délkeleti régiója    |   1      |
|Azure Cosmos DB – 100 RU/s/óra – Ázsia és a Csendes-óceáni térség keleti régiója |   Ázsia és a Csendes-óceáni térség keleti régiója   |    1     |
|Azure Cosmos DB – 100 RU/s/óra – Észak-Európa|  Észak-Európa       |    1     |
|Azure Cosmos DB – 100 RU/s/óra – Korea déli régiója|    Korea déli régiója     |     1    |
|Azure Cosmos DB – 100 RU/s/óra – Nyugat-Európa|    Nyugat-Európa     |      1   |
|Azure Cosmos DB – 100 RU/s/óra – Korea középső régiója|   Korea középső régiója    |       1  |
|Azure Cosmos DB – 100 RU/s/óra – Az Egyesült Királyság déli régiója|   Az Egyesült Királyság déli régiója      |     1    |
|Azure Cosmos DB – 100 RU/s/óra – Az Egyesült Királyság nyugati régiója|   Az Egyesült Királyság nyugati régiója      |    1     |
|Azure Cosmos DB – 100 RU/s/óra – Az Egyesült Királyság északi régiója |   Az Egyesült Királyság északi régiója    |     1    |
|Azure Cosmos DB – 100 RU/s/óra – Az Egyesült Királyság 2. déli régiója|   Az Egyesült Királyság 2. déli régiója      |     1    |
|Azure Cosmos DB – 100 RU/s/óra – USA 2. keleti régiója|  USA 2. keleti régiója     |     1    |
|Azure Cosmos DB – 100 RU/s/óra – USA északi középső régiója|   USA északi középső régiója      |     1    |
|Azure Cosmos DB – 100 RU/s/óra – USA nyugati régiója|   USA nyugati régiója      |     1    |
|Azure Cosmos DB – 100 RU/s/óra – USA középső régiója| USA középső régiója        |     1    |
|Azure Cosmos DB – 100 RU/s/óra – USA 2. nyugati régiója|   USA 2. nyugati régiója      |      1   |
|Azure Cosmos DB – 100 RU/s/óra – USA nyugati középső régiója|   USA nyugati középső régiója      |       1  |
|Azure Cosmos DB – 100 RU/s/óra – USA keleti régiója|   USA keleti régiója      |  1       |
|Azure Cosmos DB – 100 RU/s/óra – Dél-Afrika északi régiója|     Dél-Afrika északi régiója    |   1      |
|Azure Cosmos DB – 100 RU/s/óra – Dél-Afrika nyugati régiója |    Dél-Afrika nyugati régiója      |    1     |
|Azure Cosmos DB – 100 RU/s/óra – Dél-India|    Dél-India     |    1,0375    |
|Azure Cosmos DB – 100 RU/s/óra – Kelet-Kanada|   Kelet-Kanada      |    1.1      |
|Azure Cosmos DB – 100 RU/s/óra – Kelet-Japán|   Kelet-Japán      |    1,125     |
|Azure Cosmos DB – 100 RU/s/óra – Nyugat-Japán|     Nyugat-Japán    |   1,125       |
|Azure Cosmos DB – 100 RU/s/óra – Nyugat-India|     Nyugat-India    |    1,1375     |
|Azure Cosmos DB – 100 RU/s/óra – Közép-India|    Közép-India     |  1,1375       |
|Azure Cosmos DB – 100 RU/s/óra – Ausztrália keleti régiója|     Ausztrália keleti régiója    |   1,15       |
|Azure Cosmos DB – 100 RU/s/óra – Közép-Kanada|  Közép-Kanada       |   1.2       |
|Azure Cosmos DB – 100 RU/s/óra – Közép-Franciaország|   Közép-Franciaország      |    1,25      |
|Azure Cosmos DB – 100 RU/s/óra – Dél-Brazília|  Dél-Brazília       |   1.5      |
|Azure Cosmos DB – 100 RU/s/óra – Ausztrália középső régiója|   Ausztrália középső régiója      |   1.5      |
|Azure Cosmos DB – 100 RU/s/óra – Ausztrália 2. középső régiója| Ausztrália 2. középső régiója        |    1.5     |
|Azure Cosmos DB – 100 RU/s/óra – Dél-Franciaország|    Dél-Franciaország     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazásának forgatókönyvei

Vegye figyelembe a következő foglalási követelményeket:

* Szükséges átviteli sebesség: 50 000 RU/s  
* Használati régiók: 2

Ebben az esetben a teljes, igény szerinti díj a két régióban 500 darab 100 RU/s átviteli sebességű fogyasztásmérőre vonatkozik. A teljes RU/s fogyasztás óránként 100 000.

**1. forgatókönyv**

Tegyük fel például, hogy az USA északi középső régiójában és az USA nyugati régiójában van szüksége üzemelő Azure Cosmos DB-példányokra. Mindkét régió 50 000 RU/s átvitelisebesség-fogyasztással rendelkezik. Egy 100 000 RU/s átviteli sebességgel rendelkező foglalás megvásárlása teljesen kiegyenlítené az igény szerinti díjakat.

A foglalás által fedezett kedvezmény kiszámítása a következőképpen történik: átvitelisebesség-fogyasztás * a régió foglalási kedvezményének aránya. Az USA északi középső régiója és USA nyugati régiója esetében a foglalási kedvezmény aránya 1. Tehát a kedvezményes RU/s összesen 100 000. Ennek az értéknek a kiszámítása a következőképpen történik: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. A normál használatalapú díjak esetében semmilyen további díjat nem kell fizetnie.

|Fogyasztásmérő leírása | Régió |Átvitelisebesség-fogyasztás (RU/s) |Az RU/s értékre alkalmazott foglalási kedvezmény |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/óra – USA északi középső régiója  |   USA északi középső régiója  | 50 000  | 50 000  |
|Azure Cosmos DB – 100 RU/s/óra – USA nyugati régiója  |  USA nyugati régiója   |  50 000  |  50 000 |

**2. forgatókönyv**

Tegyük fel például, hogy Ausztrália 2. középső régiójában és Dél-Franciaországban van szüksége üzemelő Azure Cosmos DB-példányokra. Mindkét régió 50 000 RU/s átvitelisebesség-fogyasztással rendelkezik. Egy 100 000 RU/s átviteli sebességű foglalás megvásárlása lenne célszerű az alábbiak szerint (feltéve, hogy a kedvezmény az Ausztrália 2. középső régiójában mért használatra lett alkalmazva először):

|Fogyasztásmérő leírása | Régió |Átvitelisebesség-fogyasztás (RU/s) |Az RU/s értékre alkalmazott foglalási kedvezmény |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/óra – Ausztrália 2. középső régiója  |  Ausztrália 2. középső régiója   |  50 000  |  50 000   |
|Azure Cosmos DB – 100 RU/s/óra – Dél-Franciaország  |  Dél-Franciaország   |  50 000 |  15 384  |

Ausztrália 2. középső régiójában 50 000 egységnyi használat 75 000 RU/s számlázható használatnak (vagy normalizált használatnak) felel meg. Ennek az értéknek a kiszámítása a következőképpen történik: átvitelisebesség-fogyasztás * a régió foglalási kedvezményének aránya. A számítás eredménye 75 000 RU/s számlázható vagy normalizált használat. Ennek az értéknek a kiszámítása a következőképpen történik: 50 000 * 1,5 = 75 000 RU/s.

Az 100 000 RU/s értékű foglalás vásárlása fedezi Ausztrália 2. középső régiójának 75 000 RU/s értékét, és meghagy 25 000 RU/s-ot a Dél-Franciaország régió számára. A fennmaradó 25 000 RU/s-ból a rendszer 15 384 RU/s foglalási kedvezményt alkalmaz a Dél-Franciaország régióra. A kedvezmény kiszámítása a következőképpen történik: 25 000 / 1,625 = 15 384 RU/s. A Dél-Franciaország régióban fennmaradó 34 616 RU/s-ért normál használatalapú díjat kell fizetnie.

Az Azure számlázási rendszere hozzárendeli a foglalási számlázási kedvezményt az első feldolgozott példányhoz, amely egyezik a foglalási konfigurációval. Ebben az esetben ez Ausztrália 2. középső régiója.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](../billing/billing-understand-reserved-instance-usage-ea.md) cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:

* [Mik azok a foglalások az Azure-ban?](../billing/billing-save-compute-costs-reservations.md)  
* [Előre fizetés fenntartott Azure Cosmos DB-kapacitással rendelkező Azure Cosmos DB-erőforrásokért](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)  
* [Foglalások kezelése az Azure-ban](../billing/billing-manage-reserved-vm-instance.md)  
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../billing/billing-understand-reserved-instance-usage.md)  
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../billing/billing-understand-reserved-instance-usage-ea.md)
* [A foglalási kihasználtság ismertetése CSP-előfizetésnél](https://docs.microsoft.com/partner-center/azure-reservations)
