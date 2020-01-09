---
title: Áttekintés
description: Ismerteti, hogyan használja az Azure Resource Manager eszközt erőforrások telepítésére, felügyeletére és hozzáférés-vezérlésére az Azure portálon.
ms.topic: overview
ms.date: 08/29/2019
ms.openlocfilehash: f7e11ada8bf10baa99faf08d2b4b401e3bea2d9e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473786"
---
# <a name="azure-resource-manager-overview"></a>Az Azure Resource Manager áttekintése

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-előfizetésében lévő erőforrások létrehozását, frissítését és törlését. A felügyeleti funkciókat, például a hozzáférés-vezérlést, a zárolásokat és a címkéket használhatja az erőforrások biztonságossá tételéhez és rendszerezéséhez az üzembe helyezés után.

Azure Resource Manager-sablonokkal kapcsolatos további tudnivalókért tekintse meg a [template Deployment áttekintése](../templates/overview.md)című témakört.

## <a name="consistent-management-layer"></a>Konzisztens felügyeleti réteg

Ha a felhasználó bármely Azure-eszközből, API-ból vagy SDK-ból küld kérelmet, a Resource Manager megkapja a kérelmet. Hitelesíti és engedélyezi a kérést. A Resource Manager elküldi a kérést az Azure szolgáltatásnak, amely végrehajtja a kért műveletet. Mivel az összes kérelem kezelése ugyanazon az API-n keresztül történik, a különböző eszközök konzisztens eredményei és képességei láthatók.

Az alábbi képen látható, hogy milyen szerepkör Azure Resource Manager játszik az Azure-kérelmek kezelésére. 

![A Resource Manager kérelmi modellje](./media/overview/consistent-management-layer.png)

A portálon elérhető összes funkció a PowerShell, az Azure CLI, a REST API-k és az ügyféloldali SDK-k segítségével is elérhető. Az eredetileg API-kon keresztül kiadott funkciók a kezdeti kiadástól számított 180 napig jelennek meg a portálon.

## <a name="terminology"></a>Szakkifejezések

Ha új felhasználója az Azure Resource Managernek, találkozhat néhány olyan kifejezéssel, amelyet még nem ismer.

* **erőforrás** – Egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. A virtuális gépek, a Storage-fiókok, a webalkalmazások, az adatbázisok és a virtuális hálózatok példák az erőforrásokra.
* **erőforráscsoport** – Egy olyan tároló, amely egy Azure-megoldáshoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazza azokat az erőforrásokat, amelyeket csoportként szeretne kezelni. Ön dönti el, hogy mely erőforrások tartoznak egy erőforráscsoporthoz az alapján, hogy mi a legmegfelelőbb a szervezet számára. Lásd: [erőforráscsoportok](#resource-groups).
* **erőforrás-szolgáltató** – az Azure-erőforrásokat ellátó szolgáltatás. Egy általános erőforrás-szolgáltató például a Microsoft. számítás, amely a virtuális gép erőforrását adja meg. A Microsoft. Storage egy másik gyakori erőforrás-szolgáltató. Lásd: [erőforrás-szolgáltatók és-típusok](resource-providers-and-types.md).
* **Resource Manager-sablon** – egy JavaScript Object Notation (JSON) fájl, amely egy vagy több erőforrást ad meg egy erőforráscsoport vagy előfizetés számára történő üzembe helyezéshez. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó. Lásd: [template Deployment áttekintése](../templates/overview.md).
* **deklaratív szintaxis** – Egy olyan szintaxis, amellyel anélkül határozhatja meg, mit szeretne létrehozni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A Resource Manager-sablon a deklaratív szintaxis egy példája. A fájlban meghatározhatja az Azure-ra telepíteni kívánt infrastruktúra tulajdonságait.  Lásd: [template Deployment áttekintése](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>A Resource Manager használatának előnyei

A Resource Managerrel a következőket teheti:

* Az infrastruktúrát nem szkriptek, hanem deklaratív sablonok segítségével kezelheti.

* A megoldáshoz tartozó összes erőforrást egy csoportként telepítheti, kezelheti és figyelheti, ahelyett, hogy ezeket az erőforrásokat külön kezeli.

* A megoldás újbóli üzembe helyezése a fejlesztési életciklus során, és az erőforrások biztonságos üzembe helyezése konzisztens állapotban.

* Definiálja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

* A hozzáférés-vezérlés alkalmazása az erőforráscsoport összes szolgáltatására, mivel a szerepköralapú Access Control (RBAC) natív módon integrálva van a felügyeleti platformba.

* Címkék alkalmazása az erőforrásokra az előfizetésben lévő összes erőforrás logikus rendszerezéséhez.

* A szervezet számlázásának tisztázásához tekintse meg az azonos címkével rendelkező erőforrások egy csoportjának költségeit.

## <a name="understand-scope"></a>A hatókör megismerése

Az Azure a hatókör négy szintjét biztosítja: [felügyeleti csoportok](../../governance/management-groups/overview.md), előfizetések, [erőforráscsoportok](#resource-groups)és erőforrások. Az alábbi ábra ezekre a rétegekre mutat egy példát.

![Hatókör](./media/overview/scope-levels.png)

Felügyeleti beállításokat a hatókörszintek bármelyikéhez megadhat. A kiválasztott szint határozza meg, milyen széles körben lesz alkalmazva a beállítás. Az alacsonyabb szintek öröklik a magasabb szintek beállításait. Ha például alkalmaz egy [szabályzatot](../../governance/policy/overview.md) az előfizetésre, a rendszer az előfizetésben lévő összes erőforráscsoport és erőforrás esetében alkalmazza a szabályzatot. Ha az erőforráscsoport házirendjét alkalmazza, a házirend az erőforráscsoportot és annak összes erőforrását alkalmazza. Egy másik erőforráscsoport azonban nem rendelkezik ezzel a szabályzat-hozzárendeléssel.

A sablonokat felügyeleti csoportokra, előfizetésekre vagy erőforráscsoportokre is telepítheti.

## <a name="resource-groups"></a>Erőforráscsoportok

Néhány fontos tényezőt érdemes figyelembe venni az erőforráscsoport meghatározásakor:

* A csoportban lévő összes erőforrásnak azonos életciklussal kell rendelkeznie. Egyszerre fogja őket telepíteni, frissíteni és törölni. Ha egy erőforrásnak, például egy adatbázis-kiszolgálónak különböző fejlesztési ciklusban kell léteznie, azt másik erőforráscsoportba kell elhelyezni.

* Az egyes erőforrások csak egy erőforráscsoportban létezhetnek.

* Az erőforráscsoporthoz bármikor hozzáadhat, vagy onnan eltávolíthat egy erőforrást.

* Az erőforrásokat áthelyezheti az egyik erőforráscsoportból a másikba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

* Az erőforráscsoportok különböző régiókban található erőforrásokat is tartalmazhatnak.

* Az erőforráscsoport segítségével meghatározhatja a hozzáférés-vezérlési hatókört felügyeleti műveletekhez.

* Egy erőforrás más erőforráscsoportok erőforrásaival is interakcióba tud lépni. Ez az interakció gyakori, amikor a két erőforrás kapcsolódik, de nem ugyanaz az életciklusuk (például amikor egy webalkalmazás csatlakozik egy adatbázishoz).

Egy erőforráscsoport létrehozásakor meg kell adnia az erőforráscsoport helyét. Most felmerülhet Önben a kérdés, hogy „Miért van szüksége egy erőforráscsoportnak helyre? Ha pedig az erőforrások rendelkezhetnek az erőforrástól eltérő hellyel, akkor miért számít egyáltalán az erőforráscsoport helye?” Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Amikor megad egy helyet az erőforráscsoporthoz, meg kell adnia, hogy hol tárolja a metaadatokat. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Ha az erőforráscsoport régiója átmenetileg nem érhető el, az erőforráscsoport erőforrásai nem frissíthetők, mert a metaadatok nem érhetők el. A más régiókban lévő erőforrások továbbra is a várt módon fognak működni, de nem frissítheti őket. A megbízható alkalmazások létrehozásával kapcsolatos további információkért lásd: [megbízható Azure-alkalmazások tervezése](/azure/architecture/checklist/resiliency-per-service).

## <a name="resiliency-of-azure-resource-manager"></a>Azure Resource Manager rugalmassága

A Azure Resource Manager szolgáltatás a rugalmasság és a folyamatos rendelkezésre állás érdekében lett kialakítva. A REST API az erőforrás-kezelő és a vezérlési sík műveletei (management.azure.com küldött kérelmek) a következők:

* Régiók között elosztva. Egyes szolgáltatások regionálisak.

* Több Availability Zones rendelkező helyen Availability Zones (valamint régiókban) elosztva.

* Egyetlen logikai adatközponttól sem függ.

* A karbantartási tevékenységekhez soha nem került sor.

Ez a rugalmasság olyan szolgáltatásokra vonatkozik, amelyek a Resource Manageren keresztül kapják meg a kérelmeket. Például Key Vault a rugalmasság előnyeit.

## <a name="next-steps"></a>Következő lépések

* Az erőforrás-szolgáltatók által kínált összes műveletnél tekintse meg az [Azure REST API-kat](/rest/api/azure/).

* További információ az erőforrások áthelyezéséről: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).

* Az erőforrások címkézésével kapcsolatos további tudnivalókért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md).

* Az erőforrások zárolásának megismeréséhez lásd: [erőforrások zárolása a váratlan változtatások megelőzése érdekében](lock-resources.md).

* További információ sablonok létrehozásáról a központi telepítésekhez: [template Deployment áttekintése](../templates/overview.md).
