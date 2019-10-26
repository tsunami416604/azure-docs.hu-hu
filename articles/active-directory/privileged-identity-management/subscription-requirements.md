---
title: A Privileged Identity Management-Azure Active Directory használatára vonatkozó licencfeltételek | Microsoft Docs
description: A Azure AD Privileged Identity Management (PIM) használatára vonatkozó licencelési követelményeket ismerteti.
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895118"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>A Privileged Identity Management használatára vonatkozó licencfeltételek

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatához a címtárnak érvényes licenccel kell rendelkeznie. A licenceket továbbá a rendszergazdák és a kapcsolódó felhasználók számára is hozzá kell rendelni. Ez a cikk a Privileged Identity Management használatára vonatkozó licencfeltételeket ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Privileged Identity Management használatához a címtárnak a következő fizetős vagy próbaverziós licencek egyikével kell rendelkeznie:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5
- M5 Microsoft 365

További információkért lásd: [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Mely felhasználóknak kell licenccel rendelkezniük?

Minden olyan rendszergazdának vagy felhasználónak, aki interakciót végez, vagy a Privileged Identity Management kedvezményt kap, licenccel kell rendelkeznie. Példák:

- A PIM használatával felügyelt Azure AD-szerepkörökkel rendelkező rendszergazdák
- A PIM használatával felügyelt Azure-erőforrás-szerepkörökkel rendelkező rendszergazdák
- A Kiemelt szerepkörű rendszergazda szerepkörhöz rendelt rendszergazdák
- A PIM használatával felügyelt Azure AD-szerepköröknek megfelelőként hozzárendelt felhasználók
- A felhasználók a PIM-ban hagyhatják jóvá/utasítsa el a kérelmeket
- Az igény szerinti vagy közvetlen (időalapú) hozzárendeléssel rendelkező Azure-erőforrás-szerepkörhöz hozzárendelt felhasználók  
- Hozzáférési felülvizsgálathoz rendelt felhasználók
- Hozzáférési felülvizsgálatokat végrehajtó felhasználók

További információ a licencek felhasználási módjaihoz való hozzárendeléséről: [licencek kiosztása vagy eltávolítása a Azure Active Directory portál használatával](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Mi történik, ha egy licenc lejár?

Ha egy prémium szintű Azure AD P2, EMS E5 vagy próbaverziós licenc lejár, Privileged Identity Management funkciók többé nem lesznek elérhetők a címtárban:

- Az Azure AD-szerepkörökhöz tartozó állandó szerepkör-hozzárendelések nem lesznek hatással.
- A Azure Portal Privileged Identity Management szolgáltatása, valamint az Graph API-parancsmagok és a Privileged Identity Management PowerShell-kapcsolatai többé nem lesznek elérhetők a felhasználók számára a Kiemelt szerepkörök aktiválásához, a privilegizált hozzáférés felügyeletéhez vagy a végrehajtáshoz Kiemelt szerepkörök hozzáférési felülvizsgálatai.
- Az Azure AD-szerepkörök jogosult szerepkör-hozzárendelései el lesznek távolítva, mivel a felhasználók többé nem tudják aktiválni a Kiemelt szerepköröket.
- Az Azure AD-szerepkörök folyamatos hozzáférési felülvizsgálatai megszűnnek, és Privileged Identity Management a konfigurációs beállítások törlődnek.
- Privileged Identity Management többé nem fog e-maileket küldeni a szerepkör-hozzárendelés változásairól.

## <a name="next-steps"></a>Következő lépések

- [Privileged Identity Management üzembe helyezése](pim-deployment-plan.md)
- [Privileged Identity Management használatának megkezdése](pim-getting-started.md)
- [Nem felügyelhető szerepkörök Privileged Identity Management](pim-roles.md)
