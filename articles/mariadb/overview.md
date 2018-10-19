---
title: Az Azure Database for MariaDB relációsadatbázis-szolgáltatás áttekintése
description: Az Azure Database for MariaDB relációsadatbázis-szolgáltatás áttekintése.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 3649a173d5707179ca8547a8169b7d308c4f7f1c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249162"
---
# <a name="what-is-azure-database-for-mariadb"></a>Mi az Azure Database for MariaDB?
Az Azure Database for MariaDB egy relációsadatbázis-szolgáltatás a Microsoft Cloudban, amely a [MariaDB Community Edition](https://mariadb.org/download/) adatbázismotoron alapul. Ez a szolgáltatás nyilvános előzetes verziója. Az Azure Database for MariaDB szolgáltatás a következőket nyújtja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Méretezés igény szerint másodpercek alatt.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Segítségükkel az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

Jelen cikkünk bemutatja az Azure Database for MariaDB teljesítménnyel, skálázhatósággal és kezelhetőséggel kapcsolatos alapfogalmait és jellemzőit, és a további részletes ismertetőkre mutató hivatkozásokat tartalmaz. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:
- [Azure Database for MariaDB-kiszolgáló létrehozása az Azure Portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure Database for MariaDB-kiszolgáló létrehozása az Azure CLI használatával](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
Az előzetes verzióban az Azure Database for MariaDB szolgáltatás számos szolgáltatási szintet kínál: Alapszintű, Általános célú és Memóriahasználatra optimalizált. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [tarifacsomagok](concepts-pricing-tiers.md) leírása tartalmazza.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? Használja a virtuális magokon alapuló, teljesítmény-értékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközöket. Ezek az eszközök lehetővé teszik a virtuális magok aktuális vagy a becsült teljesítményigényeken alapuló fel- vagy leskálázása hatásainak gyors kiértékelését. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) (az előzetes verzióban nem elérhető) az ágazatban élenjáró módon 99,99 százalékos elérhetőséget biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az összes Azure Database for MariaDB kiszolgáló használatakor élvezheti a beépített biztonság, hibatűrés és adatvédelem előnyeit, amelyeket egyébként külön kellene megvásárolnia vagy megterveznie, kiépítenie és kezelnie. Az Azure Database for MariaDB-vel az időponthoz kötött visszaállítás segítségével a kiszolgálót visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure adatbázis-szolgáltatások megbízható adatbiztonságát az Azure Database for MariaDB olyan funkciói garantálják, amelyek korlátozzák a hozzáférést, védik a helyi és mozgó adatokat és hozzájárulnak a tevékenységek monitorozásához. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/en-us/trustcenter/security) talál információkat.

Az Azure Database for MariaDB szolgáltatás tárolási titkosítást használ a helyi adatokhoz. Az adatok a biztonsági másolatokkal együtt titkosítva kerülnek a lemezekre (a motor által lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével). A szolgáltatás AES 256 bites titkosítást használ, amely az Azure tárolási titkosításban szerepel, és a kulcsokat a rendszer felügyeli. A tárolási titkosítás mindig be van kapcsolva és nem tiltható le.

Alapértelmezés szerint az Azure Database for MariaDB szolgáltatás úgy van konfigurálva, hogy [SSL-kapcsolatbiztonságot](./concepts-ssl-connection-security.md) igényeljen a hálózatok között mozgó adatokhoz. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Választhatja az SSL megkövetelésének letiltását az adatbázis-szolgáltatáshoz való kapcsolódáshoz, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatokat.

## <a name="contacts"></a>Kapcsolatok
Ha kérdései vagy javaslatai vannak az Azure Database for MariaDB használatával kapcsolatban, írjon egy e-mailt az Azure Database for MariaDB csapatának ([@Ask Azure DB for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)). Ne feledje, hogy ez nem műszaki támogatás.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:
- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>További lépések
Most, hogy elolvasta az Azure Database for MariaDB-be történő bevezetést, és választ kapott a "Mi az Azure Database for MariaDB?" kérdésre, a következő lépésekkel folytathatja:
- Tekintse meg az árképzést ismertető oldalt a költségek összehasonlításáért és árkalkulációjáért. [Díjszabás](https://azure.microsoft.com/pricing/details/mariadb/)
- Első lépésként hozza létre első kiszolgálóját. [Azure Database for MariaDB-kiszolgáló létrehozása az Azure Portal használatával](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
