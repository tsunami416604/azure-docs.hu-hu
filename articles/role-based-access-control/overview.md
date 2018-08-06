---
title: Mi az a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-ban? | Microsoft Docs
description: Az Azure-beli szerepköralapú hozzáférés-vezérlés (RBAC) áttekintése. Az Azure-beli erőforrások elérését szerepkörök hozzárendelésével vezérelheti.
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
ms.date: 07/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a2e0bf35f73a355197f821f7cce12294f7b35576
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344749"
---
# <a name="what-is-role-based-access-control-rbac"></a>Mi az a szerepköralapú hozzáférés-vezérlés (RBAC)?

A felhőbeli erőforrások hozzáférésének kezelése kritikus fontosságú a felhőt használó vállalatok számára. A szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi annak meghatározását, kik férhetnek hozzá az Azure-erőforrásokhoz, mire használhatják őket, és mely területekhez kapnak hozzáférést.

Az RBAC az [Azure Resource Managerre](../azure-resource-manager/resource-group-overview.md) épülő engedélyezési rendszer, amely részletes hozzáférés-kezelési lehetőségeket nyújt az Azure-beli erőforrásokhoz.

## <a name="what-can-i-do-with-rbac"></a>Mire használhatom az RBAC-t?

Néhány példa arra, milyen lehetőségeket biztosít az RBAC:

- Az egyik felhasználó számára engedélyezheti az előfizetésben lévő virtuális gépek, a másik felhasználó számára pedig a virtuális hálózatok kezelését.
- Engedélyezheti egy adatbázis-rendszergazdai csoport számára az előfizetésben lévő SQL-adatbázisok kezelését.
- Engedélyezheti egy felhasználó számára az egy adott erőforráscsoportba tartozó összes erőforrás, például a virtuális gépek, a webhelyek és az alhálózatok kezelését.
- Engedélyezheti egy alkalmazás számára az egy adott erőforráscsoportba tartozó összes erőforrás elérését.

## <a name="best-practice-for-using-rbac"></a>Ajánlott eljárások az RBAC használatához

Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van. Ahelyett, hogy mindenki számára korlátlan engedélyeket adna az Azure-előfizetéshez vagy -erőforrásokhoz, az egyes műveletekhez hatókört adhat meg.

A hozzáférés-vezérlési stratégia megtervezésekor ajánlott eljárásként csak a munkájuk elvégzéséhez minimálisan szükséges jogosultságokat biztosítsa a felhasználók számára. A következő ábrán az RBAC használatának egy javasolt mintája látható.

![RBAC és minimális jogosultságok](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>Az RBAC működése

A szerepköralapú hozzáférés-vezérlés keretében az erőforrások elérését szerepkör-hozzárendelések létrehozásával szabályozhatja. Ez egy alapvető fogalom – ez határozza meg az engedélyek kikényszerítését. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből.

### <a name="security-principal"></a>Rendszerbiztonsági tag

A *rendszerbiztonsági tag* egy olyan objektum, amely egy, az Azure-erőforrásokhoz hozzáférést igénylő felhasználót, csoportot vagy szolgáltatásnevet jelöl.

![Szerepkör-hozzárendelések rendszerbiztonsági tagjai](./media/overview/rbac-security-principal.png)

- Felhasználó – Egy Azure Active Directory-profillal rendelkező személy. Más bérlőkön lévő felhasználókhoz is hozzárendelhet szerepköröket. A más vállalatok felhasználóira vonatkozó információkért lásd: [Azure Active Directory B2B](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Csoport – Felhasználók az Azure Active Directoryban létrehozott csoportja. Ha egy csoporthoz rendel hozzá egy szerepkört, a csoportban lévő összes felhasználó rendelkezik majd az adott szerepkörrel. 
- Szolgáltatásnév – Alkalmazások vagy szolgáltatások által adott Azure-erőforrások elérésére használt biztonsági identitás. Úgy képzelhető el, mint egy alkalmazás *felhasználói identitása* (felhasználóneve és jelszava vagy tanúsítványa).

### <a name="role-definition"></a>Szerepkör-definíció

A *szerepkör-definíció* engedélyek gyűjteménye. Szokás egyszerűen csak *szerepkörnek* is nevezni. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). A szerepkör lehet magas szintű (például tulajdonos) vagy specifikus (például virtuálisgép-olvasó).

![Szerepkör-hozzárendelések szerepkör-definíciója](./media/overview/rbac-role-definition.png)

Az Azure számos [beépített szerepkört](built-in-roles.md) biztosít, amelyeket használhat. Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

- [Tulajdonos](built-in-roles.md#owner) – teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a hozzáférés mások számára való delegálásának jogát is.
- [Közreműködő](built-in-roles.md#contributor) – bármilyen típusú Azure-erőforrást létrehozhat és kezelhet, hozzáférést azonban nem adhat mások számára.
- [Olvasó](built-in-roles.md#reader) – megtekintheti a meglévő Azure-erőforrásokat.
- [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) – kezelheti a felhasználók Azure-erőforrásokhoz való hozzáférését.

A többi beépített szerepkör adott Azure-erőforrások kezelését teszi lehetővé. Például a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé. Ha a beépített szerepkörök nem felelnek meg a cég vagy intézmény igényeinek, saját [egyéni szerepköröket](custom-roles.md) is létrehozhat.

Az Azure-ban bevezettünk különféle adatműveleteket (ezek jelenleg előzetes verzióban érhetők el), amelyek használatával az egyes objektumokban lévő adatokhoz biztosítható hozzáférés. Ha például egy felhasználó adatolvasási hozzáféréssel rendelkezik egy tárfiókhoz, akkor olvashatja az adott tárfiókban lévő blobokat vagy üzeneteket. További információt [a szerepkör-definíciókat ismertető](role-definitions.md) témakörben olvashat.

### <a name="scope"></a>Hatókör

A *hatókör* az a határ, ameddig a hozzáférés terjed. Szerepkörök hozzárendelésekor hatókör meghatározásával tovább korlátozhatja az engedélyezett műveletek körét. Ez hasznos lehet például, ha egy felhasználót [Webhelyek közreműködője](built-in-roles.md#website-contributor) szerepkörrel szeretne felruházni, de csak egyetlen erőforráscsoportra vonatkozóan.

Az Azure-ban a hatókörök több szinten határozhatók meg: az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. A hatókörök szülő-gyermek kapcsolatokba vannak strukturálva, ahol minden gyermeknek csak egy szülője lehet.

![Szerepkör-hozzárendelés hatóköre](./media/overview/rbac-scope.png)

A szülő hatókörökhöz rendelt hozzáféréseket azok gyermekei öröklik. Például:

- Ha az [Olvasó](built-in-roles.md#reader) szerepkört hozzárendeli egy csoporthoz az előfizetés hatókörében, a csoport tagjai az előfizetésben lévő összes erőforráscsoportot és erőforrást megtekinthetik.
- Ha a [Közreműködő](built-in-roles.md#contributor) szerepkört hozzárendeli egy alkalmazáshoz az erőforrás-csoport hatókörében, az alkalmazás bármilyen típusú erőforrást kezelhet majd az adott erőforráscsoportban, az előfizetésben lévő többi erőforráscsoportban azonban nem.

Az Azure tartalmaz továbbá egy, az előfizetések feletti hatókört is: ez a [felügyeleti csoport](../azure-resource-manager/management-groups-overview.md), amely jelenleg előzetes verzióban érhető el. A felügyeleti csoportokkal több előfizetés kezelhető. RBAC-hatókörök megadásakor megadhat egy felügyeleti csoportot, vagy megadhat egy előfizetést, egy erőforráscsoportot vagy egy erőforrás-hierarchiát.

### <a name="role-assignment"></a>Szerepkör-kijelölés

A *szerepkör-hozzárendelés* során egy szerepkör-definíciót rendelünk egy felhasználóhoz, csoporthoz vagy szolgáltatásnévhez egy adott hatókör mellett hozzáférés biztosítása érdekében. A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza.

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában a Marketing csoporthoz hozzárendeltük a [Közreműködő](built-in-roles.md#contributor) szerepkört a gyógyszer-értékesítési erőforráscsoportra vonatkozóan. Ez azt jelenti, hogy a Marketing csoportba tartozó felhasználók bármilyen Azure-erőforrást létrehozhatnak és kezelhetnek a gyógyszer-értékesítési erőforráscsoportban. A Marketing felhasználói a gyógyszer-értékesítési erőforráscsoporton kívüli erőforrásokhoz nem rendelkeznek hozzáféréssel, ha nem tagjai másik szerepkör-hozzárendelésnek is.

![Hozzáférés-vezérlés szerepkör-hozzárendeléssel](./media/overview/rbac-overview.png)

Szerepkör-hozzárendeléseket az Azure Portal, az Azure CLI, az Azure PowerShell, az Azure SDK-k vagy REST API-k használatával hozhat létre. Mindegyik előfizetésben legfeljebb 2000 szerepkör-hozzárendeléssel rendelkezhet. Szerepkör-hozzárendelések létrehozásához és eltávolításához `Microsoft.Authorization/roleAssignments/*` engedély szükséges. Ez a [Tulajdonos](built-in-roles.md#owner) vagy a [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) szerepkörrel biztosítható.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure Portal használatával](quickstart-assign-role-user-portal.md)
- [Hozzáférés kezelése az RBAC és az Azure Portal használatával](role-assignments-portal.md)
- [A különböző Azure-beli szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
