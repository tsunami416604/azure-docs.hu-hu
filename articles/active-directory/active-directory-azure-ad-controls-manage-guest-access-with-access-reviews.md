---
title: Vendég kezelésére, az Azure ad-vel hozzáférni értékelést |} Microsoft Docs
description: Vendégfelhasználók kezelése egy csoportba, vagy az alkalmazás Azure Active Directory hozzáférési értékelést rendelve
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.openlocfilehash: 8eb7e4bc79ee50982dae5751968eb732ef39e00d
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017780"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Az Azure ad-val vendég-hozzáférés kezelése értékelést eléréséhez


Az Azure Active Directoryval (Azure AD), akkor egyszerűen együttműködés szervezet határain túlnyúló használatával engedélyezheti a [Azure AD B2B szolgáltatás](active-directory-b2b-what-is-azure-ad-b2b.md). A többi bérlőtől vendégfelhasználók lehet [rendszergazdák által meghívott](active-directory-b2b-admin-add-users.md) vagy [más felhasználók](active-directory-b2b-how-it-works.md). Ez a funkció a közösségi identitások például a Microsoft-fiókok is vonatkozik.

Is könnyen biztosítható, hogy a vendégfelhasználók megfelelő hozzáféréssel rendelkeznek. A vendégek maguk vagy egy áttekintése és Újramegerősítés (vagy tanúsít) részt a vendégek hozzáférés döntéshozó teheti fel. A felülvizsgálatot végzők az Azure AD-ből származó javaslatok alapján dönthetnek arról, hogy az egyes felhasználóknak szükségük van-e folyamatos hozzáférésre. Ha egy áttekintése befejeződött, módosításokat és megszünteti a hozzáférést a Vendégek, akik nem szükséges.

> [!NOTE]
> Ez a dokumentum elsősorban való vendégfelhasználók számára hozzáférést ellenőrzi. Ha meg szeretné tekinteni a minden felhasználó hozzáférést, nem pedig csak vendégek [felhasználói hozzáférés kezelése a hozzáférési értékelést](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Ha meg szeretné tekinteni a rendszergazdai szerepköröket, például a globális rendszergazda felhasználók tagság [egy hozzáférés-ellenőrzés indítása az Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Előfeltételek 


A hozzáférési felülvizsgálatok az Azure AD Premium P2 kiadásában érhetők el, a Microsoft nagyvállalati mobilitási + biztonsági E5 csomagban. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások). Az ezt a funkciót használó összes felhasználónak, beleértve a felülvizsgálatot létrehozó, a felülvizsgálatot kitöltő és a hozzáférését megerősítő felhasználóknak is licencre van szükségük. 

Ha azt tervezi, kérje meg a vendégfelhasználók számára saját hozzáférési áttekintéséhez, olvassa el a Vendég felhasználó licencelésével kapcsolatban. További információkért lásd: [Azure AD B2B együttműködés licencelési](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Hozzon létre, és hajtsa végre a vendégek egy áttekintése

Először engedélyezése egy felülvizsgáló hozzáférés panelek megjelenő hozzáférés értékelést. Globális rendszergazdaként vagy felhasználói fiók rendszergazdájához, navigáljon a [hozzáférés ellenőrzi, hogy lap](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Az Azure AD lehetővé teszi több vendégfelhasználók áttekintése.

Válassza ki a következők egyikét:

 - A csoport az Azure ad-ben, amely rendelkezik egy vagy több vendégek tagként.
 - Egy alkalmazás, amely rendelkezik egy vagy több vendégfelhasználók rendelve az Azure AD kapcsolódik. 

Majd eldöntheti, hogy kérje meg a saját hozzáférésüket áttekintéséhez, és kérje meg egy vagy több felhasználó minden vendég hozzáférés áttekintéséhez minden Vendég-e.

 Ezek a forgatókönyvek a következő szakaszok ismertetnek.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Kérje meg a vendégek saját csoporttagságok áttekintése

Hozzáférés értékelést segítségével győződjön meg arról, hogy meghívót, és a csoporthoz hozzáadott felhasználók továbbra is hozzá kell férniük. Könnyen megkérheti a vendégek áttekintheti a saját csoport tagjának kell lennie.

1. A csoport egy hozzáférés-ellenőrzés indítása, jelölje be a felülvizsgálati csak a Vendég felhasználói tagjai és tagok ellenőrizze magát. További információkért lásd: [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md).

2. Kérje meg minden Vendég számára tekintse át a saját tagságát. Alapértelmezés szerint minden Vendég meghívót elfogadó kap egy e-mailt az Azure AD egy hivatkozás, az access-ellenőrzéshez. Azure AD is rendelkezik a vendégek utasításokat való [tekintse át a hozzáférésüket](active-directory-azure-ad-controls-perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md).

4. Mellett a saját szükségességét is folyamatos hozzáférést biztosíthasson megtagadva felhasználók is eltávolíthatja a felhasználók, akik nem válaszolt. A nem válaszoló felhasználók potenciálisan nem e-maileket kapni.

5. Ha hozzáférés-kezelés a csoport nem használja, is eltávolíthatja a felülvizsgálati részt, mivel azok nem fogadta el a meghívó nem kijelölt felhasználók. Nem fogad utalhat, hogy volt-e egy elírás a meghívott felhasználó e-mail címét. Egy csoport terjesztési lista használata esetén lehet, hogy néhány vendégfelhasználók részt venni, mert kapcsolattartási objektumok nem kiválasztva.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Kérje meg a megbízó egy Vendég csoporttagságok áttekintése

Kérje meg a támogató vezetővel, például egy csoport, tekintse át a Vendég szükség folyamatos csoporttagságok tulajdonosa.

1. A csoport egy hozzáférés-ellenőrzés indítása, jelölje be a Vendég felhasználói tagjaira áttekintése. Adja meg legalább egy felülvizsgálót. További információkért lásd: [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md).

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindannyian kapnak egy e-mailt az Azure AD-től, amely a hozzáférési panelre mutató hivatkozást tartalmazza, ahol [elvégezhetik a hozzáférési felülvizsgálatot](active-directory-azure-ad-controls-perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Kérje meg a vendégek saját alkalmazáshoz való hozzáférés áttekintése

Hozzáférés értékelést segítségével győződjön meg arról, hogy azok a felhasználók, akik meg lettek hívva egy adott alkalmazáshoz továbbra is hozzá kell férniük. A hozzáféréshez szükséges magukat a vendégek áttekintheti a saját egyszerűen kérhet.

1. Az alkalmazás hozzáférési ellenőrzés indítása, jelölje be a felülvizsgálati csak vendégek és, hogy a felhasználók a saját hozzáférésüket tekintse át. További információkért lásd: [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md).

2. Kérje meg minden Vendég számára tekintse át a saját hozzáférni az alkalmazáshoz. Alapértelmezés szerint minden Vendég meghívót elfogadó kap egy e-mailt egy hivatkozás, az Azure AD számára a hozzáférési panelen a szervezet áttekintése. Azure AD is rendelkezik a vendégek utasításokat való [tekintse át a hozzáférésüket](active-directory-azure-ad-controls-perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md).

4. Felhasználók, akik a saját megtagadva mellett kell a folyamatos hozzáférés, is eltávolíthatja vendég felhasználók, akik nem válaszolt. A nem válaszoló felhasználók potenciálisan nem e-maileket kapni. Nem választott részt venni, különösen akkor, ha a közelmúltban lettek meghívott vendég felhasználók is eltávolíthatja. Ezek a felhasználók nem fogadta el a meghívót, ezért nem volt hozzáférni az alkalmazáshoz. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Kérje meg a megbízó áttekintheti a Vendég hozzáférést egy alkalmazáshoz

Kérje meg a támogató vezetővel, például egy alkalmazást, tekintse át a Vendég szükségességét is folyamatos hozzáférést biztosíthasson az alkalmazás tulajdonosa.

1. Az alkalmazás hozzáférési ellenőrzés indítása, jelölje be a felülvizsgálati vendégek csak felvenni. Majd adja meg egy vagy több felhasználó felülvizsgálók. További információkért lásd: [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md).

2. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindannyian kapnak egy e-mailt az Azure AD-től, amely a hozzáférési panelre mutató hivatkozást tartalmazza, ahol [elvégezhetik a hozzáférési felülvizsgálatot](active-directory-azure-ad-controls-perform-access-review.md).

3. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Kérje meg a vendégek az kell, általában áttekintése

Egyes szervezetekben vendégek nem feltétlenül csoporttagságok tudomást.

> [!NOTE]
> Az Azure-portálon korábbi verzióiban nem engedélyezi a Vendég UserType rendelkező felhasználók rendszergazdai hozzáféréssel. Néhány esetben a rendszergazda a címtárban módosulhatott egy Vendég UserType érték tagra PowerShell használatával. Ez a változás korábban történt a címtárban, ha az előző lekérdezés nem közé tartozik a Vendég felhasználó felhasználók hagyományosan kellett a felügyeleti hozzáférési jogosultsága. Ebben az esetben kell a Vendég UserType módosítsa, vagy manuálisan közé tartozik a Vendég a csoport tagságát.

1. Biztonsági csoport létrehozása az Azure AD és a vendégek is, ha a megfelelő csoport nem létezik. Például a vendégek manuálisan karbantartott tagként egy csoportot is létrehozhat. Vagy dinamikus csoportot hozhat létre a Vendég UserType attribútuma értékkel rendelkező felhasználók a Contoso bérlő például "Contoso vendégek" nevére.  A hatékonyság érdekében győződjön meg arról a csoport döntő többsége vendégek – ne válasszon ki egy csoportot, amelyen a felhasználók, akik nem kell vizsgálni.

2. A csoport egy hozzáférés-ellenőrzés indítása, jelölje be a megtekinteni jogosultak a saját magukat. További információkért lásd: [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md).

3. Kérje meg minden Vendég számára tekintse át a saját tagságát. Alapértelmezés szerint minden Vendég meghívót elfogadó kap egy e-mailt egy hivatkozás, az Azure AD számára a hozzáférési panelen a szervezet áttekintése. Azure AD is rendelkezik a vendégek utasításokat való [tekintse át a hozzáférésüket](active-directory-azure-ad-controls-perform-access-review.md).  E vendégek, akik a meghívott felhasználó nem fogadta el a felülvizsgálati eredményeinek mint "Nem kap értesítést" megjelenik.

4. Felülvizsgáló, biztosítva a bevitel után állítsa le a áttekintése. További információkért lásd: [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md).

5. Távolítsa el a vendég hozzáférés a Vendégek, akik megvonták, nem fejeződött be a felülvizsgálati vagy korábban nem fogadta el a meghívást. Ha a vendégek némelyike partnerek, akik a felülvizsgálati részt sincs kijelölve, vagy azokat korábban nem elfogadja a meghívót, letilthatja a fiókok az Azure-portálon vagy a PowerShell használatával. Ha a vendég nem engedéllyel kell rendelkeznie, és egy ügyfél nem, akkor eltávolíthatja a user objektum a címtárban a Vendég felhasználói objektum törlése az Azure portálon vagy a PowerShell használatával.

## <a name="next-steps"></a>További lépések

[Hozzáférési felülvizsgálat létrehozása egy csoport tagjai számára vagy egy alkalmazáshoz való hozzáférés céljából](active-directory-azure-ad-controls-create-access-review.md)







