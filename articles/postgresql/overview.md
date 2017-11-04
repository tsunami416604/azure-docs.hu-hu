---
title: "PostgreSQL relációs adatbázis-szolgáltatás az Azure-adatbázis – áttekintés |} Microsoft Docs"
description: "Azure-adatbázis áttekintést nyújt a PostgreSQL relációs adatbázis-szolgáltatás."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 10/20/2017
ms.openlocfilehash: 5b5da758e966cc5ca536d7b291be74409f02ca73
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>Mi az Azure-adatbázis a PostgreSQL?

Azure-adatbázis PostgreSQL egy nyílt forráskódú közösségi verziója alapján fejlesztők számára a Microsoft felhőalapú relációs adatbázis-szolgáltatás [PostgreSQL](https://www.postgresql.org/) adatbázismotor. Ez a szolgáltatás nyilvános előzetes verziójában van. Azure-adatbázis PostgreSQL biztosítja:

- Magas rendelkezésre állást további költség nélkül.
- Kiszámítható teljesítmény, a két szélsőértéket beleértve használatalapú árképzési használatával.
- Méretezhető parancsprogramok másodpercen belül.
- Védeni bizalmas adatokat nyugalmi és a mozgási.
- Automatikus biztonsági mentés és a pont-a--visszaállítás egy korábbi időpontra legfeljebb 35 napon.
- Vállalati szintű biztonsági és megfelelőségi funkcióiról.

Ezekre a képességekre igényel szinte semmilyen felügyeleti, és minden további költségek nélkül kínál. Ezeket a képességeket engedélyezi, hogy a Gyors alkalmazásfejlesztés és forgalomba hozásának felgyorsítására piaci, hanem értékes időt és erőforrásokat virtuális gépek és infrastruktúra kezelése összpontosítanak. Ezenkívül továbbra is az alkalmazás nyílt forráskódú eszközöket, az Ön által választott platform fejlesztéséhez és a sebességgel és anélkül, hogy az új képességek további követelményeket az üzleti hatékonyságot biztosítanak. 

Ez a cikk PostgreSQL alapfogalmait és a teljesítmény, méretezhetőség és kezelhetőség kapcsolódó szolgáltatások az Azure-adatbázis bemutatása. Ezek az első lépésekhez quickstarts lásd:

- [Hozzon létre egy Azure-adatbázis PostgreSQL Azure-portál használatával](quickstart-create-server-database-portal.md)
- [Hozzon létre egy Azure-adatbázis PostgreSQL az Azure parancssori felület használatával](quickstart-create-server-database-azure-cli.md)

Az Azure parancssori felület mintákat mutat be lásd:

- [Azure CLI-minták PostgreSQL Azure-adatbázis](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodpercen belül
A minta a MySQL-szolgáltatás az Azure-adatbázishoz nyújt két szolgáltatásszintek: Basic és Standard. Minden más-más teljesítménybeli és támogatni, nehéz adatbázis munkaterhelések képességeket kínál. Az első alkalmazás kisméretű adatbázis építése néhány dollár havonta, és adja meg az igényeknek a megoldás a skála. Dinamikus méretezhetőség lehetővé teszi, hogy az adatbázis transzparens módon reagál a sűrűn módosuló az erőforrás-követelményei. Csak kell fizetnie az erőforrások akkor szükséges, és csak akkor, ha szüksége van rájuk. Lásd: [Tarifacsomagok](concepts-service-tiers.md) részleteiről.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan Ön dönt, hogy mikor felfelé vagy lefelé skáláznia? A beépített teljesítmény figyelési és riasztási szolgáltatásokat, a teljesítmény-minősítések számítási egység alapján együtt használja. Ezek az eszközök gyorsan felmérheti a számítási egység vertikális felskálázásával hatását vagy le a jelenlegi vagy tervezett teljesítmény igények alapján. Lásd: [riasztások](howto-alert-on-metric.md) részleteiről.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Azure iparágvezető 99,99 % (nem érhető el az előzetes verzió) rendelkezésre állási szolgáltatásiszint-szerződés (SLA), a Microsoft által kezelt adatbázisok globális hálózata technológiával segít a 24 és Windows 7 rendszerben futó alkalmazást. Minden Azure adatbázissal PostgreSQL-kiszolgáló használatakor élvezheti a beépített biztonsági hibatűrést és az adatvédelem, amelyeket egyébként külön megvásárlása vagy kialakításával, létrehozása és kezelése. Az Azure Database a PostgreSQL minden kínál széles választékát üzleti folytonosságot biztosító szolgáltatásokat és létrehozásához használt beállítások helyezhet és futtathat ily módon. Az [időponthoz kötött visszaállítás](howto-restore-server-portal.md) segítségével az adatbázist visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba. Emellett az adatbázisokat üzemeltető adatközpontban során kimaradás lép fel, ha visszaállítható adatbázisok a legutóbbi biztonsági mentés georedundáns példányát.

## <a name="secure-your-data"></a>Az adatok védelme
Azure-adatbázis szolgáltatások rendelkeznek hagyományokkal adatok biztonságáról, hogy PostgreSQL az Azure-adatbázis garantálják a szolgáltatásokkal, amelyek korlátozzák a hozzáférést, adatokat nyugalmi és a mozgási védheti meg és tevékenységek figyeléséhez nyújt segítséget. Látogasson el a [Azure biztonsági és adatkezelési központ](https://www.microsoft.com/TrustCenter/Security/default.aspx) Azure platform biztonsággal kapcsolatos információk.

Az Azure-adatbázishoz PostgreSQL szolgáltatás adatokat nyugalmi tárolási titkosítást használ. Adatokat, többek között a biztonsági másolatokat a lemezen (kivéve a lekérdezések futtatása során a motor által létrehozott ideiglenes fájlokat) vannak titkosítva. A szolgáltatás, amely megtalálható az Azure storage encryption AES 256 bites titkosítás használ, és a kulcsok akkor a rendszer kezeli. Tárolás titkosítása mindig be van kapcsolva, és nem tiltható le.

Alapértelmezés szerint az Azure-adatbázishoz PostgreSQL-szolgáltatás úgy van beállítva [SSL-kapcsolat biztonsági](./concepts-ssl-connection-security.md) az adatokat a-mozgásérzékelési a hálózaton keresztül. Az adatbázis-kiszolgáló és az ügyfél alkalmazások közötti SSL-kapcsolatok kényszerítése segít lánctámadások elleni védelem érdekében "man a középső" az adatfolyamot a kiszolgáló és az alkalmazás közötti titkosításával.  Másik lehetőségként letilthatja az adatbázis-szolgáltatás szeretne csatlakozni, ha az ügyfélalkalmazást nem támogatja az SSL-kapcsolatot az SSL megkövetelése.

## <a name="next-steps"></a>Következő lépések
- Tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/postgresql/) a költségeinek összehasonlításáért és árkalkulációjáért.
- Első lépések [az első Azure-adatbázis létrehozása a PostgreSQL](./quickstart-create-server-database-portal.md).
- Az első alkalmazás Python, PHP, Ruby, C elkészítésére\#, Java, Node.js: [adatkapcsolattárak](./concepts-connection-libraries.md)
