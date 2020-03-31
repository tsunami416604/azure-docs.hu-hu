---
title: B2B-vendégek hozzáadása meghívóhivatkozás vagy e-mail nélkül - Azure AD
description: Lehetővé teheti, hogy egy vendégfelhasználó további vendégfelhasználókat adjon hozzá az Azure AD-hez anélkül, hogy beváltana egy meghívót az Azure Active Directory B2B-együttműködésében.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c9caaf581fab37e2e55cbe408db6d6d846622a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050907"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>B2B együttműködési vendégfelhasználók hozzáadása meghívóhivatkozás vagy e-mail nélkül

Most már meghívhatja a vendégfelhasználókat, ha közvetlen hivatkozást küld egy megosztott alkalmazásra. Ezzel a módszerrel a vendégfelhasználóknak már nem kell használniuk a meghívó e-mailt, kivéve néhány különleges esetet. A vendégfelhasználó rákattint az alkalmazás hivatkozására, áttekinti és elfogadja az adatvédelmi feltételeket, majd zökkenőmentesen hozzáfér az alkalmazáshoz. További információ: [B2B collaboration invitation redemption](redemption-experience.md).   

Mielőtt ez az új módszer elérhetővé lett volna téve, meghívhatja a vendégfelhasználókat a meghívó e-mail megkövetelése nélkül, ha hozzáad egy meghívót (a szervezettől vagy egy partnerszervezettől) a **Vendég meghívó** címtárszerepkörhöz, majd a meghívott a címtárba, csoportokba vagy alkalmazásokba a felhasználói felületen vagy a PowerShellen keresztül adja hozzá a vendégfelhasználókat. (A PowerShell használata esetén teljesen letilthatja a meghívó e-mailt). Példa:

1. A gazdaszervezet egyik felhasználója (például a WoodGrove) vendégként hív Sam@litware.commeg egy felhasználót a partnerszervezetből (például).
2. A gazdaszervezet rendszergazdája [olyan házirendeket állít be,](delegate-invitations.md) amelyek lehetővé teszik Sam számára, hogy azonosítsa és felvegye a partnerszervezet (Litware) további felhasználóit. (Sam-et hozzá kell adni a **Vendég meghívó** szerepkörhöz.)
3. Most sam hozzáadhat más felhasználókat a Litware-től a WoodGrove könyvtárhoz, csoportokhoz vagy alkalmazásokhoz anélkül, hogy meghívókat kellene beváltania. Ha Sam rendelkezik a megfelelő enumerálási jogosultságokkal a Litware-ben, az automatikusan megtörténik.
 
Ez az eredeti módszer még mindig működik. Azonban van egy kis különbség a viselkedés. Ha PowerShellt használ, észre fogja venni, hogy egy meghívott vendégfiók **pendingelfogadási** állapottal rendelkezik, ahelyett, hogy azonnal megjelenne **az Elfogadva**. Bár az állapot függőben van, a vendégfelhasználó továbbra is bejelentkezhet és hozzáférhet az alkalmazáshoz anélkül, hogy egy e-mailben meghívóhivatkozásra kattintana. A függőben lévő állapot azt jelenti, hogy a felhasználó még nem ment át a [hozzájárulási élményen,](redemption-experience.md#consent-experience-for-the-guest)ahol elfogadja a meghívó szervezet adatvédelmi feltételeit. A vendégfelhasználó látja ezt a jóváhagyási képernyőt, amikor először jelentkezik be. 

Ha meghív egy felhasználót a címtárba, a vendégfelhasználónak közvetlenül kell https://portal.azure.com/hozzáférnie az erőforrás-bérlő-specifikus Azure-portál URL-címéhez (például *az erőforrás-bérlő*.onmicrosoft.com) az adatvédelmi feltételek megtekintéséhez és elfogadásához.

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködési meghívás visszaváltás](redemption-experience.md)
- [Azure Active Directory B2B-együttműködésre szóló meghívók delegálása](delegate-invitations.md)
- [Hogyan adják hozzá az informatikai dolgozók a B2B együttműködési felhasználókat?](add-users-information-worker.md)

