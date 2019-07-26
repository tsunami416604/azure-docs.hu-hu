---
title: Azure Active Directory Identity Protection értesítések | Microsoft Docs
description: Ismerje meg, hogy az értesítések hogyan támogatják a vizsgálati tevékenységeket.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dcc7d2d03b3c4a3f4c8c772f38aa3785080d986
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335361"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Értesítések Azure Active Directory Identity Protection

A Azure AD Identity Protection kétféle automatizált értesítő e-mailt küld a felhasználói kockázatok és kockázati események kezeléséhez:

- Veszélyeztetett felhasználók észlelt e-mail-címe
- Heti kivonatoló e-mail

Ez a cikk az értesítő e-mailek áttekintését tartalmazza.

## <a name="users-at-risk-detected-email"></a>Veszélyeztetett felhasználók észlelt e-mail-címe

A veszélyeztetett észlelt fiókra adott válaszként Azure AD Identity Protection e-mail-riasztást hoz létre a **veszélyeztetett felhasználókkal** kapcsolatban. Az e-mail tartalmazza a kockázati jelentésre **[megjelölt felhasználókra](../reports-monitoring/concept-user-at-risk.md)** mutató hivatkozást. Ajánlott eljárásként azonnal vizsgálja meg a veszélyeztetett felhasználókat.

Ennek a riasztásnak a konfigurációja lehetővé teszi, hogy meghatározza, milyen felhasználói kockázati szinten szeretné létrehozni a riasztást. A rendszer akkor hozza létre az e-mailt, ha a felhasználó kockázati szintje eléri a megadott értéket; a felhasználó számára azonban nem fogja tudni észlelni az e-mail-riasztásokat, miután erre a felhasználói kockázati szintre költöztek. Ha például úgy állítja be a házirendet, hogy közepes felhasználói kockázattal figyelmeztessen, és a felhasználó John közepes kockázatra vált, a veszélyeztetett felhasználókat a rendszer az észlelt e-mail-címre fogja kapni John számára. Ha azonban John ezt követően nagy kockázatra vált, vagy további kockázati eseményekre van szüksége, nem fog észlelni veszélyt jelentő második felhasználót.

![Veszélyeztetett felhasználók észlelt e-mail-címe](./media/notifications/01.png)

### <a name="configuration"></a>Konfiguráció

Rendszergazdaként a következőket állíthatja be:

- **Az e-mailek generációját kiváltó felhasználói kockázati szint** alapértelmezés szerint a kockázati szint "magas" kockázatra van állítva.
- **Az e-mail címzettjei** – alapértelmezés szerint a címzettek az összes globális rendszergazdát tartalmazzák. A globális rendszergazdák további globális rendszergazdákat, biztonsági rendszergazdákat és biztonsági olvasókat is hozzáadhatnak címzettként.  

A kapcsolódó párbeszédpanel megnyitásához kattintson a **riasztások** elemre az **Identity Protection** oldal **Beállítások** szakaszában.

![Veszélyeztetett felhasználók észlelt e-mail-címe](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>Heti kivonatoló e-mail

A heti kivonatoló e-mail tartalmazza az új kockázati események összegzését.  
A következőket tartalmazza:

- Érintett felhasználók
- Gyanús tevékenységek
- Észlelt biztonsági rések
- Az Identity Protection kapcsolódó jelentéseire mutató hivatkozások

    ![Szervizelés](./media/notifications/400.png "Szervizelés")

### <a name="configuration"></a>Konfiguráció

Rendszergazdaként a heti kivonatoló e-mailek küldését is átválthatja.

![Felhasználói kockázatok](./media/notifications/62.png "Felhasználói kockázatok")

A kapcsolódó párbeszédpanel megnyitásához kattintson az **Identity Protection** oldal **Beállítások** szakaszában a **heti kivonatoló** elemre.

![Veszélyeztetett felhasználók észlelt e-mail-címe](./media/notifications/04.png)

## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
