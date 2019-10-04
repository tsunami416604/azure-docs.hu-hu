---
title: A PIM használatára vonatkozó licencfeltételek – Azure Active Directory | Microsoft Docs
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
ms.date: 01/16/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83c31c2731a8e872dfd2750fced8b91d283d0892
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804074"
---
# <a name="license-requirements-to-use-pim"></a>A PIM használatára vonatkozó licencfeltételek

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatához a címtárnak érvényes licenccel kell rendelkeznie. A licenceket továbbá a rendszergazdák és a kapcsolódó felhasználók számára is hozzá kell rendelni. Ez a cikk a PIM használatára vonatkozó licencfeltételeket ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A PIM használatához a címtárnak a következő fizetős vagy próbaverziós licencek egyikével kell rendelkeznie:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- M5 Microsoft 365

További információkért lásd: [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Mely felhasználóknak kell licenccel rendelkezniük?

Minden olyan rendszergazdának vagy felhasználónak, aki a PIM-t vagy-t használja, licencre van szükség. Példák erre vonatkozóan:

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

Ha egy prémium szintű Azure AD P2, EMS E5 vagy próbaverziós licenc lejár, a PIM-funkciók többé nem lesznek elérhetők a címtárban:

- Az Azure AD-szerepkörökhöz tartozó állandó szerepkör-hozzárendelések nem lesznek hatással.
- A Azure Portal PIM szolgáltatása, valamint a PIM Graph API-parancsmagjai és PowerShell-kapcsolatai többé nem lesznek elérhetők a felhasználók számára a Kiemelt szerepkörök aktiválásához, a privilegizált hozzáférés felügyeletéhez, illetve a Kiemelt szerepkörök hozzáférési felülvizsgálatának elvégzéséhez.
- Az Azure AD-szerepkörök jogosult szerepkör-hozzárendelései el lesznek távolítva, mivel a felhasználók többé nem tudják aktiválni a Kiemelt szerepköröket.
- Az Azure AD-szerepkörök folyamatos hozzáférési felülvizsgálatai megszűnnek, és a rendszer eltávolítja a PIM konfigurációs beállításait.
- A PIM többé nem küld e-maileket a szerepkör-hozzárendelés változásairól.

## <a name="next-steps"></a>További lépések

- [PIM üzembe helyezése](pim-deployment-plan.md)
- [Ismerkedés a PIM-mel](pim-getting-started.md)
- [A PIM-ben nem felügyelhető szerepkörök](pim-roles.md)
