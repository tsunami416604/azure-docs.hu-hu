---
title: Azure-erőforrás szerepkör beállításainak konfigurálása a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure erőforrás-szerepkör beállításait a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88645d07ff213b0b7a56f2b081f0aaed01fd2929
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804203"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben

Az Azure erőforrás-szerepkör beállításainak konfigurálásakor meg kell határoznia az Azure-erőforrás szerepkör-hozzárendeléseire alkalmazott alapértelmezett beállításokat Azure Active Directory (Azure AD) Privileged Identity Management (PIM). A következő eljárásokkal konfigurálhatja a jóváhagyási munkafolyamatot, és meghatározhatja, hogy kik hagyhatják jóvá vagy tagadhatják meg a kérelmeket.

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Az alábbi lépéseket követve megnyithatja egy Azure-erőforrás szerepkör beállításait.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

1. Kattintson a kezelni kívánt erőforrásra, például egy előfizetésre vagy egy felügyeleti csoportra.

    ![Az Azure Resources lap a felügyelhető erőforrások listázása](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Kattintson a **szerepkör-beállítások**elemre.

    ![Szerepkör-beállítások lap, amely Azure-erőforrás-szerepköröket listáz](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Kattintson arra a szerepkörre, amelynek beállításait konfigurálni kívánja.

    ![Szerepkör-beállítás részletei lap, amely több hozzárendelési és aktiválási beállítást listáz](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Kattintson a **Szerkesztés** elemre a szerepkör-beállítások panel megnyitásához.

    ![Szerepkör-beállítások szerkesztése lap a hozzárendelési és aktiválási beállítások frissítésére szolgáló beállításokkal](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    Az egyes szerepkörökhöz tartozó szerepkör-beállítás ablaktáblán több beállítás is konfigurálható.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkörök beállításainak konfigurálásakor két hozzárendelési időtartam közül választhat az egyes hozzárendelési típusoknál (jogosult és aktív). Ezek a beállítások az alapértelmezett maximális időtartam lesz, amikor egy tag hozzá van rendelve a PIM szerepkörhöz.

A következő **jogosult** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó jogosult hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák állandó jogosult tagságot oszthatnak ki. |
| **Jogosult hozzárendelés lejárta a következő után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelés megadott kezdési és befejezési dátummal rendelkezzen. |

Emellett az alábbi **aktív** hozzárendelések időtartamára vonatkozó lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó aktív hozzárendelés engedélyezése** | Az erőforrás-rendszergazdák állandó aktív tagságot oszthatnak ki. |
| **Aktív hozzárendelés lejárta a következő után** | Az erőforrás-rendszergazdák megkövetelhetik, hogy minden aktív hozzárendeléshez meg legyen adva a kezdő és a záró dátum. |

> [!NOTE] 
> Az erőforrás-rendszergazdák megújítják a megadott befejezési dátummal rendelkező hozzárendeléseket. Emellett a tagok önkiszolgáló kérelmeket is indíthatnak a [szerepkör-hozzárendelések meghosszabbításához vagy megújításához](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A PIM két különböző forgatókönyv esetén az Azure Multi-Factor Authentication (MFA) opcionális kényszerítését teszi lehetővé.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Multi-Factor Authentication megkövetelése aktív hozzárendeléskor

Bizonyos esetekben előfordulhat, hogy egy tagot egy adott szerepkörhöz szeretne hozzárendelni egy rövid ideig (például egy nap). Ebben az esetben nincs szükségük a hozzárendelt tagokra az aktiválás kérelmezéséhez. Ebben az esetben a PIM nem tudja kikényszeríteni az MFA-t, ha a tag szerepkör-hozzárendelést használ, mivel azok már aktívak a szerepkörben a hozzárendelésük pillanatától kezdve.

Annak ellenőrzéséhez, hogy az erőforrás-rendszergazda teljesíti-e a hozzárendelést, kikényszerítheti az MFA használatát az aktív hozzárendeléshez, ha ellenőrzi a **szükséges multi-Factor Authentication az aktív hozzárendelési** mezőben.

### <a name="require-multi-factor-authentication-on-activation"></a>Multi-Factor Authentication megkövetelése aktiváláskor

Megkövetelheti egy szerepkör jogosult tagjai számára az MFA futtatását, mielőtt aktiválni tudnak. Ez a folyamat biztosítja, hogy az aktiválást kérő felhasználó számára ésszerű bizonyosságot biztosítson. A beállítás érvényesítése megvédi a kritikus erőforrásokat olyan helyzetekben, amikor a felhasználói fiók biztonsága sérült.

Ha meg szeretné követelni, hogy egy jogosult tag az aktiválás előtt futtasson MFA-t, jelölje be a **multi-Factor Authentication aktiválása az aktiváláskor** jelölőnégyzetet.

További információ: [többtényezős hitelesítés (MFA) és a PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Az aktiválás maximális időtartama

Az **aktiválás maximális időtartama** csúszkával állíthatja be azt a maximális időtartamot (óra), ameddig a szerepkör aktív marad a lejárat előtt. Ez az érték 1 és 24 óra között lehet.

## <a name="require-justification"></a>Indoklás megkövetelése

Megkövetelheti, hogy a tagok indoklást adjanak az aktív hozzárendeléshez vagy aktiváláskor. Az indoklás megköveteléséhez jelölje be az **aktív hozzárendeléshez szükséges indoklás** vagy az **aktiválás kötelező indoklása** jelölőnégyzetet.

## <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyásra van szükség

Ha szeretne jóváhagyást kérni egy szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Jelölje be a **jóváhagyás kötelező aktiválása** jelölőnégyzetet.

1. Kattintson a **jóváhagyók kiválasztása** elemre a tag vagy csoport kiválasztása ablaktábla megnyitásához.

    ![Válassza ki a tag vagy csoport ablaktáblát a jóváhagyók kiválasztásához.](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Válasszon ki legalább egy tagot vagy csoportot, majd kattintson a **kiválasztás**elemre. A tagok és csoportok tetszőleges kombinációját felveheti. Legalább egy jóváhagyót ki kell választania. Nincsenek alapértelmezett jóváhagyók.

    A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.

1. Miután megadta az összes szerepkör-beállítást, kattintson a **frissítés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>További lépések

- [Azure-beli erőforrás-szerepkörök kiosztása a PIM-ben](pim-resource-roles-assign-roles.md)
- [Biztonsági riasztások konfigurálása Azure-beli erőforrás-szerepkörökhöz a PIM-ben](pim-resource-roles-configure-alerts.md)
