---
title: "Azure Active Directory jellemzői bérlői interakció |} Microsoft Docs"
description: "A bérlők teljesen független erőforrásként megismerni az Azure Active bérlői bérlők kezelése"
services: active-tenant
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 9ddb5f7e6c5dbeb742002dfcdfb981fae699130b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Hogyan több Azure Active Directory-bérlő működjön ismertetése

Az Azure Active Directory (Azure AD), minden egyes bérlő teljesen független erőforrásként: egy társ logikailag független a többi bérlő, amelyeket Ön kezel. Nincs szülő-gyermek kapcsolat bérlők között. Ez a függetlenség bérlők tartalmaz erőforrás-függetlenség, a felügyelet és a szinkronizálás függetlenségét is jelenti.

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

## <a name="next-steps"></a>Következő lépések
Az Azure AD licencelési problémák és ajánlott eljárások általános áttekintést, lásd: [Mi az Azure Active bérlői licencelése?](active-directory-licensing-whatis-azure-portal.md).
