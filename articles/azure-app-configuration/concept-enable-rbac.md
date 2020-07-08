---
title: Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával
description: A RBAC engedélyezése az Azure app Configuration-példányhoz való hozzáférés engedélyezéséhez
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: c2812219e689cb42fd871f85300239a10ab0da0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116723"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával
A kivonatoló alapú üzenethitelesítő kód (HMAC) használata mellett az Azure app Configuration támogatja a Azure Active Directory (Azure AD) használatát az alkalmazás-konfigurációs példányok kéréseinek engedélyezéséhez.  Az Azure AD lehetővé teszi a szerepköralapú hozzáférés-vezérlés (RBAC) használatát a rendszerbiztonsági tag engedélyeinek megadásához.  Egy rendszerbiztonsági tag lehet egy felhasználó, egy [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) vagy egy [egyszerű alkalmazás](../active-directory/develop/app-objects-and-service-principals.md).  A szerepkörökkel és a szerepkör-hozzárendelésekkel kapcsolatos további tudnivalókért tekintse meg a [különböző szerepkörök ismertetése](../role-based-access-control/overview.md)című témakört.

## <a name="overview"></a>Áttekintés
A rendszerbiztonsági tag által az alkalmazás-konfigurációs erőforráshoz való hozzáférésre vonatkozó kéréseket engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat:
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
- **Közreműködő**: használja ezt a szerepkört az alkalmazás konfigurációs erőforrásának kezeléséhez. Az alkalmazás konfigurációs adatai hozzáférési kulcsok használatával érhetők el, ez a szerepkör nem biztosít közvetlen hozzáférést az Azure AD-vel az adathoz.
- **Olvasó**: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs erőforrásához. Ez nem biztosít hozzáférést az erőforrás elérési kulcsaihoz, és nem az alkalmazás konfigurációjában tárolt adathoz.

> [!NOTE]
> Jelenleg a Azure Portal és a CLI csak a HMAC-hitelesítést támogatja az alkalmazás konfigurációs információinak eléréséhez. Az Azure AD-hitelesítés nem támogatott. Ezért a Azure Portal és a CLI felhasználói számára a *közreműködő* szerepkört kell beszereznie az alkalmazás konfigurációs erőforrásának hozzáférési kulcsainak lekéréséhez. Az *alkalmazás konfigurációs Adatolvasójának* vagy az *alkalmazás konfigurációs adattulajdonosi* szerepköreinek megadása nincs hatással a Portálon és a CLI-n keresztül történő hozzáférésre.

## <a name="next-steps"></a>További lépések
További információ a [felügyelt identitások](howto-integrate-azure-managed-service-identity.md) használatáról az alkalmazás konfigurációs szolgáltatásának kezeléséhez.
