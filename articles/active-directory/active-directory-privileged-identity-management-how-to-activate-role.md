---
title: Aktiválása vagy deaktiválása |} Microsoft Docs
description: Útmutató a kiemelt jogosultságú identitások az Azure Privileged Identity Management alkalmazással szerepkörök aktiválása.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 057960d09a0a47544fa1295dafa4dc387c018ea6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698288"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Aktiválása vagy inaktiválása szerepkörök az Azure AD Privileged Identity Management
Az Azure Active Directory (AD) Privileged Identity Management egyszerűbbé teszi a hogyan kezelhetik a vállalatok számára az erőforrások az Azure AD és más Microsoft online szolgáltatások, például az Office 365-öt vagy a Microsoft Intune privilegizált hozzáférést.  

Ha Ön történtek egy rendszergazdai szerepkör esetében, amely azt jelenti, hogy ez a szerepkör aktiválhatja, ha privilegizált műveletek elvégzéséhez szüksége jogosult. Például ha alkalmanként kezelheti az Office 365-szolgáltatások, a szervezet kiemelt szerepkörű rendszergazda nem tehetik meg állandó globális rendszergazda, mivel ez a szerepkör túl befolyásolja az egyéb szolgáltatások. Helyette akkor Ön jogosult az Azure AD-szerepkörök, például az Exchange Online rendszergazdai. Ez a szerepkör aktiválása, ha a jogosultságokat igényel, és ezzel meglesz felügyeleti vezérlő egy előre meghatározott időszak kérhet.

Ez a cikk a rendszergazdák, akik aktiválja a szerepkört az Azure AD Privileged Identity Management (PIM) számára van. Végigvezeti a lépéseken, a szerepkör aktiválása, ha a engedélyekre van szükség, és a szerepkör inaktiválása, amikor elkészült. Ezenkívül kiemelt szerepkörű rendszergazda megkövetelheti jóváhagyás aktiválja a szerepkört (előzetes verzió). További információ [PIM jóváhagyási munkafolyamatok](./privileged-identity-management/azure-ad-pim-approval-workflow.md) itt.

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Az Azure AD Privileged Identity Management alkalmazást használja a [Azure-portálon](https://portal.azure.com/) egy szerepkör aktiválás kéréséhez, még akkor is, ha egy másik portál vagy PowerShell fog működni fog. Ha az Azure AD Privileged Identity Management alkalmazás nem rendelkezik az Azure-portál, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Jelölje be az Azure-portálon, és válassza ki a könyvtárat, ahol meg fog jobb felső sarokban a felhasználónevére működik.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="activate-a-role"></a>A szerepkör aktiválása
Amikor kell venni a szerepkör, aktiválás kérhet kiválasztásával a **saját szerepkörök** navigációs beállítása az Azure AD Privileged Identity Management alkalmazás bal oldali navigációs oszlop.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) és az Azure AD Privileged Identity Management csempe kiválasztása.
2. Válassza ki **a szerepkörök**. A hozzárendelt jogosult szerepkörök listáját a csoportosítás a lap tetején jelenik meg.
3. Válassza ki a szerepkör aktiválásához.
4. Válassza ki **aktiválása**. A **szerepkör aktiválás kéréséhez** panel jelenik meg.
5. Egyes szerepkörökhöz szükség a multi-factor Authentication (MFA), a szerepkör aktiválása előtt. Csak akkor kell munkamenetenként egyszer hitelesítéséhez.
   
    ![Ellenőrizze az MFA Használatát, mielőtt szerepkör aktiválása – képernyőkép][2]
6. A szövegmezőbe írja be az aktiválási kérelem okát.  Egyes szerepkörökhöz szükség, amelyen meg kell adnia egy hiba történt a hibajegy száma.
7. Kattintson az **OK** gombra.  Ha a szerepkör nem jóváhagyást igényelnek, most már aktiválva van, és a szerepkör (közvetlenül alatt a megfelelő szerepkör-hozzárendelések listáját) aktív szerepkörök listája jelenik meg. Ha a [szerepkör jóváhagyást igényel](./privileged-identity-management/azure-ad-pim-approval-workflow.md) aktiválásához egy bejelentési értesítést röviden jelennek meg a böngészőt, hogy értesítést küld a kérés van függőben lévő jóváhagyási jobb felső sarkában.

    ![Értesítés - képernyőkép váró kérelem][3]

## <a name="deactivate-a-role"></a>Deaktiválása
Ha a szerepkör aktiválása után azt automatikusan inaktiválja az időkorlátot (jogosult időtartama) elérésekor.

Ha a rendszergazdai feladatok elvégzéséhez korai végrehajtani, is inaktiválhatók manuálisan az Azure AD Privileged Identity Management alkalmazás szerepet.  Válassza ki **saját szerepkörök**, válassza ki a szerepkört végzett a használatával a **aktív szerepkör-hozzárendelések** csoportosítási, majd válassza **Deactivate**.  

## <a name="cancel-a-pending-request"></a>Megszakítja a függőben lévő kérelem
Abban az esetben, ha nincs szüksége, amelyhez jóváhagyás szükséges szerepkör aktiválásának, bármikor előfordulhat, hogy megszakítja a függőben lévő kérelem. Egyszerűen jelölje be a **saját szerepkörök** navigációs beállítása az Azure AD Privileged Identity Management alkalmazás bal oldali navigációs oszlop.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) és az Azure AD Privileged Identity Management csempe kiválasztása.
2. Válassza ki **a szerepkörök**. A hozzárendelt jogosult szerepkörök listáját a csoportosítás a lap tetején jelenik meg.
3. Szerepkör kiválasztása.
4. Válassza ki a **jóváhagyásra váró rendszer aktiválási** fejléc, a részletek panelen szerepkör aktiválása.
5. Válassza ki **Mégse** tetején a **jóváhagyásra váró** panelen.

   ![Megszakítja a függőben lévő kérelem képernyőképe][4]

## <a name="next-steps"></a>További lépések
Ha szeretné használni az Azure AD Privileged Identity Management többet, az alábbi hivatkozások rendelkezik további információt.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
