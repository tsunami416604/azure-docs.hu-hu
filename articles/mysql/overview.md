---
title: Azure Database MySQL relációsadatbázis-szolgáltatás áttekintése
description: Az Azure Database for MySQL relációsadatbázis-szolgáltatás áttekintése.
ms.service: mysql
author: ajlam
ms.author: andrela
ms.custom: mvc
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2852cab05fab8e15b7e60a22f54cc866d2f0f178
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295402"
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az Azure Database for MySQL-hez?

Azure Database for MySQL egy relációsadatbázis-szolgáltatás a Microsoft cloud alapján a [MySQL Community Edition](https://www.mysql.com/products/community/) (a GPLv2 licenc érhető el) adatbázismotor, 5.6-os és 5.7-es verziója. Azure Database for MySQL a következőket nyújtja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Méretezés igény szerint másodpercek alatt.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Segítségükkel az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

![Azure Database for MySQL fogalmi diagramja](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ez a cikk az Azure Database for MySQL alapfogalmait és a teljesítmény, méretezhetőség és kezelhetőség, amely további részletes ismertetőkre mutató hivatkozásokat tartalmaz a kapcsolódó szolgáltatások bemutatása. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)

Több Azure CLI-mintát talál itt:

- [Az Azure CLI-minták az Azure Database for MySQL-hez](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
Az Azure Database for MySQL-szolgáltatás számos különböző szolgáltatásszinttel rendelkezik: Alapszintű, általános célú és memóriahasználatra optimalizált. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. Lásd: [Tarifacsomagok](concepts-service-tiers.md) részleteiről.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? Használja a virtuális magokon alapuló, teljesítmény-értékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközöket. Ezek az eszközök lehetővé teszik a virtuális magok aktuális vagy a becsült teljesítményigényeken alapuló fel- vagy leskálázása hatásainak gyors kiértékelését. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) az ágazatban élenjáró módon 99,99 százalékos rendelkezésre állást biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az összes Azure Database for MySQL-kiszolgáló előnyeit beépített biztonság, hibatűrés és amelyeket egyébként külön történő vásárlás vagy tervezését, létrehozását és kezelése. Az Azure Database for MySQL időponthoz visszaállítási használhatja, ha egy kiszolgálót egy legfeljebb 35 nappal korábbi állapotba való helyreállításához.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure adatbázis-szolgáltatások megbízható adatbiztonságát az adatok biztonságát, amelyek garantálják, Azure Database for MySQL, funkciókkal, amelyek korlátozzák a hozzáférést, inaktív adatok és a mozgásban lévő védheti meg és segítséget nyújtanak a tevékenység figyelése. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/en-us/trustcenter/security) talál információkat.

Az Azure Database for MySQL-szolgáltatás tárolási titkosítást használ az inaktív adatok, és megfelel a FIPS 140-2 szabványnak. Az adatok a biztonsági másolatokkal együtt titkosítva kerülnek a lemezekre (a motor által lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével). A szolgáltatás AES 256 bites titkosítást használ, amely az Azure tárolási titkosításban szerepel, és a kulcsokat a rendszer felügyeli. A tárolási titkosítás mindig be van kapcsolva és nem tiltható le.

Alapértelmezés szerint az Azure Database for MySQL-szolgáltatás van konfigurálva, hogy kötelező legyen [SSL-kapcsolat biztonsága](./concepts-ssl-connection-security.md) az adatokat a mozgásban lévő a hálózaton keresztül. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Választhatja az SSL megkövetelésének letiltását az adatbázis-szolgáltatáshoz való kapcsolódáshoz, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatokat.

## <a name="contacts"></a>Kapcsolatok
Esetleges kérdéseivel és lehetséges, hogy működik az Azure Database for MySQL, kapcsolatos javaslatok e-mail küldése az Azure database for MySQL csapata számára ([ @Ask MySQL-hez készült Azure DB](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ne feledje, hogy ez nem műszaki támogatás.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>További lépések
Most, hogy bemutatása az Azure Database for MySQL-hez, és a "Mi van az Azure Database for MySQL?" kérdésre válaszolni, készen áll:

- Tekintse meg az árképzést ismertető oldalt a költségek összehasonlításáért és árkalkulációjáért. [Díjszabás](https://azure.microsoft.com/pricing/details/mysql/)
- Első lépésként hozza létre első kiszolgálóját. [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- Hozza létre első alkalmazását az előnyben részesített nyelvek használatával: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [go](./connect-go.md)
