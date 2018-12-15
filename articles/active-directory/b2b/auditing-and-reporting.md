---
title: Naplózás és jelentéskészítés az Azure Active Directory B2B együttműködés felhasználói |} A Microsoft Docs
description: Vendég felhasználói tulajdonságok is konfigurálható az Azure Active Directory B2B-együttműködés
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: fbe1cfdcfb1b5ec295748c3c77030df45323ee54
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434324"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Naplózás és jelentéskészítés a B2B-együttműködés felhasználói
Vendégfelhasználók rendelkező tagfelhasználó naplózás hasonló képességeket. 

## <a name="access-reviews"></a>Hozzáférési felülvizsgálatok
A hozzáférési felülvizsgálatok segítségével rendszeres időközönként ellenőrizze, hogy vendégfelhasználók továbbra is kell-e az erőforrásokhoz való hozzáférés. A **hozzáférési felülvizsgálatokkal** funkció érhető el a **Azure Active Directory** alatt **kezelés** > **szervezeti kapcsolatok**. (A is kereshet a "hozzáférési felülvizsgálatokat" **minden szolgáltatás** az Azure Portalon.) A hozzáférési felülvizsgálatok használatával kapcsolatban lásd: [vendég-hozzáférés kezelése az Azure AD hozzáférési felülvizsgálatok](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Naplók

Az Azure AD naplózási naplókat a rendszer és a felhasználó tevékenységeket, beleértve a vendég felhasználók által kezdeményezett tevékenységek rekordjait tartalmazzák. A naplók eléréséhez **Azure Active Directory**alatt **figyelés**, jelölje be **Auditnaplók**. Íme egy példa az ajánlati és érvényesítési előzményeit meghívott Sam Oogle:

![napló](./media/auditing-and-reporting/audit-log.png)

Akkor is ismerje meg, minden egyes ezek az események részletes információkkal szolgál. Például tekintsük át azokat elfogadó részleteit.

![Tevékenység részletei](./media/auditing-and-reporting/activity-details.png)

Ezek a naplók exportálása az Azure ad-ből, és a választott jelentéskészítési eszköz segítségével testre szabott jelentések lekérése is.

### <a name="next-steps"></a>További lépések

- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

