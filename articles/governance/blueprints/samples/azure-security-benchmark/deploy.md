---
title: Az Azure Security teljesítményteszt tervének üzembe helyezése minta
description: Üzembe helyezheti az Azure Security teljesítményteszt tervének lépéseit, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: 0e967e2217b90e036d809ce7baf500c3bc791c6f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725965"
---
# <a name="deploy-the-azure-security-benchmark-blueprint-sample"></a>Az Azure Security teljesítményteszt tervének üzembe helyezése minta

Az Azure-tervezetek Azure Security benchmark Blueprint-minta üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) .

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **tervrajzokat**.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg az **Azure Security teljesítményteszt** tervezetét _más minták_ területen, majd kattintson a név gombra a minta kiválasztásához.

1. Adja meg a tervezet mintájának _alapjait_ :

   - **Terv neve**: adjon meg egy nevet az Azure Security teljesítményteszt tervezetének mintája számára.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza ki az oldal tetején található _összetevők fület_ , vagy a **következőt:** összetevők az oldal alján.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás elmozdulhat az Azure biztonsági teljesítménytesztekkel kapcsolatos javaslatok segítségével.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amely az Azure Security teljesítményteszt tervezetének mintájában megjelent." Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a terv **kiosztása** elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paramétereinek

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták **díjmentesek**. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|
A Windows VM-Rendszergazdák csoportból kizárt felhasználók listája|A rendszergazdák helyi csoportba kizárandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, akiknek szerepelniük kell a Windows VM-rendszergazdák csoportban|A rendszergazdák helyi csoportba foglalandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, amelyeket a Windows VM-rendszergazdák csoportnak *csak* tartalmaznia kell|A rendszergazdák helyi csoportjának várt tagjainak pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azon régiók listája, amelyeken engedélyezni kell a Network Watcher|A régiók teljes listájának megtekintéséhez használja a Get-AzLocation|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Virtuális hálózat, amelyben a virtuális gépek csatlakozni fognak|Példa:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|A virtuális hálózatok által használandó hálózati átjáró|Példa:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azon munkaterület-azonosítók listája, amelyekben Log Analytics-ügynököknek csatlakozniuk kell|Azon munkaterület-azonosítók pontosvesszővel tagolt listája, amelyekhez a Log Analytics ügynöknek csatlakoznia kell|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak|A kiválasztott erőforrástípusok diagnosztikai beállításainak naplózása|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|A PHP legújabb verziója|A App Services legújabb támogatott PHP-verziója|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Legújabb Java-verzió|A legújabb támogatott Java-verzió a App Services|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Legújabb Windows Python-verzió|A App Services legújabb támogatott Python-verziója|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Legújabb Linux Python-verzió|A App Services legújabb támogatott Python-verziója|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure Security teljesítményteszt tervezetének üzembe helyezéséhez szükséges lépéseket, tekintse meg a következő cikkeket a terv és a vezérlési leképezés megismeréséhez:

> [!div class="nextstepaction"]
> [Az Azure Security teljesítményteszt tervezete – áttekintés](./index.md)
> az[Azure Security teljesítményteszt tervezetéről – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
