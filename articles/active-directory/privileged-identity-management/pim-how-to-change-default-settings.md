---
title: Az Azure AD szerepkör-beállítások konfigurálása az Azure Active Directory - a PIM |} A Microsoft Docs
description: Ismerje meg az Azure AD szerepkör-beállítások konfigurálása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cb8944578caf00fac5ca430e411f044a875f6af
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492277"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>A PIM az Azure AD szerepkör-beállítások konfigurálása

A kiemelt szerepkörű rendszergazda testre szabhatja az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) a szervezetben, mint például a felhasználó, aki jogosult szerepkör-hozzárendelés az aktiválás élményét módosítása.

## <a name="open-role-settings"></a>Nyissa meg a szerepkör-beállítások

Kövesse az alábbi lépéseket, nyissa meg a beállításokat az Azure AD-szerepkörhöz.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **beállítások**.

    ![Az Azure AD-szerepkörök – beállítások](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Kattintson a **szerepkörök**.

1. Kattintson a szerepkör, amelynek beállításait konfigurálni szeretné.

    ![Az Azure AD-szerepkörök – beállítások szerepkör](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    A beállítások lapon az egyes szerepkörökhöz a rendszer számos beállítást konfigurálhat. Ezek a beállítások csak a felhasználók, akik hatással **jogosult** hozzárendelések nem **állandó** hozzárendelések.

## <a name="activations"></a>Aktiválások

Használja a **aktiválások** csúszka, hogy a szerepkör aktív marad-e után járjon le (óra), a maximális idő beállítása. Ez az érték 1 és 72 óra közötti lehet.

## <a name="notifications"></a>Értesítések

Használja a **értesítések** kapcsolót, hogy adja meg, hogy a rendszergazdák e-mail értesítéseket kap, szerepkör aktiválásakor. Ez lehet hasznos, ha jogosulatlan vagy illegitimate aktiválások észlelése.

Ha a beállítása **engedélyezése**, értesítést kap:

- Kiemelt szerepkörű rendszergazda
- Biztonsági rendszergazda
- Globális rendszergazda

További információkért lásd: [E-mail-értesítések a PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Esemény/kérelmezési jegy

Használja a **esemény/kérelmezési jegy** kapcsolót, hogy adja meg, hogy jogosult rendszergazdák közé tartozik egy a jegy számát, amikor azok a szerepkör aktiválása szükséges. Ez akkor hasznos, ha szerepkör-hozzáférési eseményeket hajt végre.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Használja a **multi-factor Authentication** kapcsolót, hogy adja meg a felhasználóktól az identitásukat MFA-val, mielőtt aktiválna szerepkörökhöz. Csak rendelkeznek, a munkamenet, nem minden alkalommal, amikor azok a szerepkör aktiválásához egyszer ellenőrzése. Nincsenek két tippek a tartsa szem előtt az MFA engedélyezésekor:

* Az e-mail címüket a Microsoft-fiókkal rendelkező felhasználók (általában @outlook.com, de nem mindig) nem sikerült regisztrálni az Azure MFA-hoz. Szerepkörök hozzárendelése a Microsoft-fiókkal rendelkező felhasználók szeretne, ha kell tenni őket a állandó rendszergazdák vagy ahhoz a szerepkörhöz tartozó többtényezős hitelesítés letiltása.
* Nem lehet letiltani az MFA magas szintű jogosultsággal rendelkező szerepkörök az Azure ad és az Office 365-öt. Ez a biztonsági funkció, mert ezek a szerepkörök gondosan kell védeni:  
  
  * Számlázási adminisztrátor
  * Felhőalkalmazás-rendszergazda
  * Szabályozási ügyintéző
  * Feltételes hozzáférési rendszergazda
  * CRM-szolgáltatásgazda
  * Ügyfélszéf-hozzáférési jóváhagyó
  * Címtárírók
  * Exchange-rendszergazda
  * Globális rendszergazda
  * Information Protection-rendszergazda
  * Intune szolgáltatás rendszergazdája
  * Power BI-szolgáltatásgazda
  * Kiemelt szerepkörű rendszergazda
  * Biztonsági rendszergazda
  * SharePoint szolgáltatás-rendszergazda
  * Skype Vállalati verzió rendszergazdája
  * Felhasználói rendszergazda

További információkért lásd: [multi-factor authentication (MFA) és a PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Jóváhagyás szükséges

Ha szeretné a szerepkör aktiválásához jóváhagyás szükséges, kövesse az alábbi lépéseket.

1. Állítsa be a **jóváhagyás megkövetelése,** váltson **engedélyezve**. A panelen válassza ki a jóváhagyók beállításokkal bontja ki.

    ![Az Azure AD-szerepkörök - Settings - jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Ha Ön **nem** adja meg minden olyan jóváhagyónak, a kiemelt szerepkörű rendszergazdák lesznek a alapértelmezett jóváhagyónak. Kiemelt szerepkörű rendszergazdák jóváhagyása szükséges **összes** aktiválási kérelmek ehhez a szerepkörhöz.

1. A jóváhagyók megadásához kattintson **jóváhagyók kiválasztása**.

    ![Az Azure AD-szerepkörök - Settings - jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Egy vagy több jóváhagyók kiválasztása, és kattintson a **kiválasztása**. Felhasználók vagy csoportok kiválasztása Ajánlott legalább 2 jóváhagyó. A saját jóváhagyás nem engedélyezett.

    A beállításokat a kijelölt jóváhagyók listájában jelenik meg.

1. Miután megadta az összes szerepkör beállítást, kattintson a **mentése** a módosítások mentéséhez.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>További lépések

- [Az Azure AD PIM-szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Biztonsági riasztások az Azure AD-szerepkörök konfigurálása az PIM-ben](pim-how-to-configure-security-alerts.md)
