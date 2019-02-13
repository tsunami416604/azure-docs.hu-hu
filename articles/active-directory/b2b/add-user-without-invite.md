---
title: Azure Active Directory B2B-együttműködés felhasználók hozzáadása meghívás nélkül |} A Microsoft Docs
description: Engedélyezheti, a vendégfelhasználó más vendég felhasználók hozzáadása az Azure ad nélkül váltja be az Azure Active Directory B2B együttműködés meghívót.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90e156f1d1d7bbbb6c4f65a4762977d28a5a0b89
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182595"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>B2B együttműködés meghívó nélküli vendégfelhasználók hozzáadása

Most már ki egy megosztott alkalmazás közvetlen hivatkozás elküldésével vendégfelhasználókat is meghívhat. Ezzel a módszerrel vendégfelhasználók már nem szeretné használni a meghívó e-mailt, kivéve a bizonyos speciális esetekben. Vendégfelhasználó az alkalmazás hivatkozásra kattint, ellenőrzi és fogadja el az adatvédelmi szabályzat feltételeit, és zökkenőmentesen fér hozzá az alkalmazást. További információkért lásd: [B2B együttműködés vendégmeghívás beváltása](redemption-experience.md).   

Ez az új módszer volt elérhető, mielőtt sikerült-e vendégfelhasználók meghívása anélkül, hogy a meghívó e-mailek ad hozzá egy meghívót küldő személy (a szervezet vagy egy fiókpartner-szervezet) a **Vendég meghívója** címtárbeli szerepkört, majd vendég felhasználók hozzáadása a könyvtár, csoportokat vagy alkalmazásokat a felhasználói felületen vagy a Powershellen keresztül a meghívót küldő személy kellene. (Ha a PowerShell használatával tilthatja le a meghívó e-mailek érvényesítette). Példa:

1. A felhasználó a gazdagép-szervezetben (például WoodGrove) meghívja a fiókpartner-szervezet egyik felhasználóját (például Sam@litware.com) vendégként.
2. A gazdagép szervezet rendszergazdájának [beállítja a szabályzatok](delegate-invitations.md) , amelyek engedélyezik a Sam azonosíthatja és más felhasználók felvétele az erőforráspartner-szervezet (Litware). (Sam hozzá kell adni a **Vendég meghívója** szerepkör.)
3. Most Sam adhat hozzá más felhasználók Litware, a WoodGrove könyvtár, csoportokat vagy alkalmazásokat anélkül, hogy beváltani a meghívót. Ha a Sam Litware az enumerálás megfelelő jogosultságokkal rendelkezik, ez automatikusan megtörténik.
 
Az eredeti módszer továbbra is működik. Van azonban egy kis eltérés viselkedés. Ha a PowerShell segítségével, azt láthatja, hogy a meghívott Vendég fiókkal most már rendelkezik egy **PendingAcceptance** állapota azonnal megjelenítése helyett **elfogadva**. Bár az állapot Függőben, a vendégfelhasználó továbbra is jelentkezzen be és egy e-mailben meghívást hivatkozásra kattintás nélkül az alkalmazás eléréséhez. A függőben lévő állapot azt jelenti, hogy a felhasználó még nem volt a [élmény hozzájárulás](redemption-experience.md#privacy-policy-agreement), ahol elfogadják az adatvédelmi szabályzat feltételeit a meghívó szervezetet. A vendégfelhasználónak a jóváhagyást kérő képernyőt látja, első bejelentkezéskor. 

A könyvtár felhasználó meghívása, ha a vendégfelhasználó kell-e az erőforrás bérlőspecifikus Azure portal eléréséhez közvetlen URL-címe (például https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) megtekintéséhez, és fogadja el az adatvédelmi szabályzat feltételeit.

### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködés vendégmeghívás beváltása](redemption-experience.md)
- [Azure Active Directory B2B-együttműködésre szóló meghívók delegálása](delegate-invitations.md)
- [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködési felhasználókat?](add-users-information-worker.md)

