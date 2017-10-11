---
title: "Hibaelhárítás: \"Active Directory\" elem nem található vagy nem érhető el |} Microsoft Docs"
description: "Mi a teendő, ha az Active Directory menüpont nem jelenik meg az Azure felügyeleti portálján."
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: be3a797c4a405fd2f6636e67f4c961dd6d143486
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Hibaelhárítás: "Active Directory" elem nem található vagy nem érhető el
Az Azure Active Directory-szolgáltatások és szolgáltatások használatára vonatkozó utasításokat számos kezdődhet "nyissa meg az Azure felügyeleti portálra, és kattintson a **Active Directory**." Mi a teendő, ha az Active Directory kiterjesztés vagy a menü elem nem jelenik meg, vagy ha meg van jelölve, de **nem érhető el**? Ez a témakör célja. Azt ismerteti, hogy a feltételeket, amelyek alapján **Active Directory** nem jelenik meg vagy nem érhető el, és elmagyarázza, hogyan folytatható.

## <a name="active-directory-is-missing"></a>Az Active Directory hiányzik.
Általában egy **Active Directory** elem jelenik meg a bal oldali navigációs menü. Azure Active Directory eljárások utasítások feltételezik, hogy ezt az elemet a nézetben.

![Képernyőfelvétel: az Azure Active Directory](./media/active-directory-troubleshooting/typical-view.png)

Az Active Directory elem megjelenik a bal oldali navigációs menü, a következő feltételek teljesülése esetén. Ellenkező esetben az elem nem jelenik meg.

* Az aktuális felhasználó feliratkozva a Microsoft-fiók (korábbi nevén Windows Live ID).
  
    VAGY
* Az Azure-bérlőhöz tartozik egy könyvtárat, és a jelenlegi fiókot a címtár rendszergazdája.
  
    VAGY
* Az Azure-bérlőhöz legalább egy Azure AD-hozzáférés-vezérlés (ACS) névtérrel. További információkért lásd: [hozzáférés-vezérlési Namespace](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    VAGY
* Az Azure-bérlőhöz van legalább egy Azure multi-factor Authentication-szolgáltató. További információkért lásd: [Administering Azure többtényezős hitelesítési szolgáltatók](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Egy hozzáférés-vezérlés névtér vagy egy többtényezős hitelesítési szolgáltató létrehozásához kattintson a **+ új** > **alkalmazásszolgáltatások** > **Active Directory**.

Ahhoz, hogy a rendszergazdai jogosultságokat biztosít egy könyvtárat, kérjen meg egy rendszergazdát egy rendszergazdai szerepkört rendelni a fiókot. További információkért lásd: [rendszergazdai szerepkörök hozzárendelése](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory nem érhető el.
Amikor rákattint **+ új** > **alkalmazásszolgáltatások**, egy **Active Directory** elem jelenik meg. Pontosabban az Active Directory elem jelenik meg, ha, Directory, hozzáférés-vezérlés és többtényezős hitelesítésszolgáltató, például az Active Directory-szolgáltatások bármelyike nem érhető el az aktuális felhasználónak.

Azonban az oldal betöltése, amíg az elem nem aktív, és van megjelölve **nem érhető el**. Ez egy ideiglenes állapot. Várjon néhány másodpercet, ha a cikk elérhetővé válik. Ha a késés meghosszabbítják, gyakran frissíteni a weblap megoldja a problémát.

![Képernyőfelvétel: az Active Directory nem érhető el.](./media/active-directory-troubleshooting/not-available.png)

