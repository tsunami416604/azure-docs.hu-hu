---
title: "A MySQL adatbázis-szolgáltatás Azure-adatbázis – áttekintés |} Microsoft Docs"
description: "Az Azure-adatbázishoz a MySQL adatbázis-szolgáltatás áttekintése."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/20/2017
ms.custom: mvc
ms.openlocfilehash: 2a9efdd9285dfa5fca450ede64e5f6ee54cbc72b
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az Azure-adatbázis a MySQL?
Azure MySQL-adatbázis a Microsoft felhőalapú relációs adatbázis-szolgáltatás a [MySQL Community Edition](https://www.mysql.com/products/community/) adatbázismotor. Ez a szolgáltatás nyilvános előzetes verziójában van. Azure MySQL-adatbázis biztosítja:

- Magas rendelkezésre állást további költség nélkül.
- Kiszámítható teljesítmény, a két szélsőértéket beleértve használatalapú árképzési használatával.
- Méretezhető parancsprogramok másodpercen belül.
- Védeni bizalmas adatokat nyugalmi és a mozgási.
- Automatikus biztonsági mentés és a pont-a--visszaállítás egy korábbi időpontra legfeljebb 35 napon.
- Vállalati szintű biztonsági és megfelelőségi funkcióiról.

Ezek a képességek igényel szinte semmilyen felügyeleti és összes biztosított további költségek nélkül. Ezek lehetővé teszik, hogy az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására piaci hanem értékes időt és erőforrásokat virtuális gépek és infrastruktúra kezelése koncentrálhat. Ezenkívül folytathatja az alkalmazás nyílt forráskódú eszközöket, az Ön által választott képes biztosítani a sebesség és a hatékonyság az üzleti igények, nem kell új ismeretek nélkül platform fejlesztéséhez.

![Azure-adatbázis a MySQL fogalmi diagramja](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ez a cikk MySQL alapfogalmait és a teljesítmény, méretezhetőség és kezelhetőség, további részletes ismertetőkre mutató hivatkozásokkal kapcsolódó szolgáltatások az Azure-adatbázis bemutatása. Ezek az első lépésekhez quickstarts lásd:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)

Az Azure parancssori felület mintákat mutat be lásd:
- [Azure CLI-minták MySQL az Azure-adatbázis](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodpercen belül
A minta a MySQL-szolgáltatás az Azure-adatbázishoz nyújt két szolgáltatásszintek: Basic és Standard. Minden más-más teljesítménybeli és támogatni, nehéz adatbázis munkaterhelések képességeket kínál. Az első alkalmazás kisméretű adatbázis építése néhány dollár havonta, és adja meg az igényeknek a megoldás a skála. Dinamikus méretezhetőség lehetővé teszi, hogy az adatbázis transzparens módon reagál a sűrűn módosuló az erőforrás-követelményei. Csak kell fizetnie az erőforrások akkor szükséges, és csak akkor, ha szüksége van rájuk. Lásd: [Tarifacsomagok](concepts-service-tiers.md) részleteiről.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan Ön dönt, hogy mikor felfelé vagy lefelé skáláznia? A beépített teljesítmény figyelési és riasztási szolgáltatásokat, a teljesítmény-minősítések számítási egység alapján együtt használja. Ezek az eszközök gyorsan felmérheti a számítási egység vertikális felskálázásával hatását vagy le a jelenlegi vagy tervezett teljesítmény igények alapján. Lásd: [riasztások](howto-alert-on-metric.md) részleteiről.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Azure iparágvezető 99,99 % rendelkezésre állási szolgáltatásiszint-szerződés (SLA), a Microsoft által kezelt adatbázisok globális hálózata technológiával segít a 24 és Windows 7 rendszerben futó alkalmazást. Minden Azure adatbázissal MySQL-kiszolgáló használatakor élvezheti a beépített biztonsági hibatűrést és az adatvédelem, amelyeket egyébként külön megvásárlása vagy kialakításával, létrehozása és kezelése. MySQL az Azure-adatbázissal pont időponthoz kötött visszaállítás segítségével kötött 35 nappal korábbi állapotba való kiszolgáló helyreállítása.

## <a name="secure-your-data"></a>Az adatok védelme
Azure-adatbázis szolgáltatások, amelyek a MySQL az Azure-adatbázis garantálják adatbiztonság hagyományokkal rendelkezik, a szolgáltatásokkal, amelyek korlátozzák a hozzáférést, adatokat nyugalmi és a mozgási védheti meg és segítséget figyelése. Látogasson el a [Azure biztonsági és adatkezelési központ](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) Azure platform biztonsággal kapcsolatos információk.

Az Azure-adatbázishoz a MySQL-szolgáltatás adatokat nyugalmi tárolási titkosítást használ. Adatok biztonsági mentése, beleértve a lemezen (kivéve a lekérdezések futtatása során a motor által létrehozott ideiglenes fájlok) vannak titkosítva. A szolgáltatás, amely megtalálható az Azure storage encryption AES 256 bites titkosítás használ, és a kulcsok akkor a rendszer kezeli. Tárolás titkosítása mindig be van kapcsolva, és nem tiltható le.

Alapértelmezés szerint az Azure-adatbázishoz a MySQL-szolgáltatás úgy van beállítva [SSL-kapcsolat biztonsági](./concepts-ssl-connection-security.md) az adatokat a-mozgásérzékelési a hálózaton keresztül. Az adatbázis-kiszolgáló és az ügyfél alkalmazások közötti SSL-kapcsolatok érvényesítése segít annak az adatfolyamot a kiszolgáló és az alkalmazás közötti titkosításával "man a középső" lánctámadások elleni védelem érdekében.  Másik lehetőségként letilthatja az adatbázis-szolgáltatás szeretne csatlakozni, ha az ügyfélalkalmazást nem támogatja az SSL-kapcsolatot az SSL megkövetelése.

## <a name="next-steps"></a>Következő lépések
Most, olvassa el az Azure-adatbázis bemutatása a MySQL, és "Mi van Azure adatbázis a MySQL?" választ, akkor készen áll:
- Tekintse meg a költségeinek összehasonlításáért és árkalkulációjáért árképzést ismertető oldalra. [Díjszabás](https://azure.microsoft.com/pricing/details/mysql/)
- Ismerkedés az első kiszolgáló létrehozásával. [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- Az első alkalmazás a választott nyelv használatával elkészítésére: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [nyissa meg](./connect-go.md)
