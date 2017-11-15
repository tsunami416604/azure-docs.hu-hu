---
title: "Szerepkörök az Azure AD Privileged Identity Management |} Microsoft Docs"
description: "Ismerje meg, milyen szerepkörök kiemelt identitásokat az Azure Privileged Identity Management bővítmény használhatók."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ac812ccc-cf4e-4ac2-b981-69598056c9ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: e3f67b978ff66cbb71709f2f8d66986a33149ae6
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Az Azure Active Directory PIM különböző rendszergazdai szerepkör
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

A szervezet különböző rendszergazdai szerepkörök az Azure AD-felhasználókat rendelhet hozzá. A szerepkör-hozzárendelések szabályozására, milyen feladatokat, például hozzáadása vagy eltávolítása a felhasználók vagy a szolgáltatás beállításainak módosítása a felhasználók tudja elvégezni az Azure ad-val, Office 365 és más Microsoft Online Services és a csatlakoztatott alkalmazások.  

> [!IMPORTANT]
> A Microsoft javasolja, hogy az Azure Portalon található [Azure AD felügyeleti központból](https://aad.portal.azure.com) kezelje az Azure AD-t az ebben a cikkben javasolt klasszikus Azure portál helyett.

Egy globális rendszergazda frissítheti, amelyek felhasználók **véglegesen** rendelt szerepkörök az Azure AD PowerShell-parancsmagok használatával, mint `Add-MsolRoleMember` és `Remove-MsolRoleMember`, vagy a klasszikus portálon leírtak [ rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Az Azure AD Privileged Identity Management (PIM) a privilegizált hozzáférés érdekében a felhasználók házirendeket kezeli az Azure ad-ben. A PIM felhasználók hozzárendel egy vagy több szerepkört az Azure ad-ben, és hozzá lehet rendelni valaki véglegesen a szerepkörben vagy abban az esetben jogosult a szerepkör. Amikor a felhasználó véglegesen van rendelve egy szerepkör vagy egy erre jogosult szerepkör-hozzárendelés aktiválja, majd kezelésére Azure Active Directory, az Office 365 és az egyéb alkalmazások és az engedélyek hozzárendelve a szerepkörökhöz.

Nincs különbség a hozzáférés meg egy megfelelő szerepkör-hozzárendelés és állandó rendelkező megadott. Az egyetlen különbség az, hogy néhány felhasználó nem szükséges, hogy access folyamatosan. Abban az esetben jogosult a szerepkör történik, és kapcsolhatja be és ki, amikor szükség van.

## <a name="roles-managed-in-pim"></a>Szerepkörök kezelése a PIM
A privileged Identity Management lehetővé teszi a felhasználók hozzárendelése közös rendszergazdai szerepköröket, beleértve:

* **Globális rendszergazda** (más néven a vállalati rendszergazda) összes felügyeleti funkcióhoz hozzáférése van. A szervezet csak egy globális rendszergazdai lehet. A személy, aki az Office 365 automatikusan megvásárlásához lesz egy globális rendszergazdához.
* **Kiemelt szerepkörű rendszergazda** Azure AD PIM kezeli és frissíti a szerepkör-hozzárendelések más felhasználók számára.  
* **Számlázási rendszergazda** lebonyolítja a vásárlásokat, kezeli az előfizetéseket, támogatási jegyeket, és figyeli a szolgáltatás állapotát.
* **Jelszókezelő** átállítja a jelszavakat, kezeli a szolgáltatáskérésekat és figyeli a szolgáltatás állapotát. Jelszó-rendszergazdák a felhasználók számára jelszavak alaphelyzetbe állítását korlátozódnak.
* **Szolgáltatás-rendszergazda** kezeli a szolgáltatáskérésekat és figyeli a szolgáltatás állapotát.
  
  > [!NOTE]
  > Office 365 használatakor a szolgáltatás-rendszergazdai szerepkör hozzárendelése felhasználóhoz előtt először hozzárendelheti a felhasználó rendszergazdai engedélyek egy szolgáltatás, például az Exchange online-hoz.
  > 
  > 
* **Felhasználókezelő rendszergazda** átállítja a jelszavakat, figyeli a szolgáltatás állapotát, és kezeli a felhasználói fiókok, a felhasználói csoportok és a szolgáltatáskéréseket. A felhasználó felügyeleti admin nem lehet törölni egy globális rendszergazdai, egyéb rendszergazdai szerepköröket hozhat létre, vagy állítsa vissza a globális, számlázási és a szolgáltatás-rendszergazdák jelszavát.
* **Exchange-rendszergazda** Exchange Online rendszergazdai hozzáféréssel rendelkezik az Exchange felügyeleti központot (min) keresztül, és szinte bármilyen feladatot végrehajthat Exchange Online-ban.
* **SharePoint-rendszergazda** SharePoint Online rendszergazdai hozzáféréssel rendelkezik a SharePoint Online felügyeleti központon keresztül történik, és szinte bármilyen feladatot végrehajthat a SharePoint online rendszerben.
* **Skype vállalati rendszergazda** Skype keresztül üzleti felügyeleti központban a Skype vállalati rendszergazdai hozzáféréssel rendelkezik, és szinte bármilyen feladatot végrehajthat a a Skype vállalati online.

Ezek a cikkek további részletekért olvassa el [rendszergazdai szerepkörök hozzárendelése az Azure AD](active-directory-assign-admin-roles-azure-portal.md) és [rendszergazdai szerepkörök hozzárendelése az Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


A PIM, akkor [ezek a szerepkörök hozzárendelése egy felhasználóhoz](active-directory-privileged-identity-management-how-to-add-role-to-user.md) , hogy a felhasználó [szükség esetén a szerepkör aktiválásához](active-directory-privileged-identity-management-how-to-activate-role.md).

Ha azt szeretné, a másik felhasználó hozzáférésének kezelhetik a PIM magát, a szerepkörök, amelyek a PIM a felhasználónak rendelkeznie kell részelemcímkék ismertetését további [a PIM hozzáférésének hogyan](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>A PIM nem kezelt szerepkörök
Szerepkörök Exchange Online vagy SharePoint online-hoz, kivéve a fent említett nem találhatók az Azure ad-ben, és ezért nem láthatók a PIM. Ezek az Office 365-szolgáltatásokhoz részletes szerepkör-hozzárendelések módosításáról bővebben lásd: [engedélyek az Office 365-ben](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Azure-előfizetések és -erőforráscsoportok is nem jelennek meg az Azure ad-ben. Azure-előfizetések kezeléséhez, tekintse meg [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](../billing/billing-add-change-azure-subscription-administrator.md) és további információ az Azure RBAC lásd [átruházásához hozzáférés-vezérlés](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Felhasználói szerepkörök, és jelentkezzen be
Microsoft-szolgáltatások és alkalmazások a felhasználó hozzárendelése egy szerepkörhöz nem lehet elegendő, hogy a felhasználó rendszergazdai jogosultság.

A klasszikus Azure portál használatához a felhasználónak kell egy szolgáltatási rendszergazdának vagy társadminisztrátornak az Azure-előfizetéssel, akkor is, ha a felhasználó nem kell az Azure-előfizetések kezeléséhez.  Az Azure AD a klasszikus portálon kezelheti a konfigurációs beállításokat, például egy felhasználónak kell az Azure AD globális rendszergazda, mind az előfizetés társadminisztrátoraként az Azure-előfizetés.  Felhasználók hozzáadása az Azure-előfizetéssel, lásd: [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](../billing/billing-add-change-azure-subscription-administrator.md).

Microsoft Online Services eléréséhez szükség lehet a felhasználó is hozzá kell rendelni a licencet nyissa meg a szolgáltatás portálján és felügyeleti feladatok.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Az Azure AD egy felhasználói licenc hozzárendelése
1. Jelentkezzen be a [a klasszikus Azure portálon](http://manage.windowsazure.com) vagy globális rendszergazdai fiók, vagy egy közös rendszergazdai fiók.
2. Válassza ki **minden elem** a fő menüből.
3. Válassza ki a használni kívánt könyvtárba, és a vele társított licenccel rendelkezik, amely.
4. Válassza ki **licencek**. A rendelkezésre álló licencek listája jelenik meg.
5. Válassza ki a licenccsomag, amely tartalmazza a terjeszteni kívánt licencek.
6. Válassza ki **felhasználók hozzárendelése**.
7. Válassza ki a felhasználót, hogy ki szeretné osztani a szükséges licenccel.
8. Kattintson a **hozzárendelése** gombra.  A felhasználók most is jelentkezhetnek be az Azure-bA.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

