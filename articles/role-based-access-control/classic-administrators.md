---
title: Klasszikus Azure-előfizetés rendszergazdái | Microsoft Docs
description: Ismerteti, hogyan lehet hozzáadni vagy módosítani az Azure-beli társ-rendszergazda és szolgáltatás-rendszergazdai szerepköröket, valamint a fiók rendszergazdájának megtekintését.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c2096b6c5ddb72c8ab5c5d3203a05c94db51f6c5
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444337"
---
# <a name="azure-classic-subscription-administrators"></a>Klasszikus Azure-előfizetés rendszergazdái

A Microsoft azt javasolja, hogy szerepköralapú hozzáférés-vezérléssel (RBAC) kezelje az Azure-erőforrásokhoz való hozzáférést. Ha azonban továbbra is a klasszikus telepítési modellt használja, akkor a klasszikus előfizetés-rendszergazdai szerepkört kell használnia: Szolgáltatás-rendszergazda és társ-rendszergazda. További információ: Azure Resource Manager és [klasszikus üzembe helyezés](../azure-resource-manager/resource-manager-deployment-model.md).

Ez a cikk a társ-rendszergazda és a szolgáltatás-rendszergazdai szerepkörök hozzáadását és módosítását, valamint a fiók rendszergazdájának megtekintését ismerteti.

## <a name="add-a-co-administrator"></a>Társ-rendszergazda hozzáadása

> [!TIP]
> Csak akkor kell hozzáadnia egy társ-rendszergazdát, ha a felhasználónak az [Azure Service Management PowerShell-modul](https://docs.microsoft.com/powershell/module/servicemanagement/azure)használatával kell kezelnie a klasszikus Azure-telepítést. Ha a felhasználó csak a klasszikus erőforrások kezeléséhez használja a Azure Portal, nem kell hozzáadnia a felhasználó klasszikus rendszergazdáját.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) szolgáltatás-rendszergazdaként.

1. Nyisson [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) meg előfizetéseket, és válasszon ki egy előfizetést

    A társ-rendszergazdák csak az előfizetés hatókörében rendelhetők hozzá.

1. Kattintson a **hozzáférés-vezérlés (IAM)** .

1. Kattintson a **klasszikus rendszergazdák** fülre.

    ![A klasszikus rendszergazdákat megnyitó képernyőkép](./media/classic-administrators/classic-administrators.png)

1. Kattintson a **Hozzáadás** > a**társ-rendszergazda** hozzáadásához elemre a közös rendszergazdák hozzáadása panel megnyitásához.

    Ha a társ-rendszergazda hozzáadása lehetőség le van tiltva, akkor nem rendelkezik a megfelelő engedélyekkel.

1. Válassza ki a hozzáadni kívánt felhasználót, és kattintson a **Hozzáadás**gombra.

    ![A társ-rendszergazda hozzáadására szolgáló képernyőkép](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Vendég felhasználó hozzáadása társ-rendszergazdaként

A társ-rendszergazda szerepkörrel rendelkező [vendég felhasználók](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) bizonyos különbségeket láthatnak a társ-rendszergazda szerepkörrel rendelkező felhasználókhoz képest. Vegye figyelembe a következő helyzetet:

- Az A felhasználó Azure AD munkahelyi vagy iskolai fiókkal az Azure-előfizetések szolgáltatás-rendszergazdája.
- A B felhasználó Microsoft-fiók rendelkezik.
- Az A felhasználó hozzárendeli a társ-rendszergazda szerepkört a B felhasználóhoz.
- A B felhasználó szinte mindent megtehet, de nem tudja regisztrálni az alkalmazásokat, vagy megkeresni a felhasználókat az Azure AD-címtárban.

Azt várná, hogy a B felhasználó mindent tud kezelni. Ennek a különbségnek az az oka, hogy a Microsoft-fiók a tag felhasználója helyett vendég felhasználóként adja hozzá az előfizetéshez. A vendég felhasználók a felhasználókhoz képest eltérő alapértelmezett engedélyekkel rendelkeznek az Azure AD-ben. Például a felhasználók más felhasználókat is olvashatnak az Azure AD-ben, és a vendég felhasználói nem. A tagok felhasználói regisztrálhatnak új egyszerű szolgáltatásokat az Azure AD-ben, és a vendég felhasználói nem rendelkezhetnek. Ha egy vendégnek el kell tudnia hajtani ezeket a feladatokat, lehetséges megoldás az adott Azure AD-rendszergazdai szerepkörök hozzárendelésére a vendég felhasználói igényeinek megfelelően. Az előző forgatókönyvben például hozzárendelheti a [címtár-olvasók](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) szerepkört más felhasználók olvasásához és az [alkalmazás fejlesztői](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) szerepkörének hozzárendeléséhez, hogy az egyszerű szolgáltatásokat lehessen létrehozni. További információ a tagok és a vendég felhasználókról és azok engedélyeiről: [Mi az alapértelmezett felhasználói engedély a Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

Vegye figyelembe, hogy az [Azure-erőforrások beépített szerepkörei](../role-based-access-control/built-in-roles.md) eltérnek az [Azure ad rendszergazdai szerepköreitől](../active-directory/users-groups-roles/directory-assign-admin-roles.md). A beépített szerepkörök nem biztosítanak hozzáférést az Azure AD-hoz. További információ: [a különböző szerepkörök megismerése](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Társ-rendszergazda eltávolítása

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) szolgáltatás-rendszergazdaként.

1. Nyisson [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) meg előfizetéseket, és válasszon ki egy előfizetést

1. Kattintson a **hozzáférés-vezérlés (IAM)** .

1. Kattintson a **klasszikus rendszergazdák** fülre.

1. Vegyen fel egy pipát az eltávolítani kívánt társ-rendszergazda mellett.

1. Kattintson az **Eltávolítás**gombra.

1. A megjelenő üzenetablakban kattintson az **Igen**gombra.

    ![A társ-rendszergazda eltávolítására szolgáló képernyőkép](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Megváltoztathatja a szolgáltatás-rendszergazdát.

Csak a fiók rendszergazdája módosíthatja az előfizetés szolgáltatás-rendszergazdáját. Alapértelmezés szerint az Azure-előfizetésre való regisztráláskor a szolgáltatás rendszergazdája megegyeznek a fiók rendszergazdájával. A fiók-rendszergazdai szerepkörrel rendelkező felhasználó nem fér hozzá a Azure Portalhoz. A szolgáltatás-rendszergazdai szerepkörrel rendelkező felhasználó teljes hozzáféréssel rendelkezik a Azure Portalhoz. Ha a fiók rendszergazdája és a szolgáltatás rendszergazdája ugyanaz a felhasználó, és egy másik felhasználóra módosítja a szolgáltatás-rendszergazdát, akkor a fiók rendszergazdája elveszti a hozzáférést Azure Portal. A fiók rendszergazdája azonban mindig használhatja a Account centert a szolgáltatás-rendszergazda saját magukra való visszaállítására.

A szolgáltatásadminisztrátort kétféleképpen módosíthatja. A **Azure Portal** vagy a **fiók központban**is megváltoztathatja.

### <a name="azure-portal"></a>Azure Portal

1. Győződjön meg arról, hogy a forgatókönyvek támogatottak, ha ellenőrzi a szolgáltatás-rendszergazdák módosításának korlátozásait.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók rendszergazdájaként.

1. Nyisson [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) meg előfizetéseket, és válasszon ki egy előfizetést

1. Kattintson a **Tulajdonságok** elemre.

    ![A fiók rendszergazdáját ábrázoló képernyőfelvétel](./media/classic-administrators/account-admin.png)

1. A lap tetején kattintson a **szolgáltatás** -rendszergazda lehetőségre a szolgáltatás-rendszergazdai ablaktábla megnyitásához.

    Ha a szolgáltatás-rendszergazda gomb le van tiltva, nem rendelkezik a megfelelő engedélyekkel. Csak a fiók rendszergazdája által felügyelt felhasználó módosíthatja a szolgáltatás-rendszergazdát.

1. Válasszon ki egy új szolgáltatás-rendszergazdát, majd kattintson a **Mentés**gombra.

### <a name="account-center"></a>Account Center

1. Győződjön meg arról, hogy a forgatókönyvek támogatottak, ha ellenőrzi a szolgáltatás-rendszergazdák módosításának korlátozásait.

1. Jelentkezzen be a [Account Centerbe](https://account.windowsazure.com/subscriptions) a fiók rendszergazdájaként.

1. Kattintson egy előfizetésre.

1. A jobb oldalon kattintson az **előfizetés részleteinek szerkesztése**elemre.

    ![Képernyőfelvétel: az előfizetés szerkesztése gomb megjelenítése az Account Centerben](./media/classic-administrators/editsub.png)

1. A **szolgáltatás-rendszergazda** mezőben adja meg az új szolgáltatás-rendszergazda e-mail-címét.

    ![Képernyőfelvétel a szolgáltatás-rendszergazdai e-mail módosítására szolgáló jelölőnégyzetről](./media/classic-administrators/change-service-admin.png)

1. Kattintson a pipa jelre a módosítás mentéséhez.

### <a name="limitations-for-changing-the-service-administrator"></a>A szolgáltatás-rendszergazda módosításának korlátai

Minden előfizetés egy Azure AD-címtárhoz van társítva. Az előfizetés társított könyvtárának megkereséséhez nyissa meg  az előfizetéseket a Azure Portal, majd válasszon egy előfizetést a könyvtár megtekintéséhez.

Ha munkahelyi vagy iskolai fiókkal jelentkezik be, a szervezetében más fiókokat is hozzáadhat a szolgáltatás-rendszergazdaként. Hozzáadhat abby@contoso.com john@notcontoso.com john@notcontoso.com például szolgáltatás-rendszergazdaként, de nem lehet hozzáadni, ha a contoso.com-címtárban szerepel a jelenléte. bob@contoso.com A munkahelyi vagy iskolai fiókkal bejelentkezett felhasználók továbbra is hozzáadhatnak Microsoft-fiók felhasználókat a szolgáltatás-rendszergazdaként. Azure-előfizetések esetében csak egy szolgáltatás-rendszergazda lehet.

  | Bejelentkezési módszer | Hozzá Microsoft-fiók felhasználót szolgáltatás-rendszergazdaként? | A munkahelyi vagy iskolai fiókokat ugyanabban a szervezetben adja hozzá, mint a szolgáltatás-rendszergazda? | A munkahelyi vagy iskolai fiókokat más szervezetekben is hozzáadhatja szolgáltatás-rendszergazdaként? |
  | --- | --- | --- | --- |
  |  Microsoft-fiók |Igen |Nem |Nem |
  |  Munkahelyi vagy iskolai fiók |Igen |Igen |Nem |

## <a name="view-the-account-administrator"></a>A fiók rendszergazdájának megtekintése

A fiók rendszergazdája az a felhasználó, aki először regisztrált az Azure-előfizetésre, és felelős az előfizetés számlázási tulajdonosának. Az előfizetés rendszergazdai fiókjának módosításához tekintse meg az [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)című témakört.

A fiók rendszergazdájának megtekintéséhez kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyisson [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) meg előfizetéseket, és válasszon ki egy előfizetést

1. Kattintson a **Tulajdonságok** elemre.

    Az előfizetés fiókjának rendszergazdája megjelenik a **fiók** adminisztrátora mezőben.

    ![A fiók rendszergazdáját ábrázoló képernyőfelvétel](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>További lépések

* [A különböző Azure-beli szerepkörök ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
* [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](../billing/billing-add-change-azure-subscription-administrator.md)
