---
title: Mi az Azure-erőforrásokhoz (RBAC) való szerepköralapú hozzáférés-vezérlés? | Microsoft Docs
description: Tekintse át az Azure-erőforrások szerepköralapú hozzáférés-vezérlésének (RBAC) áttekintését. Szerepkör-hozzárendelések használata az Azure-erőforrásokhoz való hozzáférés szabályozásához.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4a1db200b88d0eabde967961d956cdd2854e828d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81686477"
---
# <a name="what-is-role-based-access-control-rbac-for-azure-resources"></a>Mi az Azure-erőforrásokhoz (RBAC) való szerepköralapú hozzáférés-vezérlés?

A felhőbeli erőforrások hozzáférésének kezelése kritikus fontosságú a felhőt használó vállalatok számára. A szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi annak meghatározását, kik férhetnek hozzá az Azure-erőforrásokhoz, mire használhatják őket, és mely területekhez kapnak hozzáférést.

A RBAC egy [Azure Resource Managerra](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer, amely az Azure-erőforrások részletes hozzáférés-kezelését teszi lehetővé.

## <a name="what-can-i-do-with-rbac"></a>Mire használhatom az RBAC-t?

Néhány példa arra, milyen lehetőségeket biztosít az RBAC:

- Az egyik felhasználó számára engedélyezheti az előfizetésben lévő virtuális gépek, a másik felhasználó számára pedig a virtuális hálózatok kezelését.
- Engedélyezheti egy adatbázis-rendszergazdai csoport számára az előfizetésben lévő SQL-adatbázisok kezelését.
- Engedélyezheti egy felhasználó számára az egy adott erőforráscsoportba tartozó összes erőforrás, például a virtuális gépek, a webhelyek és az alhálózatok kezelését.
- Engedélyezheti egy alkalmazás számára az egy adott erőforráscsoportba tartozó összes erőforrás elérését.

## <a name="how-rbac-works"></a>Az RBAC működése

A szerepköralapú hozzáférés-vezérlés keretében az erőforrások elérését szerepkör-hozzárendelések létrehozásával szabályozhatja. Ez a legfontosabb elképzelés az, hogy a rendszer hogyan érvényesíti az engedélyeket. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből.

### <a name="security-principal"></a>Rendszerbiztonsági tag

A *rendszerbiztonsági tag* egy olyan objektum, amely az Azure-erőforrásokhoz való hozzáférést kérő felhasználót, csoportot, egyszerű szolgáltatásnevet vagy felügyelt identitást jelöl.

![Szerepkör-hozzárendelések rendszerbiztonsági tagjai](./media/overview/rbac-security-principal.png)

- Felhasználó – Egy Azure Active Directory-profillal rendelkező személy. Más bérlőkön lévő felhasználókhoz is hozzárendelhet szerepköröket. A más vállalatok felhasználóira vonatkozó információkért lásd: [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Csoport – Felhasználók az Azure Active Directoryban létrehozott csoportja. Ha egy csoporthoz rendel hozzá egy szerepkört, a csoportban lévő összes felhasználó rendelkezik majd az adott szerepkörrel. 
- Szolgáltatásnév – Alkalmazások vagy szolgáltatások által adott Azure-erőforrások elérésére használt biztonsági identitás. Úgy képzelhető el, mint egy alkalmazás *felhasználói identitása* (felhasználóneve és jelszava vagy tanúsítványa).
- Felügyelt identitás – az Azure által automatikusan kezelt Azure Active Directory identitás. A Felhőbeli alkalmazások fejlesztése során jellemzően [felügyelt identitásokat](../active-directory/managed-identities-azure-resources/overview.md) használ az Azure-szolgáltatásokhoz való hitelesítéshez szükséges hitelesítő adatok kezeléséhez.

### <a name="role-definition"></a>Szerepkör-definíció

A *szerepkör-definíció* engedélyek gyűjteménye. Általában csak *szerepkörnek*nevezik. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). A szerepkör lehet magas szintű (például tulajdonos) vagy specifikus (például virtuálisgép-olvasó).

![Szerepkör-hozzárendelések szerepkör-definíciója](./media/overview/rbac-role-definition.png)

Az Azure számos [beépített szerepkört](built-in-roles.md) biztosít, amelyeket használhat. Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

- [Tulajdonos](built-in-roles.md#owner) – teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a hozzáférés mások számára való delegálásának jogát is.
- [Közreműködő](built-in-roles.md#contributor) – az összes típusú Azure-erőforrást létrehozhatja és kezelheti, de mások számára nem biztosít hozzáférést.
- [Olvasó](built-in-roles.md#reader) – megtekintheti a meglévő Azure-erőforrásokat.
- [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) – kezelheti a felhasználók Azure-erőforrásokhoz való hozzáférését.

A többi beépített szerepkör adott Azure-erőforrások kezelését teszi lehetővé. Például a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni szerepköröket az Azure-erőforrásokhoz](custom-roles.md).

Az Azure olyan adatműveletekkel rendelkezik, amelyek lehetővé teszik az adatokhoz való hozzáférés megadását egy adott objektumon belül. Ha például egy felhasználó adatolvasási hozzáféréssel rendelkezik egy tárfiókhoz, akkor olvashatja az adott tárfiókban lévő blobokat vagy üzeneteket. További információ: [Az Azure-erőforrások szerepkör-definícióinak megismerése](role-definitions.md).

### <a name="scope"></a>Hatókör

A *hatókör* azon erőforrások készlete, amelyekre a hozzáférés vonatkozik. Szerepkörök hozzárendelésekor hatókör meghatározásával tovább korlátozhatja az engedélyezett műveletek körét. Ez hasznos lehet például, ha egy felhasználót [Webhelyek közreműködője](built-in-roles.md#website-contributor) szerepkörrel szeretne felruházni, de csak egyetlen erőforráscsoportra vonatkozóan.

Az Azure-ban a hatókörök több szinten határozhatók meg: a [felügyeleti csoport](../governance/management-groups/overview.md), az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. A hatókörök szülő-gyermek (származtatott) kapcsolatokba vannak rendezve.

![Szerepkör-hozzárendelés hatóköre](./media/overview/rbac-scope.png)

Amikor hozzáférést rendel egy szülő hatókörhöz, az engedélyeket azok származtatott hatókörei is öröklik. Például:

- Ha [tulajdonosi](built-in-roles.md#owner) szerepkört rendel egy, a felügyeleti csoport hatóköréhez tartozó felhasználóhoz, a felhasználó a felügyeleti csoportban lévő összes előfizetésben mindent felügyelhet.
- Ha az [Olvasó](built-in-roles.md#reader) szerepkört hozzárendeli egy csoporthoz az előfizetés hatókörében, a csoport tagjai az előfizetésben lévő összes erőforráscsoportot és erőforrást megtekinthetik.
- Ha a [Közreműködő](built-in-roles.md#contributor) szerepkört hozzárendeli egy alkalmazáshoz az erőforrás-csoport hatókörében, az alkalmazás bármilyen típusú erőforrást kezelhet majd az adott erőforráscsoportban, az előfizetésben lévő többi erőforráscsoportban azonban nem.

### <a name="role-assignments"></a>Szerepkör-hozzárendelések

A *szerepkör-hozzárendelés* az a folyamat, amellyel a szerepkör-definíciót egy adott hatókörhöz tartozó felhasználóhoz, csoporthoz, egyszerű szolgáltatáshoz vagy felügyelt identitáshoz csatlakoztathatja a hozzáférés biztosítása céljából. A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza.

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában a Marketing csoporthoz hozzárendeltük a [Közreműködő](built-in-roles.md#contributor) szerepkört a gyógyszer-értékesítési erőforráscsoportra vonatkozóan. Ez azt jelenti, hogy a Marketing csoportba tartozó felhasználók bármilyen Azure-erőforrást létrehozhatnak és kezelhetnek a gyógyszer-értékesítési erőforráscsoportban. A Marketing felhasználói a gyógyszer-értékesítési erőforráscsoporton kívüli erőforrásokhoz nem rendelkeznek hozzáféréssel, ha nem tagjai másik szerepkör-hozzárendelésnek is.

![Hozzáférés-vezérlés szerepkör-hozzárendeléssel](./media/overview/rbac-overview.png)

Szerepkör-hozzárendeléseket az Azure Portal, az Azure CLI, az Azure PowerShell, az Azure SDK-k vagy REST API-k használatával hozhat létre. Az egyes előfizetésekben és a **500** szerepkör-hozzárendelésekben akár **2000** szerepkör-hozzárendelést is megadhat az egyes felügyeleti csoportokban. Szerepkör-hozzárendelések létrehozásához és eltávolításához `Microsoft.Authorization/roleAssignments/*` engedély szükséges. Ez a [Tulajdonos](built-in-roles.md#owner) vagy a [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) szerepkörrel biztosítható.

## <a name="multiple-role-assignments"></a>Több szerepkör-hozzárendelés

Tehát mi történik, ha több átfedésben lévő szerepkör-hozzárendelés van? A RBAC egy adalékanyag-modell, így az érvényes engedélyek a szerepkör-hozzárendelések összege. Vegye figyelembe a következő példát, amikor egy felhasználó megkapja a közreműködő szerepkört az előfizetés hatókörében, valamint az olvasó szerepkört egy erőforráscsoporthoz. A közreműködői engedélyek és az olvasói engedélyek összege ténylegesen az erőforráscsoport közreműködői szerepköre. Ezért ebben az esetben az olvasó szerepkör-hozzárendelésnek nincs hatása.

![Több szerepkör-hozzárendelés](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Megtagadás-hozzárendelések

Korábban az RBAC csak megengedő, megtagadás nélküli modell volt, de az RBAC jelenleg korlátozott módon támogatja a megtagadás-hozzárendeléseket. A szerepkör-hozzárendeléshez hasonlóan a *megtagadási hozzárendelések* egy adott hatókörben egy felhasználóhoz, csoporthoz, egyszerű szolgáltatáshoz vagy felügyelt identitáshoz tartozó megtagadási műveletet rendelnek a hozzáférés megtagadása céljából. A szerepkör-hozzárendelések olyan műveletek készletét határozzák meg, amelyek *engedélyezve*vannak, míg a megtagadási hozzárendelések olyan műveletek készletét határozzák meg, amelyek *nem engedélyezettek*. Vagyis a megtagadás-hozzárendelések akkor is meggátolják, hogy a felhasználók elvégezzék a megadott műveleteket, ha egy szerepkör-hozzárendelés hozzáférést nyújt azokhoz. A megtagadás-hozzárendelések elsőbbséget élveznek a szerepkör-hozzárendelésekkel szemben. További információ: [Az Azure-erőforrások megtagadási hozzárendeléseinek megismerése](deny-assignments.md).

## <a name="how-rbac-determines-if-a-user-has-access-to-a-resource"></a>Hogyan határozza meg az RBAC, hogy egy felhasználó rendelkezik-e hozzáféréssel egy erőforráshoz?

Az RBAC a következő főbb lépésekkel határozza meg, hogy Ön hozzáféréssel rendelkezik-e egy erőforráshoz a felügyeleti síkon. Ennek ismerete hozzáférési problémák elhárításakor lehet hasznos.

1. A felhasználó (vagy szolgáltatásnév) jogkivonatot szerez be az Azure Resource Managerhez.

    A jogkivonatban szerepelnek a felhasználó csoporttagságai (beleértve a tranzitív csoporttagságokat is).

1. A felhasználó REST API-hívást indít az Azure Resource Managerhez a csatolt jogkivonattal.

1. Az Azure Resource Manager lekéri az azon erőforrásra érvényes összes szerepkör-hozzárendelést és megtagadás-hozzárendelést, amelyen a művelet történik.

1. Az Azure Resource Manager leszűkíti a felhasználóra vagy a csoportjára érvényes szerepkör-hozzárendeléseket, és meghatározza, hogy a felhasználó milyen szerepkörökkel rendelkezik ehhez az erőforráshoz.

1. Az Azure Resource Manager meghatározza, hogy az API-hívásban lévő művelet szerepel-e a felhasználó ezen erőforrásra vonatkozó szerepköreiben.

1. Ha a felhasználó nem rendelkezik szerepkörrel a kért hatókörben lévő művelettel, a hozzáférés nem adható meg. Ha rendelkezik a megfelelő szerepkörrel az Azure Resource Manager ellenőrzi, hogy van-e érvényes megtagadás-hozzárendelés.

1. Ha van érvényes megtagadás-hozzárendelés, megtagadja a hozzáférést. Egyéb esetben hozzáférést biztosít.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>További lépések

- [Gyors útmutató: a felhasználó hozzáférésének megtekintése az Azure-erőforrásokhoz a Azure Portal használatával](check-access.md)
- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](role-assignments-portal.md)
- [A különböző Azure-beli szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
- [Felhőalapú bevezetési keretrendszer: erőforrás-hozzáférés kezelése az Azure-ban](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
