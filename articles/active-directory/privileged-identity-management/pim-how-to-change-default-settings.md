---
title: Azure AD-szerepkör beállításainak konfigurálása a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure AD szerepkör beállításait a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804439"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Az Azure AD szerepkör beállításainak konfigurálása a PIM-ben

A Kiemelt szerepkörű rendszergazda testre szabhatja Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szervezetében, beleértve a jogosult szerepkör-hozzárendelést aktiváló felhasználó élményének módosítását is.

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Kövesse az alábbi lépéseket egy Azure AD-szerepkör beállításainak megnyitásához.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson a **Beállítások**elemre.

    ![Azure AD-szerepkörök – beállítások](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Kattintson a **szerepkörök**elemre.

1. Kattintson arra a szerepkörre, amelynek beállításait konfigurálni kívánja.

    ![Azure AD-szerepkörök – beállítások szerepkörei](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Az egyes szerepkörök beállítások lapján több beállítás is konfigurálható. Ezek a beállítások csak azokra a felhasználókra vonatkoznak, akik **jogosult** hozzárendelések, nem **végleges** hozzárendelések.

## <a name="activations"></a>Aktiválások

Az **aktiválások** csúszkával állíthatja be azt a maximális időtartamot (órában), ameddig a szerepkör aktív marad a lejárat előtt. Ez az érték 1 és 72 óra közötti lehet.

## <a name="notifications"></a>Értesítések

Az **értesítések** kapcsoló segítségével megadhatja, hogy a rendszergazdák kapnak-e e-mail-értesítéseket a szerepkörök aktiválása után. Ez hasznos lehet a jogosulatlan vagy törvénytelen aktiválások észleléséhez.

Az **Engedélyezés**beállítás megadása esetén a rendszer a következő értesítéseket küldi el:

- Kiemelt szerepkörű rendszergazda
- Biztonsági rendszergazda
- Globális rendszergazda

További információ: [e-mailes értesítések a PIM-ben](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incidens/kérelem jegye

Az **incidens/kérelem jegy** kapcsolójának használatával megadhatja, hogy a jogosult rendszergazdák megkövetelhetik-e a jegy számának megadását a szerepkörük aktiválásakor. Ez akkor lehet hasznos, ha szerepkör-hozzáférési naplózást hajt végre.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Használja a **multi-Factor Authentication** kapcsolót annak megadásához, hogy a felhasználóknak meg kell-e adniuk identitását az MFA-ban, mielőtt aktiválni tudják a szerepköreiket. Csak egyszer kell ellenőrizniük, hogy csak egyszer kell-e meggyőződnie, nem minden alkalommal, amikor aktiválnak egy szerepkört. Az MFA engedélyezésekor két tippet kell szem előtt tartani:

* Azok a felhasználók, akik rendelkeznek Microsoft-fiókkal az @outlook.come-mail-címeiknek (általában, de nem mindig), nem regisztrálhatják az Azure MFA-t Ha a Microsoft-fiókkal rendelkező felhasználók számára szeretné hozzárendelni a szerepköröket, akkor a rendszergazdáknak állandó rendszergazdai jogosultságokkal kell rendelkezniük, vagy le kell tiltaniuk az MFA-t.
* Az MFA nem tiltható le magas jogosultsági szintű szerepkörökhöz az Azure AD-ben és a Office 365-ben. Ez egy biztonsági funkció, mivel ezeket a szerepköröket gondosan védeni kell:  
  
  * Azure Information Protection rendszergazda
  * Számlázási adminisztrátor
  * Felhőalkalmazás-rendszergazda
  * Szabályozási ügyintéző
  * Feltételes hozzáférésű rendszergazda
  * CRM-szolgáltatásgazda
  * Ügyfélszéf-hozzáférési jóváhagyó
  * Címtárírók
  * Exchange-rendszergazda
  * Globális rendszergazda
  * Intune szolgáltatás rendszergazdája
  * Power BI-szolgáltatásgazda
  * Kiemelt szerepkörű rendszergazda
  * Biztonsági rendszergazda
  * SharePoint szolgáltatás-rendszergazda
  * Skype Vállalati verzió rendszergazdája
  * Felhasználói rendszergazda

További információ: [többtényezős hitelesítés (MFA) és a PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Jóváhagyás szükséges

Ha szeretne jóváhagyást kérni egy szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Állítsa be a **jóváhagyás megkövetelése** kapcsolót az **engedélyezve**értékre. A panel kibontja a beállításokat a jóváhagyók kiválasztásához.

    ![Azure AD-szerepkörök – beállítások – jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Ha **nem** ad meg jóváhagyókat, a Kiemelt szerepkörű rendszergazdák az alapértelmezett jóváhagyók lesznek. A Kiemelt szerepkörű rendszergazdáknak **minden** aktiválási kérelmet jóvá kell hagynia ehhez a szerepkörhöz.

1. A jóváhagyók megadásához kattintson a **jóváhagyók kiválasztása**elemre.

    ![Azure AD-szerepkörök – beállítások – jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Válasszon ki egy vagy több jóváhagyót, majd kattintson a **kiválasztás**elemre. Felhasználókat vagy csoportokat választhat ki. Legalább két jóváhagyó ajánlott. Az önjóváhagyás nem engedélyezett.

    A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.

1. Miután megadta az összes szerepkör-beállítást, kattintson a **Save (Mentés** ) gombra a módosítások mentéséhez.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök kiosztása a PIM-ben](pim-how-to-add-role-to-user.md)
- [Biztonsági riasztások konfigurálása Azure AD-szerepkörökhöz a PIM-ben](pim-how-to-configure-security-alerts.md)
