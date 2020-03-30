---
title: Vendéghozzáférés kezelése hozzáférési felülvizsgálatokkal – Azure AD
description: Vendégfelhasználók kezelése egy csoport tagjaként vagy egy alkalmazáshoz rendelve az Azure Active Directory hozzáférési ellenőrzésével
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bf2f9503ae704110786a1e73aec3da18c17e4ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932426"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal


Az Azure Active Directory (Azure AD) segítségével egyszerűen engedélyezheti az együttműködést a szervezeti határokon keresztül az [Azure AD B2B szolgáltatás](../b2b/what-is-b2b.md)használatával. Más bérlők vendégfelhasználóit a rendszergazdák vagy [más felhasználók](../b2b/what-is-b2b.md) [is meghívhatják.](../b2b/add-users-administrator.md) Ez a funkció a közösségi identitásokra, például a Microsoft-fiókokra is vonatkozik.

Azt is könnyedén biztosíthatja, hogy a vendégfelhasználók megfelelő hozzáféréssel rendelkezzenek. Megkérheti a vendégeket vagy a döntéshozót, hogy vegyenek részt a hozzáférés felülvizsgálatában, és tanúsítsák újra (vagy tanúsítsák) a vendégek hozzáférését. A felülvizsgálatot végzők az Azure AD-ből származó javaslatok alapján dönthetnek arról, hogy az egyes felhasználóknak szükségük van-e folyamatos hozzáférésre. Amikor a hozzáférési felülvizsgálat befejeződött, módosíthatja és eltávolíthatja a hozzáférést azon vendégek számára, akiknek már nincs rá szükségük.

> [!NOTE]
> Ez a dokumentum a vendégfelhasználók hozzáférésének áttekintésével kapcsolatos. Ha nem csak a vendégek, hanem az összes felhasználó hozzáférését is át szeretné tekinteni, olvassa el [a Felhasználói hozzáférés kezelése hozzáférési ellenőrzésekkel című témakört.](manage-user-access-with-access-reviews.md) Ha át szeretné tekinteni a felhasználók felügyeleti szerepkörökben ( például a globális rendszergazda) tagságát, olvassa el a [Hozzáférés-felülvizsgálat indítása az Azure AD kiemelt identitáskezelés szolgáltatásában című témakört.](../privileged-identity-management/pim-how-to-start-security-review.md)

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2

További információ: [Licenckövetelmények](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Hozzáférési ellenőrzés létrehozása és végrehajtása a vendégek számára

Először globális rendszergazdaként vagy felhasználói rendszergazdaként nyissa meg az [Identitáscégcég lapra,](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) és győződjön meg arról, hogy a hozzáférés-felülvizsgálatok készen állnak a szervezet számára.

Az Azure AD számos forgatókönyvet tesz lehetővé a vendégfelhasználók áttekintéséhez.

A következőket tekintheti meg:

 - Az Azure AD egy csoportja, amely egy vagy több vendéget tagként.
 - Az Azure AD-hez csatlakoztatott alkalmazás, amelyhez egy vagy több vendégfelhasználó van hozzárendelve. 

Ezután eldöntheti, hogy minden vendéget megkér-e, hogy tekintse át a saját hozzáférését, vagy kérjen meg egy vagy több felhasználót, hogy tekintse át minden vendég hozzáférését.

 Ezeket a forgatókönyveket a következő szakaszok ismertetik.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Kérd meg a vendégeket, hogy tekintsék át saját csoporttagságukat

A hozzáférési felülvizsgálatok segítségével biztosíthatja, hogy a csoporthoz meghívott és hozzáadott felhasználóknak továbbra is szükségük legyen a hozzáférésre. Könnyedén megkérheti a vendégeket, hogy tekintsék át saját tagságukat az adott csoportban.

1. A csoport hozzáférési felülvizsgálatának létrehozásához jelölje ki az értékelést úgy, hogy csak a vendégfelhasználók tagjai is szerepeljenek, és hogy a tagok saját magukat nézzék meg. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának létrehozása.](create-access-review.md)

2. Kérd meg a vendégeket, hogy tekintsék át saját tagságukat. Alapértelmezés szerint minden vendég, aki elfogadta a meghívást kap egy e-mailt az Azure AD egy linket a hozzáférési felülvizsgálat. Az Azure AD utasításokat tartalmaz a vendégek számára a [csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintéséhez.](perform-access-review.md)

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése.](complete-access-review.md)

4. Azokon a felhasználókon kívül, akik megtagadták a folyamatos hozzáférés iránti igényüket, eltávolíthatja azokat a felhasználókat is, akik nem válaszoltak. A nem válaszoló felhasználók potenciálisan már nem kapnak e-mailt.

5. Ha a csoport nincs hozzáférés-kezelésre használva, eltávolíthatja azokkal a felhasználókkal is, akik nem lettek kiválasztva az ellenőrzésben való részvételre, mert nem fogadták el a meghívást. A nem fogadás azt jelezheti, hogy a meghívott felhasználó e-mail címének elírása volt. Ha egy csoportot terjesztési listaként használ, akkor előfordulhat, hogy néhány vendégfelhasználó nem lett kiválasztva a részvételre, mert kapcsolattartó objektumok.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Kérjen meg egy szponzort, hogy tekintse át egy vendég tagságát egy csoportban

Megkérhetegy szponzort, például egy csoport tulajdonosát, hogy tekintse át a vendég nek a csoportban való folyamatos tagságiránti igényét.

1. Ha hozzáférési felülvizsgálatot szeretne létrehozni a csoporthoz, jelölje ki az értékelést úgy, hogy csak a vendégfelhasználói tagokat tartalmazza. Ezután adjon meg egy vagy több ellenőrzőt. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának létrehozása.](create-access-review.md)

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindegyik kap egy e-mailt az Azure AD-től egy hivatkozást a hozzáférési panelre, ahol [áttekintik a csoportokhoz vagy alkalmazásokhoz való hozzáférést.](perform-access-review.md)

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése.](complete-access-review.md)

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Kérje meg a vendégeket, hogy tekintsék át az alkalmazáshoz való saját hozzáférésüket

A hozzáférési felülvizsgálatok segítségével biztosíthatja, hogy az adott alkalmazáshoz meghívott felhasználóknak továbbra is szükségük van-e a hozzáférésre. Könnyedén kérheti a vendégeket, hogy vizsgálják felül saját hozzáférési igényüket.

1. Hozzáférési vélemény létrehozásához az alkalmazáshoz válassza ki az értékelést, hogy csak a vendégek et tartalmazza, és hogy a felhasználók saját hozzáférésüket tekintsék meg. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának létrehozása.](create-access-review.md)

2. Kérje meg a vendégeket, hogy tekintsék át saját hozzáférésüket az alkalmazáshoz. Alapértelmezés szerint minden meghívott vendég, aki elfogadta a meghívást, kap egy e-mailt az Azure AD-től. Az e-mail a szervezet hozzáférési paneljén található hozzáférési felülvizsgálatra mutató hivatkozást is rendelkezik. Az Azure AD utasításokat tartalmaz a vendégek számára a [csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintéséhez.](perform-access-review.md)

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése.](complete-access-review.md)

4. Azon felhasználók on kívül, akik megtagadták a folyamatos hozzáférés iránti igényüket, eltávolíthatja azokat a vendégfelhasználókat is, akik nem válaszoltak. A nem válaszoló felhasználók potenciálisan már nem kapnak e-mailt. Eltávolíthatja azon vendégfelhasználókat is, akik nem lettek kiválasztva a részvételre, különösen akkor, ha nem hívták meg őket a közelmúltban. Ezek a felhasználók nem fogadták el a meghívást, és így nem férnek hozzá az alkalmazáshoz. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Kérjen meg egy szponzort, hogy tekintse át a vendég hozzáférését egy alkalmazáshoz

Megkérhet egy szponzort, például egy alkalmazás tulajdonosát, hogy tekintse át a vendég nek az alkalmazáshoz való folyamatos hozzáférés iránti igényét.

1. Ha hozzáférési felülvizsgálatot szeretne létrehozni az alkalmazáshoz, válassza ki az értékelést, hogy csak a vendégek et is tartalmazza. Ezután adjon meg egy vagy több felhasználót ellenőrzőként. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának létrehozása.](create-access-review.md)

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindegyik kap egy e-mailt az Azure AD-től egy hivatkozást a hozzáférési panelre, ahol [áttekintik a csoportokhoz vagy alkalmazásokhoz való hozzáférést.](perform-access-review.md)

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése.](complete-access-review.md)

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Kérje meg a vendégeket, hogy vizsgálják felül a hozzáférés iránti igényüket,

Egyes szervezetekben előfordulhat, hogy a vendégek nem ismerik a csoporttagságukat.

> [!NOTE]
> Az Azure Portal korábbi verziói nem engedélyezték a rendszergazdai hozzáférést a vendég UserType használatával rendelkező felhasználók számára. Bizonyos esetekben előfordulhat, hogy a címtár egyik rendszergazdája a PowerShell használatával tagra módosította a vendég UserType értékét. Ha ez a változás korábban történt a címtárban, előfordulhat, hogy az előző lekérdezés nem tartalmazza az összes olyan vendégfelhasználót, aki korábban rendszergazdai hozzáférési jogosultságokkal rendelkezett. Ebben az esetben módosítania kell a vendég UserType-ját, vagy manuálisan kell felvennie a vendéget a csoporttagságba.

1. Hozzon létre egy biztonsági csoportot az Azure AD-ben a vendégek, mint a tagok, ha a megfelelő csoport még nem létezik. Létrehozhat például egy csoportot manuálisan karbantartott vendégtagsággal. Vagy létrehozhat egy dinamikus csoportot egy névvel, például "Contoso vendégei" a Contoso-bérlő ben lévő felhasználók számára, akik a Guest UserType attribútumértékkel rendelkeznek.  A hatékonyság érdekében győződjön meg arról, hogy a csoport túlnyomórészt vendégek – ne válasszon olyan csoportot, amelynek tagfelhasználói vannak, mivel a tagfelhasználókat nem kell felülvizsgálni.  Ne feledje azt is, hogy a csoport tagjaként vendégfelhasználó láthatja a csoport többi tagját.

2. Ha hozzáférési felülvizsgálatot szeretne létrehozni az adott csoporthoz, jelölje ki az ellenőrzőket, hogy maguk a tagok legyenek. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának létrehozása.](create-access-review.md)

3. Kérd meg a vendégeket, hogy tekintsék át saját tagságukat. Alapértelmezés szerint minden meghívott vendég, aki elfogadta a meghívást kap egy e-mailt az Azure AD egy linket a hozzáférési felülvizsgálat a szervezet hozzáférési panelen. Az Azure AD utasításokat tartalmaz a vendégek számára a [csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintéséhez.](perform-access-review.md)  Azok a vendégek, akik nem fogadták el a meghívást, "Nem értesítettek" formában jelennek meg az értékelés eredményében.

4. Miután a véleményezők adtak bemenetet, állítsa le a hozzáférési felülvizsgálatot. További információ: [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése.](complete-access-review.md)

5. Távolítsa el a meghívott, nem fejezték be az értékelést, vagy korábban nem fogadták el a meghívást. Ha a vendégek egy része olyan kapcsolattartó, aki az értékelésben való részvételre lett kiválasztva, vagy korábban nem fogadott el meghívást, letilthatja a fiókjukat az Azure Portalon vagy a PowerShellen keresztül. Ha a vendégnek már nincs szüksége hozzáférésre, és nem kapcsolattartó, eltávolíthatja a felhasználói objektumot a címtárból az Azure Portal vagy a PowerShell használatával a vendég felhasználói objektum törléséhez.

## <a name="next-steps"></a>További lépések

[Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)







