---
title: B2B együttműködési felhasználó naplózása és jelentése – Azure AD
description: A vendégfelhasználói tulajdonságok konfigurálhatók az Azure Active Directory B2B együttműködésében
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273285"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>B2B együttműködési felhasználó naplózása és jelentése
A vendégfelhasználók esetében a tagfelhasználókhoz hasonló naplózási funkciókkal rendelkezik. 

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok
A hozzáférési felülvizsgálatok segítségével rendszeresen ellenőrizheti, hogy a vendégfelhasználóknak továbbra is szükségük van-e az erőforrásokhoz való hozzáférésre. Az **Access-ellenőrzések** szolgáltatás az **Azure Active Directoryban** érhető el a Szervezeti**kapcsolatok** **kezelése** > csoportban. (Az Azure **Portalon minden szolgáltatásból** is kereshet "hozzáférési felülvizsgálatokat".) A hozzáférési felülvizsgálatok használatáról a [Vendéghozzáférés kezelése az Azure AD-hozzáférés-felülvizsgálatokkal című témakörben olvashat.](../governance/manage-guest-access-with-access-reviews.md)

## <a name="audit-logs"></a>Naplók

Az Azure AD naplózási naplók rendszer- és felhasználói tevékenységek rekordjait biztosítják, beleértve a vendégfelhasználók által kezdeményezett tevékenységeket is. A naplók eléréséhez az **Azure Active Directory** **figyelés e**területen válassza a Naplók **naplózása**lehetőséget. Íme egy példa a meghívásés megváltás története meghívott Sam Oogle:

![Képernyőkép és példa a napló kimenetére](./media/auditing-and-reporting/audit-log.png)

Akkor belevetik magukat az egyes események, hogy a részleteket. Nézzük meg például az elfogadás részleteit.

![Képernyőkép és a tevékenység részleteinek kimenete](./media/auditing-and-reporting/activity-details.png)

Ezeket a naplókat exportálhatja az Azure AD-ből is, és a választott jelentéskészítő eszközzel személyre szabott jelentéseket kaphat.

### <a name="next-steps"></a>További lépések

- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

