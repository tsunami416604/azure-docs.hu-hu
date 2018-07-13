---
title: Szerepkörök az Azure AD Privileged Identity Management |} A Microsoft Docs
description: Ismerje meg, milyen szerepkörök használhatók az Azure Privileged Identity Management bővítménnyel emelt jogosultsági szintű identitásait.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 03/04/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: bbc87f529cb70b6f4b21984199ed87427b4e3108
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590344"
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Az Azure Active Directory PIM-ben különböző rendszergazdai szerepkör
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Hozzárendelheti a felhasználók a szervezet különböző rendszergazdai szerepkörökkel az Azure ad-ben. A szerepkör-hozzárendelések szabályozza, mely tevékenységek hozzáadásával vagy a felhasználók eltávolítása vagy módosítása a szolgáltatás beállításait, például a felhasználók átállíthatják a Azure AD-ben az Office 365-höz és más Microsoft Online Services és a csatlakoztatott alkalmazások hajthatja végre.  

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy kezelje az Azure AD-t a [Azure AD felügyeleti központ](https://aad.portal.azure.com) az Azure Portalon.

Globális rendszergazda frissítheti, amelyek felhasználók **véglegesen** rendelt szerepkörök, az Azure AD-ben például a PowerShell-parancsmagok használatával `Add-MsolRoleMember` és `Remove-MsolRoleMember`, és leírtak szerint a portálon keresztül [hozzárendelése rendszergazdai szerepkörök az Azure Active Directoryban](../users-groups-roles/directory-assign-admin-roles.md).

Az Azure AD Privileged Identity Management (PIM) kezeli a szabályzatok a felhasználók számára a privilegizált hozzáférés érdekében az Azure ad-ben. A PIM rendel a felhasználók egy vagy több szerepkört az Azure ad-ben, és rendelhet hozzá valaki véglegesen a szerepkörben vagy jogosultságot a szerepkörre. Amikor egy felhasználó tartósan van hozzárendelve egy szerepkörhöz, vagy egy jogosult szerepkör-hozzárendelés aktiválja, majd az Azure Active Directory, az Office 365 és más alkalmazások képesek kezelni a szerepkörökhöz rendelt engedélyekkel.

Nincs megadott valaki egy állandó jogosult szerepkör-hozzárendelés és hozzáférési nincs különbség. Az egyetlen különbség, hogy néhány felhasználó nem kell olyan folyamatosan. Jogosult a szerepkörre jönnek létre, és kapcsolhatja be és ki minden alkalommal, amikor szükségük van.

## <a name="roles-managed-in-pim"></a>Szerepkörök kezelése a PIM
Privileged Identity Management lehetővé teszi az általános rendszergazdai szerepköröket, beleértve a felhasználók hozzárendelése:

* **Globális rendszergazda** (más néven a vállalati rendszergazda) összes rendszergazdai funkciójához hozzáfér. A szervezetben egynél több globális rendszergazda rendelkezhet. Az Office 365 automatikusan beszerzési feliratkozó személy lesz globális rendszergazda.
* **Kiemelt szerepkörű rendszergazda** kezeli az Azure AD PIM-ben, és frissíti a szerepkör-hozzárendelések más felhasználók számára.  
* **Számlázási adminisztrátor** lebonyolítja a vásárlásokat, kezeli az előfizetéseket, támogatási jegyeket, és figyeli a szolgáltatás állapotát.
* **Jelszókezelő** átállítja a jelszavakat, kezeli a szolgáltatáskéréseket, és figyeli a szolgáltatás állapotát. Jelszó rendszergazdák korlátozva, a felhasználók jelszavainak visszaállítását.
* **Szolgáltatás-rendszergazda** kezeli a szolgáltatáskéréseket, és figyeli a szolgáltatás állapotát.
  
  > [!NOTE]
  > Ha Office 365-höz használ, majd előtt a szolgáltatás-rendszergazdai szerepkör hozzárendelése egy felhasználóhoz, először a felhasználó hozzárendelése rendszergazdai engedélyekkel egy szolgáltatáshoz, például az Exchange online-hoz.
  > 
  > 
* **Felhasználókezelő rendszergazda** átállítja a jelszavakat, figyeli a szolgáltatás állapotát, és kezeli a felhasználói fiókok, a felhasználói csoportok és a szolgáltatáskéréseket. A Felhasználókezelő rendszergazda nem törölheti a globális rendszergazdákat, hozhat létre más rendszergazdai szerepköröket, vagy állítsa vissza a globális, a számlázási és szolgáltatás-rendszergazdák jelszavát.
* **Exchange-rendszergazda** Exchange Online rendszergazdai hozzáféréssel rendelkezik az Exchange felügyeleti központot (EAC segítségével) keresztül, és szinte bármilyen feladatot végrehajthat az Exchange online-ban.
* **SharePoint-rendszergazda (előzetes verzió)** a SharePoint online rendszergazdai hozzáféréssel rendelkezik a SharePoint Online felügyeleti központban, és szinte bármilyen feladatot végrehajthat a SharePoint online-ban. Ez a szerepkör jelenleg előzetes verzióban érhető el. Jogosult felhasználók tapasztalhat az késleltetések a PIM aktiválása után a sharepointból szerepkör használatával.
* **Skype vállalati rendszergazda** rendszergazdai hozzáférést a Skype for Business keretében Skype for Business felügyeleti központban, és szinte bármilyen feladatot végrehajthat a a Skype vállalati online verziójához.

Ezek a cikkek további részletekért olvassa el [rendszergazdai szerepkörök hozzárendelése az Azure ad-ben](../users-groups-roles/directory-assign-admin-roles.md) és [rendszergazdai szerepkörök hozzárendelése az Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


A PIM, is [ezeket a szerepköröket hozzárendelni egy felhasználóhoz](pim-how-to-add-role-to-user.md) , hogy a felhasználó [szükség esetén a szerepkör aktiválását](pim-how-to-activate-role.md).

Ha azt szeretné, egy másik felhasználói hozzáférésének kezelése a PIM magát, a szerepköröket a PIM a felhasználónak szüksége van, amely olyan további [hozzáférés biztosítása a PIM számára](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>A PIM által nem felügyelt szerepkörök
Szerepkörök az Exchange Online vagy SharePoint online-hoz, kivéve azokat, a fent említett nem szerepelnek az Azure AD-ben, és ezért nem láthatók a PIM. Az alábbi Office 365-szolgáltatások részletes szerepkör-hozzárendelések módosítása további információkért lásd: [engedélyeket az Office 365-ben](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Az Azure-előfizetések és -erőforráscsoportok még nem szerepelnek az Azure AD-ben. Azure-előfizetések kezeléséhez lásd: [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](../../billing/billing-add-change-azure-subscription-administrator.md) és további információ az Azure RBAC lásd [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Felhasználói szerepkörök, és jelentkezzen be
Bizonyos Microsoft-szolgáltatások és alkalmazások hozzárendelése egy felhasználói szerepkörhöz nem elegendő lehet, hogy a rendszergazdai felhasználó engedélyezése.

Az Azure portal eléréséhez kell, hogy a felhasználó lehet egy szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként az Azure-előfizetéssel, akkor is, ha a felhasználónak nem kell az Azure-előfizetések kezelése.  Például konfigurációs beállítások kezelése az Azure ad-hez, a felhasználónak kell lennie az Azure AD globális rendszergazda és a egy Azure-előfizetés előfizetéshez társadminisztrátorként.  Felhasználók hozzáadása az Azure-előfizetések kezelésével kapcsolatos információkért lásd: [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](../../billing/billing-add-change-azure-subscription-administrator.md).

Online Microsoft-szolgáltatásokhoz való hozzáférés szükségessé, a felhasználó is hozzá lehet rendelni egy licenc nyissa meg a szolgáltatási portált vagy felügyeleti feladatok végrehajtása előtt.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Rendeljen egy licencet a felhasználó Azure AD-ben
1. Jelentkezzen be a [az Azure portal](http://portal.azure.com) egy globális rendszergazdai fiókkal vagy egy társ-rendszergazda fiókkal.
3. Válassza ki az Azure ad-ben, és dolgozhat, és a könyvtárban társított licenccel rendelkezik.
4. Válassza ki **licencek** a bal oldalon. A rendelkezésre álló licencek listája jelenik meg.
5. Válassza ki a licenccsomag, amely tartalmazza a terjeszteni kívánt licenceket.
6. Válassza ki **hozzárendelhet felhasználókat**.
7. Válassza ki a felhasználót, amelyet szeretne hozzárendelni a licencet.
8. Kattintson a **hozzárendelése** gombra.  A felhasználó most már bejelentkezhet az Azure-bA.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

