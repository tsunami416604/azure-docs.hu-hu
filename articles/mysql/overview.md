---
title: Overview of Azure Database for MySQL relational database service
description: Learn about the Azure Database for MySQL service, a relational database service in the Microsoft cloud based on the MySQL Community Edition.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 680b5e9ef8e7e8ed59d3b502b49fc1b45d016e80
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483518"
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az Azure Database for MySQL?

Azure Database for MySQL is a relational database service in the Microsoft cloud based on the [MySQL Community Edition](https://www.mysql.com/products/community/) (available under the GPLv2 license) database engine, versions 5.6, 5.7, and 8.0. Azure Database for MySQL delivers:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Méretezés igény szerint másodpercek alatt.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Segítségükkel az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

![Azure Database for MySQL conceptual diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

This article is an introduction to Azure Database for MySQL core concepts and features related to performance, scalability, and manageability, with links to explore details. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)

Több Azure CLI-mintát talál itt:

- [Azure CLI samples for Azure Database for MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
The Azure Database for MySQL service offers several service tiers: Basic, General Purpose, and Memory Optimized. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. See [Pricing tiers](concepts-service-tiers.md) for details.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? Használja a virtuális magokon alapuló, teljesítmény-értékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközöket. Ezek az eszközök lehetővé teszik a virtuális magok aktuális vagy a becsült teljesítményigényeken alapuló fel- vagy leskálázása hatásainak gyors kiértékelését. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) az ágazatban élenjáró módon 99,99 százalékos rendelkezésre állást biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. With every Azure Database for MySQL server, you take advantage of built-in security, fault tolerance, and data protection that you would otherwise have to buy or design, build, and manage. With Azure Database for MySQL, you can use point-in-time restore to recover a server to an earlier state, as far back as 35 days.

## <a name="secure-your-data"></a>Adatbiztonság
Azure database services have a tradition of data security that Azure Database for MySQL upholds, with features that limit access, protect data at-rest and in-motion, and help you monitor activity. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter/security) talál információkat. For more information about Azure Database for MySQL security features, see the [security overview](concepts-security.md).

## <a name="contacts"></a>Kapcsolatok
For any questions or suggestions you might have about working with Azure Database for MySQL, send an email to the Azure Database for MySQL Team ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). This email address is not a technical support alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Következő lépések
Now that you've read an introduction to Azure Database for MySQL and answered the question "What is Azure Database for MySQL?" you're ready to:

- Tekintse meg az árképzést ismertető oldalt a költségek összehasonlításáért és árkalkulációjáért. [Díjszabás](https://azure.microsoft.com/pricing/details/mysql/)
- Első lépésként hozza létre első kiszolgálóját. [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
