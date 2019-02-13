---
title: Az Azure Active Directory Identity Protection |} A Microsoft Docs
description: Ismerje meg, hogyan Azure AD Identity Protection segítségével is korlátozhatja, hogy a támadó biztonsági rés kiaknázása elleni egy sérült biztonságú identitás vagy egy eszközt, és biztonságos identitás vagy egy eszköz, amely korábban gyanús vagy ismert legyen feltörni.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: eecdf4382fff441ca7c7d6bd4c63d12b87a73ccf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169700"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Mi az Azure Active Directory Identity Protection?

Az Azure Active Directory Identity Protection az Azure AD Premium P2 kiadás egyik szolgáltatása, amely lehetővé teszi a következőket:

- A szervezet identitásait érintő esetleges biztonsági rések észlelése

- Automatikus válaszok észlelt gyanús tevékenységeket, a szervezet identitásait kapcsolódó konfigurálása  

- Gyanús incidensek kivizsgálása, és a szükséges intézkedések megtétele azok megoldásához   


## <a name="get-started"></a>Bevezetés

A Microsoft több mint egy évtizede nyújt védelmet a felhőalapú Identitások biztonságos vannak. Az Azure Active Directory Identity Protection a környezetben használhatja ugyanezeket a védelmi rendszereket Microsoft identitások védelmére használja.

Biztonsági rések túlnyomó többsége kerül sor, amikor a támadók próbál a jeggyel hozzáférést egy környezethez lopásának megjelölése a felhasználó identitását. Az évek során a támadók egyre inkább hatékony kihasználva külső illetéktelen behatolásokat és adathalászati támadások használatával váltak. Amint egy támadó hozzáférést még alacsony jogosultsági szintű felhasználó fiókok, viszonylag egyszerű, az oldalirányú mozgás keresztül fontos vállalati erőforrásokhoz való hozzáférést nyerhetnek a hozzájuk.

Ennek következtében kell tennie:

- Függetlenül attól, hogy jogosultsági szint összes identitások védelme

- Proaktív módon megakadályozza, hogy feltört identitásokat visszaélt folyamatban

Feltört identitásokat felderítése nem könnyű feladat. Az Azure Active Directory adaptív gépi tanulási algoritmusokat használ, és heurisztikus észlelheti a rendellenességeket és gyanús incidensek felderíthetik a vélhetően feltört identitásokat. Ezen adatok alapján Identity Protection állít elő, jelentéseket és riasztásokat, amelyek lehetővé teszik, hogy kiértékelhetik az észlelt problémákat, és a megfelelő kockázatcsökkentési, illetve a szervizelési műveletek végrehajtása.

Az Azure Active Directory Identity Protection több mint egy olyan figyelési és jelentéskészítési eszköz. Védelme érdekében a szervezet identitásait, konfigurálhatja a kockázatalapú szabályzatok, amelyek az automatikusan észlelt problémák reagálnak adott kockázati szint elérésekor. Ezek a szabályzatok mellett más feltételes hozzáférés-vezérlés az Azure Active Directory által biztosított és [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), automatikusan letiltja, vagy kezdeményezzen adaptív szervizelési műveletek, beleértve a Új jelszó kérésekor és a többtényezős hitelesítés kényszerítése.


#### <a name="identity-protection-capabilities"></a>Identitásvédelmi képességeket

**Biztonsági rések és a kockázatos fiókok észlelése:**  

* Így egyéni kapcsolatos ajánlásokat átfogó biztonsági rendszer kialakításához biztonsági rések kiemelésével
* Bejelentkezési kockázati szintek kiszámítása
* Felhasználói kockázati szintek kiszámítása


**Kockázati események vizsgálata:**

* Kockázati események értesítések küldése
* Megfelelő és környezetfüggő adatok felhasználásával kockázati események vizsgálata
* Biztosít a vizsgálatokhoz nyomon követéséhez alapvető munkafolyamatok
* Szervizelési műveleteket hajthat végre, például a jelszó-visszaállítás könnyű hozzáférést biztosít

**Kockázatalapú feltételes hozzáférési szabályzatok:**

* Szabályzat blokkolja a bejelentkezéseket, vagy a multi-factor authentication kihívások igénylő csökkentése érdekében a kockázatos bejelentkezések
* Szabályzat letiltása vagy biztonságos kockázatos felhasználói fiókok
* Felhasználók regisztráljanak a multi-factor Authentication hitelesítést megkövetelő szabályzata



## <a name="identity-protection-roles"></a>Identity Protection szerepkörök

Terheléselosztás a felügyeleti tevékenységek körül az Identity Protection implementáció számos szerepköröket rendelhet. Az Azure AD Identity Protection 3 címtárszerepkört támogat:

| Szerepkör                         | Teheti meg                          | Nem hajtható végre
| :--                          | ---                                |  ---   |
| Globális rendszergazda         | Teljes hozzáférés az Identity Protection előkészítése Identity Protection| |
| Biztonsági rendszergazda       | Teljes hozzáférés az Identity Protection | Identity Protection előkészítése, a felhasználó a jelszavak alaphelyzetbe állítása |
| Biztonsági olvasó              | Csak olvasási hozzáférés az Identity Protection | Identity Protection előkészítése, felhasználók szervizelése, a szabályzatok konfigurálására, jelszavak alaphelyzetbe állítása |




További részletekért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Észlelés

### <a name="vulnerabilities"></a>Biztonsági rések

Az Azure Active Directory Identity Protection elemzi a konfigurációt, és észleli a sérülékenységeket, amelyek hatással lehetnek a felhasználói identitásokat. További részletekért lásd: [Azure Active Directory Identity Protection által észlelt biztonsági rések](vulnerabilities.md).

### <a name="risk-events"></a>Kockázati események

Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a felhasználói identitások kapcsolódó gyanús tevékenységeket. A rendszer létrehoz egy rekordot észlelt gyanús műveletek. Ezeket a rekordokat kockázati események is ismertek.  
További részletek: [Az Azure Active Directory kockázati eseményei](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Vizsgálat

Identity Protection örömét általában az Identity Protection irányítópultjának kezdődik.

![Szervizelési](./media/overview/1000.png "szervizelés")

Az irányítópult hozzáférést biztosít a következőkhöz:

* Például a jelentések **kockázatosként megjelölt felhasználók**, **kockázati események** és **biztonsági rések**
* Beállítások, például konfigurációját a **biztonsági házirendek**, **értesítések** és **többtényezős hitelesítési regisztráció**

Általában célszerű a vizsgálat, amely azt a folyamatot, tekintse át a tevékenységeket, naplók és egyéb releváns információkkal kapcsolatos egy kockázati esemény eldönteni, hogy szervizelési vagy kockázatcsökkentési lépések szükségesek, és milyen a identitása volt a kiindulási pont biztonsága sérült, és megismerheti, hogyan lett megadva a feltört identitását.

A vizsgálat tevékenységek köthet a [értesítések](notifications.md) Azure Active Directory Protection küld egy e-mailt.



## <a name="policies"></a>Házirendek

Automatikus válaszok implementálásához az Azure Active Directory Identity Protection nyújt három olyan szabályzatot:

- [A multi-factor authentication regisztrációs szabályzat](howto-mfa-policy.md)

- [Felhasználói kockázati házirend](howto-user-risk-policy.md)

- [Bejelentkezési kockázati házirend](howto-sign-in-risk-policy.md)


## <a name="next-steps"></a>További lépések

- [Channel 9-on: Az Azure AD és az identitás megjelenítése: Identity Protection előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Az Azure Active Directory Identity Protection engedélyezése](enable.md)

