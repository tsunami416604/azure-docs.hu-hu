---
title: Hozzáférés engedélyezése az Azure App Konfigurációjához az Azure Active Directory használatával
description: Az RBAC engedélyezése az Azure App konfigurációs példányához való hozzáférés engedélyezéséhez
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472621"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Hozzáférés engedélyezése az Azure App Konfigurációjához az Azure Active Directory használatával
Az Azure App Configuration támogatja az Azure Active Directory (Azure AD) használatát az alkalmazáskonfigurációs példányok kéréseinek engedélyezéséhez.  Az Azure AD lehetővé teszi, hogy a szerepköralapú hozzáférés-vezérlés (RBAC) használatával engedélyeket adjon egy rendszerbiztonsági tag.  A rendszerbiztonsági tag lehet felhasználó vagy [egyszerű alkalmazásszolgáltatás.](../active-directory/develop/app-objects-and-service-principals.md)  Ha többet szeretne tudni a szerepkörökről és a szerepkör-hozzárendelésekről, olvassa el [a Különböző szerepkörök ismertetése című témakört.](../role-based-access-control/overview.md)

## <a name="overview"></a>Áttekintés
A rendszerbiztonsági tag (egy felhasználó vagy egy alkalmazás) által egy alkalmazáskonfigurációs erőforrás elérésére tett kérelmeket engedélyezni kell.  Az Azure AD-vel az erőforráshoz való hozzáférés két lépésből áll.
1. A rendszerbiztonsági tag identitása hitelesítve van, és egy OAuth 2.0-s jogkivonatot ad vissza.  A jogkivonat igényléséhez `https://login.microsoftonline.com/{tenantID}` szükséges `{tenantID}` erőforrás neve az, ahol megegyezik az Azure Active Directory-bérlői azonosítóval, amelyhez a szolgáltatásnév tartozik.
2. A jogkivonat egy kérelem részeként kerül átadásra az alkalmazás konfigurációs szolgáltatásának a megadott erőforráshoz való hozzáférés engedélyezéséhez.

A hitelesítési lépés megköveteli, hogy egy alkalmazáskérelem tartalmazzon egy OAuth 2.0 hozzáférési jogkivonatot futásidőben.  Ha egy alkalmazás fut egy Azure-entitáson belül, például egy Azure Functions-alkalmazás, egy Azure Web App vagy egy Azure virtuális gép, egy felügyelt identitás használatával hozzáférhet az erőforrásokhoz.  Ha meg szeretné tudni, hogyan hitelesítheti a felügyelt identitás által az Azure App Configuration szolgáltatásba tett kérelmeket, olvassa [el az Azure App Configuration erőforrásaihoz való hozzáférés hitelesítése az Azure Active Directoryval és az Azure Resources felügyelt identitásaival.](howto-integrate-azure-managed-service-identity.md)

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC szerepkörök legyenek hozzárendelve a rendszerbiztonsági taghoz. Az Azure App Configuration rbac-szerepköröket biztosít, amelyek az alkalmazáskonfigurációs erőforrások engedélykészleteit foglalják magukban. A rendszerbiztonsági taghoz rendelt szerepkörök határozzák meg a rendszerbiztonsági tagnak biztosított engedélyeket. Az RBAC-szerepkörökről további információt az [Azure App Configuration beépített RBAC szerepkörei című témakörben talál.](#built-in-rbac-roles-for-azure-app-configuration) 

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-szerepkörök hozzárendelése a hozzáférési jogokhoz
Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az Access hatóköre az alkalmazáskonfigurációs erőforráshoz tartozik. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, vagy egy alkalmazás egyszerű alkalmazás, vagy egy [felügyelt identitás az Azure-erőforrások.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Beépített RBAC-szerepkörök az Azure App Configuration szolgáltatáshoz
Az Azure a következő beépített RBAC-szerepköröket biztosítja az alkalmazáskonfigurációs adatokhoz való hozzáférés engedélyezéséhez az Azure AD és az OAuth használatával:

- Az Azure App konfigurációs adatok tulajdonosa: Ezzel a szerepkörral olvasási/írási hozzáférést adhat az alkalmazáskonfigurációs erőforrásokhoz.
- Azure App konfigurációs adatolvasó: Ezzel a szerepkörral olvasási hozzáférést biztosít az alkalmazás konfigurációs erőforrásokhoz.
- Közreműködő: Ezzel a szerepkörvel rendszergazdai hozzáférést biztosíthat a szolgáltatáshoz anélkül, hogy hozzáférést biztosítana az alkalmazáskonfigurációs példányban tárolt adatokhoz.

## <a name="next-steps"></a>További lépések
További információ a [felügyelt identitások](howto-integrate-azure-managed-service-identity.md) használatáról az alkalmazáskonfigurációs szolgáltatás felügyeletéhez.