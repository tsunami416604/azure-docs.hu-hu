---
title: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintése
description: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintését nyújtja.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: f4023fa84215a0319669de0d812d8306b62278e3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073266"
---
# <a name="what-is-azure-database-for-postgresql"></a>Mi az Azure Database for PostgreSQL?
Azure Database for PostgreSQL relációsadatbázis-szolgáltatás a fejlesztők számára készült Microsoft-felhőben. Nyílt forráskódú közösségi verzióján alapul [PostgreSQL](https://www.postgresql.org/) adatbázismotor, és két üzembe helyezési lehetőség érhető el: Egyetlen kiszolgáló és a nagy kapacitású (Citus) (előzetes verzió).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – egyetlen kiszolgáló
Az egykiszolgálós telepítési lehetőséget kínál:

- Beépített magas rendelkezésre állás további költség nélkül (99,99 %-os SLA-t)
- Kiszámítható teljesítmény, használatalapú díjszabással
- Függőleges méretezés igény, néhány másodperc alatt
- Monitorozási és riasztási segítségével mérheti fel gyorsan hatásainak
- Az inaktív és átvitel alatt álló bizalmas adatok védelme
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig
- Vállalati szintű biztonság és megfelelőség

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Lehetővé teszik, hogy arra koncentrálhasson, az alkalmazások gyors fejlesztésére és felgyorsítja a piacra ahelyett, hogy értékes időt és erőforrásokat a virtuális gépek és infrastruktúra kezeléséhez. Továbbra is a nyílt forráskódú eszközök és a választott platformnak az alkalmazás fejlesztését, új készségek nélkül.

Egykiszolgálós telepítési beállítást három tarifacsomagot kínál: Alapszintű, általános célú és memóriahasználatra optimalizált. Az egyes szintek különböző erőforrásokat és képességeket kínálnak az adatbázisok számítási feladatainak támogatásához. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. Lásd: [Tarifacsomagok](concepts-pricing-tiers.md) részleteiről.

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)
A nagy kapacitású (Citus) beállítás vízszintesen méretezhető lekérdezések horizontális particionálás segítségével több gép között. A lekérdezési motor parallelizes bejövő SQL-lekérdezések gyorsabb válaszok nagy méretű adatkészletek esetén ezek a kiszolgálók között. Nagyobb méretezést és teljesítményt igénylő alkalmazások, általában a számítási feladatok hamarosan eléri –, vagy már meghaladja – 100 GB adatot szolgál.

A nagy kapacitású (Citus) telepítési lehetőséget kínál:

- Vízszintes méretezés horizontális particionálás segítségével több gép között
- Lekérdezés ezerszer gyorsabb válaszok nagy méretű adatkészletek esetén ezek a kiszolgálók között
- Több-bérlős alkalmazások, a valós idejű működési elemzések és a nagy teljesítményű tranzakciós munkaterhelések magas szintű támogatást

A PostgreSQL futtathatja az elosztott lekérdezések nagy kapacitású (Citus) standard-val készített alkalmazások [adatkapcsolattárak](./concepts-connection-libraries.md) és minimális módosításait.

Vegye figyelembe, hogy nagy kapacitású (Citus) nyilvános előzetes verzióban érhető el, és így még nem vonatkozik szolgáltatásiszint-szerződés.

## <a name="data-security"></a>Adatbiztonság
Azure Database for postgresql-hez az Azure adatbázis-szolgáltatások hagyomány adatbiztonsági garantálják. Az funkciók, amelyek korlátozzák a hozzáférést, inaktív adatok és a mozgásban lévő védheti meg és monitorozhatja a tevékenység rendelkezik. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/overview/trusted-cloud/) talál információkat.

Az Azure Database for PostgreSQL szolgáltatás tárolási titkosítást használ az inaktív adatok, és megfelel a FIPS 140-2 szabványnak. Adatok biztonsági másolatokkal együtt titkosítva kerülnek a lemezekre. A szolgáltatás használ a 256 bites AES-titkosítás az Azure tárolási tartalmazza, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le. Alapértelmezés szerint az Azure Database for PostgreSQL szolgáltatás szükséges biztonságos kapcsolatok adatokat a mozgásban lévő a hálózati és az adatbázis és az ügyfél alkalmazás között is.

## <a name="contacts"></a>Kapcsolatok
Esetleges kérdéseivel és javaslatok használatáról az Azure Database for postgresql-hez, az e-mail küldése az Azure database for PostgreSQL csapata ([ @Ask PostgreSQL-hez készült Azure DB](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ez a cím a támogatási jegyeket, hanem általános kérdések.

Ezenkívül fontolja meg a következőket kapcsolódási szükség szerint:
- Forduljon az Azure ügyfélszolgálatához, vagy megoldani a problémát a fiókjával [jegyet az Azure Portalról](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>További lépések
- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/postgresql/) a költségek összehasonlításáért és árkalkulációjáért.
- Első lépésként hozza létre az első Azure Database for postgresql-hez [egykiszolgálós](./quickstart-create-server-database-portal.md) vagy [nagy kapacitású (Citus) (előzetes verzió)](./quickstart-create-hyperscale-portal.md)
- Hozza létre első alkalmazását Python, PHP, Ruby, C, a\#, Java, node.js nyelven: [Csatlakozási kódtárak](./concepts-connection-libraries.md)
