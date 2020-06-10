---
title: Biztonság Azure-beli digitális Twins-megoldásokhoz
titleSuffix: Azure Digital Twins
description: Az Azure digitális ikrekkel kapcsolatos ajánlott biztonsági eljárások ismertetése.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 1464d1b8da4b9f0389b192f0876ed52ad5e4ad44
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613011"
---
# <a name="secure-azure-digital-twins-with-role-based-access-control"></a>Biztonságos Azure-beli digitális Twins szerepköralapú hozzáférés-vezérléssel

A biztonság érdekében az Azure Digital Twins pontos hozzáférés-vezérlést tesz lehetővé az üzembe helyezés meghatározott adatai, erőforrásai és műveletei számára. Ez egy, a **szerepköralapú hozzáférés-vezérlés (RBAC)** nevű részletes szerepkör és az engedélyek kezelési stratégiája segítségével történik. Az Azure-hoz készült RBAC általános elveiről [itt](../role-based-access-control/overview.md)olvashat.

## <a name="rbac-through-azure-ad"></a>RBAC az Azure AD-n keresztül

A RBAC az Azure Digital Twins [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) szolgáltatással való integrációja révén biztosítjuk.

A RBAC segítségével engedélyeket adhat egy *rendszerbiztonsági tag*számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű alkalmazás. A rendszerbiztonsági tag hitelesítve van az Azure AD-ben, és egy OAuth 2,0 tokent kap vissza. Ez a jogkivonat egy Azure Digital Twins-példány hozzáférési kérelmének engedélyezésére használható.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

Az Azure AD-vel a hozzáférés egy kétlépéses folyamat. Ha egy rendszerbiztonsági tag (felhasználó, csoport vagy alkalmazás) megpróbál hozzáférni az Azure digitális Twinshoz, a kérést *hitelesíteni* és *engedélyezni*kell. 

1. Először a rendszerbiztonsági tag identitása *hitelesítve*van, és a rendszer egy OAuth 2,0 tokent ad vissza.
2. Ezután a tokent egy, az Azure Digital Twins szolgáltatásnak küldött kérelem részeként továbbítja a rendszer a megadott erőforráshoz való hozzáférés *engedélyezéséhez* .

A hitelesítési lépés megköveteli, hogy az alkalmazás kérése OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha egy alkalmazás egy Azure-entitáson (például egy [Azure functions](../azure-functions/functions-overview.md) alkalmazáson) fut, akkor a **felügyelt identitás** használatával férhet hozzá az erőforrásokhoz. További információk a felügyelt identitásokról a következő szakaszban olvashatók.

Az engedélyezési lépés megköveteli, hogy a rendszerbiztonsági tag hozzá legyen rendelve egy RBAC-szerepkörhöz. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. Az Azure Digital Twins olyan RBAC-szerepköröket biztosít, amelyek az Azure digitális Twins erőforrásaihoz tartozó engedélyek készleteit foglalják magukban. Ezeket a szerepköröket a cikk későbbi részében ismertetjük.

Ha többet szeretne megtudni az Azure-ban támogatott szerepkörökről és szerepkör-hozzárendelésekről, tekintse meg az Azure RBAC dokumentációjának [különböző szerepköreinek megismerése](../role-based-access-control/rbac-and-directory-admin-roles.md) című témakört.

### <a name="authentication-with-managed-identities"></a>Hitelesítés felügyelt identitásokkal

Az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) egy Azure-beli szolgáltatás, amely lehetővé teszi, hogy az alkalmazás kódjának futtatásához szükséges biztonságos identitást hozzon létre. Ezt az identitást hozzáférés-vezérlési szerepkörökkel társíthatja, így egyéni engedélyeket adhat meg az alkalmazás által igényelt egyes Azure-erőforrások eléréséhez.

A felügyelt identitások esetében az Azure platform kezeli ezt a futásidejű identitást. Az alkalmazás kódjában vagy konfigurációjában nem kell tárolnia és védelemmel ellátnia a hozzáférési kulcsokat, vagy magát az identitást, vagy a hozzáféréshez szükséges erőforrásokat. Egy Azure App Service alkalmazáson belül futó Azure Digital Twins-ügyfélalkalmazás nem igényel SAS-szabályokat és-kulcsokat, vagy bármely más hozzáférési jogkivonatot. Az ügyfélalkalmazás csak az Azure Digital Twins-névtér végpont-címeként szükséges. Az alkalmazás csatlakozásakor az Azure Digital Twins a felügyelt entitás környezetét az ügyfélhez köti. Ha egy felügyelt identitáshoz van társítva, az Azure digitális Twins-ügyfele minden jóváhagyott műveletet végrehajthat. Az engedélyezést ezután egy felügyelt entitás egy Azure digitális Twins RBAC-szerepkörrel való társításával lehet megadni (lásd alább).

### <a name="authorization-rbac-roles-for-azure-digital-twins"></a>Engedélyezés: RBAC szerepkörök az Azure Digital Twins-hoz

Az Azure az alábbi beépített RBAC-szerepköröket biztosítja az Azure digitális Twins-erőforrásokhoz való hozzáférés engedélyezéséhez:
* Azure digitális ikrek tulajdonosa (előzetes verzió) – ezt a szerepkört használva teljes hozzáférést biztosíthat az Azure digitális Twins-erőforrásaihoz.
* Azure digitális ikrek olvasója (előzetes verzió) – Ez a szerepkör csak olvasási hozzáférést biztosít az Azure digitális Twins erőforrásaihoz.

A beépített szerepkörök meghatározásával kapcsolatos további információkért lásd: a szerepkör- [definíciók megismerése](../role-based-access-control/role-definitions.md) az Azure RBAC dokumentációjában. Az egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért lásd: [Egyéni szerepkörök az Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md).

A szerepköröket kétféleképpen rendelheti hozzá:
* Az Azure Digital Twins hozzáférés-vezérlés (IAM) paneljén a Azure Portalban (lásd: [szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md))
* szerepkör hozzáadására vagy eltávolítására szolgáló CLI-parancsok használatával

A részletes útmutatásért próbálja ki az [Azure Digital Twins oktatóanyagban: teljes körű megoldás összekapcsolása](tutorial-end-to-end.md).

## <a name="permission-scopes"></a>Engedélyek hatókörei

Mielőtt RBAC-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférésének hatókörét. Az ajánlott eljárások azt írják elő, hogy a legjobb, ha csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyeken az Azure Digital Twins-erőforrásokhoz való hozzáférés hatóköre elérhető.
* Modellek: az erőforrás műveletei szabályozzák az Azure digitális Twins-ban feltöltött [modellek](concepts-models.md) feletti irányítást.
* Digitális Twins-gráf lekérdezése: az erőforrás műveletei határozzák meg a [lekérdezési műveletek](concepts-query-language.md) futtatásának képességét a digitális ikrek számára az Azure digitális Twins-gráfon belül.
* Digital Twin: az erőforrás műveletei lehetővé teszik a SZIFILISZi műveletek vezérlését a [digitális ikrekben](concepts-twins-graph.md) a Twin gráfban.
* Digitális kettős kapcsolat: az erőforráshoz tartozó műveletek határozzák meg a SZIFILISZi műveletek vezérlését a digitális ikrek közötti [kapcsolatokon](concepts-twins-graph.md) a Twin gráfban.
* Esemény útvonala: az erőforrás műveletei határozzák meg az Azure digitális ikrek [eseményeinek továbbítására](concepts-route-events.md) vonatkozó engedélyeket egy végponti szolgáltatáshoz, például az [Event Hub](../event-hubs/event-hubs-about.md), a [Event Grid](../event-grid/overview.md)vagy a [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="next-steps"></a>Következő lépések

* Tekintse meg, hogyan hajthatja végre ezeket a lépéseket egy példaként szolgáló ügyfélalkalmazás használatával [: az ügyfélalkalmazás hitelesítése](how-to-authenticate-client.md).

* További információ [Az Azure](../role-based-access-control/overview.md)-beli RBAC.
