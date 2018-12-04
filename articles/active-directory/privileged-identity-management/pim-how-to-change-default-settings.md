---
title: Az Azure AD-címtár szerepkör-beállítások konfigurálása a PIM |} A Microsoft Docs
description: Ismerje meg az Azure AD-címtár szerepkör-beállítások konfigurálása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 11/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 87003a053823cfc257a3b6f41699feefadbbcc66
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835165"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>A PIM az Azure AD directory szerepkör-beállítások konfigurálása

A kiemelt szerepkörű rendszergazda testre szabhatja az Azure AD Privileged Identity Management (PIM) a szervezetben, mint például a felhasználó, aki jogosult szerepkör-hozzárendelés az aktiválás élményét módosítása.

## <a name="open-role-settings"></a>Nyissa meg a szerepkör-beállítások

Kövesse az alábbi lépéseket egy Azure AD-címtárbeli szerepkör beállításainak megnyitásához.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **beállítások**.

    ![Az Azure AD-címtárbeli szerepkörök - beállítások](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Kattintson a **szerepkörök**.

1. Kattintson a szerepkör, amelynek beállításait konfigurálni szeretné.

    ![Az Azure AD-címtárbeli szerepkörök - beállítások szerepkörök](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

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
  * Feltételes hozzáférésű rendszergazda
  * CRM-szolgáltatásadminisztrátor
  * Ügyfélszéf hozzáférés-jóváhagyója
  * Directory-írók
  * Exchange-rendszergazda
  * Globális rendszergazda
  * Information Protection-rendszergazda
  * Intune szolgáltatás rendszergazdája
  * Power BI-szolgáltatásadminisztrátor
  * Kiemelt szerepkörű rendszergazda
  * Biztonsági rendszergazda
  * SharePoint szolgáltatás-rendszergazda
  * Skype Vállalati verzió-rendszergazda
  * Felhasználói rendszergazda

További információkért lásd: [multi-factor authentication (MFA) és a PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Jóváhagyás szükséges

Ha szeretné a szerepkör aktiválásához jóváhagyás szükséges, kövesse az alábbi lépéseket.

1. Állítsa be a **jóváhagyás megkövetelése,** váltson **engedélyezve**. A panelen válassza ki a jóváhagyók beállításokkal bontja ki.

    ![Az Azure AD-címtárbeli szerepkörök - Settings - jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Ha Ön **nem** adja meg minden olyan jóváhagyónak, a kiemelt szerepkörű rendszergazdák lesznek a alapértelmezett jóváhagyónak. Kiemelt szerepkörű rendszergazdák jóváhagyása szükséges **összes** aktiválási kérelmek ehhez a szerepkörhöz.

1. A jóváhagyók megadásához kattintson **jóváhagyók kiválasztása**.

    ![Az Azure AD-címtárbeli szerepkörök - Settings - jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Egy vagy több jóváhagyók kiválasztása, és kattintson a **kiválasztása**. Felhasználók vagy csoportok kiválasztása Ajánlott legalább 2 jóváhagyó. A saját jóváhagyás nem engedélyezett.

    A beállításokat a kijelölt jóváhagyók listájában jelenik meg.

1. Miután megadta az összes szerepkör beállítást, kattintson a **mentése** a módosítások mentéséhez.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Az Azure AD-címtárbeli szerepkörökhöz tartozó biztonsági riasztások konfigurálása az PIM-ben](pim-how-to-configure-security-alerts.md)
