---
title: Hárítsa el a meglévő Azure AD Domain Services felügyelt tartományok nem egyező könyvtár hibákat |} A Microsoft Docs
description: A meglévő Azure AD Domain Services felügyelt tartományok nem egyező könyvtár-hibák megértése és megoldása
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: ergreenl
ms.openlocfilehash: d3586293574cc71011ad96fece5e5a4fdfa0b70f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191992"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Nem egyező könyvtár ki a hibákat a meglévő Azure AD Domain Services felügyelt tartományok
Meglévő Azure AD tartományi szolgáltatásokkal felügyelt tartományban van. Ha az Azure Portalon keresse meg, és megtekintheti a felügyelt tartományhoz, a következő hibaüzenet jelenik meg:

![Nem egyező könyvtár hiba](./media/getting-started/mismatched-tenant-error.png)

A felügyelt tartomány a hiba kijavításáig nem lehet felügyelni.


## <a name="whats-causing-this-error"></a>Mi okozza a hibát?
A hiba fordul elő, ha a felügyelt tartományra és engedélyezve van a virtuális hálózat tartozik két különböző Azure AD-bérlőt. Például "contoso.com" nevű felügyelt tartományhoz van, és engedélyezve lett a Contoso Azure AD-bérlő számára. Azonban, amelyben a felügyelt tartomány engedélyezve lett az Azure virtuális hálózat tartozik a Fabrikam - egy másik Azure AD-bérlővel.

Az új Azure Portalon (és a kifejezetten az Azure AD Domain Services-bővítmény) van építve az Azure Resource Managerrel. A modern Azure Resource Manager-környezetben bizonyos korlátozások érvényben vannak nagyobb biztonságot nyújthat és a szerepköralapú hozzáférés (RBAC) az erőforrásokhoz. Az Azure AD-bérlő Azure AD tartományi szolgáltatások engedélyezése egy olyan bizalmas művelet, mivel azt eredményezi, hogy a kivonatok szinkronizálását a felügyelt tartományhoz. Ez a művelet megköveteli, hogy a címtár egy bérlői rendszergazdája lesz. Emellett a virtuális hálózaton, amelyben engedélyezése a felügyelt tartomány rendszergazdai jogosultságokkal kell rendelkeznie. RBAC-ellenőrzések egységesen működjenek a felügyelt tartomány és a virtuális hálózat ugyanahhoz az Azure AD-bérlőhöz kell tartoznia.

Röviden nem engedélyezheti a felügyelt tartomány a "contoso.com" Azure AD-bérlővel tulajdonosa egy másik Azure AD-bérlő "fabrikam.com" Azure-előfizetéshez tartozó virtuális hálózat. 

**Érvényes konfigurációs**: Ez a telepítési forgatókönyvben a Contoso a felügyelt tartomány engedélyezve van a Contoso Azure AD-bérlő. A felügyelt tartomány a Contoso Azure AD-bérlő által birtokolt Azure-előfizetéshez tartozó virtuális hálózat van közzétéve. Ezért a felügyelt tartomány és is a virtuális hálózat tartozik az Azure AD-bérlőhöz. Ez a konfiguráció érvényes és teljes mértékben támogatott.

![Érvényes bérlő konfigurációjához](./media/getting-started/valid-tenant-config.png)

**Eltérő bérlők konfigurációs**: Ez a telepítési forgatókönyvben a Contoso a felügyelt tartomány engedélyezve van a Contoso Azure AD-bérlő. A felügyelt tartomány azonban egy virtuális hálózatot, amelyhez tartozik egy Azure-előfizetést a Fabrikam az Azure AD-bérlő tulajdonában van közzétéve. Ezért a felügyelt tartomány és a virtuális hálózat tartozik két különböző Azure AD-bérlőt. Ez a konfiguráció az eltérő bérlők konfigurációs, és nem támogatott. A virtuális hálózaton át kell helyezni az Azure AD-bérlőhöz (vagyis a Contoso), a felügyelt tartományhoz. Tekintse meg a [feloldási](#resolution) című szakasz részletezi.

![Eltérő bérlők konfiguráció](./media/getting-started/mismatched-tenant-config.png)

Ezért, ha a felügyelt tartomány és engedélyezve van a virtuális hálózat tartozik két különböző Azure AD-bérlők ezt a hibaüzenetet.

Az alábbi szabályok vonatkoznak a Resource Manager-környezetben:
- Az Azure AD-címtárral rendelkezhet több Azure-előfizetéssel.
- Előfordulhat, hogy az Azure-előfizetések több erőforrást, például a virtuális hálózatok.
- Egyetlen Azure AD Domain Services felügyelt tartomány engedélyezve van az Azure AD-címtárral.
- Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz engedélyezhető az Azure-előfizetések belül az Azure AD-bérlőhöz tartozó virtuális hálózaton.


## <a name="resolution"></a>Megoldás:
A nem egyező könyvtár hiba megoldásához két lehetősége van. A következő lehetőségekkel:

- Kattintson a **törlése** gombra kattintva törölje a meglévő felügyelt tartományt. Hozza létre a használatával a [az Azure portal](https://portal.azure.com), így a felügyelt tartomány és érhető el a virtuális hálózat tartozik az Azure AD-címtárban. Összes gép korábban a tartományhoz csatlakoztatott törölve az újonnan létrehozott felügyelt tartományhoz csatlakozzon.

- Helyezze át a virtuális hálózat az Azure AD-címtárhoz, amelyhez a felügyelt tartományhoz tartozik tartalmazó Azure-előfizetés. Kövesse a [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md) cikk.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Hibaelhárítási útmutató – Azure AD tartományi szolgáltatások](active-directory-ds-troubleshooting.md)
