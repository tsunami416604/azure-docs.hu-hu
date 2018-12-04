---
title: Azure AD címtár szerepköreinek segítségével kezelheti a PIM |} A Microsoft Docs
description: Az Azure AD Privileged Identity Management (PIM) kezelheti az Azure AD-címtárbeli szerepkörök ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 531d19925d24930b6a2bd642a8ff0ec55bd6d16f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841574"
---
# <a name="azure-ad-directory-roles-you-can-manage-in-pim"></a>A PIM segítségével kezelheti az Azure AD címtárszerepkörök
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Hozzárendelheti a felhasználók a szervezet különböző rendszergazdai szerepkörökkel az Azure ad-ben. A szerepkör-hozzárendelések szabályozza, mely tevékenységek hozzáadásával vagy a felhasználók eltávolítása vagy módosítása a szolgáltatás beállításait, például a felhasználók átállíthatják a hajtsa végre az Azure Active Directory, az Office 365, és más Microsoft Online Services és a csatlakoztatott alkalmazások.  

Globális rendszergazda frissítheti, amelyek felhasználók **véglegesen** leírtak szerint az Azure ad-ben a portálon keresztül szerepkörökhöz rendelt [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) vagy használatával[ PowerShell-parancsok](/powershell/module/azuread#directory_roles).

Az Azure AD Privileged Identity Management (PIM) kezeli a szabályzatok a felhasználók számára a privilegizált hozzáférés érdekében az Azure ad-ben. A PIM rendel a felhasználók egy vagy több szerepkört az Azure ad-ben, és rendelhet hozzá valaki véglegesen a szerepkörben vagy jogosultságot a szerepkörre. Amikor egy felhasználó tartósan van hozzárendelve egy szerepkörhöz, vagy egy jogosult szerepkör-hozzárendelés aktiválja, majd az Azure Active Directory, az Office 365 és más alkalmazások képesek kezelni a szerepkörökhöz rendelt engedélyekkel.

Nincs megadott valaki egy állandó jogosult szerepkör-hozzárendelés és hozzáférési nincs különbség. Az egyetlen különbség, hogy néhány felhasználó nem kell olyan folyamatosan. Jogosult a szerepkörre jönnek létre, és kapcsolhatja be és ki minden alkalommal, amikor szükségük van.

## <a name="roles-managed-in-pim"></a>Szerepkörök kezelése a PIM
Privileged Identity Management lehetővé teszi az általános rendszergazdai szerepköröket, beleértve a felhasználók hozzárendelése:

* **Globális rendszergazda** (más néven a vállalati rendszergazda) összes rendszergazdai funkciójához hozzáfér. A szervezetben egynél több globális rendszergazda rendelkezhet. Az Office 365 automatikusan beszerzési feliratkozó személy lesz globális rendszergazda.
* **Kiemelt szerepkörű rendszergazda** kezeli az Azure AD PIM-ben, és frissíti a szerepkör-hozzárendelések más felhasználók számára.  
* **Számlázási adminisztrátor** lebonyolítja a vásárlásokat, kezeli az előfizetéseket, támogatási jegyeket, és figyeli a szolgáltatás állapotát.
* **Jelszókezelő** átállítja a jelszavakat, kezeli a szolgáltatáskéréseket, és figyeli a szolgáltatás állapotát. Jelszókezelők korlátozva, a felhasználók jelszavainak visszaállítását.
* **Szolgáltatás-rendszergazda** kezeli a szolgáltatáskéréseket, és figyeli a szolgáltatás állapotát.
  
  > [!NOTE]
  > Office 365-höz használ, ha egy felhasználónak, a szolgáltatás-rendszergazdai szerepkör hozzárendelése előtt először hozzárendelheti a felhasználó rendszergazdai engedélyek egy szolgáltatáshoz, például az Exchange online-hoz.
  > 
  > 
* **Felhasználói rendszergazda** átállítja a jelszavakat, figyeli a szolgáltatás állapotát, és kezeli a felhasználói fiókok, a felhasználói csoportok és a szolgáltatáskéréseket. A felhasználó rendszergazda nem törölhet globális rendszergazdát, hozhat létre más rendszergazdai szerepköröket, vagy állítsa vissza a globális, a számlázási és szolgáltatás-rendszergazdák jelszavát.
* **Exchange-rendszergazda** Exchange Online rendszergazdai hozzáféréssel rendelkezik az Exchange felügyeleti központot (EAC segítségével) keresztül, és szinte bármilyen feladatot végrehajthat az Exchange online-ban.
* **A SharePoint szolgáltatás-rendszergazda** a SharePoint online rendszergazdai hozzáféréssel rendelkezik a SharePoint Online felügyeleti központban, és szinte bármilyen feladatot végrehajthat a SharePoint online-ban. Jogosult felhasználók tapasztalhat az késleltetések a PIM aktiválása után a sharepointból szerepkör használatával.
* **Skype for Business rendszergazda** rendszergazdai hozzáférést a Skype for Business keretében Skype for Business felügyeleti központban, és szinte bármilyen feladatot végrehajthat a a Skype vállalati online verziójához.

Ezek a cikkek további részletekért olvassa el [rendszergazdai szerepkörök hozzárendelése az Azure ad-ben](../users-groups-roles/directory-assign-admin-roles.md) és [rendszergazdai szerepkörök hozzárendelése az Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


A PIM, is [ezeket a szerepköröket hozzárendelni egy felhasználóhoz](pim-how-to-add-role-to-user.md) , hogy a felhasználó [szükség esetén a szerepkör aktiválását](pim-how-to-activate-role.md).

Ha azt szeretné, egy másik felhasználói hozzáférésének kezelése a PIM magát, a szerepköröket a PIM a felhasználónak szüksége van, amely olyan további [hozzáférés biztosítása a PIM számára](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>A PIM által nem felügyelt szerepkörök
Szerepkörök az Exchange Online vagy SharePoint online-hoz, kivéve azokat, a fent említett nem szerepelnek az Azure AD-ben, és ezért nem láthatók a PIM. Az alábbi Office 365-szolgáltatások részletes szerepkör-hozzárendelések módosítása további információkért lásd: [engedélyeket az Office 365-ben](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Felhasználói szerepkörök, és jelentkezzen be
Bizonyos Microsoft-szolgáltatások és alkalmazások hozzárendelése egy felhasználói szerepkörhöz nem elegendő lehet, hogy a rendszergazdai felhasználó engedélyezése.

Az Azure portal eléréséhez kell, hogy a felhasználó Azure-előfizetés tulajdonosa legyen akkor is, ha a felhasználónak nem kell az Azure-előfizetések kezelése.  Például konfigurációs beállítások kezelése az Azure ad-hez, a felhasználónak rendszergazdának kell lennie mindkét egy globális Azure AD-ben és a egy Azure-előfizetés tulajdonosa.  Felhasználók hozzáadása az Azure-előfizetések kezelésével kapcsolatos információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../..//role-based-access-control/role-assignments-portal.md).

Online Microsoft-szolgáltatásokhoz való hozzáférés szükségessé, a felhasználó is hozzá lehet rendelni egy licenc nyissa meg a szolgáltatási portált vagy felügyeleti feladatok végrehajtása előtt.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Rendeljen egy licencet a felhasználó Azure AD-ben

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy globális rendszergazdai vagy tulajdonosi szerepkörrel rendelkező.

1. Válassza ki a használni kívánt Azure AD-címtár, és licenceket társítva van.

1. A bal oldali navigációs sávján kattintson **Azure Active Directory**.

1. Kattintson a **licencek**. A rendelkezésre álló licencek listája jelenik meg.

    ![Az Azure Active Directory-licencek](./media/pim-roles/licenses-overview.png)

1. Kattintson a **termék**.

1. Kattintson a licenccsomag, amely tartalmazza a licencek terjeszteni szeretné.

    ![Licencek termékek](./media/pim-roles/licenses-products.png)

1. Kattintson a **hozzárendelése** a licenc hozzárendelése panel megnyitásához.

    ![A licenccel rendelkező felhasználók](./media/pim-roles/licenses-licensed-users.png)

1. Válassza ki a felhasználót vagy csoportot, amelyet szeretne hozzárendelni a licencet.

    ![Licenc hozzárendelése](./media/pim-roles/licenses-assign-license.png)

1. Kattintson a **hozzárendelési beállítások** a hozzárendelési beállítások konfigurálása.

    ![Hozzárendelési beállítások](./media/pim-roles/licenses-assignment-options.png)

1. Kattintson a **hozzárendelése** hozzárendelni a licencet. A felhasználó már rendelkezik a licenc.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

- [Ismerkedés a PIM-mel](pim-getting-started.md)
- [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)

