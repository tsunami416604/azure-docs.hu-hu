---
title: Az Azure Storage-erőforrás-szolgáltató használata a felügyeleti erőforrások eléréséhez
description: Az Azure Storage-erőforrás-szolgáltató egy olyan szolgáltatás, amely hozzáférést biztosít az Azure Storage felügyeleti erőforrásokhoz. Az Azure Storage-erőforrás-szolgáltató használatával hozhat létre, frissítheti, kezelheti és törölheti az erőforrásokat, például a tárfiókokat, a magánvégpontokat és a fiókhozzáférési kulcsokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972347"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Az Azure Storage-erőforrás-szolgáltató használata a felügyeleti erőforrások eléréséhez

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Az Azure Storage-erőforrás-szolgáltató egy olyan szolgáltatás, amely az Azure Resource Manageren alapul, és hozzáférést biztosít az Azure Storage felügyeleti erőforrásaihoz. Az Azure Storage-erőforrás-szolgáltató használatával hozhat létre, frissítheti, kezelheti és törölheti az erőforrásokat, például a tárfiókokat, a magánvégpontokat és a fiókhozzáférési kulcsokat. Az Azure Resource Managerről az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](/azure/azure-resource-manager/resource-group-overview)

Az Azure Storage-erőforrás-szolgáltató segítségével műveleteket hajthat végre, például egy tárfiók létrehozása vagy törlése, vagy egy előfizetésben lévő tárfiókok listájának beszerzése. Az Azure Storage-erőforrás-szolgáltatóval szembeni kérelmek engedélyezéséhez használja az Azure Active Directoryt (Azure AD). Ez a cikk ismerteti, hogyan rendelhet idákat felügyeleti erőforrásokhoz, és rámutat, hogy példákat, amelyek bemutatják, hogyan lehet az Azure Storage-erőforrás-szolgáltató kérelmeket.

## <a name="management-resources-versus-data-resources"></a>Felügyeleti erőforrások és adaterőforrások

A Microsoft két REST API-t biztosít az Azure Storage-erőforrásokkal való munkához. Ezek az API-k képezik az Azure Storage-on végrehajtható összes művelet alapját. Az Azure Storage REST API lehetővé teszi, hogy a tárfiókban lévő adatokkal dolgozzon, beleértve a blobot, a várólistát, a fájlt és a táblaadatokat. Az Azure Storage-erőforrás-szolgáltató REST API lehetővé teszi, hogy a tárfiókkal és a kapcsolódó erőforrásokkal dolgozzon.

Blobadatok olvasására vagy írására irányuló kérelem más engedélyeket igényel, mint egy felügyeleti műveletet végző kérelem. Az RBAC mindkét típusú erőforrás engedélyeinek részletes vezérlését biztosítja. Amikor rbac szerepkört rendel egy rendszerbiztonsági taghoz, győződjön meg arról, hogy megértette, hogy milyen engedélyeket kap a rendszernévi tag. Az Azure-erőforrások beépített szerepkörei című részletes útmutatót, amely leírja, hogy mely műveletek vannak társítva az egyes beépített RBAC-szerepkörökhöz, lásd: Beépített szerepkörök az [Azure-erőforrásokhoz.](../../role-based-access-control/built-in-roles.md)

Az Azure Storage támogatja az Azure AD használatát a Blob és a Queue storage elleni kérelmek engedélyezéséhez. A blob- és várólista-adatműveletek RBAC-szerepköreiről az [Active Directory használatával a blobokhoz és várólistákhoz való hozzáférés engedélyezése című témakörben](storage-auth-aad.md)talál további információt.

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Felügyeleti engedélyek hozzárendelése szerepköralapú hozzáférés-vezérléssel (RBAC)

Minden Azure-előfizetéshez tartozik egy Azure Active Directory, amely kezeli a felhasználókat, csoportokat és alkalmazásokat. A microsoftos [identitáskezelési platform](/azure/active-directory/develop/)környezetében egy felhasználót, csoportot vagy alkalmazást rendszerbiztonsági tagnak is nevezünk. Az előfizetésben lévő erőforrásokhoz szerepköralapú hozzáférés-vezérlés (RBAC) használatával hozzáférést biztosíthat az Active Directoryban definiált rendszerbiztonsági tag hoz.

Amikor rbac szerepkört rendel egy rendszerbiztonsági taghoz, azt is jelzi, hogy a szerepkör által megadott engedélyek milyen hatókörben vannak érvényben. A felügyeleti műveletekhez az előfizetés, az erőforráscsoport vagy a tárfiók szintjén rendelhet hozzá egy szerepkört. RBAC-szerepkört rendelhet egy rendszerbiztonsági taghoz az [Azure Portal](https://portal.azure.com/), az [Azure CLI-eszközök](../../cli-install-nodejs.md), a [PowerShell](/powershell/azureps-cmdlets-docs)vagy az [Azure Storage-erőforrás-szolgáltató REST API](/rest/api/storagerp)használatával.

Az RBAC-ról további információt a [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz](../../role-based-access-control/overview.md) és [a klasszikus előfizetéses rendszergazdai szerepkörökhöz, az Azure RBAC-szerepkörökhöz és az Azure AD-rendszergazdai szerepkörökhöz](../../role-based-access-control/rbac-and-directory-admin-roles.md)című témakörben talál.

### <a name="built-in-roles-for-management-operations"></a>Beépített szerepkörök felügyeleti műveletekhez

Az Azure beépített szerepköröket biztosít, amelyek engedélyeket adnak a híváskezelési műveletekhez. Az Azure Storage beépített szerepköröket is biztosít kifejezetten az Azure Storage-erőforrás-szolgáltatóval való használatra.

A tárolási felügyeleti műveletekhívási engedélyeket adó beépített szerepkörök közé tartoznak az alábbi táblázatban leírt szerepkörök:

|    RBAC-szerepkör    |    Leírás    |    Tartalmazza a fiókkulcsokhoz való hozzáférést?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Tulajdonos** | Kezelheti az összes tárolási erőforrást és az erőforrásokhoz való hozzáférést.  | Igen, engedélyeket biztosít a tárfiók kulcsainak megtekintéséhez és újragenerálásához. |
| **Közreműködő**  | Kezelheti az összes tárolási erőforrást, de nem tudja kezelni az erőforrásokhoz való hozzárendelést. | Igen, engedélyeket biztosít a tárfiók kulcsainak megtekintéséhez és újragenerálásához. |
| **Olvasó** | Megtekintheti a tárfiók adatait, de a fiókkulcsokat nem. | Nem. |
| **Tárfiók-közreműködő** | Kezelheti a tárfiókot, információkat kaphat az előfizetés erőforráscsoportjairól és erőforrásairól, és létrehozhat és kezelhet előfizetési erőforráscsoport-telepítéseket. | Igen, engedélyeket biztosít a tárfiók kulcsainak megtekintéséhez és újragenerálásához. |
| **Felhasználói hozzáférés adminisztrátora** | Kezelheti a tárfiókhoz való hozzáférést.   | Igen, lehetővé teszi, hogy a rendszerbiztonsági tag bármilyen engedélyt hozzárendelje saját magának és másoknak. |
| **Virtuális gépek közreműködője** | Kezelheti a virtuális gépeket, de azt a tárfiókot, amelyhez csatlakoztatva vannak.   | Igen, engedélyeket biztosít a tárfiók kulcsainak megtekintéséhez és újragenerálásához. |

A táblázat harmadik oszlopa azt jelzi, hogy a beépített szerepkör támogatja-e a **Microsoft.Storage/storageAccounts/listkeys/action műveletet.** Ez a művelet engedélyeket ad a tárfiók kulcsainak olvasásához és újragenerálásához. Az Azure Storage felügyeleti erőforrásaihoz való hozzáféréshez való engedélyek nem tartalmazzák az adatok elérésére vonatkozó engedélyeket. Ha azonban egy felhasználó hozzáfér a fiókkulcsokhoz, akkor a fiókkulcsok használatával férhetnek hozzá az Azure Storage-adatok megosztott kulcs engedélyezése révén.

### <a name="custom-roles-for-management-operations"></a>Egyéni szerepkörök felügyeleti műveletekhez

Az Azure azt is támogatja, hogy egyéni RBAC-szerepköröket határozzon meg a felügyeleti erőforrásokhoz való hozzáféréshez. Az egyéni szerepkörökről az [Egyéni szerepkörök az Azure-erőforrásokhoz](../../role-based-access-control/custom-roles.md)című témakörben talál további információt.

## <a name="code-samples"></a>Kódminták

Az Azure Storage felügyeleti kódtárakból származó felügyeleti műveletek engedélyezését és híváskezelési műveleteit bemutató kódpéldákért tekintse meg a következő mintákat:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Az Azure Resource Manager és a klasszikus telepítések

A Resource Manager-alapú és a klasszikus üzemi modell két eltérő módost kínál az Azure-megoldások üzembe helyezésére és felügyeletére. A Microsoft azt javasolja, hogy az Azure Resource Manager központi telepítési modelljét, amikor új tárfiókot hoz létre. Ha lehetséges, a Microsoft azt is javasolja, hogy hozza létre újra a meglévő klasszikus tárfiókokat az Erőforrás-kezelő modellel. Bár a klasszikus üzembe helyezési modell használatával létrehozhat egy tárfiókot, a klasszikus modell kevésbé rugalmas, és végül elavult.

Az Azure üzembe helyezési modelljeiről további információt az [Erőforrás-kezelő és a klasszikus telepítés című témakörben talál.](../../azure-resource-manager/management/deployment-models.md)

## <a name="next-steps"></a>További lépések

- [Az Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview)
- [Mi az Azure-erőforrásokhoz (RBAC) való szerepköralapú hozzáférés-vezérlés?](../../role-based-access-control/overview.md)
- [Méretezhetőségi célok az Azure Storage-erőforrás-szolgáltatószámára](scalability-targets-resource-provider.md)
