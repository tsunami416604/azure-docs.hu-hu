---
title: Használható az Azure PIM - licenckövetelményekért |} A Microsoft Docs
description: Az Azure AD Privileged Identity Management (PIM) használata a licencelési követelményeket ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ad64fba0288aa5663e008484d6f0d2cbdff0bca6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187283"
---
# <a name="license-requirements-to-use-pim"></a>Licenckövetelmények vonatkoznak, miközben a PIM használata

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatához egy könyvtárat egy érvényes licenccel kell rendelkeznie. Ezenkívül licencet hozzá kell rendelni a rendszergazdák és a megfelelő felhasználók. Ez a cikk ismerteti a PIM használatát tervezi a licenckövetelmények vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek

A PIM használatát tervezi, a következő fizetett vagy a próbaverziós licencek a címtárban kell rendelkeznie:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5

További információkért lásd: [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Mely felhasználók rendelkeznie kell licencekkel?

Minden egyes rendszergazda vagy a felhasználó kommunikál, vagy fogad az egyik előnye a PIM-licenccel kell rendelkeznie. Példák erre vonatkozóan:

- A rendszergazdák az Azure AD-szerepkörök felügyelt PIM használatával
- A rendszergazdák a felügyelt, miközben a PIM használata Azure-erőforrásszerepkörök
- A kiemelt szerepkörű Rendszergazda szerepkörhöz hozzárendelt rendszergazdák
- Címtárszerepkörök a PIM használatával kezelhetők a jogosult felhasználók
- A felhasználók tudni a PIM kérések jóváhagyása vagy elutasítása
- A just-in-time vagy közvetlen (időalapú) hozzárendelést egy Azure-erőforrás szerepkörhöz rendelt felhasználók  
- Hozzáférési felülvizsgálat rendelt felhasználók
- A hozzáférési felülvizsgálatok végrehajtó felhasználók

Licencek hozzárendelése a által használt kapcsolatos információkért lásd: [hozzárendelni, vagy távolítsa el az Azure Active Directory portálon licenceit](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Mi történik, ha egy licenc lejár?

Ha egy Azure AD Premium P2, EMS E5 vagy próbaverziós licenc lejár, miközben a PIM szolgáltatás már nem a címtárban érhető el:

- Az Azure AD-szerepkörök állandó szerepkör-hozzárendelések nem érinti.
- A PIM szolgáltatáshoz az Azure Portalon, valamint a Graph API-parancsmagok és a PowerShell-felületek, miközben a PIM, már nem lesz elérhető a felhasználók emelt szintű szerepkörök aktiválása, emelt szintű hozzáférések kezeléséhez, vagy a kiemelt szerepkörök hozzáférési felülvizsgálatai.
- Az Azure AD-szerepkörök jogosult szerepkör-hozzárendelés lesz eltávolítva, ahogy a felhasználók többé nem fognak tudni emelt szintű szerepkörök aktiválása.
- Minden folyamatban lévő hozzáférési felülvizsgálatok az Azure AD-szerepkörök megszűnik, és a PIM-konfigurációs beállítások törlődnek.
- A PIM szerepkör-hozzárendelési módosítások a már nem küld e-maileket.

## <a name="next-steps"></a>További lépések

- [Ismerkedés a PIM-mel](pim-getting-started.md)
- [Nem kezelheti a PIM szerepkörök](pim-roles.md)
