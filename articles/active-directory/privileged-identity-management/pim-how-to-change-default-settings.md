---
title: Azure AD-szerepkör beállításainak konfigurálása a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure AD szerepkör beállításait a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee99a8e75fe8da85b1cf82623ed110991db24b66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021798"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Azure AD-szerepkör beállításainak konfigurálása Privileged Identity Management

A Kiemelt szerepkörű rendszergazda testre szabhatja Privileged Identity Management (PIM) Azure Active Directory (Azure AD) szervezetében, beleértve a jogosult szerepkör-hozzárendelést aktiváló felhasználó élményének módosítását is.

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

Az **értesítések** kapcsoló segítségével megadhatja, hogy a rendszergazdák kapnak-e e-mail-értesítéseket a szerepkörök aktiválása után. Ez az értesítés hasznos lehet a jogosulatlan vagy törvénytelen aktiválások észleléséhez.

Az **Engedélyezés**beállítás megadása esetén a rendszer a következő értesítéseket küldi el:

- Kiemelt szerepkörű rendszergazda
- Biztonsági rendszergazda
- Globális rendszergazda

További információkért tekintse [meg az e-mailes értesítéseket Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incidens/kérelem jegye

Az **incidens/kérelem jegy** kapcsolójának használatával megkövetelheti, hogy a jogosult rendszergazdák a szerepkörük aktiválása után is tartalmazzák a jegy számát. Ezzel a gyakorlattal hatékonyabbá teheti a szerepkör-hozzáférés naplózását.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Használja a **multi-Factor Authentication** kapcsolót annak megadásához, hogy a felhasználóknak meg kell-e adniuk identitását az MFA-ban, mielőtt aktiválni tudják a szerepköreiket. Csak egyszer kell meggyőződniük a saját identitásukat, nem minden alkalommal, amikor aktiválnak egy szerepkört. Az MFA engedélyezésekor két tippet kell szem előtt tartani:

- Azok a felhasználók, akik rendelkeznek Microsoft-fiókkal az e-mail-címükhez (általában @outlook.com, de nem mindig), nem regisztrálhatnak az Azure Multi-Factor Authentication. Ha a Microsoft-fiókkal rendelkező felhasználók számára szeretné hozzárendelni a szerepköröket, a rendszergazdáknak állandó rendszergazdai jogosultságokkal kell rendelkezniük, vagy le kell tiltaniuk a többtényezős hitelesítést a szerepkörhöz.
- Az Azure Multi-Factor Authentication nem tiltható le magas jogosultsági szintű szerepkörökhöz az Azure AD-ben és az Office 365-ben. Ez a biztonsági funkció a következő szerepkörök védelme érdekében nyújt segítséget:  
  
  - Azure Information Protection rendszergazda
  - Számlázási rendszergazda
  - Cloud Application Administrator
  - Megfelelőségi rendszergazda
  - Feltételes hozzáférésű rendszergazda
  - Dynamics 365-rendszergazda
  - Ügyfél-kulcstároló hozzáférési jóváhagyója
  - Címtár-írók
  - Exchange-rendszergazda
  - Globális rendszergazda
  - Intune-rendszergazda
  - Power BI rendszergazda
  - Kiemelt szerepkörű rendszergazda
  - Biztonsági rendszergazda
  - SharePoint-rendszergazda
  - Skype vállalati verzió-rendszergazda
  - Felhasználói rendszergazda

További információ: [multi-Factor Authentication és Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Jóváhagyás megkövetelése

Ha a szükséges jóváhagyást delegálni szeretné a szerepkör aktiválásához, kövesse az alábbi lépéseket.

1. Állítsa be a **jóváhagyás megkövetelése** kapcsolót az **engedélyezve**értékre. A panel kibontja a beállításokat a jóváhagyók kiválasztásához.

    ![Azure AD-szerepkörök – beállítások – jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Ha nem ad meg jóváhagyókat, a Kiemelt szerepkörű rendszergazda lesz az alapértelmezett jóváhagyó, és ezután jóvá kell hagynia a szerepkörre vonatkozó összes aktiválási kérést.

1. A jóváhagyók megadásához kattintson a **jóváhagyók kiválasztása**elemre.

    ![Azure AD-szerepkörök – beállítások – jóváhagyás szükséges](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Válasszon ki egy vagy több jóváhagyót a Kiemelt szerepkörű rendszergazda mellett, majd kattintson a **kiválasztás**elemre. Felhasználókat vagy csoportokat választhat ki. Javasoljuk, hogy legalább két jóváhagyó legyen. Még ha Ön is jóváhagyja a saját magát, nem hagyhat jóvá önálló szerepkör-aktiválást. A kiválasztott jóváhagyók listáján megjelennek a kiválasztások.

1. Miután megadta az összes szerepkör-beállítást, kattintson a **Save (Mentés** ) gombra a módosítások mentéséhez.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure AD-szerepkörökhöz tartozó biztonsági riasztások konfigurálása Privileged Identity Management](pim-how-to-configure-security-alerts.md)
