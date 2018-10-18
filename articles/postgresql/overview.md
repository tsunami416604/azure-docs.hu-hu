---
title: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintése
description: Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintését nyújtja.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 06/15/2018
ms.openlocfilehash: 86a8b824479173024b1a14bb8612073ffbfac468
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092157"
---
# <a name="what-is-azure-database-for-postgresql"></a>Mi az Azure Database for PostgreSQL?

Az Azure Database for PostgreSQL egy fejlesztőknek készült relációsadatbázis-szolgáltatás a Microsoft-felhőben, amely a nyílt forráskódú [PostgreSQL](https://www.postgresql.org/) adatbázismotor közösségi verzióján alapul. Az Azure Database for PostgreSQL a következőket nyújtja:

- Beépített magas rendelkezésre állás további költség nélkül
- Kiszámítható teljesítmény, használatalapú díjszabással
- Méretezés igény szerint másodpercek alatt
- Az inaktív és átvitel alatt álló bizalmas adatok védelme
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig
- Vállalati szintű biztonság és megfelelőség

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Ezen képességek birtokában az alkalmazások gyors fejlesztésére és piacra juttatására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezenkívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül. 

Jelen cikkünk bemutatja az Azure Database for PostgreSQL teljesítménnyel, skálázhatósággal és kezelhetőséggel kapcsolatos alapfogalmait és jellemzőit. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

- [Azure Database for PostgreSQL létrehozása az Azure Portal használatával](quickstart-create-server-database-portal.md)
- [Azure Database for PostgreSQL létrehozása az Azure CLI használatával](quickstart-create-server-database-azure-cli.md)

Több Azure CLI-mintát talál itt:

- [Azure CLI-minták az Azure Database for PostgreSQL szolgáltatáshoz](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
Az Azure Database for PostgreSQL szolgáltatás három tarifacsomagot kínál: Alapszintű, Általános célú és Memóriahasználatra optimalizált. Az egyes szintek különböző erőforrásokat és képességeket kínálnak az adatbázisok számítási feladatainak támogatásához. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [tarifacsomagok](concepts-pricing-tiers.md) leírása tartalmazza.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- vagy leskálázásra? Használja az Azure beépített monitorozó és riasztási eszközeit. Ezek az eszközök lehetővé teszik az aktuális vagy becsült teljesítményigényeken vagy tárolási szükségleteken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) az ágazatban élenjáró módon 99,99 százalékos rendelkezésre állást biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az összes Azure Database for PostgreSQL kiszolgáló használatakor élvezheti a beépített biztonság, hibatűrés és adatvédelem előnyeit, amelyeket egyébként külön kellene megvásárolnia vagy megterveznie, kiépítenie és kezelnie. Az Azure Database for PostgreSQL minden egyes tarifacsomagja az üzletmenet folytonosságát biztosító szolgáltatások és lehetőségek átfogó készletét kínálja, amelyeket tetszés szerint futtathat és helyezhet üzembe. Az [időponthoz kötött visszaállítás](howto-restore-server-portal.md) segítségével az adatbázist visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba. Továbbá, ha az adatbázisokat üzemeltető adatközpontban szolgáltatáskimaradás következik be, visszaállíthatja az adatbázisokat a legújabb biztonsági mentések georedundáns példányaiból.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure adatbázis-szolgáltatások megbízható adatbiztonságát az Azure Database for PostgreSQL olyan funkciói garantálják, amelyek korlátozzák a hozzáférést, védik a helyi és mozgó adatokat és hozzájárulnak a tevékenységek monitorozásához. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/en-us/trustcenter/security) talál információkat.

Az Azure Database for PostgreSQL szolgáltatás tárolási titkosítást használ a helyi adatokhoz. Az adatok a biztonsági másolatokkal együtt titkosítva kerülnek a lemezekre (a motor által lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével). A szolgáltatás AES 256 bites titkosítást használ, amely az Azure tárolási titkosításban szerepel, és a kulcsokat a rendszer felügyeli. A tárolási titkosítás mindig be van kapcsolva és nem tiltható le.

Alapértelmezés szerint az Azure Database for PostgreSQL szolgáltatás úgy van konfigurálva, hogy [SSL-kapcsolatbiztonságot](./concepts-ssl-connection-security.md) igényeljen a hálózatok között mozgó adatokhoz. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Választhatja az SSL megkövetelésének letiltását az adatbázis-szolgáltatáshoz való kapcsolódáshoz, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatokat.

## <a name="contacts"></a>Kapcsolatok
Ha kérdései vagy javaslatai vannak az Azure Database for PostgreSQL használatával kapcsolatban, írjon egy e-mailt az Azure Database for PostgreSQL csapatának ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ne feledje, hogy ez nem műszaki támogatás.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:
- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>További lépések
- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/postgresql/) a költségek összehasonlításáért és árkalkulációjáért.
- Első lépésként [hozza létre első Azure Database for PostgreSQL adatbázist](./quickstart-create-server-database-portal.md).
- Építse fel az első alkalmazását Python, PHP, Ruby, C\#, Java, Node.js nyelven: [Csatlakozási kódtárak](./concepts-connection-libraries.md)
