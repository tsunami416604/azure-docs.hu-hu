---
title: Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez
description: Az Azure Storage erőforrás-szolgáltató egy olyan szolgáltatás, amely hozzáférést biztosít az Azure Storage felügyeleti erőforrásaihoz. Az Azure Storage erőforrás-szolgáltatója olyan erőforrások létrehozására, frissítésére, kezelésére és törlésére használható, mint a Storage-fiókok, a privát végpontok és a fiók-hozzáférési kulcsok.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e93343708ce8a89e9c6e8f57c6cebb198578e325
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805679"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Az Azure Storage erőforrás-szolgáltató a Azure Resource Manageron alapuló szolgáltatás, amely hozzáférést biztosít az Azure Storage felügyeleti erőforrásaihoz. Az Azure Storage erőforrás-szolgáltatója olyan erőforrások létrehozására, frissítésére, kezelésére és törlésére használható, mint a Storage-fiókok, a privát végpontok és a fiók-hozzáférési kulcsok. További információ a Azure Resource Managerről: [Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview).

Az Azure Storage erőforrás-szolgáltatóval olyan műveleteket hajthat végre, mint például a Storage-fiók létrehozása vagy törlése, illetve az előfizetéshez tartozó Storage-fiókok listájának beolvasása. Az Azure Storage erőforrás-szolgáltatótól érkező kérések engedélyezéséhez használja a Azure Active Directory (Azure AD) szolgáltatást. Ez a cikk azt ismerteti, hogyan rendelhet hozzá engedélyeket a felügyeleti erőforrásokhoz, és olyan példákat mutat be, amelyek bemutatják, hogyan lehet kérelmeket készíteni az Azure Storage erőforrás-szolgáltatón.

## <a name="management-resources-versus-data-resources"></a>Felügyeleti erőforrások és adatforrások

A Microsoft két REST API-t biztosít az Azure Storage-erőforrások használatához. Ezek az API-k az Azure Storage-ban végrehajtható összes művelet alapját képezik. Az Azure Storage REST API lehetővé teszi a Storage-fiókban tárolt adatkezelést, beleértve a blob-, üzenetsor-, fájl-és táblázat-adatmennyiséget. Az Azure Storage erőforrás-szolgáltató REST API lehetővé teszi a Storage-fiókkal és a kapcsolódó erőforrásokkal való munkát.

A blob-adatok olvasását vagy írását kérő kérelem eltérő engedélyeket igényel, mint a felügyeleti műveletet végrehajtó kérelem. A RBAC részletesen szabályozza az engedélyeket mindkét típusú erőforráshoz. Ha RBAC-szerepkört rendel egy rendszerbiztonsági tag számára, győződjön meg arról, hogy megértette, hogy milyen engedélyeket kap a résztvevő. Az egyes beépített RBAC-szerepkörökhöz társított műveletek részletes ismertetését lásd: [beépített szerepkörök az Azure-erőforrásokhoz](../../role-based-access-control/built-in-roles.md).

Az Azure Storage támogatja az Azure AD-t a blob-és üzenetsor-tárolással kapcsolatos kérések engedélyezéséhez. A blob-és üzenetsor-műveletek RBAC szerepköreivel kapcsolatos további információkért lásd: a [blobok és várólisták hozzáférésének engedélyezése Active Directory használatával](storage-auth-aad.md).

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Felügyeleti engedélyek kiosztása szerepköralapú hozzáférés-vezérléssel (RBAC)

Minden Azure-előfizetéshez tartozik egy társított Azure Active Directory, amely kezeli a felhasználókat, a csoportokat és az alkalmazásokat. A [Microsoft Identity platform](/azure/active-directory/develop/)kontextusában egy felhasználót, csoportot vagy alkalmazást is a rendszerbiztonsági tag néven is ismert. A szerepkör alapú hozzáférés-vezérlés (RBAC) segítségével hozzáférést biztosíthat az előfizetésben lévő erőforrásokhoz egy olyan rendszerbiztonsági tag számára, amely a Active Directoryban van definiálva.

Ha RBAC-szerepkört rendel egy rendszerbiztonsági tag számára, akkor azt a hatókört is jelzi, amelyen a szerepkör által biztosított engedélyek érvényben vannak. Felügyeleti műveletekhez hozzárendelhet egy szerepkört az előfizetés, az erőforráscsoport vagy a Storage-fiók szintjén. RBAC-szerepkört hozzárendelhet egy rendszerbiztonsági tag számára a [Azure Portal](https://portal.azure.com/), az [Azure CLI-eszközök](../../cli-install-nodejs.md), a [PowerShell](/powershell/azureps-cmdlets-docs)vagy az [Azure Storage erőforrás-szolgáltató REST API](/rest/api/storagerp)használatával.

További információ a RBAC: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz?](../../role-based-access-control/overview.md) és a [klasszikus előfizetés-rendszergazdai szerepkörök, az Azure RBAC szerepkörei és az Azure ad rendszergazdai szerepkörei](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Beépített szerepkörök felügyeleti műveletekhez

Az Azure olyan beépített szerepköröket biztosít, amelyek engedélyeket biztosítanak a felügyeleti műveletek meghívásához. Az Azure Storage beépített szerepköröket is biztosít kifejezetten az Azure Storage erőforrás-szolgáltatóval való használatra.

A tárolási felügyeleti műveletek meghívására engedélyt biztosító beépített szerepkörök közé tartoznak a következő táblázatban ismertetett szerepkörök:

|    RBAC-szerepkör    |    Leírás    |    A fiók kulcsaihoz való hozzáférést is tartalmaz?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Tulajdonos** | Képes kezelni az összes tárolási erőforrást és az erőforrásokhoz való hozzáférést.  | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |
| **Közreműködő**  | Kezelheti az összes tárolási erőforrást, de nem kezelheti az erőforrásokhoz való hozzárendelést. | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |
| **Olvasó** | Megtekintheti a Storage-fiók adatait, de nem tudja megtekinteni a fiók kulcsait. | Nem. |
| **Tárfiók-közreműködő** | Kezelheti a Storage-fiókot, lekérheti az előfizetéshez tartozó erőforráscsoportok és erőforrások adatait, valamint előfizetési erőforráscsoport-telepítéseket hozhat létre és kezelhet. | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |
| **Felhasználói hozzáférés adminisztrátora** | Kezelheti a Storage-fiókhoz való hozzáférést.   | Igen, lehetővé teszi a rendszerbiztonsági tag számára, hogy engedélyeket rendeljenek magukhoz és másokhoz. |
| **Virtuális gépek közreműködője** | Felügyelheti a virtuális gépeket, de nem azt a Storage-fiókot, amelyhez csatlakoznak.   | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |

A tábla harmadik oszlopa azt jelzi, hogy a beépített szerepkör támogatja-e a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**. Ez a művelet engedélyt ad a Storage-fiók kulcsainak olvasására és újbóli előállítására. Az Azure Storage felügyeleti erőforrásainak eléréséhez szükséges engedélyek nem tartalmazzák az adathozzáférésre vonatkozó engedélyeket is. Ha azonban egy felhasználó hozzáfér a fiók kulcsaihoz, akkor a fiókok kulcsaival érheti el az Azure Storage-adatvédelmet a megosztott kulcsos hitelesítésen keresztül.

### <a name="custom-roles-for-management-operations"></a>Egyéni szerepkörök felügyeleti műveletekhez

Az Azure Emellett támogatja az egyéni RBAC-szerepkörök definiálását a felügyeleti erőforrásokhoz való hozzáféréshez. További információ az egyéni szerepkörökről: [Egyéni szerepkörök az Azure-erőforrásokhoz](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Kódminták

A következő példákban megtudhatja, hogyan engedélyezheti és hívhatja meg a felügyeleti műveleteket az Azure Storage felügyeleti könyvtáraiban:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager a klasszikus üzemelő példányok ellen

A Resource Manager-alapú és a klasszikus üzemi modell két eltérő módost kínál az Azure-megoldások üzembe helyezésére és felügyeletére. A Microsoft a Azure Resource Manager üzembe helyezési modell használatát javasolja új Storage-fiók létrehozásakor. Ha lehetséges, a Microsoft azt is javasolja, hogy a meglévő klasszikus Storage-fiókokat a Resource Manager-modellel hozza létre újra. Bár a klasszikus üzembe helyezési modellel létrehozhat egy Storage-fiókot, a klasszikus modell kevésbé rugalmas, és végül elavulttá válik.

További információ az Azure-beli üzembe helyezési modellekről: [Resource Manager és klasszikus üzembe helyezés](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>További lépések

- [Az Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview)
- [Mi az Azure-erőforrásokhoz (RBAC) való szerepköralapú hozzáférés-vezérlés?](../../role-based-access-control/overview.md)
- [Az Azure Storage erőforrás-szolgáltató skálázhatósági céljai](scalability-targets-resource-provider.md)
