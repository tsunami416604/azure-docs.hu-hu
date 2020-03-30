---
title: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintése
description: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintését nyújtja.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74481662"
---
# <a name="what-is-azure-database-for-postgresql"></a>Mi az Azure Database for PostgreSQL?
Az Azure Database for PostgreSQL egy relációs adatbázis-szolgáltatás a Microsoft felhőben, amelyet fejlesztők nek készítettek. A nyílt forráskódú [PostgreSQL](https://www.postgresql.org/) adatbázis-motor közösségi verzióján alapul, és két telepítési lehetőségben érhető el: single server és hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Önálló kiszolgáló
Az egykiszolgálós telepítési lehetőség a következőket biztosítja:

- Beépített, [magas rendelkezésre állás](concepts-high-availability.md) további költségek nélkül (99,99%-os SLA)
- Kiszámítható teljesítmény, használatalapú díjszabással
- [Szükség szerint függőleges lépték](concepts-pricing-tiers.md) másodperceken belül
- A kiszolgáló [figyelése és riasztása](concepts-monitoring.md) a kiszolgáló értékeléséhez
- Vállalati szintű biztonság és megfelelőség
- [Biztosítva az](concepts-security.md) érzékeny adatok védelmére nyugalmi és mozgásközbeni védelemérdekében
- [Automatikus biztonsági mentések és időponthoz kötött visszaállítás](concepts-business-continuity.md) akár 35 napig


Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Lehetővé teszik, hogy a gyors alkalmazásfejlesztésre összpontosítson, és felgyorsítsa a piacra jutási időt, ahelyett, hogy értékes időt és erőforrásokat töltene a virtuális gépek és infrastruktúra kezelésére. Az alkalmazást az Ön által választott nyílt forráskódú eszközökkel és platformokkal tovább fejlesztheti anélkül, hogy új készségeket kellene elsajátítania.

Az egykiszolgálós telepítési lehetőség három tarifacsomagot kínál: alapszintű, általános célú és memóriaoptimalizált. Az egyes szintek különböző erőforrásokat és képességeket kínálnak az adatbázisok számítási feladatainak támogatásához. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [Tarifacsomagok](concepts-pricing-tiers.md) ban találja.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure-adatbázis postgreSQL- nagy kapacitású (Citus)
A nagykapacitású (Citus) beállítás vízszintesen skálázhatja a lekérdezéseket több gép en horizontális an- A lekérdezési motor párhuzamosítja a bejövő SQL-lekérdezések ezeken a kiszolgálókon a nagyobb adatkészletek gyorsabb válaszokat. Olyan alkalmazásokat szolgál ki, amelyek nagyobb léptéket és teljesítményt igényelnek, általában olyan munkaterheléseket, amelyek megközelítik a 100 GB-ot.

A Nagykapacitású (Citus) telepítési lehetőség a következőket biztosítja:

- Horizontális skálázás több gépen horizontális horizontális horizontális sértéssel
- Lekérdezési párhuzamosítás ezeken a kiszolgálókon a nagyobb adatkészleteken adott gyorsabb válaszok érdekében
- Kiváló támogatás a több-bérlős alkalmazásokhoz, a valós idejű operatív elemzésekhez és a nagy átviteli sebességű tranzakciós számítási feladatokhoz

A PostgreSQL számára készült alkalmazások elosztott lekérdezéseket futtathatnak a Nagykapacitású (Citus) [szabvány-kapcsolatkódokkal](./concepts-connection-libraries.md) és minimális módosításokkal.

## <a name="contacts"></a>Kapcsolatok
Ha bármilyen kérdése vagy javaslata van az Azure Database for PostgreSQL használatával kapcsolatban, küldjön egy e-mailt az Azure Database for PostgreSQL Team[ @Ask (Azure DB for PostgreSQL)](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)számára. Ez a cím általános kérdésekre szól, nem pedig támogatási jegyekre.

Ezen túlmenően, úgy ezeket a kapcsolattartási pontokat, mint a megfelelő:
- Ha kapcsolatba szeretne lépni az Azure-támogatással, vagy ki szeretne javítani egy problémát a fiókjával [kapcsolatban, kérjen jegyet az Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>További lépések
- A költségösszehasonlításokat és számológépeket az [árképzési oldalon](https://azure.microsoft.com/pricing/details/postgresql/) találja.
- Első lépések: első Azure-adatbázisa a PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) vagy [a Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md) számára
- Építse fel az első alkalmazását Python, PHP, Ruby, C\#, Java, Node.js nyelven: [Csatlakozási kódtárak](./concepts-connection-libraries.md)
