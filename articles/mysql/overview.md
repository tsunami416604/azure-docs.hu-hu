---
title: A MySQL adatbázis-szolgáltatás Azure-adatbázis – áttekintés
description: Az Azure-adatbázishoz a MySQL adatbázis-szolgáltatás áttekintése.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.custom: mvc
ms.openlocfilehash: 3b7ae08446e672c218e7278cff4f77739286fead
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az Azure-adatbázis a MySQL?
Azure MySQL-adatbázis a Microsoft felhőalapú relációs adatbázis-szolgáltatás a [MySQL Community Edition](https://www.mysql.com/products/community/) adatbázismotor. Azure MySQL-adatbázis biztosítja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Skálázás menet közben, másodperceken belül.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezek a képességek igényel szinte semmilyen felügyeleti és összes biztosított további költségek nélkül. Ezek lehetővé teszik, hogy az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására piaci hanem értékes időt és erőforrásokat virtuális gépek és infrastruktúra kezelése koncentrálhat. Ezenkívül folytathatja az alkalmazás nyílt forráskódú eszközöket, az Ön által választott képes biztosítani a sebesség és a hatékonyság az üzleti igények, nem kell új ismeretek nélkül platform fejlesztéséhez.

![Azure-adatbázis a MySQL fogalmi diagramja](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ez a cikk MySQL alapfogalmait és a teljesítmény, méretezhetőség és kezelhetőség, további részletes ismertetőkre mutató hivatkozásokkal kapcsolódó szolgáltatások az Azure-adatbázis bemutatása. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)

Több Azure CLI-mintát talál itt:
- [Azure CLI-minták MySQL az Azure-adatbázis](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
Az Azure-adatbázishoz a MySQL-szolgáltatás többféle szolgáltatáscsomagban kapható kínál: alapvető, általános célú és Memóriaoptimalizált. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [tarifacsomagok](concepts-service-tiers.md) leírása tartalmazza.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? A beépített teljesítmény figyelési és riasztási szolgáltatásokat, a teljesítmény értékelések alapján vCores együtt használja. Ezen eszközök segítségével gyorsan felmérheti a méretezési vCores hatását felfelé vagy lefelé a jelenlegi vagy tervezett teljesítmény igények alapján. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Azure iparágvezető 99,99 % rendelkezésre állási szolgáltatásiszint-szerződés (SLA), a Microsoft által kezelt adatbázisok globális hálózata technológiával segít a 24 és Windows 7 rendszerben futó alkalmazást. Minden Azure adatbázissal MySQL-kiszolgáló használatakor élvezheti a beépített biztonsági hibatűrést és az adatvédelem, amelyeket egyébként külön megvásárlása vagy kialakításával, létrehozása és kezelése. MySQL az Azure-adatbázissal pont időponthoz kötött visszaállítás segítségével kötött 35 nappal korábbi állapotba való kiszolgáló helyreállítása.

## <a name="secure-your-data"></a>Az adatok védelme
Azure-adatbázis szolgáltatások, amelyek a MySQL az Azure-adatbázis garantálják adatbiztonság hagyományokkal rendelkezik, a szolgáltatásokkal, amelyek korlátozzák a hozzáférést, adatokat nyugalmi és a mozgási védheti meg és segítséget figyelése. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/en-us/trustcenter/security) talál információkat.

Az Azure-adatbázishoz a MySQL-szolgáltatás adatokat nyugalmi tárolási titkosítást használ. Adatok biztonsági mentése, beleértve a lemezen (kivéve a lekérdezések futtatása során a motor által létrehozott ideiglenes fájlok) vannak titkosítva. A szolgáltatás AES 256 bites titkosítást használ, amely az Azure tárolási titkosításban szerepel, és a kulcsokat a rendszer felügyeli. A tárolási titkosítás mindig be van kapcsolva és nem tiltható le.

Alapértelmezés szerint az Azure-adatbázishoz a MySQL-szolgáltatás úgy van beállítva [SSL-kapcsolat biztonsági](./concepts-ssl-connection-security.md) az adatokat a-mozgásérzékelési a hálózaton keresztül. Az adatbázis-kiszolgáló és az ügyfél alkalmazások közötti SSL-kapcsolatok érvényesítése segít annak az adatfolyamot a kiszolgáló és az alkalmazás közötti titkosításával "man a középső" lánctámadások elleni védelem érdekében.  Választhatja az SSL megkövetelésének letiltását az adatbázis-szolgáltatáshoz való kapcsolódáshoz, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatokat.

## <a name="next-steps"></a>További lépések
Most, olvassa el az Azure-adatbázis bemutatása a MySQL, és "Mi van Azure adatbázis a MySQL?" választ, akkor készen áll:
- Tekintse meg a költségeinek összehasonlításáért és árkalkulációjáért árképzést ismertető oldalra. [Díjszabás](https://azure.microsoft.com/pricing/details/mysql/)
- Ismerkedés az első kiszolgáló létrehozásával. [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- Az első alkalmazás a választott nyelv használatával elkészítésére: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [nyissa meg](./connect-go.md)
