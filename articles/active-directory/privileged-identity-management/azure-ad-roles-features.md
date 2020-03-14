---
title: Azure AD-szerepkörök kezelése Privileged Identity Managementban (PIM) | Microsoft Docs
description: Azure AD-szerepkörök kezelése hozzárendelési Privileged Identity Management (PIM)
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245979"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Az Azure AD-szerepkörök felügyeleti képességei Privileged Identity Management

Az Azure ad-szerepkörök kezelési felülete Privileged Identity Management frissült az Azure AD-szerepkörök és az Azure-erőforrás-szerepkörök kezelésének egységesítése érdekében. Korábban az Azure-beli erőforrás-szerepkörökhöz tartozó Privileged Identity Management néhány kulcsfontosságú funkciója volt, amelyek nem voltak elérhetők az Azure AD-szerepkörökhöz.

Ha a frissítés jelenleg ki van építve, a kettőt egyetlen felügyeleti kezelőfelületbe egyesítjük, és a szolgáltatásban ugyanazokat a funkciókat fogja használni az Azure AD-szerepkörökhöz, mint az Azure erőforrás-szerepkörökhöz. Ez a cikk a frissített funkciókról és a szükséges követelményekről tájékoztat.


## <a name="time-bound-assignments"></a>Időhöz kötött hozzárendelések

Korábban Privileged Identity Management Azure AD-szerepkörökhöz, a szerepkör-hozzárendeléseket a következő két lehetséges állapottal ismerték: *jogosult* és *állandó*. Most már beállíthatja a kezdési és befejezési időpontot az egyes hozzárendelési típusoknál. Ez a Hozzáadás négy lehetséges állapotot biztosít, amelyekben egy hozzárendelés helyezhető el:

- Jogosult véglegesen
- Aktív állandó
- Jogosult, megadott kezdő/záró dátumokkal a hozzárendeléshez
- Aktív, a hozzárendelés kezdő/záró dátumaival

Sok esetben akkor is, ha nem szeretné, hogy a felhasználók jogosultak legyenek a megfelelő hozzárendelésre, és minden alkalommal aktiválják a szerepköröket, a hozzárendelések lejárati idejének beállításával továbbra is biztosíthatja az Azure AD-szervezet Ha például vannak olyan ideiglenes felhasználók, akik jogosultak, érdemes lehet lejáratot beállítani, hogy automatikusan eltávolítsa őket a szerepkör-hozzárendelésből a munka befejezésekor.

## <a name="new-role-settings"></a>Új szerepkör-beállítások

Az Azure AD-szerepkörök új beállításait is hozzáadjuk. Korábban csak az aktiválási beállításokat konfigurálhatja szerepkör alapján. Az aktiválási beállítások, például a többtényezős hitelesítési követelmények, valamint az incidens/kérelmek jegyre vonatkozó követelmények az adott szerepkörre jogosult összes felhasználóra érvényesek voltak. Most beállíthatja, hogy egy adott felhasználónak a többtényezős hitelesítést kell-e végrehajtania, mielőtt aktiválni tudná a szerepkört. Emellett speciális vezérléssel láthatja el az adott szerepkörökhöz kapcsolódó Privileged Identity Management e-maileket.

## <a name="extend-and-renew-assignments"></a>Hozzárendelések kiterjesztése és megújítása

Amint kideríti az időkorlátot, az első kérdés, hogy mi történik, ha egy szerepkör lejár? Ebben az új verzióban két lehetőséget biztosítunk ehhez a forgatókönyvhöz:

- Kiterjesztés – ha egy szerepkör-hozzárendelés hamarosan lejár, a felhasználó a Privileged Identity Management használatával kérheti le a szerepkör-hozzárendelés kiterjesztését
- Megújítás – ha egy szerepkör-hozzárendelés lejárt, a felhasználó a Privileged Identity Management használatával kérheti a szerepkör-hozzárendelés megújítását

A felhasználó által kezdeményezett műveletekhez a globális rendszergazda vagy a Kiemelt szerepkörű rendszergazda jóváhagyása szükséges. A rendszergazdáknak többé nem kell megadniuk a lejáratok kezelésének üzleti tevékenységét. Csak meg kell várniuk a kiterjesztési vagy megújítási kérelmeket, és jóvá kell hagynia őket, ha a kérelem érvényes.

## <a name="api-changes"></a>API-változások

Ha az ügyfelek a frissített verziót az Azure AD-szervezetbe építették, a meglévő Graph API működése leáll. Át kell térnie az [Azure-erőforrás szerepköreinek Graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)használatára. Az API-t használó Azure AD-szerepkörök kezeléséhez a `/azureResources` az aláírásban `/aadroles`, majd a `resourceId`könyvtár-AZONOSÍTÓjának használatával kell felváltania.

Mindent megtettünk, hogy megismerjük a korábbi API-t használó ügyfeleket, hogy tájékoztassák őket erről a változásról az idő előtt. Ha az Azure AD-szervezet át lett helyezve az új verzióra, és továbbra is függ a régi API-tól, forduljon a csapathoz pim_preview@microsoft.comcímen.

## <a name="powershell-change"></a>PowerShell-változás

Azon ügyfelek esetében, akik az Azure AD-szerepkörökhöz tartozó Privileged Identity Management PowerShell-modult használják, a PowerShell nem fog működni a frissítéssel. Az előző parancsmagok helyett a Privileged Identity Management-parancsmagokat kell használnia az Azure AD előzetes verzió PowerShell-modulján belül. Telepítse az Azure AD PowerShell-modult a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Most már [elolvashatja a PIM-műveletekhez tartozó dokumentációt és mintákat ebben a PowerShell-modulban](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>További lépések

- [Egyéni Azure AD-szerepkör kiosztása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
