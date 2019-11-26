---
title: Az Azure Database for MariaDB relációsadatbázis-szolgáltatás áttekintése
description: Learn about the Azure Database for MariaDB service, a relational database service in the Microsoft cloud based on the MySQL community edition.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: aed2e090a1e3a6a4c80f70ba25da9a903a6192c1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484157"
---
# <a name="what-is-azure-database-for-mariadb"></a>Mi az Azure Database for MariaDB?

Az Azure Database for MariaDB egy fejlesztőknek készült relációsadatbázis-szolgáltatás a Microsoft Cloudban. Azure Database for MariaDB is based on the [MariaDB community edition](https://mariadb.org/download/) (available under the GPLv2 license) database engine, version 10.2 and 10.3.

Az Azure Database for MariaDB szolgáltatás a következőket nyújtja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Méretezés igény szerint, másodpercek alatt.
- Az inaktív és mozgásban lévő bizalmas adatok megerősített védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek használata szinte semmilyen felügyeletet nem igényel, és nem jár további költségekkel. Az Azure Database for MariaDB segítségével gyorsabban fejlesztheti ki és hozhatja forgalomba az alkalmazásait. Nem kell értékes időt és erőforrásokat szánnia a virtuális gépek és az infrastruktúra kezelésére. Az alkalmazások fejlesztését a használni kívánt nyílt forráskódú eszközökkel és platformon folytathatja. A folyamatot az üzlet által megkövetelt sebességen és hatékonysággal hajthatja végre, és új készségeket sem kell elsajátítania.

Ha szeretne megismerkedni az Azure Database for MariaDB alapfogalmaival és jellemzőivel, beleértve a teljesítményt, a méretezhetőséget és a kezelhetőséget, tekintse át a következő rövid útmutatókat:

- [Azure Database for MariaDB-kiszolgáló létrehozása az Azure Portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure Database for MariaDB-kiszolgáló létrehozása az Azure CLI használatával](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül

The Azure Database for MariaDB service offers several service tiers: Basic, General Purpose, and Memory Optimized. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőségnek köszönhetően az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor valóban szüksége van rájuk. See [Pricing tiers](concepts-pricing-tiers.md) for details.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

Hogyan lehet megállapítani, hogy mikor van szükség vertikális fel- vagy leskálázásra? Használja az Azure Database for MariaDB virtuális magokon alapuló, teljesítményértékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközeit. Ezekkel az eszközökkel gyorsan kiértékelheti, milyen hatásai vannak a virtuális magok aktuális vagy becsült teljesítményigényeken alapuló vertikális fel- vagy leskálázásának. A részleteket a [riasztások](howto-alert-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését

Azure's industry-leading 99.99% availability SLA is powered by a global network of Microsoft-managed datacenters. Ez a hálózat segíti elő, hogy alkalmazása a hét minden napján, napi 24 órában fusson. Az Azure Database for MariaDB által biztosított beépített biztonság, hibatűrés és adatvédelem előnyeit is kihasználhatja. Az Azure Database for MariaDB-vel az időponthoz kötött visszaállítás segítségével a kiszolgálót visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba.

## <a name="secure-your-data"></a>Adatbiztonság

Az Azure-beli adatbázis-szolgáltatásokra jellemző adatvédelmet az Azure Database for MariaDB is biztosítja. Az Azure Database for MariaDB funkciói korlátozzák a hozzáférést, védik az inaktív és a mozgásban lévő adatokat, és segítenek a tevékenységek monitorozásában. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter/security) talál információkat. For more information about Azure Database for MySQL security features, see the [security overview](concepts-security.md).

## <a name="contacts"></a>Kapcsolatok

Az Azure Database for MariaDB használatával kapcsolatos kérdéseit és javaslatait elküldheti egy e-mailben az [Azure Database for MariaDB csapatának](mailto:AskAzureDBforMariaDB@service.microsoft.com) (ez nem egyenlő a műszaki támogatással).

A következő elérhetőségeket is használhatja:
- Ha az Azure ügyfélszolgálatával szeretne kapcsolatba lépni, [nyújtson be támogatási kérelmet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az Azure Portalon.
- Ha a fiókjával van probléma, akkor is [nyújtson be támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új funkciókra szeretne javaslatot tenni, hozzon létre egy bejegyzést az [Azure visszajelzési fórumain](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Következő lépések

Most, hogy elolvasta az Azure Database for MariaDB bevezetését, készen áll a következőkre:
- Megtekintheti az [árképzést](https://azure.microsoft.com/pricing/details/mariadb/) ismertető oldalt, ahol összehasonlíthatja a költségeket, és árkalkulációt végezhet. 
- Első lépésként [létrehozhatja az első kiszolgálóját](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
