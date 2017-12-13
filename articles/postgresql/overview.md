---
title: "Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintése | Microsoft Docs"
description: "Az Azure Database for PostgreSQL relációsadatbázis-szolgáltatás áttekintését nyújtja."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 10/20/2017
ms.openlocfilehash: 9aa24dd10ef29c716c05cafeb84e0beb23d50628
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>Mi az Azure Database for PostgreSQL?

Az Azure Database for PostgreSQL egy fejlesztőknek készült relációsadatbázis-szolgáltatás a Microsoft-felhőben, amely a nyílt forráskódú [PostgreSQL](https://www.postgresql.org/) adatbázismotor közösségi verzióján alapszik. Ez a szolgáltatás nyilvános előzetes verziója. Az Azure Database for PostgreSQL a következőket nyújtja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Skálázás menet közben, másodperceken belül.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Ezen képességek birtokában az alkalmazások gyors fejlesztésére és piacra juttatására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezenkívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül. 

Jelen cikkünk bemutatja az Azure Database for PostgreSQL teljesítménnyel, skálázhatósággal és kezelhetőséggel kapcsolatos alapfogalmait és jellemzőit. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

- [Azure Database for PostgreSQL létrehozása az Azure Portal használatával](quickstart-create-server-database-portal.md)
- [Azure Database for PostgreSQL létrehozása az Azure CLI használatával](quickstart-create-server-database-azure-cli.md)

Több Azure CLI-mintát talál itt:

- [Azure CLI-minták az Azure Database for PostgreSQL szolgáltatáshoz](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
Az előzetes verzióban az Azure Database for PostgreSQL szolgáltatás két szolgáltatásszintet nyújt: az Alapszintű és a Standard szintet. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részleteket a [tarifacsomagok](concepts-service-tiers.md) leírása tartalmazza.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? Használja a számítási egységeken alapuló, teljesítmény-értékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközöket. Ezek az eszközök lehetővé teszik a számítási egységek aktuális vagy a becsült teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) az ágazatban élenjáró módon 99,99 százalékos rendelkezésre állást biztosítunk (az előzetes verzióban nem érhető el) – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az összes Azure Database for PostgreSQL kiszolgáló használatakor élvezheti a beépített biztonság, hibatűrés és adatvédelem előnyeit, amelyeket egyébként külön kellene megvásárolnia vagy megterveznie, kiépítenie és kezelnie. Az Azure Database for PostgreSQL minden egyes szolgáltatásszintje az üzletmenet folytonosságát biztosító szolgáltatások és lehetőségek átfogó készletét kínálja, amelyeket tetszés szerint futtathat és helyezhet üzembe. Az [időponthoz kötött visszaállítás](howto-restore-server-portal.md) segítségével az adatbázist visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba. Továbbá, ha az adatbázisokat üzemeltető adatközpontban szolgáltatáskimaradás következik be, visszaállíthatja az adatbázisokat a legújabb biztonsági mentések georedundáns példányaiból.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure adatbázis-szolgáltatások megbízható adatbiztonságát az Azure Database for PostgreSQL olyan funkciói garantálják, amelyek korlátozzák a hozzáférést, védik a helyi és mozgó adatokat és hozzájárulnak a tevékenységek monitorozásához. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/TrustCenter/Security/default.aspx) talál információkat.

Az Azure Database for PostgreSQL szolgáltatás tárolási titkosítást használ a helyi adatokhoz. Az adatok a biztonsági másolatokkal együtt titkosítva kerülnek a lemezekre (a motor által lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével). A szolgáltatás AES 256 bites titkosítást használ, amely az Azure tárolási titkosításban szerepel, és a kulcsokat a rendszer felügyeli. A tárolási titkosítás mindig be van kapcsolva és nem tiltható le.

Alapértelmezés szerint az Azure Database for PostgreSQL szolgáltatás úgy van konfigurálva, hogy [SSL-kapcsolatbiztonságot](./concepts-ssl-connection-security.md) igényeljen a hálózatok között mozgó adatokhoz. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.  Választhatja az SSL megkövetelésének letiltását az adatbázis-szolgáltatáshoz való kapcsolódáshoz, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatokat.

## <a name="next-steps"></a>Következő lépések
- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/postgresql/) a költségek összehasonlításáért és árkalkulációjáért.
- Első lépésként [hozza létre első Azure Database for PostgreSQL adatbázist](./quickstart-create-server-database-portal.md).
- Építse fel az első alkalmazását Python, PHP, Ruby, C\#, Java, Node.js nyelven: [Csatlakozási kódtárak](./concepts-connection-libraries.md)
