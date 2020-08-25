---
title: Áttekintés – Azure Database for MySQL
description: Ismerkedjen meg az Azure Database for MySQL szolgáltatással, amely a Microsoft Cloud-on alapuló, a MySQL Community Edition rendszerre épülő, kapcsolódó adatbázis-szolgáltatás.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 37bc99d9f83f185a5372fd45634351987b85e20b
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763660"
---
# <a name="what-is-azure-database-for-mysql"></a>Mi az Azure Database for MySQL?

A Azure Database for MySQL a Microsoft Cloud-on alapuló, a [MySQL Community Edition](https://www.mysql.com/products/community/) (a GPLv2 licenc alatt elérhető) adatbázismotor, a 5,6, a 5,7 és a 8,0 verziók alapján készült, a Microsoft felhőben található, Azure Database for MySQL a következőket biztosítja:

- Beépített magas rendelkezésre állás további költség nélkül.
- Kiszámítható teljesítmény, használatalapú díjszabással.
- Méretezés igény szerint másodpercek alatt.
- A helyi és mozgó bizalmas adatok védelme.
- Automatikus biztonsági mentések és időponthoz kötött visszaállítás akár 35 napig.
- Vállalati szintű biztonság és megfelelőség.

Ezen képességek szinte semmilyen felügyeletet nem igényelnek, és mindegyik további költség nélkül érhető el. Segítségükkel az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Ezen kívül folytathatja az alkalmazás fejlesztését a kívánt nyílt forráskódú eszközökkel és platformmal, és ezeket az üzletvitel által igényelt sebességgel és hatékonysággal készítheti el, új készségek elsajátításának szükségessége nélkül.

![Azure Database for MySQL fogalmi diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Ez a Azure Database for MySQL cikk a teljesítménygel, a méretezhetőséggel és a kezelhetőséggel kapcsolatos alapfogalmakat és funkciókat mutatja be, amelyek a részletekre mutató hivatkozásokat tartalmaznak. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md)

Több Azure CLI-mintát talál itt:

- [Azure CLI-példák az Azure Database for MySQL-hez](sample-scripts-azure-cli.md)

## <a name="automated-patching"></a>Automatikus javítás
A szolgáltatás a mögöttes hardver, az operációs rendszer és az adatbázis motorjának automatizált javítását végzi. A javítás a mögöttes hardver, az operációs rendszer és az adatbázismotor biztonsági és szoftverfrissítési szolgáltatásait is tartalmazza. A MySQL-motor esetében az alverziók frissítése automatikusan megtörténik, és a javítási kiadás részeként szerepel. Ha a Közösség alverziót bocsát ki, az automatikusan integrálva lesz a szolgáltatás tesztelési ciklusának részeként. Az alverzió tesztelése a MySQL-hez készült kanonikus számítási feladatokon történik. A MySQL-motor alverzióit a rendszer kiértékeli a megbízhatóság (összeomlás nélkül), a rendelkezésre állás, a biztonság és a teljesítmény szempontjából. A szolgáltatás nem minden alverziót bocsát ki éles környezetben, de a hibajavítások és az új növekményes értékek kritikus evaluted alapul. Ezzel a megfelelő egyensúlyt kell megteremteni az új növekményes érték és a stabilitási rendszer változóinak minimalizálása között. Nincs szükség felhasználói műveletre vagy konfigurációs beállításra a javításhoz. A javítási gyakoriság a szolgáltatás által felügyelt, az adattartalom kritikus jellemzői alapján. Általánosságban elmondható, hogy a szolgáltatás a folyamatos integráció és a kiadás részeként havi kiadási ütemtervet követ. A felhasználók előfizethetnek a [tervezett karbantartási értesítésre](concepts-monitoring.md) , hogy értesítést kapjanak a közelgő karbantartási 72 órával az esemény előtt.

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül
A Azure Database for MySQL szolgáltatás számos szolgáltatási szintet kínál: alapszintű, általános célú és memória optimalizálva. Az egyes szintek különböző teljesítményt és képességeket kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz számítási feladatokig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később a megoldása szükségletei alapján módosíthatja a méretet. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokért az erőforrásokért kell fizetnie, amelyekre szüksége van, és csak akkor, amikor szüksége van rájuk. A részletekért tekintse meg a [díjszabási szintet](concepts-service-tiers.md) .

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan lehet megállapítani, hogy mikor van szükség fel- és leskálázásra? Használja a virtuális magokon alapuló, teljesítmény-értékeléssel kombinált, beépített teljesítménymonitorozó és riasztási eszközöket. Ezek az eszközök lehetővé teszik a virtuális magok aktuális vagy a becsült teljesítményigényeken alapuló fel- vagy leskálázása hatásainak gyors kiértékelését. A részleteket a [riasztások](howto-alert-on-metric.md) leírása tartalmazza.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében (SLA) az ágazatban élenjáró módon 99,99 százalékos rendelkezésre állást biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Minden Azure Database for MySQL-kiszolgáló esetében kihasználhatja a beépített biztonság, hibatűrés és adatvédelem előnyeit, amelyeket egyébként nem kell megvásárolnia vagy megterveznie, kiépítenie és kezelnie. A Azure Database for MySQL használatával az időponthoz tartozó visszaállítással visszaállíthatja a kiszolgálót egy korábbi állapotba, a 35 nap visszamenőleg visszamenőleg.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure Database Services olyan adatbiztonságot biztosít, amely Azure Database for MySQL megtartja az olyan funkciókat, amelyek korlátozzák a hozzáférést, a REST-alapú és a mozgásban lévő adatok védelméhez, és a tevékenységek figyeléséhez. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter/security) talál információkat. A Azure Database for MySQL biztonsági funkcióival kapcsolatos további információkért tekintse meg a [Biztonság áttekintése](concepts-security.md)című témakört.

## <a name="contacts"></a>Kapcsolattartók
Ha bármilyen kérdése vagy javaslata van a Azure Database for MySQL használatáról, küldjön e-mailt a Azure Database for MySQL csapatnak ([ @Ask a MySQL-hez készült Azure-adatbázis](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ez az e-mail-cím nem technikai támogatási alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>További lépések
Most, hogy elolvasta a Azure Database for MySQL bevezetését, és megválaszolta a "mi az Azure Database for MySQL?" kérdést? készen áll a következőre:

- Tekintse meg az árképzést ismertető oldalt a költségek összehasonlításáért és árkalkulációjáért. [Díjszabás](https://azure.microsoft.com/pricing/details/mysql/)
- Első lépésként hozza létre első kiszolgálóját. [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md)
- Hozza létre első alkalmazását a kívánt nyelv használatával:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Ugrás](./connect-go.md)
