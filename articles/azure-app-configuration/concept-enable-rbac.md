---
title: Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával
description: A RBAC engedélyezése az Azure app Configuration-példányhoz való hozzáférés engedélyezéséhez
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 3ec30aafe63259237a89de6597970b908fb969cf
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773440"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával
Az Azure app Configuration a Azure Active Directory (Azure AD) használatával támogatja az alkalmazás-konfigurációs példányokra irányuló kérések engedélyezését.  Az Azure AD lehetővé teszi a szerepköralapú hozzáférés-vezérlés (RBAC) használatát a rendszerbiztonsági tag engedélyeinek megadásához.  Egy rendszerbiztonsági tag lehet egy felhasználó vagy egy [egyszerű alkalmazás](../active-directory/develop/app-objects-and-service-principals.md).  A szerepkörökkel és a szerepkör-hozzárendelésekkel kapcsolatos további tudnivalókért tekintse meg a [különböző szerepkörök ismertetése](../role-based-access-control/overview.md)című témakört.

## <a name="overview"></a>Áttekintés
Az alkalmazás-konfigurációs erőforrás eléréséhez a rendszerbiztonsági tag (felhasználó vagy alkalmazás) által kezdeményezett kérelmeket engedélyezni kell.  Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat.
1. A rendszerbiztonsági tag identitása hitelesítve van, és a rendszer OAuth 2,0 tokent ad vissza.  A jogkivonatot kérő erőforrás neve `https://login.microsoftonline.com/{tenantID}` `{tenantID}` megegyezik azzal a Azure Active Directory BÉRLŐi azonosítóval, amelyhez az egyszerű szolgáltatásnév tartozik.
2. A jogkivonat a megadott erőforráshoz való hozzáférés engedélyezésére irányuló kérelem részeként lett átadva az alkalmazás konfigurációs szolgáltatásának.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben.  Ha egy alkalmazás egy Azure-entitáson belül fut, például egy Azure Functions alkalmazásban, egy Azure-webalkalmazásban vagy egy Azure-beli virtuális gépen, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz.  Ha meg szeretné tudni, hogyan hitelesítheti a felügyelt identitások által kezdeményezett kérelmeket az Azure app Configuration szolgáltatásban, tekintse meg az Azure [-erőforrásokhoz való hozzáférés hitelesítése Azure Active Directory és felügyelt identitások](howto-integrate-azure-managed-service-identity.md)használatával című témakört.

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. Az Azure app Configuration olyan RBAC-szerepköröket biztosít, amelyek magukban foglalják az alkalmazás-konfigurációs erőforrások engedélyeinek készletét. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg a résztvevő számára biztosított engedélyeket. További információ a RBAC szerepköreiről: [beépített RBAC-szerepkörök az Azure app Configuration szolgáltatáshoz](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Hozzáférési jogosultságok RBAC-szerepköreinek kiosztása
Azure Active Directory (Azure AD) a [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz.

Ha egy Azure AD-rendszerbiztonsági tag egy RBAC-szerepkört rendel hozzá, az Azure hozzáférést biztosít ezen rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az alkalmazás konfigurációs erőforrása. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó vagy egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Az Azure-alkalmazások konfigurációjának beépített RBAC szerepkörei
Az Azure az alábbi beépített RBAC szerepköröket biztosítja az alkalmazás konfigurációs adataihoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- **Alkalmazás-konfigurációs adat tulajdonosa**: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.
- **Alkalmazás-konfigurációs Adatolvasó**: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.
- **Közreműködő**: használja ezt a szerepkört az alkalmazás konfigurációs erőforrásának kezeléséhez. Az alkalmazás konfigurációs adatai hozzáférési kulcsok használatával érhetők el, ez a szerepkör nem biztosít hozzáférést az Azure AD-vel az adathoz.
- **Olvasó**: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs erőforrásához. Ez nem biztosít hozzáférést az erőforrás elérési kulcsaihoz, és nem az alkalmazás konfigurációjában tárolt adathoz.

## <a name="next-steps"></a>További lépések
További információ a [felügyelt identitások](howto-integrate-azure-managed-service-identity.md) használatáról az alkalmazás konfigurációs szolgáltatásának kezeléséhez.
