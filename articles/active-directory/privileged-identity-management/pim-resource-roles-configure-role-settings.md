---
title: Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure erőforrás-szerepkör beállításait a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a302e723aa898a62bc43ecb38343bcf026dc769e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022940"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management

Az Azure erőforrás-szerepkör beállításainak konfigurálásakor meg kell határoznia az Azure-erőforrás szerepkör-hozzárendeléseire alkalmazott alapértelmezett beállításokat Azure Active Directory (Azure AD) Privileged Identity Management (PIM). A következő eljárásokkal konfigurálhatja a jóváhagyási munkafolyamatot, és meghatározhatja, hogy kik hagyhatják jóvá vagy tagadhatják meg a kérelmeket.

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Az alábbi lépéseket követve megnyithatja egy Azure-erőforrás szerepkör beállításait.

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com/) a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörrel rendelkező felhasználóval.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést vagy egy felügyeleti csoportot.

    ![Az Azure Resources lap a felügyelhető erőforrások listázása](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Válassza a **szerepkör-beállítások**lehetőséget.

    ![Szerepkör-beállítások lap, amely Azure-erőforrás-szerepköröket listáz](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Válassza ki azt a szerepkört, amelynek beállításait konfigurálni kívánja.

    ![Szerepkör-beállítás részletei lap, amely több hozzárendelési és aktiválási beállítást listáz](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. A szerepkör-beállítások ablaktábla megnyitásához válassza a **Szerkesztés** lehetőséget.

    ![Szerepkör-beállítások szerkesztése lap a hozzárendelési és aktiválási beállítások frissítésére szolgáló beállításokkal](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    Az egyes szerepkörökhöz tartozó szerepkör-beállítás ablaktáblán több beállítás is konfigurálható.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkörök beállításainak konfigurálásakor két hozzárendelési időtartam közül választhat az egyes hozzárendelési típusoknál (jogosult és aktív). Ezek a beállítások az alapértelmezett maximális időtartam lesz, amikor egy felhasználó a szerepkörhöz van rendelve Privileged Identity Managementban.

A következő **jogosult** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó jogosult hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák állandó jogosult hozzárendelést rendelhetnek hozzá. |
| **Jogosult hozzárendelés lejárta a következő után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelés megadott kezdési és befejezési dátummal rendelkezzen. |

Emellett az alábbi **aktív** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó aktív hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák kioszthatnak állandó aktív hozzárendelést. |
| **Aktív hozzárendelés lejárta a következő után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden aktív hozzárendeléshez meg legyen adva a kezdő és a záró dátum. |

> [!NOTE]
> Az erőforrás-rendszergazdák megújítják a megadott befejezési dátummal rendelkező hozzárendeléseket. Emellett a felhasználók önkiszolgáló kérelmeket is indíthatnak a [szerepkör-hozzárendelések meghosszabbításához vagy megújításához](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A Privileged Identity Management két különböző forgatókönyv esetén az Azure Multi-Factor Authentication opcionális kényszerítését teszi lehetővé.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication megkövetelése aktív hozzárendeléskor

Bizonyos esetekben előfordulhat, hogy egy felhasználót vagy csoportot egy adott szerepkörhöz szeretne hozzárendelni egy rövid ideig (például egy nap). Ebben az esetben a hozzárendelt felhasználóknak nem kell aktiválást kérniük. Ebben a forgatókönyvben a Privileged Identity Management nem tudja kikényszeríteni a többtényezős hitelesítést, ha a felhasználó a szerepkör-hozzárendelését használja, mert már aktívak a szerepkörben a hozzárendelésük időpontjában.

Annak biztosítása érdekében, hogy az erőforrás-rendszergazda teljesítse a hozzárendelést, ki is mondhatja őket, a többtényezős hitelesítés az aktív hozzárendelésre való kikényszeríthető az **aktív hozzárendelés multi-Factor Authenticationának megkövetelése** jelölőnégyzet bejelölésével.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication megkövetelése aktiváláskor

Megkövetelheti, hogy azok a felhasználók, akik jogosultak a szerepkörre, bizonyítani tudják, kik használják az Azure Multi-Factor Authentication az aktiválás előtt. A többtényezős hitelesítés biztosítja, hogy a felhasználó, aki azt mondják, ésszerű bizonyossággal rendelkeznek. A beállítás érvényesítése megvédi a kritikus erőforrásokat olyan helyzetekben, amikor a felhasználói fiók biztonsága sérült.

Ha a többtényezős hitelesítést az aktiválás előtt szeretné megkövetelni, jelölje be a **multi-Factor Authentication aktiválása az aktiváláskor** jelölőnégyzetet.

További információ: [multi-Factor Authentication és Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Az **aktiválás maximális időtartama** csúszkával állíthatja be azt a maximális időtartamot (óra), ameddig a szerepkör aktív marad a lejárat előtt. Ez az érték 1 és 24 óra között lehet.

## <a name="require-justification"></a>Indoklás megkövetelése

Megkövetelheti, hogy a felhasználók az aktiváláskor üzleti indoklást adjanak. Az indoklás megköveteléséhez jelölje be az **aktív hozzárendeléshez szükséges indoklás** vagy az **aktiválás kötelező indoklása** jelölőnégyzetet.

## <a name="require-approval-to-activate"></a>Jóváhagyás megkövetelése az aktiváláshoz

Ha szeretne jóváhagyást kérni egy szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Jelölje be a **jóváhagyás kötelező aktiválása** jelölőnégyzetet.

1. Válassza a **jóváhagyók kiválasztása** lehetőséget a **tag vagy csoport kiválasztása** lap megnyitásához.

    ![Válassza ki a felhasználó vagy csoport ablaktáblát a jóváhagyók kiválasztásához.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Válasszon ki legalább egy felhasználót vagy csoportot, majd kattintson a **kiválasztás**gombra. A felhasználók és csoportok tetszőleges kombinációját felveheti. Legalább egy jóváhagyót ki kell választania. Nincsenek alapértelmezett jóváhagyók.

    A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.

1. Miután megadta az összes szerepkör-beállítást, válassza a **frissítés** lehetőséget a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések

- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Azure-beli erőforrás-szerepkörökre vonatkozó biztonsági riasztások konfigurálása Privileged Identity Management](pim-resource-roles-configure-alerts.md)
