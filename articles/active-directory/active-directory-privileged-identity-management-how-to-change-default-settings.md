---
title: "Aktiválási beállítások kezelése |} Microsoft Docs"
description: "Útmutató az Azure Active Directory Privileged Identity Management bővítmény kiemelt identitásokat alapértelmezett beállításait."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: f6cbcb6a-8a89-4077-afd8-06c94a64f4aa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 23605e89cd1846d2e06e48cb5d3e0191cb9e9b4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management szerepkör-aktiválási beállításainak kezelése
A kiemelt szerepkörű rendszergazda testre szabhatja az Azure AD Privileged Identity Management (PIM) a szervezetek, beleértve az élményét, a felhasználó, aki az aktiválás a megfelelő szerepkör-hozzárendelés módosítása.

## <a name="manage-the-role-activation-settings"></a>A szerepkör-aktiválási beállításainak kezelése
1. Lépjen a [Azure-portálon](https://portal.azure.com) válassza ki a **Azure AD Privileged Identity Management** alkalmazást az irányítópultról.
2. Válassza ki **kiemelt szerepköröket kezelése** > **beállítások** > **kiemelt szerepköröket**.
3. Válassza ki a szerepkört, amelynek beállításokat szeretne kezelni.

A beállítások lapon az egyes szerepkörökhöz számos a konfigurálható beállítások. Ezek a beállítások csak a jogosult rendszergazdai, nem állandó rendszergazdák felhasználók számára is vonatkoznak.

**Aktiválás**: az idő órában, amely a szerepkör érvényességének lejárata előtt. Ez az 1 és 72 óra közötti lehet.

**Értesítések**: választja-e a rendszer küld e-mailek rendszergazdák meggyőződött arról, hogy a szerepkör aktiválta. Ez lehet hasznos, ha a jogosulatlan vagy illegitimate aktiválások észlelése.

**Incidens/kérelmezési jegye**: dönthet úgy, hogy jogosult rendszergazdák számára, hogy a hibajegy száma tartalmazhat, ha azokat a szerepkör aktiválása szükséges-e. Ez akkor lehet hasznos, szerepkör-hozzáférési eseményeket végrehajtásakor.

**A multi-factor Authentication**: dönthet úgy, hogy a felhasználóknak kell igazolniuk az identitásukat, MFA-val, mielőtt azok a szerepkörök aktiválásához-e. Csak rendelkeznek, a munkamenet, nem minden alkalommal, amikor azok a szerepkör aktiválásához egyszer ellenőrzése. Nincsenek két tippek szem előtt tartani, ha a többtényezős hitelesítés engedélyezése:

* E-mail címüket a Microsoft-fiókkal rendelkező felhasználók számára (általában @outlook.com, de nem minden esetben) az Azure MFA nem regisztrálható. Ha azt szeretné, ha szerepköröket a Microsoft-fiókkal rendelkező felhasználók számára, kell tenni őket állandó rendszergazdák vagy a többtényezős hitelesítés letiltása adott szerepkörhöz.
* Nem tiltható le MFA kiemelt jogosultságokkal rendelkező szerepkörök az Azure AD és az Office365. Ez egy olyan biztonsági beállítás, mert ezeket a szerepköröket kell védeni:  
  
  * Alkalmazás-rendszergazda
  * Alkalmazás-Proxy kiszolgáló-rendszergazda
  * Számlázási rendszergazda  
  * Megfelelőségi rendszergazda  
  * CRM szolgáltatás-rendszergazda
  * Ügyfél kulcstároló hozzáférési jóváhagyó
  * Directory írója  
  * Exchange-rendszergazda  
  * Globális rendszergazda
  * Intune szolgáltatás-rendszergazda
  * Postaláda-rendszergazda  
  * Partner tier1 támogatása  
  * Partner tier2 támogatása  
  * Kiemelt szerepkörű rendszergazda   
  * Biztonsági rendszergazda  
  * SharePoint-rendszergazda  
  * Skype Vállalati verzió-rendszergazda  
  * Felhasználói fiók rendszergazdája  

További információ a többtényezős hitelesítés használata a PIM: [szükséges többtényezős hitelesítés hogyan](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

