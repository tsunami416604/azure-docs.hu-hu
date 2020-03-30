---
title: Áttekintés - Azure Database for MySQL
description: Ismerje meg az Azure Database for MySQL szolgáltatást, amely a MySQL Community Edition szolgáltatáson alapuló, a Microsoft felhőjében található relációs adatbázis-szolgáltatás.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 8f49811ad0d40c70933d32227cfb17a5144b857a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067825"
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az Azure Database for MySQL?

Az Azure Database for MySQL egy relációs adatbázis-szolgáltatás a Microsoft felhőben, amely a [MySQL Community Edition](https://www.mysql.com/products/community/) (gplv2 licenc alatt érhető el) adatbázismotoron, az 5.6-os, az 5.7-es és a 8.0-s verziókon alapul. Az Azure Database for MySQL a következőket nyújtja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Méretezés igény szerint másodpercek alatt.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Segítségükkel az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

![Azure Database for MySQL fogalmi diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ez a cikk az Azure Database for MySQL alapfogalmait és a teljesítményre, méretezhetőségre és kezelhetőségre vonatkozó funkcióit ismerteti, a részletek feltárására szolgáló hivatkozásokkal. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)

Több Azure CLI-mintát talál itt:

- [Azure CLI-példák az Azure Database for MySQL-hez](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
Az Azure Database for MySQL szolgáltatás számos szolgáltatási szintet kínál: alapszintű, általános célú és memóriaoptimalizált. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [Tarifacsomagok](concepts-service-tiers.md) ban találja.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? Használja a virtuális magokon alapuló, teljesítmény-értékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközöket. Ezek az eszközök lehetővé teszik a virtuális magok aktuális vagy a becsült teljesítményigényeken alapuló fel- vagy leskálázása hatásainak gyors kiértékelését. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) az ágazatban élenjáró módon 99,99 százalékos rendelkezésre állást biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. A MySQL-kiszolgálókhoz készült minden Azure Database használatával kihasználhatja a beépített biztonságot, a hibatűrést és az adatvédelmet, amelyeket egyébként meg kellene vásárolnia vagy meg kellene terveznie, megkell építenie és kezelnie kellene. Az Azure Database for MySQL segítségével a kiszolgáló korábbi állapotának helyreállításához használhatja az időponthoz való tapont-visszaállítást, akár 35 napig is vissza.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure adatbázis-szolgáltatásai nak hagyománya van az adatbiztonság, amelyet az Azure Database for MySQL védelme biztosít, olyan funkciókkal, amelyek korlátozzák a hozzáférést, védik az inaktív és mozgásban lévő adatokat, és segítenek a tevékenységek figyelésében. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter/security) talál információkat. Az Azure Database for MySQL biztonsági funkcióiról a biztonsági áttekintésben olvashat [bővebben.](concepts-security.md)

## <a name="contacts"></a>Kapcsolatok
Ha bármilyen kérdése vagy javaslata merülhet fel az Azure Database for MySQL használatával kapcsolatban, küldjön egy e-mailt az Azure Database for MySQL Team[ @Ask (Azure DB for MySQL)](mailto:AskAzureDBforMySQL@service.microsoft.com)számára. Ez az e-mail cím nem technikai támogatási alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>További lépések
Most, hogy elolvasta az Azure Database for MySQL bevezetőjét, és megválaszolta a "Mi az Azure Database for MySQL?" kérdésre. készen áll a következőkre:

- Tekintse meg az árképzést ismertető oldalt a költségek összehasonlításáért és árkalkulációjáért. [Díjszabás](https://azure.microsoft.com/pricing/details/mysql/)
- Első lépésként hozza létre első kiszolgálóját. [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- Készítsd el az első alkalmazást a kívánt nyelv használatával: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
