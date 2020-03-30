---
title: Azure AD egyéni szerepkör konfigurálása – Kiemelt identitáskezelés (PIM)
description: Az Azure AD egyéni szerepkörök konfigurálása a kiemelt identitáskezelésben (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498678"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Azure AD egyéni szerepkörök konfigurálása a kiemelt identitáskezelésben

A kiemelt szerepszintű rendszergazda módosíthatja a szerepkör-beállításokat, amelyek a felhasználóra vonatkoznak, amikor aktiválja a hozzárendelést egy egyéni szerepkörre, és más alkalmazás-rendszergazdák számára, akik egyéni szerepköröket rendelnek hozzá.

> [!NOTE]
> Az Azure AD egyéni szerepkörök nincsenek integrálva a beépített címtárszerepkörök előzetes verzió során. Ha a képesség általánosan elérhetővé válik, a szerepkör-kezelés a beépített szerepkörök ben kerül sor. Ha a következő szalagcím jelenik meg, ezeket a szerepköröket [a beépített szerepkörök ben](pim-how-to-activate-role.md) kell kezelni, és ez a cikk nem vonatkozik:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Az alábbi lépésekkel nyissa meg az Azure AD-szerepkör beállításait.

1. Jelentkezzen be a [kiemelt identitáskezelés](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) az Azure Portalon egy felhasználói fiókkal, amely hozzá van rendelve a kiemelt szerepkör-rendszergazdai szerepkörhöz.
1. Válassza az **Azure AD egyéni szerepkörök (előzetes verzió)** lehetőséget.

    ![Válassza ki az Azure AD egyéni szerepkörök előnézetét a jogosult szerepkör-hozzárendelések megtekintéséhez](./media/azure-ad-custom-roles-configure/settings-list.png)

1. A **Beállítások** lap megnyitásához válassza a **Beállítás** lehetőséget. Válassza ki a konfigurálni kívánt beállítások szerepkörét.
1. A **Szerkesztés gombra** a **Szerepkör-beállítások** lap megnyitásához kattintson a Szerkesztés gombra.

    ![Az Azure AD egyéni szerepkör megnyitása a beállítások szerkesztéséhez](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Szerepkör-beállítások

Számos beállítás konfigurálható.

### <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkör beállításainak konfigurálásakor az egyes hozzárendeléstípusokhoz (jogosult vagy aktív) két hozzárendelés-időtartam-beállítás közül választhat. Ezek a beállítások lesznek az alapértelmezett maximális időtartam, ha egy tag van rendelve a szerepkör privilegizált identitáskezelés.

A *jogosult* hozzárendelés időtartamának egyik beállítását választhatja.

- **Állandó jogosult hozzárendelés engedélyezése**: A rendszergazdák állandó jogosult tagságot rendelhetnek hozzá.
- **A jogosult hozzárendelés lejárata a következő után:** A rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelésnek meghatározott kezdési és befejezési dátummal kell rendelkeznie.

Az *aktív* hozzárendelés időtartamának egyik beállítását is megadhatja:

- **Állandó aktív hozzárendelés engedélyezése**: A rendszergazdák állandó aktív tagságot rendelhetnek hozzá.
- **Az aktív hozzárendelés lejárata a következő után:** A rendszergazdák megkövetelhetik, hogy minden aktív hozzárendelésnek meghatározott kezdési és befejezési dátummal kell rendelkeznie.

### <a name="require-azure-multi-factor-authentication"></a>Azure többtényezős hitelesítésének megkövetelése

A kiemelt identitáskezelés az Azure többtényezős hitelesítés opcionális kényszerítése két különböző forgatókönyv esetén.

- **Többtényezős hitelesítés megkövetelése aktív hozzárendelésesetén**

  Ha csak rövid ideig (például egy napig) szeretne egy tagot egy szerepkörhöz rendelni, akkor túl lassú lehet a hozzárendelt tagoktól az aktiválás kérelmezése. Ebben a forgatókönyvben a kiemelt identitáskezelés nem kényszerítheti ki a többtényezős hitelesítést, amikor a felhasználó aktiválja a szerepkör-hozzárendelést, mert már aktívak a szerepkörben attól a pillanattól kezdve, hogy hozzá vannak rendelve. Annak érdekében, hogy a hozzárendelést teljesítő rendszergazda az legyen, akinek mondja magát, jelölje be a **Többtényezős hitelesítés megkövetelése az aktív hozzárendeléshez** jelölőnégyzetet.

- **Többtényezős hitelesítés megkövetelése aktiváláskor**

  Megkövetelheti, hogy egy szerepkörhöz hozzárendelt jogosult felhasználók regisztrálják az Azure többtényezős hitelesítést, mielőtt aktiválhatnák. Ez a folyamat biztosítja, hogy az aktiválást kérő felhasználó az, akinek azt mondják, hogy ésszerű bizonyossággal. A beállítás kényszerítése védi a kritikus szerepköröket olyan helyzetekben, amikor a felhasználói fiók biztonsága sérülhet. Ha azt szeretné, hogy egy jogosult tag futtassa az Azure többtényezős hitelesítést az aktiválás előtt, válassza **a Többtényezős hitelesítés megkövetelése az aktiváláskor** jelölőnégyzetet.

További információ: [Többtényezős hitelesítés és kiemelt identitáskezelés.](pim-how-to-require-mfa.md)

### <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Az **Aktiválás maximális** időtartamcsúszkája segítségével beállíthatja azt a maximális időtartamot órákban, amerre egy szerepkör aktív marad, mielőtt lejárna. Ez az érték lehet 1 és 24 óra.

### <a name="require-justification"></a>Sorkizárás megkövetelése

Megkövetelheti, hogy a tagok adjanak meg indoklást az aktív hozzárendeléskor vagy az aktiváláskor. Ha sorkizárást szeretne igényelni, jelölje be a **Sorkizárás megkövetelése az aktív hozzárendelésnél** jelölőnégyzetet, vagy az **Igazítás megkövetelése aktiváláskor** jelölőnégyzetet.

### <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyás szükséges

Ha jóváhagyást szeretne igényelni egy szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Jelölje be az **Aktiváláshoz jóváhagyás megkövetelése** jelölőnégyzetet.
1. Válassza **a Jóváhagyók kijelölése lehetőséget** a Tag vagy csoport **kijelölése** lista megnyitásához.

    ![Az Azure AD egyéni szerepkör megnyitása a beállítások szerkesztéséhez](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Jelöljön ki legalább egy tagot vagy csoportot, majd kattintson a **Kijelölés gombra.** Legalább egy jóváhagyót ki kell jelölnie. Nincsenek alapértelmezett jóváhagyók. A kiválasztott jóváhagyók listájában megjelennek a beállítások.
1. Miután megadta a szerepkör-beállításokat, a módosítások mentéséhez válassza a **Frissítés** lehetőséget.

## <a name="next-steps"></a>További lépések

- [Egyéni Azure AD-szerepkör aktiválása](azure-ad-custom-roles-activate.md)
- [Azure AD egyéni szerepkör hozzárendelése](azure-ad-custom-roles-assign.md)
- [Azure AD egyéni szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
