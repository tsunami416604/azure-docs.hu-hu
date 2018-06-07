---
title: Azure Active Directory B2B együttműködés felhasználók hozzáadása nélkül |} Microsoft Docs
description: Beállítható, hogy a Vendég felhasználó más vendég felhasználók hozzáadása az Azure ad az Azure Active Directory B2B együttműködés meghívót gyűjtésével nélkül.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/21/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 50c64386f1eab07c299112617283b1d8d7295295
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591051"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Adja hozzá a B2B együttműködés vendégfelhasználók nélkül

Most felajánlhatja a vendégfelhasználók küldésével, a megosztott alkalmazások egy közvetlen hivatkozást. Ezzel a módszerrel vendégfelhasználók már nem szeretné használni, a meghívó e-mailek kivételével néhány bizonyos esetekben. A Vendég felhasználó az alkalmazás hivatkozásra kattint, ellenőrzi, hogy fogadja el az adatvédelmi feltételeket és zökkenőmentesen éri el az alkalmazást. További információkért lásd: [B2B együttműködés meghívó érvényesítési](redemption-experience.md).   

A metódus volt elérhető, mielőtt sikerült-e vendégfelhasználók meghívása anélkül, hogy a meghívó e-mail hozzáadásával egy meghívó (a munkahelyi vagy az erőforráspartner-szervezet) a **Vendég meghívó** directory szerepkört, majd Ha a meghívó vendég felhasználók hozzáadása a könyvtár, csoportok vagy alkalmazások a felhasználói felületen vagy a Powershellen keresztül. (Ha a PowerShell használatával tilthatja le a meghívó e-mail teljesen). Példa:

1. A gazdagép szervezetében (például WoodGrove) a felhasználó meghívja a fiókpartner-szervezet egy felhasználót (például Sam@litware.com) vendégként.
2. A rendszergazda az állomás szervezetben [állít be házirendek](delegate-invitations.md) , amelyek lehetővé teszik a Sam azonosíthatja és más felhasználók felvétele az erőforráspartner-szervezet (Litware). (Sam hozzá kell adni a **Vendég meghívó** szerepkör.)
3. Most Sam származó is hozzáadhat más felhasználók Litware a WoodGrove könyvtár, csoportok vagy alkalmazások felhívás váltható anélkül. Ha a Sam Litware a számbavételi megfelelő jogosultságokkal rendelkezik, akkor automatikusan megtörténik.
 
Ez a eredeti módszer továbbra is működik. Van azonban egy kis eltérés viselkedését. PowerShell használatakor megfigyelhető, hogy a meghívott Vendég fiókkal most már rendelkezik egy **PendingAcceptance** állapot helyett azonnal megjelenítő **elfogadott**. Bár a állapot Függőben, a Vendég felhasználói továbbra is bejelentkezhet és az alkalmazás elérését egy e-mailek meghívó hivatkozásra kattintva. A függőben lévő állapot azt jelenti, hogy a felhasználó nem még keresztülment a [élmény hozzájárulás](redemption-experience.md#privacy-policy-agreement), ha elfogadja őket hívja fel a szervezet adatvédelmi feltételeit. A Vendég felhasználónál a hozzájárulási képernyő első bejelentkezéskor. 

A címtár felhasználó meghívása, ha a Vendég felhasználói hozzáférést kell az erőforrás-bérlő-specifikus Azure portálon közvetlenül URL-címe (például https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) tekintheti meg és fogadja el az adatvédelmi szerződést.

### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködés meghívó érvényesítési](redemption-experience.md)
- [Az Azure Active Directory B2B együttműködés meghívókat delegálása](delegate-invitations.md)
- [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](add-users-information-worker.md)

