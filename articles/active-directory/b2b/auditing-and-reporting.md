---
title: B2B együttműködési felhasználó naplózása és jelentése – Azure AD
description: A vendég felhasználói tulajdonságok Azure Active Directory B2B együttműködésben konfigurálhatók.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273285"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>B2B csoportmunka-felhasználó naplózása és jelentése
A vendég felhasználóival a felhasználókhoz hasonló naplózási funkciókkal rendelkezhet. 

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok
A hozzáférési felülvizsgálatok segítségével rendszeres időközönként ellenőrizheti, hogy a vendég felhasználóknak továbbra is szükségük van-e az erőforrásokhoz való hozzáférésre. A **hozzáférési felülvizsgálatok** szolgáltatás **Azure Active Directory** a > **szervezeti kapcsolatok** **kezelése** területen érhető el. (A Azure Portal **összes szolgáltatásának** "hozzáférési felülvizsgálatok" kifejezésére is lehetősége van.) A hozzáférési felülvizsgálatok használatának megismeréséhez lásd: [a vendég hozzáférésének kezelése az Azure ad hozzáférési felülvizsgálatokkal](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Naplók

Az Azure AD-naplók rögzítik a rendszer-és felhasználói tevékenységeket, beleértve a vendég felhasználók által kezdeményezett tevékenységeket is. A naplók eléréséhez **Azure Active Directory**a **figyelés**területen válassza a **naplók**lehetőséget. Íme egy példa a Meghívási és visszaváltási előzményekre a meghívott Sam oogle:

![A napló kimenetét bemutató képernyőkép és példa](./media/auditing-and-reporting/audit-log.png)

Ezeket az eseményeket a részletek megismeréséhez is elsajátíthatja. Tekintse meg például az elfogadás részleteit.

![A tevékenység részleteinek kimenetét bemutató képernyőkép és példa](./media/auditing-and-reporting/activity-details.png)

Ezeket a naplókat az Azure AD-ből is exportálhatja, és az Ön által választott jelentéskészítő eszköz használatával testreszabott jelentéseket kaphat.

### <a name="next-steps"></a>További lépések

- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

