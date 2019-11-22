---
title: B2B-vendégek hozzáadása Meghívási hivatkozás vagy e-mail küldése nélkül – Azure AD
description: Lehetővé teheti, hogy a vendég felhasználó más vendég felhasználókat is hozzáadhat az Azure AD-hoz anélkül, hogy meghívót vált Azure Active Directory B2B együttműködésben.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9efbb941e589cb8e4cf56ee06a697a1557a3cc89
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74268929"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>B2B Collaboration Guest Users hozzáadása meghívó hivatkozás vagy e-mail küldése nélkül

Most meghívhatja a vendég felhasználókat úgy, hogy közvetlen hivatkozást küld egy megosztott alkalmazáshoz. Ezzel a módszerrel a vendég felhasználóknak már nem kell a meghívó e-mailt használniuk, kivéve bizonyos speciális esetekben. A vendég felhasználó az alkalmazás hivatkozására kattint, megtekinti és elfogadja az adatvédelmi feltételeket, majd zökkenőmentesen hozzáfér az alkalmazáshoz. További információ: [vállalatközi együttműködés meghívásának beváltása](redemption-experience.md).   

Ahhoz, hogy ez az új metódus elérhetővé vált, a vendég felhasználókat a meghívót kérő e-mailek meghívása nélkül is meghívhatja a meghívó (a szervezet vagy a partner szervezete) és a **vendég meghívó** címtár szerepkörbe, majd a meghívóval a felhasználói felületen vagy a powershellen keresztül adhat hozzá vendégeket a címtárhoz, a csoporthoz vagy az alkalmazásokhoz. (Ha a PowerShellt használja, a meghívót tartalmazó e-mailt összesen is kihagyhatja). Például:

1. A gazda szervezet egyik felhasználója (például a WoodGrove) egy felhasználót hív meg a partner szervezettől (például Sam@litware.com) vendégként.
2. A gazdagép rendszergazdája olyan [házirendeket állít be](delegate-invitations.md) , amelyek lehetővé teszik a Sam számára a partner szervezet (Litware) más felhasználóinak azonosítását és hozzáadását. (A Sam-t fel kell venni a **vendég meghívó** szerepkörbe.)
3. Mostantól a Sam más felhasználókat is hozzáadhat a Litware a WoodGrove-címtárhoz,-csoportokhoz vagy-alkalmazásokhoz anélkül, hogy be kellene váltania a meghívókat. Ha a Sam rendelkezik a megfelelő enumerálási jogosultságokkal a Litware-ben, az automatikusan megtörténik.
 
Ez az eredeti metódus továbbra is működik. Van azonban némi különbség a viselkedésben. Ha a PowerShellt használja, megfigyelheti, hogy egy meghívott vendég fiók most már rendelkezik egy **PendingAcceptance** állapottal, és nem **fogadja el**azonnal az elfogadott értéket. Bár az állapot függőben van, a vendég felhasználó továbbra is bejelentkezhet, és anélkül férhet hozzá az alkalmazáshoz, hogy rákattint egy e-mail meghívására szolgáló hivatkozásra. A függőben lévő állapot azt jelenti, hogy a felhasználó még nem ment át a [jóváhagyáson](redemption-experience.md#consent-experience-for-the-guest), ahol elfogadják a meghívó szervezet adatvédelmi feltételeit. A vendég felhasználó ezt a beleegyezési képernyőt látja, amikor első alkalommal jelentkeznek be. 

Ha meghívja a felhasználót a címtárba, a vendég felhasználónak közvetlenül hozzá kell férnie az erőforrás-bérlőre jellemző Azure Portal URL-címhez (például https://portal.azure.com/*resourcetenant*. onmicrosoft.com) az adatvédelmi feltételek megtekintéséhez és elfogadásához.

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [VÁLLALATKÖZI együttműködés meghívásának beváltása](redemption-experience.md)
- [Azure Active Directory B2B-együttműködésre szóló meghívók delegálása](delegate-invitations.md)
- [Hogyan vehetik fel az Information Worker a B2B együttműködési felhasználókat?](add-users-information-worker.md)

