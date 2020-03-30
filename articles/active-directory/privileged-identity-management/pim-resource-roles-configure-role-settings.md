---
title: Az Azure-erőforrásszerepkör-beállítások konfigurálása a PIM-ben – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja az Azure-erőforrás-szerepkör-beállításokat az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db330a875b8241b642bcbc71fb0866c9833ee7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638680"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Az Azure-erőforrásszerepkör-beállítások konfigurálása a Kiemelt identitáskezelés ben

Az Azure-erőforrás-szerepkör-beállítások konfigurálásakor megadhatja az Azure-erőforrás-szerepkör-hozzárendelésekre az Azure Active Directory (Azure AD) emelt szintű identitáskezelés (PIM) alkalmazott alapértelmezett beállításait. Az alábbi eljárásokkal konfigurálhatja a jóváhagyási munkafolyamatot, és megadhatja, hogy ki utasíthat jóvá vagy utasíthat el kéréseket.

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Az alábbi lépésekkel nyissa meg egy Azure-erőforrásszerepkör beállításait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörben lévő felhasználóval.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure-erőforrások lehetőséget.**

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést vagy felügyeleti csoportot.

    ![Az Azure-erőforrások lap felsorolja a kezelhető erőforrásokat](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Válassza a **Szerepkör-beállítások lehetőséget**.

    ![Az Azure-erőforrás-szerepköröket felsoroló szerepkör-beállítások lapja](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Válassza ki azt a szerepkört, amelynek beállításait konfigurálni szeretné.

    ![Szerepkör-beállítás részletei lap felsorolja több hozzárendelési és aktiválási beállítások](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. A **Szerkesztés gombra** a **Szerepkör-beállítások** ablaktábla megnyitásához válassza a Szerkesztés lehetőséget. Az első lapon frissítheti a szerepkör aktiválásának konfigurációját a Kiemelt identitáskezelés ben.

    ![Szerepkör-beállítások szerkesztése lap megnyitott Aktiválás lappal](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. A **hozzárendelés-beállítás** lap megnyitásához válassza a Hozzárendelés vagy a **Tovább: Hozzárendelés** gombot a lap alján. Ezek a beállítások szabályozzák a kiemelt identitáskezelési felületen belül végzett szerepkör-hozzárendeléseket.

    ![Szerepkör-hozzárendelés lap a szerepkörbeállítások lapon](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Az **Értesítés** vagy a **Tovább: Aktiválás** gomb segítségével lépjen a szerepkör értesítési beállításlapjára. Ezek a beállítások szabályozzák a szerepkörhöz kapcsolódó összes e-mail értesítést.

    ![Szerepkörértesítések lap a szerepkörbeállítások lapon](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    A szerepkör-beállítások lap **Értesítések** lapján a Kiemelt identitáskezelés lehetővé teszi, hogy részletesen szabályozható, hogy ki és milyen értesítéseket kap.

    - **E-mail kikapcsolása**<br>Bizonyos e-maileket úgy kapcsolhat ki, hogy törli az alapértelmezett címzett jelölőnégyzetét, és törli a további címzetteket.  

    - **E-mailek korlátozása a megadott e-mail címekre**<br>Az alapértelmezett címzetteknek küldött e-maileket az alapértelmezett címzettek jelölőnégyzetének törlésével kapcsolhatja ki. Ezután további e-mail címeket adhat hozzá további címzettként. Ha egynél több e-mail címet szeretne hozzáadni, válassza el őket pontosvesszővel (;).

    - **E-mailek küldése az alapértelmezett és a további címzetteknek is**<br>Az alapértelmezett címzett jelölőnégyzet bejelölésével és további címzettek e-mail címeinek hozzáadásával e-maileket küldhet az alapértelmezett címzetteknek.

    - **Csak kritikus e-mailek**<br>Az egyes e-mailek típusánál bejelölheti a jelölőnégyzetet, ha csak a kritikus e-maileket szeretné fogadni. Ez azt jelenti, hogy a kiemelt identitáskezelés továbbra is e-maileket küld a konfigurált címzetteknek, ha az e-mail azonnali műveletet igényel. Például e-maileket kérve a felhasználókat, hogy hosszabbítsák meg a szerepkör-hozzárendelés nem aktiválódik, míg egy e-maileket igénylő rendszergazdák számára, hogy jóváhagyja a kiterjesztés kérelmet fog előidézni.

1. A **szerepkör-beállítások** frissítéséhez bármikor válassza a Frissítés gombot.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkör beállításainak konfigurálásakor az egyes hozzárendeléstípusokhoz (jogosult és aktív) két hozzárendelés-időtartam-beállítás közül választhat. Ezek a beállítások lesznek az alapértelmezett maximális időtartam, amikor egy felhasználó van rendelve a szerepkör privilegizált identitáskezelés.

A **következő jogosult** hozzárendelési időtartam-beállítások közül választhat:

| | |
| --- | --- |
| **Állandó jogosult hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák állandó jogosult hozzárendelést rendelhetnek hozzá. |
| **Jogosult hozzárendelés lejárata után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelésnek meghatározott kezdési és befejezési dátummal kell rendelkeznie. |

Az **aktív** hozzárendelés idotartama lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó aktív hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák állandó aktív hozzárendelést rendelhetnek hozzá. |
| **Aktív hozzárendelés lejárata után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden aktív hozzárendelésnek meghatározott kezdési és befejezési dátummal kell rendelkeznie. |

> [!NOTE]
> Minden megadott befejezési dátummal rendelkező hozzárendelést az erőforrás-rendszergazdák megújíthatnak. A felhasználók önkiszolgáló kéréseket is kezdeményezhetnek a [szerepkör-hozzárendelések bővítésére vagy megújítására.](pim-resource-roles-renew-extend.md)

## <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A kiemelt identitáskezelés az Azure többtényezős hitelesítés opcionális kényszerítése két különböző forgatókönyv esetén.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Többtényezős hitelesítés megkövetelése aktív hozzárendelésesetén

Bizonyos esetekben előfordulhat, hogy egy felhasználót vagy csoportot rövid ideig (például egy napig) szeretne egy szerepkörhöz rendelni. Ebben az esetben a hozzárendelt felhasználóknak nem kell aktiválást kérni. Ebben a forgatókönyvben a kiemelt identitáskezelés nem kényszerítheti ki a többtényezős hitelesítést, ha a felhasználó a szerepkör-hozzárendelést használja, mert már aktív a szerepkörben a hozzárendelés időpontjától kezdve.

Annak érdekében, hogy a hozzárendelést teljesítő erőforrás-rendszergazda az legyen, akinek mondják magát, az aktív hozzárendelésnél a többtényezős hitelesítést az **aktív hozzárendelés többtényezős hitelesítésének megkövetelése** jelölőnégyzet bejelölésével kényszerítheti ki.

### <a name="require-multi-factor-authentication-on-activation"></a>Többtényezős hitelesítés megkövetelése aktiváláskor

Megkövetelheti a szerepkörre jogosult felhasználóktól, hogy az aktiválás előtt bizonyítsák, hogy kit használnak az Azure többtényezős hitelesítésével. A többtényezős hitelesítés biztosítja, hogy a felhasználó az, akinek mondja magát, és kellő bizonyossággal. A beállítás kényszerítése megvédi a kritikus erőforrásokat olyan helyzetekben, amikor a felhasználói fiók biztonsága sérülhet.

Ha az aktiválás előtt többtényezős hitelesítést szeretne igényelni, jelölje be a **Többtényezős hitelesítés megkövetelése az aktiváláskor** jelölőnégyzetet.

További információ: [Többtényezős hitelesítés és kiemelt identitáskezelés.](pim-how-to-require-mfa.md)

## <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Az **Aktiválás maximális** időtartamcsúszkája segítségével beállíthatja azt a maximális időtartamot órákban, amerre egy szerepkör aktív marad, mielőtt lejárna. Ez az érték 1 és 24 óra között lehet.

## <a name="require-justification"></a>Sorkizárás megkövetelése

Megkövetelheti, hogy a felhasználók az aktiváláskor adjanak meg üzleti indoklást. Ha sorkizárást szeretne igényelni, jelölje be az **Aktív hozzárendelés sorkizárásának megkövetelése** vagy **az Aktiválások megkövetelése** jelölőnégyzetet.

## <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyás szükséges

Ha jóváhagyást szeretne igényelni egy szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Jelölje be az **Aktiváláshoz jóváhagyás megkövetelése** jelölőnégyzetet.

1. Válassza **a Jóváhagyók kijelölése lehetőséget** a Tag vagy csoport **kijelölése** lap megnyitásához.

    ![Felhasználó vagy csoport ablaktáblájának kijelölése jóváhagyók kiválasztásához](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Jelöljön ki legalább egy felhasználót vagy csoportot, majd kattintson a **Kijelölés gombra.** A felhasználók és csoportok tetszőleges kombinációját hozzáadhatja. Legalább egy jóváhagyót ki kell jelölnie. Nincsenek alapértelmezett jóváhagyók.

    A kiválasztott jóváhagyók listájában megjelennek a beállítások.

1. Miután megadta az összes szerepkör-beállítást, válassza a **Frissítés** lehetőséget a módosítások mentéséhez.

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-resource-roles-assign-roles.md)
- [Biztonsági riasztások konfigurálása az Azure-erőforrásszerepkörökhöz a Kiemelt identitáskezelés ben](pim-resource-roles-configure-alerts.md)
