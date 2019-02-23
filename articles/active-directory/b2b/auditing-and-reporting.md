---
title: Naplózás és a egy B2B együttműködés felhasználói – Azure Active Directory reporting |} A Microsoft Docs
description: Vendég felhasználói tulajdonságok is konfigurálható az Azure Active Directory B2B-együttműködés
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: dad4872f9bc32a1978de47a52cea23d6bb2742a1
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673613"
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

