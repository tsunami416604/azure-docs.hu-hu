---
title: Azure Active Directory jellemzői bérlői interakció |} Microsoft Docs
description: A bérlők teljesen független erőforrásként megismerni az Azure Active bérlői bérlők kezelése
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 81edc75f84c1dcb4f7b94878c472569d392175b1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Hogyan több Azure Active Directory-bérlő működjön ismertetése

Azure Active Directory (Azure AD), az egyes bérlők teljesen független erőforrásként: egy társ logikailag független a többi bérlő, amelyeket Ön kezel. Nincs szülő-gyermek kapcsolat bérlők között. Ez a függetlenség bérlők tartalmaz erőforrás-függetlenség, a felügyelet és a szinkronizálás függetlenségét is jelenti.

## <a name="resource-independence"></a>Erőforrás-függetlenség
* Hoz létre, vagy egy bérlői erőforrás törlése, ha nincs hatással van a valamilyen más bérlőket, a külső felhasználók részleges kivétellel erőforrás. 
* Ha a tartomány neveinek és egy bérlő használ, azt semmilyen más bérlővel nem használható.

## <a name="administrative-independence"></a>Felügyeleti függetlenség
Ha a "Contoso" bérlő nem rendszergazda jogosultságú felhasználók létrehoz egy tesztelési bérlőn "Test", majd:

* Alapértelmezés szerint egy bérlői létrehozó felhasználó esetleg fel, mert egy külső felhasználó új bérlőre, és a globális rendszergazdai szerepkörrel rendelkeznek, hogy a bérlő.
* A bérlői "Contoso" rendszergazdái nincs közvetlen rendszergazdai jogosultságai a "Teszt" bérlő rendelkezik, kivéve, ha a "Teszt" rendszergazdája kifejezetten megadja számára ezeket a jogokat. Azonban a "Contoso" rendszergazdái való hozzáférés szabályozásának bérlői "Teszt" Ha azok szabályozhatja, hogy a felhasználói fiók létrehozása a "Teszt".
* Ha Ön hozzáadása egy egy felhasználó egy Bérlői rendszergazda szerepkör, a változás nincs hatással a felhasználó rendelkezik-e egy másik bérlői rendszergazdai szerepköröket.

## <a name="synchronization-independence"></a>Szinkronizálási függetlenség
Beállíthatja, hogy minden Azure AD-bérlő egymástól függetlenül, hogy a következők egyetlen példányából származó adatok szinkronizálásához:

* Az Azure AD Connect eszközzel történő szinkronizáláshoz; egyetlen AD-erdővel.
* Az Azure Active bérlő összekötő a Forefront Identity Manager, szinkronizálja az adatokat egy vagy több helyszíni erdővel és/vagy nem Azure AD-adatforrásokat a.

## <a name="add-an-azure-ad-tenant"></a>Az Azure AD-bérlő hozzáadása
Az Azure-portálon az Azure AD-bérlő hozzáadásához jelentkezzen be [az Azure-portálon](https://portal.azure.com) az Azure AD globális rendszergazdai fiókkal, és a bal oldali, válassza ki a **új**.

> [!NOTE]
> Egyéb Azure-erőforrásokat, eltérően a bérlők csak egy Azure-előfizetés alsóbb szintű erőforrásai. Ha az Azure-előfizetéshez megszakítva, vagy lejárt, továbbra is elérheti a bérlő adatokat az Azure PowerShell, az Azure Graph API vagy az Office 365 felügyeleti központot. Emellett [más előfizetéseket társíthat a bérlő](active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>További lépések
Az Azure AD licencelési problémák és ajánlott eljárások általános áttekintést, lásd: [Mi az Azure Active bérlői licencelése?](active-directory-licensing-whatis-azure-portal.md).
