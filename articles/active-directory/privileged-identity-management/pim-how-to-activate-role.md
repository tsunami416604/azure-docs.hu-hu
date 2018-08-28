---
title: Aktiválása vagy a szerepkör inaktiválása |} A Microsoft Docs
description: Ismerje meg, hogy az emelt szintű identitások az Azure Privileged Identity Management alkalmazással szerepkörök aktiválása.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 54e092cb98df6c092e6cf3ed8c365f966587b0db
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091601"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Aktiválása vagy inaktiválása az Azure AD Privileged Identity Management szerepkörök
Az Azure Active Directory (AD) Privileged Identity Management egyszerűsíti, hogy hogyan kezelhetik a vállalatok számára az erőforrások az Azure AD és más Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune emelt szintű hozzáférés.  

Ha Ön történtek egy rendszergazdai szerepkör jogosult, ez azt jelenti, is aktiválhatja a szerepkört, amikor szüksége van privilegizált műveletek elvégzéséhez. Például alkalmanként kezelheti az Office 365-funkciókat, ha a szervezet a kiemelt szerepkörű rendszergazdák nem teheti elérhetővé, állandó globális rendszergazda, mivel az adott szerepkör milyen hatással van más szolgáltatások túl. Ehelyett, győződjön meg arról, hogy jogosult az Azure AD-szerepkörök, például az Exchange Online rendszergazdai. Kérheti, hogy a szerepkör aktiválása, ha a jogosultságok szükségesek, és ezután lesz rendszergazdai irányítást egy előre meghatározott időtartamra.

Ez a cikk olyan rendszergazdák, akik az Azure AD Privileged Identity Management (PIM) a szerepkör aktiválásához. Végigvezeti a lépéseken, a szerepkör aktiválása, ha az engedélyek szükségesek, és a szerepkör inaktiválása, amikor elkészült. Emellett a kiemelt szerepkörű rendszergazdák (előzetes verzió) szerepkör aktiválásához jóváhagyás lehet szükséges. Tudjon meg többet [PIM jóváhagyási munkafolyamatokat](./azure-ad-pim-approval-workflow.md) itt.

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Az az Azure AD Privileged Identity Management alkalmazást használja a [az Azure portal](https://portal.azure.com/) egy szerepkör-aktiválás kéréséhez, még akkor is, ha egy másik portál vagy PowerShell működéséhez fog. Ha az Azure AD Privileged Identity Management alkalmazás nem rendelkezik az Azure Portalon, kövesse az alábbi lépéseket a kezdéshez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson a felhasználónevére a jobb felső sarkában az Azure Portalon, és válassza a könyvtárban, ahol Ön lesz működik.

1. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.

1. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

## <a name="activate-a-role"></a>A szerepkör aktiválása
Amikor szüksége van egy szerepkör, aktiválás kattintva kérheti az **saját szerepkörök** navigációs beállítást, az Azure AD Privileged Identity Management alkalmazás bal oldali navigációs oszlopban.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) és az Azure AD Privileged Identity Management csempe kiválasztásával.

1. Válassza ki **saját szerepkörök**. Az Önhöz rendelt elérhető szerepköröket listáját a csoportosítást, amely a lap tetején jelennek meg.

1. Válassza ki a szerepkör aktiválásához.

1. Válassza ki **aktiválása**. A **szerepkör aktiválásának kérése** panel jelenik meg.

1. Egyes szerepkörök szükség a multi-factor Authentication (MFA), a szerepkör aktiválása előtt. Csak egyszer munkamenetenként hitelesítésre van.

    ![Ellenőrizze a többtényezős hitelesítéssel, mielőtt a szerepkör aktiválása – képernyőkép](./media/pim-how-to-activate-role/PIM_activation_MFA.png)

1. A szövegmezőbe írja be az aktiválási kérés okát.  Egyes szerepkörök szükséges egy probléma jegyszám fogja tartalmazni.

1. Kattintson az **OK** gombra.  Ha a szerepkör nem igényel jóváhagyási, most már aktiválva van, és a szerepkör megjelenik a lista aktív szerepkör (közvetlenül a jogosult szerepkör-hozzárendelések lista) alatt. Ha a [a szerepkör megköveteli a jóváhagyási](./azure-ad-pim-approval-workflow.md) aktiválásához egy bejelentési értesítést röviden jelenik meg a böngészőben arról értesíti, a kérelem van függőben lévő jóváhagyási jobb felső sarkában.

    ![Irányuló kérelem függőben van az értesítési – képernyőkép](./media/pim-how-to-activate-role/PIM_Request_Pending_Toast2.png)

## <a name="use-a-role-immediately-after-activation"></a>Olyan szerepkört használjon az aktiválás után azonnal

Gyorsítótárazás miatt aktiválások nem történik meg azonnal a frissítés nélkül az Azure Portalon. Ha az késleltetések lehetőségét csökkentheti a szerepkör aktiválása után van szüksége, használhatja a **alkalmazás-hozzáférés** lapot a portálon. Ezen a lapon elérhető alkalmazások azonnal ellenőrizze az új szerepkör-hozzárendeléseket.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **alkalmazás-hozzáférés** lapot.

    ![A PIM-alkalmazás-hozzáférés – képernyőkép](./media/pim-how-to-activate-role/pim-application-access.png)

1. Kattintson a **Azure Active Directory** , nyissa meg újra a portálon a a **minden felhasználó** lapot.

    Erre a hivatkozásra kattint, frissítésének kényszerítése, ha van egy jelölőnégyzetet az új Azure AD szerepkör-hozzárendeléseket.

## <a name="deactivate-a-role"></a>A szerepkör inaktiválása
Miután aktiválta már a szerepkör, azt automatikusan inaktiválja az időkorlátot (jogosult időtartam) elérésekor.

Korai befejezi a rendszergazdai feladatok, ha egy szerepkört az Azure AD Privileged Identity Management alkalmazásban manuálisan is inaktiválhatók.  Válassza ki **saját szerepkörök**, válassza ki a szerepkört elkészült a használatával a **aktív szerepkör-hozzárendelések** csoportosítási, és válassza **inaktiválás**.  

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása
Abban az esetben, ha nincs szüksége a jóváhagyást igénylő szerepkörök aktiválási, függőben lévő kérelem bármikor lemondható. Egyszerűen válassza ki a **saját szerepkörök** navigációs beállítást, az Azure AD Privileged Identity Management alkalmazás bal oldali navigációs oszlopban.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) és az Azure AD Privileged Identity Management csempe kiválasztásával.

1. Válassza ki **saját szerepkörök**. Az Önhöz rendelt elérhető szerepköröket listáját a csoportosítást, amely a lap tetején jelennek meg.

1. Válasszon ki egy szerepkört.

1. Válassza ki a **jóváhagyásra van az aktiválás** fejléc a szerepkör aktiválási részletei panelen.

1. Válassza ki **Mégse** felső részén a **jóváhagyásra váró** panelen.

   ![Képernyőfelvétel a függőben lévő kérelem megszakítása](./media/pim-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png)

## <a name="next-steps"></a>További lépések
Ha érdeklik az Azure AD Privileged Identity Management többet, a következő hivatkozások több adat áll.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
