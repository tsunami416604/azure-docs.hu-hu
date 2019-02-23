---
title: Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal |} A Microsoft Docs
description: Vendégfelhasználók kezelése egy csoporthoz, vagy az Azure Active Directory hozzáférési felülvizsgálatokkal alkalmazáshoz hozzárendelt
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd3fd396333c00f8f1983250b732e69531d5d1d
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737553"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatok


Az Azure Active Directory (Azure AD), akkor egyszerűen együttműködési határait átlépve használatával engedélyezheti a [az Azure AD B2B szolgáltatás](../b2b/what-is-b2b.md). Vendég más bérlők felhasználói is lehetnek [meghívhatják a rendszergazdák által](../b2b/add-users-administrator.md) vagy [más felhasználók](../b2b/what-is-b2b.md). Ez a funkció a közösségi identitásuk, például a Microsoft-fiókok is vonatkozik.

Is könnyedén biztosíthatja, hogy vendég felhasználók rendelkeznek-e megfelelő hozzáféréssel. Megkérheti a vendégek maguk vagy egy döntéshozót, hogy a vendégek hozzáférési a hozzáférési felülvizsgálatban és hitelesítsék újból (vagy tanúsít) részt. A felülvizsgálatot végzők az Azure AD-ből származó javaslatok alapján dönthetnek arról, hogy az egyes felhasználóknak szükségük van-e folyamatos hozzáférésre. Hozzáférési felülvizsgálat befejezésekor módosításokat és hozzáférés letiltása a Vendégek, akiknek többé nincs rá szükségük.

> [!NOTE]
> Ez a dokumentum a vendégfelhasználók hozzáférés felülvizsgálata összpontosít. Ha meg szeretné tekinteni az összes felhasználói hozzáférés, nem csak vendégek [felhasználói hozzáférés felügyelete a hozzáférési felülvizsgálatok az](manage-user-access-with-access-reviews.md). Ha meg szeretné tekinteni a rendszergazdai szerepköröket, például a globális rendszergazdai felhasználói csoporttagság [hozzáférési felülvizsgálat indítása az Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Előfeltételek 


A hozzáférési felülvizsgálatok az Azure AD Premium P2 kiadásában érhetők el, a Microsoft nagyvállalati mobilitási + biztonsági E5 csomagban. További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások). Az ezt a funkciót használó összes felhasználónak, beleértve a felülvizsgálatot létrehozó, a felülvizsgálatot kitöltő és a hozzáférését megerősítő felhasználóknak is licencre van szükségük. 

Vendégfelhasználók tekintse át a saját hozzáférését is kérhet. Minden fizetős Azure AD Premium P2-licenc, amely a saját szervezet felhasználói egyik rendel B2B használatával a külső felhasználó keretet alatt legfeljebb öt vendégfelhasználók meghívása. Ezek a vendég felhasználók is az Azure AD Premium P2-funkciók is használhatók. További információkért lásd: [Azure AD B2B-együttműködés licencelése](../b2b/licensing-guidance.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Hozzon létre, és a vendégek hozzáférési felülvizsgálat végrehajtása

Először egy globális rendszergazdai vagy a felhasználóifiók-adminisztrátor, nyissa meg a [hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) annak biztosításához, hogy a hozzáférési felülvizsgálatok készen áll a szervezet számára. 

Az Azure AD lehetővé teszi, hogy számos forgatókönyv esetében a vendégfelhasználók áttekintése.

Áttekintheti a következők:

 - A csoport az Azure ad-ben, amely rendelkezik egy vagy több vendégek tagként.
 - Egy alkalmazás csatlakozik az Azure ad-ben, amely rendelkezik egy vagy több hozzárendelt vendég felhasználók. 

Ezután eldöntheti, kérje meg az egyes vendég, tekintse át a saját hozzáférését, vagy kérje meg egy vagy több felhasználó, tekintse át a minden vendéghozzáférés-e.

 Ezek a forgatókönyvek az alábbi szakaszokban található terjed ki.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Kérje meg a vendégek saját csoportbeli tagság felülvizsgálata

A hozzáférési felülvizsgálatok segítségével győződjön meg arról, hogy meghívott és a csoportba felvett felhasználók továbbra is hozzá kell férniük. Tekintse át a saját a csoporttagság, hogy a vendégek egyszerűen teheti fel.

1. A csoporthoz tartozó hozzáférési felülvizsgálat létrehozása, jelölje be a felülvizsgálatot, csak a Vendég felhasználói tagok és a tagok áttekintése magukat. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md).

2. Kérje meg, tekintse át a saját tagság egyes vendég. Alapértelmezés szerint minden Vendég, aki elfogadta a meghívást kap egy e-mailt egy hivatkozást az Azure AD-ből, a hozzáférési felülvizsgálatot. Az Azure AD a vendégek utasításokat rendelkezik való [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md).

4. Megtagadta a saját felhasználók kívül kell e folyamatos hozzáférésre, valamint eltávolíthatja a felhasználók, akik nem válaszolt. A nem válaszoló felhasználók vélhetően többé nem kapja meg e-mailt.

5. Ha a csoport nem használható az hozzáférés-kezelés, is eltávolíthatja felhasználók, akik a ki nem választott a felülvizsgálatot részt, mivel azok nem fogadja el a meghívót. Nem fogad jelezheti, hogy a meghívott felhasználó e-mail címének elgépelte volt-e. Egy csoportot, terjesztési lista használata esetén akár néhány vendégfelhasználók ki nem választott részt venni, mivel korábban már kapcsolattartási objektumokat.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Kérje meg a szponzor egy Vendég-csoportbeli tagság felülvizsgálata

Megkérheti a szponzor, például egy csoportba, tekintse át a Vendég szükség folyamatos csoportbeli tagság tulajdonosa.

1. A csoporthoz tartozó hozzáférési felülvizsgálat létrehozása, jelölje be a felülvizsgálatot, csak a Vendég felhasználói tagok belefoglalása. Majd adjon meg egy vagy több felülvizsgálók. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md).

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindannyian kapnak egy e-mailt egy hivatkozást az Azure AD-ből, a hozzáférési panelen, ahol azok [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Kérje meg vendégeket tekintse át a saját hozzáférést egy alkalmazáshoz

A hozzáférési felülvizsgálatok segítségével győződjön meg arról, hogy azok a felhasználók, akik meg lettek hívva egy adott alkalmazásban továbbra is hozzá kell férniük. Könnyedén megkérheti magukat a Vendégek, tekintse át a saját hozzáférés szükséges.

1. Az alkalmazás hozzáférési felülvizsgálat létrehozása, jelölje be a felülvizsgálatot, csak a vendégek és, hogy a felhasználók a saját hozzáférés áttekintése. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md).

2. Kérje meg az egyes vendég, tekintse át a saját hozzáférni az alkalmazáshoz. Alapértelmezés szerint minden Vendég, aki elfogadta a meghívást kap egy e-mailt az Azure ad-ből. E-mailt a szervezet hozzáférési panel a hozzáférési felülvizsgálat mutató hivatkozást tartalmaz. Az Azure AD a vendégek utasításokat rendelkezik való [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md).

4. Felhasználók, akik a saját megtagadva kívül kell e folyamatos hozzáférésre, is eltávolíthatja vendég felhasználók, akik nem válaszolt. A nem válaszoló felhasználók vélhetően többé nem kapja meg e-mailt. Aki részt venni, ki nem választott különösen akkor, ha mostanában nem meghívott vendégfelhasználók is eltávolíthatja. Ezek a felhasználók nem fogadja el a meghívót, így nem kell hozzáférni az alkalmazáshoz. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Kérje meg a szponzor, tekintse át a Vendég hozzáférést egy alkalmazáshoz

Megkérheti a szponzor, például egy alkalmazást, tekintse át a Vendég szüksége folyamatos hozzáférésre a az alkalmazás tulajdonosának.

1. Az alkalmazás hozzáférési felülvizsgálat létrehozása, jelölje be a felülvizsgálatot, csak a vendégek tartalmazza. Ezután adja meg egy vagy több felhasználó felülvizsgálók. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md).

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindannyian kapnak egy e-mailt egy hivatkozást az Azure AD-ből, a hozzáférési panelen, ahol azok [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Kérje meg a vendégek hozzáférési van szükségük az általános áttekintése

Egyes szervezetekben a vendégek nem feltétlenül csoporttagságok figyelembe.

> [!NOTE]
> Az Azure Portalon korábbi verziói nem lehetővé teszik a rendszergazdai hozzáférést a felhasználók a Vendég UserType. Bizonyos esetekben a címtár-rendszergazdája esetleg megváltoztak a Vendég UserType érték tag a PowerShell használatával. Ha ez a változás korábban történt a címtárban, az előző lekérdezés nem tartalmazhat minden vendég felhasználók, akik hagyományosan felügyeleti hozzáférési jogosultságokat. Ebben az esetben kell a Vendég UserType módosítsa, vagy kézzel felvenni a Vendég a csoport tagságát.

1. Biztonsági csoport létrehozása az Azure AD-tagként, a Vendégek, ha egy megfelelő csoport már nem létezik. Például létrehozhat egy csoportot is vendégek karbantartott manuális tagság. Vagy létrehozhat egy nevet, például "Contoso vendégek" a Contoso bérlő rendelkező felhasználók számára a UserType attribútuma értékkel Vendég dinamikus csoportot.  Hatékonyságát hogy a csoport döntő többsége vendégek – egy csoportot, amelynek tagfelhasználó, ne válassza ki, mert nem tag felhasználók kell vizsgálni.  Ezenkívül vegye figyelembe, hogy látja-e a csoport más tagjai vendégfelhasználó, aki a csoport tagja.

2. Az adott csoporthoz tartozó hozzáférési felülvizsgálat létrehozása, válassza ki a teszik a felülvizsgálók számára a csoporttagokat magukat. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md).

3. Kérje meg, tekintse át a saját tagság egyes vendég. Alapértelmezés szerint minden Vendég, aki elfogadta a meghívást kap egy e-mailt egy hivatkozást az Azure AD-ből, a hozzáférési felülvizsgálatot, a szervezet hozzáférési panelen. Az Azure AD a vendégek utasításokat rendelkezik való [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md).  Ezen Vendégek, aki nem fogadja el a meghívást, a "Nem kap értesítést" a felülvizsgálati eredmények megjelennek.

4. Miután a felülvizsgálók nyilvánítsanak, állítsa le a hozzáférési felülvizsgálatot. További információkért lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md).

5. Távolítsa el a vendéghozzáférés Vendégek, akik el lett utasítva, nem fejeződött be a felülvizsgálatot, vagy korábban nem fogadja el a meghívót. Ha a vendégek néhány partnerek, akik a felülvizsgálatot részt kijelölve, vagy nem korábban elfogadják a meghívást, az Azure portal vagy a PowerShell használatával letilthatják fiókjukat is. Ha a Vendég már hozzá kell férnie, és nem egy névjegyet, eltávolíthatja a felhasználói objektum a címtárból a Vendég felhasználói objektum törlése az Azure portal vagy a PowerShell használatával.

## <a name="next-steps"></a>További lépések

[Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md)







