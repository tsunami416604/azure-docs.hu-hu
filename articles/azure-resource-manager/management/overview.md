---
title: Áttekintés
description: Ismerteti, hogyan használja az Azure Resource Manager eszközt erőforrások telepítésére, felügyeletére és hozzáférés-vezérlésére az Azure portálon.
ms.topic: overview
ms.date: 03/25/2020
ms.openlocfilehash: 1e2a6959117749b4e7d08a9768b4189b97ef08bd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80258141"
---
# <a name="what-is-azure-resource-manager"></a>Mi az Azure Resource Manager?

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Felügyeleti réteget biztosít, amely lehetővé teszi az Azure-előfizetéserőforrásainak létrehozását, frissítését és törlését. A felügyeleti funkciókat, például a hozzáférés-vezérlést, a zárolásokat és a címkéket az erőforrások üzembe helyezés utáni védelmére és rendszerezésére használhatja.

Az Azure Resource Manager-sablonokról a [Sablon üzembe helyezése – áttekintés című témakörben](../templates/overview.md)olvashat.

## <a name="consistent-management-layer"></a>Konzisztens felügyeleti réteg

Amikor egy felhasználó az Azure-eszközök, API-k vagy SDK-k bármelyikéből küld kérelmet, az Erőforrás-kezelő megkapja a kérést. Hitelesíti és engedélyezi a kérelmet. Az Erőforrás-kezelő elküldi a kérelmet az Azure-szolgáltatásnak, amely végrehajtja a kért műveletet. Mivel az összes kérelmet ugyanazon az API-n keresztül kezeli, egységes eredményeket és képességeket láthat a különböző eszközökben.

Az alábbi képen látható, hogy az Azure Resource Manager milyen szerepet játszik az Azure-kérések kezelésében.

![A Resource Manager kérelmi modellje](./media/overview/consistent-management-layer.png)

A portálon elérhető összes funkció a PowerShellen, az Azure CLI-n, a REST API-kon és az ügyfél SDK-kon keresztül is elérhető. Az eredetileg API-kon keresztül kiadott funkciók a kezdeti kiadástól számított 180 napig jelennek meg a portálon.

## <a name="terminology"></a>Terminológia

Ha új felhasználója az Azure Resource Managernek, találkozhat néhány olyan kifejezéssel, amelyet még nem ismer.

* **erőforrás** – Egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. A virtuális gépek, a tárfiókok, a webalkalmazások, az adatbázisok és a virtuális hálózatok példák az erőforrásokra.
* **erőforráscsoport** – Egy olyan tároló, amely egy Azure-megoldáshoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport a csoportként kezelni kívánt erőforrásokat tartalmazza. A cég szempontjai alapján Ön döntheti el, hogy mely erőforrások tartozzanak ugyanahhoz az erőforráscsoporthoz. Lásd: [erőforráscsoportok](#resource-groups).
* **erőforrás-szolgáltató** – Olyan szolgáltatás, amely Azure-erőforrásokat biztosít. Egy közös erőforrás-szolgáltató például a Microsoft.Compute, amely a virtuálisgép-erőforrást látja el. A Microsoft.Storage egy másik gyakori erőforrás-szolgáltató. Lásd: [Erőforrás-szolgáltatók és -típusok](resource-providers-and-types.md).
* **Resource Manager-sablon** – Egy JavaScript-objektumjelölési (JSON) fájl, amely egy vagy több erőforrást határoz meg egy erőforráscsoportba vagy előfizetésbe való üzembe helyezéshez. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó. Lásd: [Sablon üzembe helyezése – áttekintés.](../templates/overview.md)
* **deklaratív szintaxis** – Egy olyan szintaxis, amellyel anélkül határozhatja meg, mit szeretne létrehozni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A Resource Manager-sablon a deklaratív szintaxis egy példája. A fájlban meghatározhatja az Azure-ra telepíteni kívánt infrastruktúra tulajdonságait.  Lásd: [Sablon üzembe helyezése – áttekintés.](../templates/overview.md)

## <a name="the-benefits-of-using-resource-manager"></a>A Resource Manager használatának előnyei

Az Erőforrás-kezelővel a következőket teheti:

* Az infrastruktúrát parancsfájlok helyett deklaratív sablonokkal kezelheti.

* A megoldás hozadékát, kezelése és figyelése csoportot, és nem külön-külön kezeli ezeket az erőforrásokat.

* Telepítse újra a megoldást a fejlesztési életciklus teljes életciklusa során, és bízzon abban, hogy az erőforrások konzisztens állapotban vannak üzembe helyezve.

* Határozza meg az erőforrások közötti függőségeket, hogy a megfelelő sorrendben legyenek üzembe helyezve.

* Hozzáférés-vezérlés alkalmazása az erőforráscsoport összes szolgáltatására, mert a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.

* Címkék alkalmazása az erőforrásokra az előfizetés összes erőforrásának logikai rendezéséhez.

* Tisztázza a szervezet számlázását az azonos címkével megegyező címkével megegyező erőforráscsoport költségeinek megtekintésével.

## <a name="understand-scope"></a>A hatókör bemutatása

Az Azure négy hatóköri szintet biztosít: [felügyeleti csoportokat,](../../governance/management-groups/overview.md)előfizetéseket, [erőforráscsoportokat](#resource-groups)és erőforrásokat. Az alábbi ábra ezekre a rétegekre mutat egy példát.

![Hatókör](./media/overview/scope-levels.png)

Felügyeleti beállításokat a hatókörszintek bármelyikéhez megadhat. A kiválasztott szint határozza meg, milyen széles körben lesz alkalmazva a beállítás. Az alacsonyabb szintek öröklik a magasabb szintek beállításait. Ha például egy [szabályzatot](../../governance/policy/overview.md) alkalmaz az előfizetésre, a szabályzat az előfizetés összes erőforráscsoportjára és erőforrására vonatkozik. Amikor házirendet alkalmaz az erőforráscsoportra, akkor a házirend az erőforráscsoportot és annak összes erőforrását alkalmazza. Egy másik erőforráscsoport azonban nem rendelkezik ezzel a házirend-hozzárendelésel.

Sablonokat felügyeleti csoportokba, előfizetésekbe vagy erőforráscsoportokba helyezhet.

## <a name="resource-groups"></a>Erőforráscsoportok

Néhány fontos tényezőt érdemes figyelembe venni az erőforráscsoport meghatározásakor:

* A csoportban lévő összes erőforrásnak azonos életciklussal kell rendelkeznie. Egyszerre fogja őket telepíteni, frissíteni és törölni. Ha egy erőforrásnak, például egy adatbázis-kiszolgálónak különböző fejlesztési ciklusban kell léteznie, azt másik erőforráscsoportba kell elhelyezni.

* Az egyes erőforrások csak egy erőforráscsoportban létezhetnek.

* Az erőforráscsoporthoz bármikor hozzáadhat, vagy onnan eltávolíthat egy erőforrást.

* Az erőforrásokat áthelyezheti az egyik erőforráscsoportból a másikba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

* Az erőforráscsoportok tartalmazhatnak különböző régiókban található erőforrásokat.

* Az erőforráscsoport segítségével meghatározhatja a hozzáférés-vezérlési hatókört felügyeleti műveletekhez.

* Egy erőforrás más erőforráscsoportok erőforrásaival is interakcióba tud lépni. Ez az interakció gyakori, amikor a két erőforrás kapcsolódik, de nem ugyanaz az életciklusuk (például amikor egy webalkalmazás csatlakozik egy adatbázishoz).

Egy erőforráscsoport létrehozásakor meg kell adnia az erőforráscsoport helyét. Most felmerülhet Önben a kérdés, hogy „Miért van szüksége egy erőforráscsoportnak helyre? Ha pedig az erőforrások rendelkezhetnek az erőforrástól eltérő hellyel, akkor miért számít egyáltalán az erőforráscsoport helye?” Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Amikor megadja az erőforráscsoport helyét, megadja a metaadatok tárolási helyét. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Ha az erőforráscsoport régiója átmenetileg nem érhető el, az erőforráscsoport erőforrásai nem frissíthetők, mert a metaadatok nem érhetők el. A többi régió erőforrásai továbbra is a várt módon fognak működni, de nem frissíthetők. A megbízható alkalmazások létrehozásáról a [Megbízható Azure-alkalmazások tervezése](/azure/architecture/checklist/resiliency-per-service)című témakörben talál további információt.

## <a name="resiliency-of-azure-resource-manager"></a>Az Azure Resource Manager rugalmassága

Az Azure Resource Manager szolgáltatás rugalmasságra és folyamatos rendelkezésre állásra lett tervezve. Az erőforrás-kezelő és a vezérlősík-műveletek (a MANAGEMENT.AZURE.COM-nek küldött kérelmek) a REST API-ban a következők:

* Régiók között elosztva. Egyes szolgáltatások regionálisak.

* Rendelkezésre állási zónák (valamint régiók) között elosztva olyan helyeken, amelyek több rendelkezésre állási zónával rendelkeznek.

* Nem függ egyetlen logikai adatközponttól.

* Soha nem vették le a karbantartási tevékenységek.

Ez a rugalmasság azokra a szolgáltatásokra vonatkozik, amelyek az Erőforrás-kezelőn keresztül fogadnak kéréseket. Például a Key Vault előnyeit ez a rugalmasság előnyeit.

## <a name="next-steps"></a>További lépések

* Az erőforrások áthelyezéséről az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](move-resource-group-and-subscription.md)témakörben olvashat.

* Az erőforrások címkézéséről az [Azure-erőforrások rendszerezéséhez címkék használata.](tag-resources.md)

* Az erőforrások zárolásáról az [Erőforrások zárolásának megakadályozása a váratlan változások megelőzése című](lock-resources.md)témakörben olvashat.
