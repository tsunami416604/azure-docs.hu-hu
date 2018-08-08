---
title: Szerepkör-aktiválási beállításokat kezelése |} A Microsoft Docs
description: Megtudhatja, hogyan módosíthatja az alapértelmezett beállításokat az Azure Active Directory Privileged Identity Management kiterjesztésű emelt jogosultsági szintű identitásait.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4ca74c001ba379b287c0c9799d90336eb187b2c2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619600"
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Szerepkör-aktiválási beállításokat, az Azure AD Privileged Identity Management kezelése
A kiemelt szerepkörű rendszergazda testre szabhatja az Azure AD Privileged Identity Management (PIM) a szervezetben, mint például a felhasználó, aki jogosult szerepkör-hozzárendelés az aktiválás élményét módosítása.

## <a name="manage-the-role-activation-settings"></a>A szerepkör-aktiválási beállítások felügyelete
1. Nyissa meg a [az Azure portal](https://portal.azure.com) , és válassza ki a **Azure AD Privileged Identity Management** alkalmazás az irányítópultról.
2. Válassza ki **kiemelt szerepkörök kezelése** > **beállítások** > **kiemelt szerepkörök**.
3. Válassza ki a szerepkört, amelynek beállításait szeretné kezelni.

A beállítások lapon az egyes szerepkörökhöz a rendszer számos beállítást konfigurálhat. Ezek a beállítások csak a felhasználók, akik jogosult rendszergazdák, nem állandó rendszergazdák hatással.

**Aktiválások**: az idő órában, amely egy szerepkör marad aktív, után járjon le. Ez az 1 és 72 óra közötti lehet.

**Értesítések**: választhat-e a rendszer e-mailt küld a rendszergazdák megerősíti, hogy a szerepkör aktiválta. Ez lehet hasznos, ha jogosulatlan vagy illegitimate aktiválások észlelése.

**Esemény/kérelmezési jegy**: választhat, hogy jogosult a rendszergazdák közé tartozik egy a jegy számát, amikor azok a szerepkör aktiválása szükséges-e. Ez akkor hasznos, ha szerepkör-hozzáférési eseményeket hajt végre.

**A multi-factor Authentication szolgáltatás**: választhat-e a felhasználóktól az identitásukat MFA-val, mielőtt aktiválna szerepkörökhöz. Csak rendelkeznek, a munkamenet, nem minden alkalommal, amikor azok a szerepkör aktiválásához egyszer ellenőrzése. Nincsenek két tippek a tartsa szem előtt az MFA engedélyezésekor:

* Az e-mail címüket a Microsoft-fiókkal rendelkező felhasználók (általában @outlook.com, de nem mindig) nem sikerült regisztrálni az Azure MFA-hoz. Szerepkörök hozzárendelése a Microsoft-fiókkal rendelkező felhasználók szeretne, ha kell tenni őket a állandó rendszergazdák vagy ahhoz a szerepkörhöz tartozó többtényezős hitelesítés letiltása.
* Nem lehet letiltani az MFA magas szintű jogosultsággal rendelkező szerepkörök az Azure ad és az Office 365-öt. Ez a biztonsági funkció, mert ezek a szerepkörök gondosan kell védeni:  
  
  * Alkalmazás-rendszergazda
  * Alkalmazás-Proxy kiszolgáló-rendszergazda
  * Számlázási rendszergazda  
  * Szabályozási ügyintéző  
  * CRM-szolgáltatásadminisztrátor
  * Ügyfél Ügyfélszéf hozzáférés-jóváhagyója
  * Címtár-írója  
  * Exchange-rendszergazda  
  * Globális rendszergazda
  * Intune szolgáltatás rendszergazdája
  * Postaláda-adminisztrátor  
  * Partnerek 1. rétegbeli támogatása  
  * Partnerek 2. rétegbeli támogatása  
  * Kiemelt szerepkörű rendszergazda   
  * Biztonsági rendszergazda  
  * SharePoint-rendszergazda  
  * Skype Vállalati verzió-rendszergazda  
  * Felhasználóifiók-adminisztrátor  

A PIM többtényezős hitelesítés használatával kapcsolatos további információk: [többtényezős hitelesítés megkövetelése hogyan](pim-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

