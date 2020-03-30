---
title: Az Azure Active Directory identitásvédelemre vonatkozó értesítések
description: További információ arról, hogy az értesítések hogyan támogatják a vizsgálati tevékenységeket.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120129"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Az Azure Active Directory identitásvédelemre vonatkozó értesítések

Az Azure AD Identity Protection kétféle automatikus értesítési e-mailt küld a felhasználói kockázatok és a kockázatok észlelésének kezeléséhez:

- A veszélyeztetett felhasználók e-mailt észleltek
- Heti kivonatoló e-mail

Ez a cikk áttekintést nyújt mindkét értesítő e-mailről.

## <a name="users-at-risk-detected-email"></a>A veszélyeztetett felhasználók e-mailt észleltek

Egy észlelt kockázatnak kitett fiókra adott válaszként az Azure AD Identity Protection e-mail-riasztást hoz létre, amelyben **a veszélyeztetett felhasználók** érintettek. Az e-mail tartalmaz egy hivatkozást a **[kockázati jelentéshez megjelölt Felhasználók ra.](../reports-monitoring/concept-user-at-risk.md)** Ajánlott eljárásként azonnal vizsgálja meg a veszélyeztetett felhasználókat.

A riasztás konfigurációja lehetővé teszi, hogy adja meg, hogy milyen felhasználói kockázati szinten szeretné generálni a riasztást. Az e-mail akkor jön létre, amikor a felhasználó kockázati szintje eléri az Ön által megadott szintet; azonban nem kap új felhasználókat a kockázat észlelt e-mail riasztások at ez a felhasználó, miután átkerült erre a felhasználói kockázati szintre. Ha például úgy állítja be a házirendet, hogy figyelmeztesse a közepes felhasználói kockázatot, és a felhasználó John közepes kockázatra kerül, megkapja a veszélyeztetett e-maileket János számára észlelt e-mailek. Azonban nem kap egy második felhasználó a kockázat észlelése riasztást, ha János majd költözik a magas kockázatú, vagy további kockázatészlelések.

![A veszélyeztetett felhasználók e-mailt észleltek](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>A kockázatos riasztások konfigurálása

Rendszergazdaként a következőket állíthatja be:

- **Az e-mail létrehozását kiváltó felhasználói kockázati szint** – Alapértelmezés szerint a kockázati szint "Magas" kockázatra van állítva.
- **Az e-mail címzettjei** – Alapértelmezés szerint a címzettek tartalmazzák az összes globális rendszergazdát. A globális rendszergazdák más globális rendszergazdákat, biztonsági rendszergazdákat, biztonsági olvasókat is hozzáadhatnak címzettként.
   - Szükség esetén **további e-maileket adhat hozzá a riasztási értesítések fogadásához,** ez a funkció egy előzetes verzió, és a felhasználók számára meghatározott felhasználóknak rendelkezniük kell a megfelelő engedélyekkel a csatolt jelentések megtekintéséhez az Azure Portalon.

Konfigurálja a veszélyeztetett e-maileket az **Azure-portálon** az **Azure Active Directory** > **biztonsági** > **identitásvédelem** > felhasználói által észlelt**riasztások**alatt.

## <a name="weekly-digest-email"></a>Heti kivonatoló e-mail

A heti kivonatoló e-mail az új kockázatészlelések összegzését tartalmazza.  
A következőket tartalmazza:

- Veszélyeztetett felhasználók
- Gyanús tevékenységek
- Észlelt biztonsági rések
- Linkek a kapcsolódó jelentések identitásvédelem

![Heti kivonatoló e-mail](./media/howto-identity-protection-configure-notifications/400.png)

Alapértelmezés szerint a címzettek tartalmazzák az összes globális rendszergazdát. A globális rendszergazdák más globális rendszergazdákat, biztonsági rendszergazdákat, biztonsági olvasókat is hozzáadhatnak címzettként.

### <a name="configure-weekly-digest-email"></a>Heti kivonatoló e-mail beállítása

Rendszergazdaként átkapcsolhatja a heti kivonatoló e-mailek küldését, és kiválaszthatja az e-mail fogadásához hozzárendelt felhasználókat.

Konfigurálja a heti kivonatolt e-mailt > az Azure Active Directory**Security** > **Identity Protection** > **Weekly heti kivonata**alatt az Azure **Portalon.** **Azure Active Directory**

## <a name="see-also"></a>Lásd még

- [Azure Active Directory identitásvédelem](../active-directory-identityprotection.md)
