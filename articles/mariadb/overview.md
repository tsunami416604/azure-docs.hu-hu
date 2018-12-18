---
title: Az Azure Database for MariaDB relációsadatbázis-szolgáltatás áttekintése
description: Az Azure Database for MariaDB relációsadatbázis-szolgáltatás áttekintése.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: 507425a6cffc6f3b49e117ebf5a604bf0d252164
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542016"
---
# <a name="what-is-azure-database-for-mariadb"></a>Mi az Azure Database for MariaDB?

Az Azure Database for MariaDB egy fejlesztőknek készült relációsadatbázis-szolgáltatás a Microsoft Cloudban. Azure Database for MariaDB alapul a [MariaDB community Edition kiadását](https://mariadb.org/download/) database engine, a verzió 10.2.

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

Az Azure Database for MariaDB szolgáltatás számos különböző szolgáltatásszinttel rendelkezik: Alapszintű, általános célú és memóriahasználatra optimalizált. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőségnek köszönhetően az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor valóban szüksége van rájuk. Lásd: [Tarifacsomagok](concepts-pricing-tiers.md) részleteiről.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

Hogyan lehet megállapítani, hogy mikor van szükség vertikális fel- vagy leskálázásra? Használja az Azure Database for MariaDB virtuális magokon alapuló, teljesítményértékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközeit. Ezekkel az eszközökkel gyorsan kiértékelheti, milyen hatásai vannak a virtuális magok aktuális vagy becsült teljesítményigényeken alapuló vertikális fel- vagy leskálázásának. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését

Az Azure-szolgáltatás piacvezető 99,99 %-os rendelkezésre állási SLA-t a Microsoft által felügyelt adatközpontok globális hálózata működteti. Ez a hálózat segíti elő, hogy alkalmazása a hét minden napján, napi 24 órában fusson. Az Azure Database for MariaDB által biztosított beépített biztonság, hibatűrés és adatvédelem előnyeit is kihasználhatja. Az Azure Database for MariaDB-vel az időponthoz kötött visszaállítás segítségével a kiszolgálót visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba.

## <a name="secure-your-data"></a>Az adatok védelme

Az Azure-beli adatbázis-szolgáltatásokra jellemző adatvédelmet az Azure Database for MariaDB is biztosítja. Az Azure Database for MariaDB funkciói korlátozzák a hozzáférést, védik az inaktív és a mozgásban lévő adatokat, és segítenek a tevékenységek monitorozásában. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/en-us/trustcenter/security) talál információkat.

Az Azure Database for MariaDB tárolótitkosítást használ az inaktív adatokhoz. Az adatok, beleértve a biztonsági másolatokat is, titkosítva kerülnek a lemezre. (A motor általi lekérdezések futtatásakor létrejövő ideiglenes fájlok nem lesznek titkosítva a lemezen.) A szolgáltatás AES 256 bites titkosítást használ, amely része az Azure Storage-titkosításnak. A kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.

Alapértelmezés szerint az Azure Database for MariaDB úgy van konfigurálva, hogy [biztonságos SSL-kapcsolatot](./concepts-ssl-connection-security.md) követeljen meg a hálózatok között mozgó adatokhoz. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Megteheti, hogy eltekint az SSL megkövetelésétől, amikor az adatbázis-szolgáltatáshoz kapcsolódik, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatokat.

## <a name="contacts"></a>Kapcsolatok

Az Azure Database for MariaDB használatával kapcsolatos kérdéseit és javaslatait elküldheti egy e-mailben az [Azure Database for MariaDB csapatának](mailto:AskAzureDBforMariaDB@service.microsoft.com) (ez nem egyenlő a műszaki támogatással).

A következő elérhetőségeket is használhatja:
- Ha az Azure ügyfélszolgálatával szeretne kapcsolatba lépni, [nyújtson be támogatási kérelmet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az Azure Portalon.
- Ha a fiókjával van probléma, akkor is [nyújtson be támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új funkciókra szeretne javaslatot tenni, hozzon létre egy bejegyzést az [Azure visszajelzési fórumain](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>További lépések

Most, hogy elolvasta az Azure Database for MariaDB bevezetését, készen áll a következőkre:
- Megtekintheti az [árképzést](https://azure.microsoft.com/pricing/details/mariadb/) ismertető oldalt, ahol összehasonlíthatja a költségeket, és árkalkulációt végezhet. 
- Első lépésként [létrehozhatja az első kiszolgálóját](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
