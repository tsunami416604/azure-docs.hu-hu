---
title: Az Azure Resource Graph áttekintése
description: Ismerje meg, hogyan teszi lehetővé az Azure Resource Graph szolgáltatás az erőforrások összetett lekérdezését nagy léptékben.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: faad0a0166c1b11f51f814bb6ce3351d7a017239
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900026"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Az Azure Resource Graph szolgáltatás áttekintése

Az Azure Resource Graph egy Azure-beli szolgáltatás, amely az Azure Erőforrás-kezelés kibővítésére szolgál azáltal, hogy hatékony és teljesítménybeli erőforrás-feltárást biztosít, és lehetővé teszi az adott előfizetések nagy léptékű lekérdezését, így hatékonyan szabályozhatja a környezet. Ezek a lekérdezések a következő funkciókat biztosítják:

- Erőforrások lekérdezése az erőforrás-tulajdonságok alapján végzett összetett szűréssel, csoportosítással és rendezéssel.
- Az erőforrások az irányítási követelmények alapján iteratív.
- Szabályzatok alkalmazásából adódó következmények felmérése kiterjedt felhőkörnyezetben.
- [Részletes adatok az erőforrás-tulajdonságok](./how-to/get-resource-changes.md) (előzetes verzió) változásairól.

Ez a dokumentáció mindegyik funkciót részletesen tárgyalja.

> [!NOTE]
> Az Azure Resource Graph a Azure Portal keresési sávját, az új "minden erőforrás" funkciót és a Azure Policy [változási előzményeinek](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _vizuális diff_elemeit használja. Ez úgy lett kialakítva, hogy segítse az ügyfeleknek a nagyméretű környezetek kezelését.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hogyan egészíti ki a Resource Graph az Azure Resource Managert

Azure Resource Manager jelenleg a lekérdezéseket támogatja az alapszintű erőforrások mezőin, különösen az erőforrás neve, az azonosító, a típus, az erőforráscsoport, az előfizetés és a hely alapján. A Resource Manager emellett olyan létesítményeket is biztosít, amelyekkel az egyes erőforrás-szolgáltatók meghívhatók a részletes tulajdonságok egy erőforrással egyidejűleg.

Az Azure Resource Graph segítségével az erőforrás-szolgáltatók egyenkénti hívása nélkül is hozzáférhet az általuk visszaadott tulajdonságokhoz. A támogatott erőforrástípusok listáját a [teljes módú központi telepítések](../../azure-resource-manager/complete-mode-deletion.md) táblázatának erőforrásait ismertető részében tekintheti meg.

Az Azure Resource Graph segítségével a következőket teheti:

- Az erőforrás-szolgáltatók által visszaadott tulajdonságok elérése anélkül, hogy az egyes erőforrás-szolgáltatóknak külön hívásokat kellene végezniük.
- Megtekintheti az Erőforrás változási előzményeinek utolsó 14 napját, és megtekintheti, hogy milyen tulajdonságok változtak és mikor. (előzetes verzió)

## <a name="how-resource-graph-is-kept-current"></a>Az erőforrás-gráf naprakészen tartása

Az Azure-erőforrások frissítésekor a változás erőforrás-kezelője értesíti az erőforrás-diagramot.
Az erőforrás-gráf ezután frissíti az adatbázisát. Az erőforrás-gráf emellett normál _teljes vizsgálatot_is végez. Ez a vizsgálat biztosítja, hogy az erőforrás-gráf-adat aktuális, ha kimaradt értesítések vannak, vagy ha egy erőforrást a Resource Manageren kívülről frissítenek.

## <a name="the-query-language"></a>A lekérdezőnyelv

Most, hogy már jobban érti az Azure Resource Graph lényegét, ismerkedjünk meg közelebbről a lekérdezések összeállításával.

Fontos tisztában lenni azzal, hogy az Azure Resource Graph lekérdezési nyelve az Azure Adatkezelő által használt [Kusto](../../data-explorer/data-explorer-overview.md) -lekérdezési nyelven alapul.

Első lépésként olvassa el az Azure Resource Graphfal használható műveleteket és funkciókat ismertető, [a Resource Graph lekérdezőnyelve](./concepts/query-language.md) című cikket.
Az erőforrások tallózását az [erőforrások kezeléséről](./concepts/explore-resources.md) szóló cikk írja le.

## <a name="permissions-in-azure-resource-graph"></a>Engedélyek az Azure Resource Graphban

A Resource Graph használatához megfelelő jogosultságokkal kell rendelkeznie a [szerepköralapú hozzáférés-vezérlésben](../../role-based-access-control/overview.md) (RBAC), és legalább olvasási jogosultsággal kell rendelkeznie a lekérdezni kívánt erőforrásokon. Ha nem rendelkezik legalább `read` engedélyekkel az Azure-objektumhoz vagy -objektumcsoporthoz, a rendszer nem ad vissza eredményeket.

> [!NOTE]
> Az erőforrás-diagram a rendszerbiztonsági tag számára elérhető előfizetéseket használja a bejelentkezés során. Egy aktív munkamenet során hozzáadott új előfizetés erőforrásainak megtekintéséhez a rendszerbiztonsági tag frissítenie kell a környezetet. Ez a művelet automatikusan megtörténik a kijelentkezéskor és vissza.

Az Azure CLI és Azure PowerShell olyan előfizetéseket használ, amelyekhez a felhasználónak hozzáférése van. REST API közvetlen használatakor az előfizetési listát a felhasználó kapja meg. Ha a felhasználó hozzáfér a listában szereplő egyik előfizetéshez, a rendszer visszaadja a lekérdezés eredményét azon előfizetések esetében, amelyekhez a felhasználónak hozzáférése van. Ez a viselkedés ugyanaz, mint az erőforráscsoportok meghívásakor [– listázza](/rest/api/resources/resourcegroups/list) \- azokat az erőforráscsoportokat, amelyekhez hozzáférése van, anélkül, hogy az eredmény részleges lenne.
Ha nincsenek olyan előfizetések az előfizetések listájában, amelyhez a felhasználó megfelelő jogosultsággal rendelkezik, a válasz _403_ (tiltott).

## <a name="throttling"></a>Szabályozás

Ingyenes szolgáltatásként az erőforrás-gráfra irányuló lekérdezések szabályozva vannak, így biztosítva a legjobb élményt és a válaszadási időt az összes ügyfél számára. Ha a szervezet a nagyméretű és gyakori lekérdezésekhez Graph API erőforrást szeretné használni, használja a "visszajelzés" lehetőséget az erőforrás- [gráf portál oldalon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Adja meg az üzleti esetét, és válassza a "Microsoft e-mail küldése a visszajelzéshez" jelölőnégyzetet, hogy a csapat felvegye Önnel a kapcsolatot.

Az erőforrás-gráf a lekérdezéseket a felhasználói szinten szabályozza. A szolgáltatás válasza a következő HTTP-fejléceket tartalmazza:

- `x-ms-user-quota-remaining`(int): A felhasználó fennmaradó erőforrás-kvótája. Ez az érték leképezi a lekérdezések darabszámát.
- `x-ms-user-quota-resets-after`(óó: PP: mm): Az időtartam, amíg a felhasználó kvótájának felhasználását vissza nem állítja

További információ: [útmutatás a szabályozott kérelmekhez](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Az első lekérdezés futtatása

Az erőforrás-gráf támogatja az Azure CLI-t, a Azure PowerShell és a .NET-hez készült Azure SDK-t. A lekérdezés minden nyelv esetében azonos szerkezetű. Útmutató a Resource Graph engedélyezéséhez az [Azure CLI-ben](first-query-azurecli.md#add-the-resource-graph-extension) és az [Azure PowerShellben](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>További lépések

- Futtassa az első lekérdezést az [Azure CLI](first-query-azurecli.md)-vel.
- Futtassa az első lekérdezést a [Azure PowerShell](first-query-powershell.md).
- Kezdje a [](./samples/starter.md)kezdő lekérdezésekkel.
- Fokozza a [speciális lekérdezések](./samples/advanced.md)megismerését.