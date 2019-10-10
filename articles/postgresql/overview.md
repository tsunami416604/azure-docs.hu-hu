---
title: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintése
description: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintését nyújtja.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177955"
---
# <a name="what-is-azure-database-for-postgresql"></a>Mi az Azure Database for PostgreSQL?
A Azure Database for PostgreSQL egy, a fejlesztők számára készült Microsoft-felhőben található, a Microsoft felhőalapú szolgáltatása. A szolgáltatás a nyílt forráskódú [PostgreSQL](https://www.postgresql.org/) adatbázismotor közösségi verzióján alapul, és két telepítési lehetőségben érhető el: egyetlen kiszolgáló és nagy kapacitású (Citus) (előzetes verzió).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – egyetlen kiszolgáló
Az egykiszolgálós üzembe helyezési lehetőség a következőket biztosítja:

- Beépített magas rendelkezésre állás további díjszabás nélkül (99,99%-os SLA)
- Kiszámítható teljesítmény, használatalapú díjszabással
- A vertikális skálázás szükség szerint másodpercek alatt
- Monitorozás és riasztás a skálázás hatásának gyors felméréséhez
- Az inaktív és átvitel alatt álló bizalmas adatok védelme
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig
- Nagyvállalati szintű biztonság és megfelelőség

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Lehetővé teszik az alkalmazások gyors fejlesztését és a piacra kerülési idő felgyorsítását ahelyett, hogy értékes időt és erőforrásokat kellene kitölteni a virtuális gépek és az infrastruktúra kezeléséhez. Az alkalmazást továbbra is kifejlesztheti a kívánt nyílt forráskódú eszközökkel és platformmal, anélkül, hogy új ismereteket kellene megtanulnia.

Az egykiszolgálós üzembe helyezési lehetőség három díjszabási szintet kínál: alapszintű, általános célú és memória optimalizálva. Az egyes szintek különböző erőforrásokat és képességeket kínálnak az adatbázisok számítási feladatainak támogatásához. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részletekért tekintse meg a [díjszabási szintet](concepts-pricing-tiers.md) .

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL-nagy kapacitású (Citus) (előzetes verzió)
A nagy kapacitású (Citus) lehetőség vízszintesen méretezi a lekérdezéseket több gép között a horizontális skálázással. A lekérdezési motor parallelizes a bejövő SQL-lekérdezéseket ezen kiszolgálókon a nagyméretű adatkészletek gyorsabb megválaszolásához. Olyan alkalmazásokat kínál, amelyek nagyobb méretet és teljesítményt igényelnek, és általában a várható munkaterhelések, vagy már meghaladja a-100 GB-ot.

A nagy kapacitású (Citus) telepítési lehetőség a következőket biztosítja:

- Horizontális skálázás több gépen több szegmensen keresztül
- Párhuzamos lekérdezése a kiszolgálók között a nagyméretű adatkészletek gyorsabb reagálásához
- Kiváló támogatás a több-bérlős alkalmazások, a valós idejű operatív elemzések és a nagy teljesítményű tranzakciós számítási feladatok számára

A PostgreSQL-re épülő alkalmazások [a standard szintű](./concepts-connection-libraries.md) nagy kapacitású (Citus) elosztott lekérdezéseket futtathatnak, és minimális módosításokat hajtanak végre.

Vegye figyelembe, hogy a nagy kapacitású (Citus) nyilvános előzetes verzióban érhető el, így még nem kínál SLA-t.

## <a name="data-security"></a>Adatbiztonság
Azure Database for PostgreSQL megtartja az Azure Database Services adatbiztonságának hagyományát. Olyan funkciókkal rendelkezik, amelyek korlátozzák a hozzáférést, a REST-alapú és a mozgásban lévő adatok védelméhez, és segít a tevékenységek figyelésében. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/overview/trusted-cloud/) talál információkat.

A Azure Database for PostgreSQL szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adattárolási titkosításhoz. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva a lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le. Alapértelmezés szerint a Azure Database for PostgreSQL szolgáltatás a hálózaton belül és az adatbázis és az ügyfélalkalmazás között biztonságos kapcsolatot igényel a mozgásban lévő adateléréshez.

## <a name="contacts"></a>Kapcsolatok
Ha bármilyen kérdése vagy javaslata van a Azure Database for PostgreSQL használatáról, küldjön e-mailt a Azure Database for PostgreSQL csapatnak ([@Ask Azure-adatbázis PostgreSQL-hez](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ez a címe a támogatási jegyek helyett általános kérdésekre szolgál.

Emellett vegye figyelembe az alábbi kapcsolattartási pontokat a megfelelő módon:
- Ha kapcsolatba szeretne lépni az Azure támogatási szolgálatával, vagy javítsa ki a fiókkal kapcsolatos problémát, vegyen fel [egy jegyet a Azure Portalból](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Következő lépések
- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/postgresql/) a költségek összehasonlításáért és árkalkulációjáért.
- Első lépésként hozza létre első Azure Database for PostgreSQL [egy kiszolgálóját](./quickstart-create-server-database-portal.md) vagy [nagy kapacitású (Citus) (előzetes verzió)](./quickstart-create-hyperscale-portal.md)
- Építse fel az első alkalmazását Python, PHP, Ruby, C\#, Java, Node.js nyelven: [Csatlakozási kódtárak](./concepts-connection-libraries.md)
