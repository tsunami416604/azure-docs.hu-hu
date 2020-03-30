---
title: Az Azure Resource Graph áttekintése
description: Ismerje meg, hogy az Azure Resource Graph szolgáltatás hogyan teszi lehetővé az erőforrások összetett lekérdezését az előfizetések és a bérlők között.
ms.date: 03/02/2020
ms.topic: overview
ms.openlocfilehash: 38ead7be09e038b19c390acd9f10e1c0ccf9d858
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240057"
---
# <a name="what-is-azure-resource-graph"></a>Mi az az Azure Resource Graph?

Az Azure Resource Graph egy olyan azure-szolgáltatás, amelynek célja az Azure Erőforrás-kezelés kiterjesztése azáltal, hogy hatékony és eredményes erőforrás-feltárást biztosít, és lehetővé teszi a lekérdezést nagy méretekben egy adott előfizetés-készleten keresztül, hogy hatékonyan szabályozhassa Környezet. Ezek a lekérdezések a következő funkciókat biztosítják:

- Erőforrások lekérdezése az erőforrás-tulajdonságok alapján végzett összetett szűréssel, csoportosítással és rendezéssel.
- Képes iteratív módon feltárni az erőforrásokat a cégirányítási követelmények alapján.
- Szabályzatok alkalmazásából adódó következmények felmérése kiterjedt felhőkörnyezetben.
- Az [erőforrás-tulajdonságokon végrehajtott módosítások részletezésének](./how-to/get-resource-changes.md) lehetősége (előzetes verzió).

Ez a dokumentáció mindegyik funkciót részletesen tárgyalja.

> [!NOTE]
> Az Azure Resource Graph az Azure Portal keresősávját, az új "Minden erőforrás" felületet és az Azure Policy [változási előzmények vizuális](../policy/how-to/determine-non-compliance.md#change-history-preview)
> diff szolgáltatását_működteti._ Úgy tervezték, hogy segítsen az ügyfeleknek a nagyméretű környezetek kezelésében.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hogyan egészíti ki a Resource Graph az Azure Resource Managert

Az Azure Resource Manager jelenleg támogatja az alapvető erőforrásmezők lekérdezéseit, különösen – Erőforrás név, azonosító, típus, erőforráscsoport, előfizetés és hely. Az Erőforrás-kezelő lehetővé teszi az egyes erőforrás-szolgáltatók hívását is a részletes tulajdonságokhoz, egyszerre egy erőforrást.

Az Azure Resource Graph segítségével az erőforrás-szolgáltatók egyenkénti hívása nélkül is hozzáférhet az általuk visszaadott tulajdonságokhoz. A támogatott erőforrástípusok listájáért tekintse át a [tábla- és erőforrástípus-hivatkozást.](./reference/supported-tables-resources.md) A támogatott erőforrástípusok megtekintésének másik módja az [Azure Resource Graph Explorer sémaböngészője.](./first-query-portal.md#schema-browser)

Az Azure Resource Graph segítségével a következőket teheti:

- Az erőforrás-szolgáltatók által visszaadott tulajdonságok elérése anélkül, hogy az egyes erőforrás-szolgáltatókhoz külön hívásokat kellene kezdeményeznie.
- Tekintse meg az erőforráson végzett módosítási előzmények utolsó 14 napját, és tekintse meg, hogy milyen tulajdonságok változtak, és mikor. (előzetes verzió)

## <a name="how-resource-graph-is-kept-current"></a>Az Erőforrás-diagram naprakészen tartása

Egy Azure-erőforrás frissítésekor a Resource Graph értesíti az Erőforrás-kezelő a változásról.
Az Resource Graph ezután frissíti az adatbázist. Resource Graph is csinál egy szabályos _teli átka._ Ez a vizsgálat biztosítja, hogy az Erőforrás-diagram adatai aktuálisak legyenek, ha nem fogadott értesítések vannak, vagy ha egy erőforrást az Erőforrás-kezelőn kívül frissítenek.

> [!NOTE]
> A Resource `GET` Graph az egyes erőforrás-szolgáltatók legújabb nem előzetes API-ját használja a tulajdonságok és értékek összegyűjtéséhez. Ennek eredményeképpen előfordulhat, hogy a várt tulajdonság nem érhető el. Bizonyos esetekben a használt API-verzió felülbírálta, hogy több aktuális vagy széles körben használt tulajdonságokat az eredményekben. Tekintse meg az [API-verzió megjelenítése minden erőforrástípus](./samples/advanced.md#apiversion) minta egy teljes listát a környezetben.

## <a name="the-query-language"></a>A lekérdezőnyelv

Most, hogy jobban megértheti, hogy mi az Azure Resource Graph, vessünk egy kis betekintést a lekérdezések létrehozásához.

Fontos megérteni, hogy az Azure Resource Graph lekérdezési nyelve az Azure Data Explorer által használt [Kusto lekérdezési nyelvalapján](../../data-explorer/data-explorer-overview.md) történik.

Első lépésként olvassa el az Azure Resource Graphfal használható műveleteket és funkciókat ismertető, [a Resource Graph lekérdezőnyelve](./concepts/query-language.md) című cikket.
Az erőforrások tallózását az [erőforrások kezeléséről](./concepts/explore-resources.md) szóló cikk írja le.

## <a name="permissions-in-azure-resource-graph"></a>Engedélyek az Azure Resource Graphban

A Resource Graph használatához megfelelő jogosultságokkal kell rendelkeznie a [szerepköralapú hozzáférés-vezérlésben](../../role-based-access-control/overview.md) (RBAC), és legalább olvasási jogosultsággal kell rendelkeznie a lekérdezni kívánt erőforrásokon. Ha nem rendelkezik legalább `read` engedélyekkel az Azure-objektumhoz vagy -objektumcsoporthoz, a rendszer nem ad vissza eredményeket.

> [!NOTE]
> A Resource Graph a bejelentkezés során a rendszerbiztonsági tag számára elérhető előfizetéseket használja. Egy aktív munkamenet során hozzáadott új előfizetés erőforrásainak megtekintéséhez a főtagnak frissítenie kell a környezetet. Ez a művelet automatikusan megtörténik, amikor kijelentkezik, majd vissza.

Az Azure CLI és az Azure PowerShell olyan előfizetéseket használ, amelyekhez a felhasználó rendelkezik hozzáféréssel. A REST API közvetlen használata esetén az előfizetési listát a felhasználó biztosítja. Ha a felhasználó hozzáfér a listában szereplő előfizetések bármelyikéhez, a lekérdezés eredménye azokhoz az előfizetésekhez ad vissza, amelyekhez a felhasználó hozzáféréssel rendelkezik. Ez a viselkedés ugyanaz, mint amikor [meghívja az erőforráscsoportokat – Lista,](/rest/api/resources/resourcegroups/list) \- amelyhez olyan erőforráscsoportokat kap, amelyekhez hozzáfér, anélkül, hogy jeleznék, hogy az eredmény részleges lehet.
Ha nincsenek olyan előfizetések az előfizetési listában, amelyhez a felhasználó rendelkezik a megfelelő jogokkal, a válasz egy _403-as_ (Tiltott).

## <a name="throttling"></a>Throttling

Ingyenes szolgáltatásként a Resource Graph lekérdezések szabályozása, hogy a legjobb élményt és válaszidőt minden ügyfél számára. Ha a szervezet az Erőforrásgráf API-t szeretné használni a nagyméretű és gyakori lekérdezésekhez, használja a "Visszajelzés" portált az [Erőforrásgráf portál lapról.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)
Adja meg az üzleti esetet, és jelölje be a "Microsoft e-mailben küldheti el visszajelzését" jelölőnégyzetet, hogy a csapat kapcsolatba léphetjön Önnel.

A Resource Graph felhasználói szinten szabályozza a lekérdezéseket. A szolgáltatásválasz a következő HTTP-fejléceket tartalmazza:

- `x-ms-user-quota-remaining`(int): A felhasználó fennmaradó erőforráskvótája. Ez az érték leképezi a lekérdezések számát.
- `x-ms-user-quota-resets-after`(óó:pp:ss): A felhasználó kvótafelhasználásának alaphelyzetbe állításáig tartó időtartam

További információ: [Útmutató a szabályozott kérelmekhez](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Az első lekérdezés futtatása

Az Azure Resource Graph Explorer, amely az Azure Portal része, lehetővé teszi az Erőforrás-gráf lekérdezések közvetlen futtatását az Azure Portalon. Az eredményeket dinamikus diagramként rögzítheti, hogy valós idejű dinamikus információkat nyújtson a portálmunkafolyamatának. További információ: [First query with Azure Resource Graph Explorer](first-query-portal.md).

A Resource Graph támogatja az Azure CLI-t, az Azure PowerShellt, az Azure SDK-t a .NET-hez és még sok mást. A lekérdezés minden nyelven azonos struktúrában van felépítve. További információ az Erőforrás-diagram engedélyezéséről:

- [Az Azure Portal és a Resource Graph Explorer](first-query-portal.md) 
- [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>További lépések

- Futtassa az első lekérdezést az [Azure Portal](first-query-portal.md)használatával.
- Futtassa az első lekérdezést az [Azure CLI](first-query-azurecli.md)segítségével.
- Futtassa az első lekérdezést az [Azure PowerShell](first-query-powershell.md)használatával.