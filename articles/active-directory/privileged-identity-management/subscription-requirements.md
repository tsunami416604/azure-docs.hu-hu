---
title: A kiemelt identitáskezelés használatára vonatkozó licenckövetelmények – Azure Active Directory | Microsoft dokumentumok
description: Az Azure AD emelt szintű identitáskezelés (PIM) használatának licencelési követelményeit ismerteti.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932317"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>A kiemelt identitáskezelés használatára vonatkozó licenckövetelmények

Az Azure Active Directory (Azure AD) privilegizált identitáskezelés (PIM) használatához a címtárnak érvényes licenccel kell rendelkeznie. Ezenkívül a licenceket hozzá kell rendelni a rendszergazdákhoz és az érintett felhasználókhoz. Ez a cikk a kiemelt identitáskezelés használatának licenckövetelményeit ismerteti.

## <a name="valid-licenses"></a>Érvényes licencek

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Hány licenccel kell rendelkeznie?

Győződjön meg arról, hogy a címtár legalább annyi Azure AD Premium P2 licenccel rendelkezik, mint ahány alkalmazottal rendelkezik, akik a következő feladatokat fogják ellátni:

- A PIM-vel kezelt Azure AD-szerepkörökhöz jogosultként hozzárendelt felhasználók
- Aktiválási kérelmeket jóváhagyó vagy elutasító felhasználók a PIM-ben
- Az Azure-erőforrásszerepkörhöz hozzárendelt felhasználók just-in-time vagy közvetlen (időalapú) hozzárendelésekkel  
- Hozzáférés-felülvizsgálathoz rendelt felhasználók
- Hozzáférési ellenőrzéseket végző felhasználók

Az Azure AD Premium P2 licencek **nem** szükségesek a következő feladatokhoz:

- Nincs szükség licencre a PIM-et beállító, házirendeket konfiguráló, riasztásokat tartalmazó és hozzáférési felülvizsgálatokat beállító globális rendszergazdai vagy kiemelt szerepszintű szerepkörrel rendelkező felhasználók számára.

A licencekről további információt [a Licencek hozzárendelése vagy eltávolítása az Azure Active Directory portálon című témakörben talál.](../fundamentals/license-users-groups.md)

## <a name="example-license-scenarios"></a>Példa licencforgatókönyvekre

Íme néhány példa licencforgatókönyv, amely segít meghatározni a licencek számát.

| Forgatókönyv | Számítás | Licencek száma |
| --- | --- | --- |
| A Woodgrove Bank 10 rendszergazdával rendelkezik a különböző részlegek számára, és 2 globális rendszergazdával, akik konfigurálják és kezelik a PIM-et. Öt adminisztrátort tesznek jogosulttá. | Öt licenc a jogosult rendszergazdák számára | 5 |
| A Graphic Design Institute 25 rendszergazdával rendelkezik, akik közül 14-et a PIM-en keresztül kezelnek. A szerepkör aktiválásához jóváhagyás szükséges, és a szervezetben három különböző felhasználó hagyja jóvá az aktiválásokat. | 14 licenc a jogosult szerepkörökhöz + három jóváhagyó | 17 |
| A Contoso 50 rendszergazdával rendelkezik, amelyek közül 42-t a PIM-en keresztül kezelnek. A szerepkör aktiválásához jóváhagyás szükséges, és a szervezetben öt különböző felhasználó hagyja jóvá az aktiválásokat. A Contoso a rendszergazdai szerepkörökhöz rendelt felhasználók havi felülvizsgálatát is végzi, és az ellenőrzők azok a felhasználók kezelői, amelyek közül hat nem a PIM által kezelt rendszergazdai szerepkörben van. | 42 licenc a jogosult szerepkörökhöz + öt jóváhagyó + hat ellenőrző | 53 |

## <a name="what-happens-when-a-license-expires"></a>Mi történik, ha lejár a licenc?

Ha egy Azure AD Premium P2, EMS E5 vagy próbalicenc lejár, a kiemelt identitáskezelési funkciók a továbbiakban nem lesznek elérhetők a címtárban:

- Az Azure AD-szerepkörökhöz való állandó szerepkör-hozzárendelések nem változnak.
- A kiemelt identitáskezelési szolgáltatás az Azure Portalon, valamint a Graph API-parancsmagok és a PowerShell-felületek kiemelt identitáskezelés, a továbbiakban nem lesz elérhető a felhasználók számára a kiemelt szerepkörök aktiválása, a kiemelt hozzáférés kezelése, vagy végre a kiemelt szerepkörök felülvizsgálatának elérése.
- Az Azure AD-szerepkörök jogosult szerepkör-hozzárendelései törlődnek, mivel a felhasználók már nem aktiválhatják a kiemelt szerepköröket.
- Az Azure AD-szerepkörök folyamatos hozzáférési felülvizsgálata véget ér, és a kiemelt identitáskezelés konfigurációs beállításai törlődnek.
- A kiemelt identitáskezelés a továbbiakban nem küld e-maileket a szerepkör-hozzárendelés módosításairól.

## <a name="next-steps"></a>További lépések

- [A Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)
- [A Privileged Identity Management használatának első lépései](pim-getting-started.md)
- [A kiemelt identitáskezelésben nem kezelhető szerepkörök](pim-roles.md)
