---
title: Értesítések Azure Active Directory Identity Protection
description: Ismerje meg, hogy az értesítések hogyan támogatják a vizsgálati tevékenységeket.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 200ede6b4c5565a8eab95b0398abaa1c056c612f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853132"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Értesítések Azure Active Directory Identity Protection

Azure AD Identity Protection kétféle automatizált értesítő e-mailt küld a felhasználói kockázatok és a kockázati észlelések kezeléséhez:

- Veszélyeztetett felhasználók észlelt e-mail-címe
- Heti kivonatoló e-mail

Ez a cikk az értesítő e-mailek áttekintését tartalmazza.

## <a name="users-at-risk-detected-email"></a>Veszélyeztetett felhasználók észlelt e-mail-címe

A veszélyeztetett észlelt fiókra adott válaszként Azure AD Identity Protection e-mail-riasztást hoz létre a **veszélyeztetett felhasználókkal** kapcsolatban. Az e-mail tartalmazza a kockázati jelentésre **[megjelölt felhasználókra](../reports-monitoring/concept-user-at-risk.md)** mutató hivatkozást. Ajánlott eljárásként azonnal vizsgálja meg a veszélyeztetett felhasználókat.

Ennek a riasztásnak a konfigurációja lehetővé teszi, hogy meghatározza, milyen felhasználói kockázati szinten szeretné létrehozni a riasztást. A rendszer akkor hozza létre az e-mailt, ha a felhasználó kockázati szintje eléri a megadott értéket; a felhasználó számára azonban nem fogja tudni észlelni az e-mail-riasztásokat, miután erre a felhasználói kockázati szintre költöztek. Ha például úgy állítja be a házirendet, hogy közepes felhasználói kockázattal figyelmeztessen, és a felhasználó John közepes kockázatra vált, a veszélyeztetett felhasználókat a rendszer az észlelt e-mail-címre fogja kapni John számára. Azonban nem kap a veszélyeztetett második felhasználót a riasztás, ha János ezt követően magas kockázatra vált, vagy további kockázati észleléseket is tartalmaz.

![Veszélyeztetett felhasználók észlelt e-mail-címe](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Veszélyeztetett felhasználók konfigurálása észlelt riasztásokkal

Rendszergazdaként a következőket állíthatja be:

- **Az e-mailek generációját kiváltó felhasználói kockázati szint** alapértelmezés szerint a kockázati szint "magas" kockázatra van állítva.
- **Az e-mail címzettjei** – alapértelmezés szerint a címzettek az összes globális rendszergazdát tartalmazzák. A globális rendszergazdák további globális rendszergazdákat, biztonsági rendszergazdákat és biztonsági olvasókat is hozzáadhatnak címzettként.
   - Opcionálisan **további e-maileket is hozzáadhat a riasztási értesítések fogadásához** . Ez a funkció előzetes verzió, és a felhasználók által definiált felhasználóknak rendelkezniük kell a megfelelő engedélyekkel a csatolt jelentések megtekintéséhez a Azure Portal.

Konfigurálja a veszélyeztetett felhasználókat a **Azure Portal** **Azure Active Directory** > **biztonsági** > **identitások védelme** > a**veszélyeztetett felhasználók által észlelt riasztások**területen.

## <a name="weekly-digest-email"></a>Heti kivonatoló e-mail

A heti kivonatoló e-mail tartalmazza az új kockázati észlelések összegzését.  
A következőket tartalmazza:

- Új kockázatos felhasználók észlelhetők
- Új kockázatos bejelentkezések észlelhetők (valós időben)
- Az Identity Protection kapcsolódó jelentéseire mutató hivatkozások

![Heti kivonatoló e-mail](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Alapértelmezés szerint a címzettek a globális rendszergazdákat is tartalmazzák. A globális rendszergazdák további globális rendszergazdákat, biztonsági rendszergazdákat és biztonsági olvasókat is hozzáadhatnak címzettként.

### <a name="configure-weekly-digest-email"></a>Heti kivonatoló e-mail konfigurálása

Rendszergazdaként a heti kivonatoló e-mailek küldését vagy kikapcsolását is elvégezheti, és kiválaszthatja az e-mailek fogadásához hozzárendelt felhasználókat.

Konfigurálja a heti kivonatoló e-mailt **Azure portal** a Azure Portal **Azure Active Directory** > **biztonsági** > **Identity Protection** > **heti kivonatoló**területen.

## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
