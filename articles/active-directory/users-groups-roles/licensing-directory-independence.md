---
title: Jellemzők több interakciót – Azure Active Directory bérlői |} A Microsoft Docs
description: Bérlői bérlői Azure Active kezelheti a bérlők számára teljesen független erőforrásként ismertetése
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.openlocfilehash: 6dcc02d8d4c1f0629d69460be28a70c470704d75
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511377"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Megismerheti, hogyan több Azure Active Directory-bérlő használatához.

Az Azure Active Directoryban (Azure AD), minden bérlő teljesen független erőforrásként: az Ön által kezelt többi bérlő logikailag független társ. Nincs szülő-gyermek kapcsolat a bérlők között. Ez a függetlenség bérlők tartalmazza az erőforrás-függetlenség, a felügyelet és a szinkronizálás függetlenségét is jelenti.

## <a name="resource-independence"></a>Erőforrás-függetlenség
* Hoz létre, vagy egy bérlő erőforrás törlése, ha ez nem befolyásolja a külső felhasználókra vonatkozó részleges kivétellel, egy másik bérlőben található erőforrásokat. 
* Ha a tartomány neveinek egy bérlővel használja, azt semmilyen más bérlővel nem használható.

## <a name="administrative-independence"></a>Felügyeleti függetlenség
Ha a "Contoso" bérlő nem rendszergazda jogosultságú felhasználó létrehoz egy tesztelési bérlőn 'Test', majd:

* Alapértelmezés szerint egy bérlői létrehozó felhasználónak, hogy új bérlőn külső felhasználóként hozzá, és a globális rendszergazdai szerepkörrel az adott bérlőn belüli.
* A bérlő "Contoso" rendszergazdái nincs közvetlen rendszergazdai jogosultság a "Teszt" bérlő, ha a "Teszt" rendszergazdája kifejezetten megadja számára ezeket a jogosultságokat. Azonban "Contoso" rendszergazdái szabályozhatja a hozzáférést az bérlő "Teszt" Ha azok szabályozhatja a felhasználói fiókot, amelyet hozott létre "Teszt".
* Ha Ön hozzáadása egy felhasználó egy bérlői rendszergazdai szerepkör, a változás nem érinti a rendszergazdai szerepköröket, amelyek a felhasználó rendelkezik egy másik bérlőben.

## <a name="synchronization-independence"></a>Szinkronizálási függetlenség
Konfigurálhatja az egyes Azure AD-bérlők egymástól függetlenül a következők egyetlen példányából származó adatok szinkronizálásához:

* Az Azure AD Connect eszköz-egyetlen AD-erdővel történő szinkronizáláshoz.
* Az Azure Active bérlő összekötő a Forefront Identity Manager, adat szinkronizálása egy vagy több helyszíni erdővel és/vagy nem Azure AD-adatforrások.

## <a name="add-an-azure-ad-tenant"></a>Az Azure AD-bérlő hozzáadása
Az Azure Portalon az Azure AD-bérlő hozzáadásához jelentkezzen be [az Azure Portalon](https://portal.azure.com) egy olyan fiókkal, amely az Azure AD globális rendszergazda, és a bal oldalon található, válassza ki a **új**.

> [!NOTE]
> Más Azure-erőforrástól eltérően a bérlők nem tartoznak gyermekerőforrásait Azure-előfizetés. Ha az Azure-előfizetésében meg lett szakítva, vagy lejárt, a bérlő adatainak Azure PowerShell-lel, az Azure Graph API vagy az Office 365 felügyeleti központ használatával továbbra is elérheti. Emellett [más előfizetéseket társíthat a bérlő](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>További lépések
Az Azure AD-licencelési problémák és ajánlott eljárások széles körű áttekintéséért lásd: [mi van a bérlő Azure Active licencelése?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
