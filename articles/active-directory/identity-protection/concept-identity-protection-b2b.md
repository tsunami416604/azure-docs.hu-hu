---
title: Identitásvédelem és B2B-felhasználók – Azure Active Directory
description: Az Identitásvédelem használata b2B felhasználókkal, hogyan működik, és ismert korlátozások
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
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881310"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection- és B2B-felhasználók

Az Azure AD B2B együttműködéssel a szervezetek kikényszeríthetik a kockázatalapú szabályzatokat a B2B-felhasználók számára az Identity Protection használatával. Ezek a házirendek kétféleképpen konfigurálhatók:

- A rendszergazdák konfigurálhatják a beépített Identity Protection kockázatalapú szabályzatokat, amelyek minden alkalmazásra vonatkoznak, beleértve a vendégfelhasználókat is.
- A rendszergazdák konfigurálhatják a feltételes hozzáférési házirendeket, a bejelentkezési kockázat feltételként, amely magában foglalja a vendég felhasználók.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Hogyan értékelik a kockázatot a B2B együttműködési felhasználók számára?

A B2B együttműködési felhasználók felhasználói kockázatának kiértékelése a kezdőkönyvtárban történik. A valós idejű bejelentkezési kockázatot ezek a felhasználók kiértékelése az erőforrás-könyvtárban, amikor megpróbálnak hozzáférni az erőforráshoz.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Az identitásvédelem korlátai a B2B együttműködési felhasználók számára

Az identitásvédelem végrehajtása a B2B együttműködési felhasználók számára az erőforrás-címtárban a kezdőkönyvtárban meglévő identitásuk miatt korlátozott. A fő korlátozások a következők:

- Ha egy vendégfelhasználó elindítja az Identity Protection felhasználói kockázati házirendet a jelszó alaphelyzetbe **állításához, a rendszer letiltja őket.** Ez a blokk annak köszönhető, hogy az erőforráskönyvtárban nem lehet alaphelyzetbe állítani a jelszavakat.
- **A vendégfelhasználók nem jelennek meg a kockázatos felhasználók jelentésében.** Ez a láthatóságvesztés a B2B-felhasználó kezdőkönyvtárában előforduló kockázatértékelésnek köszönhető.
- A rendszergazdák **nem utasíthatnak el és nem orvosolhatnak egy kockázatos B2B együttműködési felhasználót** az erőforráskönyvtárukban. Ez a funkcióvesztés annak köszönhető, hogy az erőforráskönyvtár rendszergazdái nem férnek hozzá a B2B-felhasználó kezdőkönyvtárához.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Miért nem tudom kiújítani a kockázatos B2B együttműködési felhasználókat a címtárban?

A B2B-felhasználók kockázatértékelése és szervizelése a kezdőkönyvtárban történik. Ennek következtében a vendég felhasználók nem jelennek meg a kockázatos felhasználók jelentés az erőforrás könyvtárban, és a rendszergazdák az erőforrás könyvtárban nem kényszerítheti a biztonságos jelszó-visszaállítás ezek a felhasználók számára.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Mit tegyek, ha egy B2B együttműködési felhasználó le van tiltva a szervezetben lévő kockázatalapú házirend miatt?

Ha a kockázatalapú házirend blokkolja a címtárban lévő kockázatos B2B-felhasználót, a felhasználónak a kezdőkönyvtárában kell orvosolnia ezt a kockázatot. A felhasználók a saját címtárukban biztonságos jelszó-visszaállítással orvosolhatják a kockázatot. Ha a kezdőkönyvtárban nincs engedélyezve az önkiszolgáló jelszó-visszaállítás, akkor kapcsolatba kell lépniük a saját szervezet informatikai személyzetével, hogy a rendszergazda manuálisan utasítsa el a kockázatot, vagy állítsa alaphelyzetbe a jelszavát.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Hogyan akadályozhatom meg, hogy a B2B együttműködési felhasználókat befolyásolják a kockázatalapú házirendek?

Ha kizárja a B2B-felhasználókat a szervezet kockázatalapú feltételes hozzáférés-házirendjeiből, a B2B-felhasználókat nem érinti vagy blokkolja a kockázatértékelés. Ezek a B2B-felhasználók kizárásához hozzon létre egy csoportot az Azure AD-ben, amely tartalmazza a szervezet összes vendégfelhasználóját. Ezután adja hozzá ezt a csoportot kizárásként a beépített Identity Protection felhasználói kockázat és a bejelentkezési kockázati szabályzatok, valamint a feltételes hozzáférés házirendek, amelyek a bejelentkezési kockázatot feltételként használják.

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket az Azure AD B2B együttműködés:

- [Mi az az Azure AD B2B együttműködés?](../b2b/what-is-b2b.md)
