---
title: Azure AD-szerepkörök kezelése a kiemelt identitáskezelés (PIM) területén | Microsoft dokumentumok
description: Azure AD-szerepkörök kezelése a hozzárendelési jogosultságú identitáskezeléshez (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245979"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Az Azure AD-szerepkörök felügyeleti képességei a kiemelt identitáskezelésben

Az Azure AD-szerepkörök felügyeleti felülete a kiemelt identitáskezelésben frissült, hogy egyesítse az Azure AD-szerepkörök és az Azure-erőforrás-szerepkörök kezelésének módját. Korábban a kiemelt identitáskezelés az Azure-erőforrás-szerepkörök rendelkezik egy pár kulcsfontosságú funkciók, amelyek nem voltak elérhetők az Azure AD-szerepkörök.

A frissítés jelenleg bevezetve, a kettő egyesítése egyetlen felügyeleti élmény, és ebben ugyanazokat a funkciókat kapja az Azure AD-szerepkörök, mint az Azure erőforrás-szerepkörök. Ez a cikk tájékoztatja Önt a frissített funkciókról és az esetleges követelményekről.


## <a name="time-bound-assignments"></a>Időhöz kötött hozzárendelések

Korábban az Azure AD-szerepkörök kiemelt identitáskezelése során két lehetséges állapottal – *jogosult* és *állandó*– ismerte a szerepkör-hozzárendeléseket. Most antól beállíthatja az egyes hozzárendeléstípusok kezdési és befejezési idejét. Ez a kiegészítés négy lehetséges állapotot ad, amelyekben feladatot helyezhet el:

- Tartósan támogatható
- Aktív tartósan
- Jogosult, meghatározott kezdési/befejezési dátumokkal a hozzárendeléshez
- Aktív, megadott kezdési/befejezési dátumokkal a hozzárendeléshez

Sok esetben, még akkor is, ha nem szeretné, hogy a felhasználók jogosult hozzárendeléssel rendelkeznek, és minden alkalommal aktiválják a szerepköröket, továbbra is megvédheti az Azure AD-szervezetet a hozzárendelések lejárati idejének beállításával. Ha például van néhány ideiglenes felhasználója, akik jogosultak, érdemes lehet úgy beadni egy lejárati beállítást, hogy automatikusan eltávolítsa őket a szerepkör-hozzárendelésből, amikor a munka befejeződött.

## <a name="new-role-settings"></a>Új szerepkör-beállítások

Az Azure AD-szerepkörökhöz új beállításokat is hozzáadunk. Korábban csak szerepkörenként lehetett konfigurálni az aktiválási beállításokat. Ez azt, hogy az aktiválási beállítások, például a többtényezős hitelesítési követelmények és az incidens/kérelem jegy követelményei minden, egy adott szerepkörre jogosult felhasználóra vonatkoztak. Most beállíthatja, hogy egy adott felhasználónak többtényezős hitelesítést kell-e végrehajtania ahhoz, hogy aktiválhatna egy szerepkört. Emellett speciális vezérlést is biztosíthat a kiemelt identitáskezelés adott szerepkörökhöz kapcsolódó e-mailjei felett.

## <a name="extend-and-renew-assignments"></a>Hozzárendelések bővítése és megújítása

Amint kitalálja az időhöz kötött hozzárendelést, az első kérdés, amit feltehet, hogy mi történik, ha egy szerepkör lejár? Ebben az új verzióban két lehetőséget biztosítunk erre a forgatókönyvre:

- Kiterjesztés – Ha egy szerepkör-hozzárendelés lejár, a felhasználó a Kiemelt identitáskezelés segítségével bővítményt kérhet az adott szerepkör-hozzárendeléshez.
- Megújítás – Ha egy szerepkör-hozzárendelés lejárt, a felhasználó a Kiemelt identitáskezelés segítségével kérheti a szerepkör-hozzárendelés megújítását.

Mindkét felhasználó által kezdeményezett művelethez globális rendszergazda vagy kiemelt szereprendszergazda jóváhagyása szükséges. A rendszergazdáknak már nem kell a lejárati műveletek kezelésével kapcsolatosüzleti tevékenységet folytatniuk. Csak meg kell várniuk a bővítmény- vagy megújítási kérelmeket, és jóvá kell hagyniuk őket, ha a kérelem érvényes.

## <a name="api-changes"></a>API-módosítások

Amikor az ügyfelek a frissített verzió továbbfejlesztett az Azure AD-szervezet, a meglévő graph API leáll. Át kell térnie a [Graph API Azure-beli erőforrás-szerepkörök használatához.](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta) Az Azure AD-szerepkörök api `/azureResources` `/aadroles` használatával történő kezeléséhez cserélje fel `resourceId`az aláírást, és használja a címtárazonosítót a hoz.

Mindent megtettünk, hogy minden olyan ügyfelet megérjünk, aki az előző API-t használja, hogy előre megismerjük ezt a változást. Ha az Azure AD-szervezet átkerült az új verzióra, és továbbra is pim_preview@microsoft.coma régi API-tól függ, a csoport elérése a.

## <a name="powershell-change"></a>PowerShell-módosítás

Az Azure AD-szerepkörökhöz a kiemelt identitáskezelési PowerShell-modult használó ügyfelek számára a PowerShell leáll a frissítéssel. Az előző parancsmagok helyett az Azure AD Preview PowerShell-modulon belül kell használnia a kiemelt identitáskezelési parancsmagokat. Telepítse az Azure AD PowerShell-modult a [PowerShell-galériából.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Most már [elolvashatja a pim-műveletek dokumentációját és mintáit ebben a PowerShell-modulban.](powershell-for-azure-ad-roles.md)

## <a name="next-steps"></a>További lépések

- [Azure AD egyéni szerepkör hozzárendelése](azure-ad-custom-roles-assign.md)
- [Azure AD egyéni szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Azure AD egyéni szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
