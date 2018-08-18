---
title: Az Azure Active Directory rendszergazdai szerepkörök delegálása |} A Microsoft Docs
description: Delegálás modellek, példákat és az Azure Active Directory szerepköralapú biztonság
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/09/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: ad772a2e0c036c30aca2918496ac01f31157fc64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208573"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Felügyelet delegálása az Azure Active Directoryban

Szervezeti növekedést is tartalmaz a további összetettséget, és egy közös választ csökkentheti a helyszín hozzáférés-kezelés az Azure Active Directory (AD) rendszergazdai szerepkörök járó többletterhelést. A lehető legkevesebb jogosultságot rendelhet a felhasználók férhetnek hozzá az alkalmazásokhoz és a feladatok elvégzéséhez. Előfordulhat, hogy nem szeretné a globális rendszergazdai szerepkör hozzárendelése a minden alkalmazás tulajdonosa, de a hosszabb lehet, hogy kötelező-e alkalmazások felügyeleti feladatai alakzatot a meglévő globális rendszergazdák. Nincsenek számos oka lehet egy szervezet áthelyezése egy több központosított felügyelet felé. Ez a cikk alapján megtervezheti a szervezet delegáláshoz.

<!--What about reporting? Who has which role and how do I audit?-->


## <a name="centralized-versus-delegated-permissions"></a>Központosított és delegált engedélyek

Egy szervezet növekedésével a felhasználók adott rendszergazdai szerepkörökkel rendelkezik nyomon követheti, nehézkes lehet. Egy szervezet ki van téve a biztonsági szabályok megsértése esetén alkalmazandók lehetnek, ha egy alkalmazott azokat a nem rendszergazdai jogosultságokkal rendelkezik. A rendszergazdák számát és a rájuk vonatkozó engedélyek közül általában attól függ, méretét és a központi telepítés bonyolultságát.

* A kis- és proof-of-concept-környezetekben egy vagy több rendszergazdák mindent; Nincs nem delegálás. Ebben az esetben hozzon létre minden egyes rendszergazda globális rendszergazdai szerepkörrel.
* Nagyobb telepítések a további gépek, alkalmazások és asztali számítógépek a további delegálás van szükség. Előfordulhat, hogy a rendszergazdák számos pontosabban meghatározott működési feladatai (szerepkörök). Például előfordulhat, egyes Privileged Identity rendszergazdák, és mások esetleg az alkalmazás-rendszergazdák. Emellett a rendszergazda az objektumok, például az eszközök csak bizonyos csoportok előfordulhat, hogy kezelése.
* Még nagyobb telepítések még részletesebb engedélyek, és valószínűleg karaktereknél rendelkező rendszergazdák vagy hibrid szerepkörök lehet szükség.

Az Azure AD-portálon is [bármely szerepkör összes tagja megtekintheti](directory-manage-roles-portal.md), amelyek segítségével gyorsan ellenőrizheti az üzembe helyezés és a delegált engedélyeit.

Ha érdekli a delegálása az Azure-erőforrásokhoz való hozzáférés és a nem rendszergazdai hozzáférés az Azure ad-ben, tekintse meg [szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör hozzárendelése](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Delegálás megtervezése

A küszöbértéket, hogy a szervezet egyedi igényeinek megfelelő delegálási modell fejlesztése, amíg a az igazság, hogy nincsenek-e nagyon egyszerű modelleket, amelyek kis módosítással alkalmazhatók. A delegálási modell fejlesztéséhez egy iteratív tervezési folyamatát, és javasoljuk, hogy kövesse az alábbi lépéseket:

* A szükséges szerepkörök definiálása
* Alkalmazások felügyelete
* Alkalmazások regisztrálása megadása
* Delegált alkalmazás tulajdonosa
* Biztonsági terv kidolgozása
* Vészhelyzeti fiókok létrehozása
* A rendszergazdai szerepkörök védelme
* Győződjön meg, a kiemelt jogosultságú jogosultságszint-emelési ideiglenes

## <a name="define-roles"></a>Szerepkörök definiálása

Határozza meg, mely az Active Directory-feladatokat végeznek a rendszergazdák, és hogyan ezeket a feladatokat szerepkörök vannak leképezve. Az Active Directory-hely létrehozása például a szolgáltatás felügyeleti feladat, a biztonságicsoport-tagság módosítása általában adatok felügyelete alá tartozik. Is [megtekintése részletes szerepköreinek leírása](directory-manage-roles-portal.md) az Azure Portalon.

Minden tevékenység gyakorisága, fontosság és nehézség kell kiértékelni. Ezek a feladatdefiníció létfontosságú aspektusait, mert azok szabályozzák, hogy delegálja az engedélyt. Rendszeresen el kell végezni, kockázati korlátozott, és feladatokat triviális végrehajtásához alkalmasak a delegálást. Másrészről ritkán hajtja végre, de nagy hatással van a szervezeten belül és szükséges ismeretek magas szintű feladatokat érdemes alaposan nagyon delegálása előtt. Ehelyett [ideiglenesen fiók jogosultsági szintjének emelése egy, a szükséges szerepkör](../active-directory-privileged-identity-management-configure.md) vagy újbóli hozzárendelése a feladatot.

## <a name="delegate-app-administration"></a>Alkalmazások felügyelete

A szervezeten belüli alkalmazások elterjedésével a delegálási modell is törzs. A globális rendszergazda helyezi az alkalmazáshozzáférés-kezeléshez nehezedő terheket, valószínű, hogy a modell a terhelés növekszik, idő előrehaladtával. Ha például a vállalati alkalmazások konfigurálása a globális rendszergazdai szerepkörrel rendelkezik kapott személyek, most kiszervezheti őket a következő kevesebb kiemelt jogosultságú szerepkörök. Ezzel javíthatja biztonsági helyzetét segítségével, és csökkenti a potenciális kellemetlen hibáktól. Az alkalmazás a legtöbb kiemelt jogosultságú rendszergazdai szerepköröket használják:

* A **alkalmazás-rendszergazda** szerepkört, amely képes kezelni a könyvtárban, beleértve a regisztrációk, egyszeri bejelentkezési beállításainak, felhasználó és csoport-hozzárendelések és licencelés, alkalmazást proxybeállításokat, minden alkalmazás biztosít és engedélyt. Nem biztosítja feltételes hozzáférés kezelését.
* A **Felhőalkalmazás-rendszergazda** szerepkört, amely engedélyezi a képességek az alkalmazás-rendszergazda, azzal a különbséggel, nem biztosíthat hozzáférést az alkalmazásproxy-beállítások (mert nem a helyszíni jogosult azt). ## delegált alkalmazás tulajdonosi engedélyekkel alkalmazásonkénti

## <a name="delegate-app-registration"></a>Delegált alkalmazásregisztráció

Alapértelmezés szerint minden felhasználó alkalmazásregisztrációkat hozhat létre. Ha azt szeretné, külön-külön megadni alkalmazásregisztrációkat hozhat létre, lehetővé teszi, kell beállítani **felhasználók regisztrálhatnak alkalmazásokat** nem a felhasználói beállítások és a majd rendelje hozzá a alkalmazásfejlesztő szerepkör. Ez a szerepkör listázását teszi alkalmazásregisztrációkat hozhat létre, csak akkor, ha a **felhasználók regisztrálhatnak alkalmazásokat** ki van kapcsolva. Az alkalmazásfejlesztők maguk is hagyhatja jóvá során a **felhasználók engedélyezhetik alkalmazások hozzáférjenek a vállalati adatokhoz a felhasználók nevében** értéke nem. Amikor a alkalmazásfejlesztő létrehoz egy új alkalmazás regisztrálása, a rendszer automatikusan felveszi első tulajdonosként.

## <a name="delegate-app-ownership"></a>Delegált alkalmazás tulajdonosa

Még részletesebben app access delegáláshoz hozzárendelheti tulajdonjoga az egyes vállalati alkalmazásokhoz. Ez egészíti ki a regisztrációs alkalmazástulajdonosok hozzárendelése meglévő támogatása. Tulajdonos hozzárendelése a vállalati alkalmazások panelen-enterprise alkalmazás alapon. Az az előnye tulajdonosai csak a saját vállalati alkalmazások kezelheti. Például hozzárendelhet a Salesforce alkalmazás tulajdonosát, és a tulajdonos kezelheti a hozzáférést és konfigurációs a Salesforce és más alkalmazások nem. Vállalati alkalmazás számos tulajdonosokkal rendelkezhetnek, és a felhasználó lehet a tulajdonosa, számos vállalati alkalmazások számára. Két alkalmazás tulajdonosa szerepkörök állnak rendelkezésre:

* A **vállalati alkalmazás tulajdonosa** szerepkörök kezelését a "nagyvállalati alkalmazásokhoz, amelyeket a felhasználó tulajdonában lévő, ideértve az egyszeri bejelentkezés beállításai, felhasználó és csoport-hozzárendeléseit, illetve további tulajdonosok hozzáadása. Nem képes kezelni az alkalmazásproxy-beállítások vagy feltételes hozzáférés biztosítja.
* A **regisztrációs Alkalmazástulajdonos** szerepköre lehetővé teszi, hogy a felhasználó tulajdonában lévő, beleértve az alkalmazásjegyzékben, és további tulajdonosok hozzáadása az alkalmazást az alkalmazásregisztrációk kezelését.

## <a name="develop-a-security-plan"></a>Biztonsági terv kidolgozása

Az Azure AD biztosít egy széles körű útmutató a tervezési és a egy biztonsági csomag végrehajtása az Azure AD felügyeleti szerepkörök [Securing privileged hozzáférni a felhőbeli és hibrid környezetekben](directory-admin-roles-secure.md). 

## <a name="establish-emergency-accounts"></a>Vészhelyzeti fiókok létrehozása

Kezelése az identity management-áruház elérését, ha probléma merül fel, hogy készítse elő a következők szerint a vészelérési fiókok [válságkezelési hozzáférés rendszergazdai fiókok létrehozása](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>A rendszergazdai szerepkörök védelme

Kézben a kiemelt jogosultságú fiókok beolvasása, akik is tegye áttekintse kárt, így ezeket a fiókokat először használatával véd a [alapvető hozzáférési házirendet](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) , amely érhető el az összes Azure AD-bérlő alapértelmezés szerint (nyilvános előzetes verzió). A szabályzat kötelezővé teszi a multi-factor authentication az emelt szintű Azure AD-fiókokat. Az alábbi Azure AD-szerepkörök által az Azure AD alapvető házirendet terjed ki:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférésű rendszergazda
* Biztonsági rendszergazda

## <a name="elevate-privilege-temporarily"></a>Jogosultság ideiglenesen jogosultságszint-emelés

A legtöbb napi tevékenységek nem minden felhasználónak kell a globális rendszergazdai jogosultságokkal. És nem minden felhasználó véglegesen hozzá kell rendelni a globális rendszergazdai szerepkörrel. Amikor a felhasználóknak szükségük van ahhoz globális rendszergazdaként, a szerepkör-hozzárendelés az Azure ad-ben kell aktiválnak [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) saját vagy egy másik rendszergazdai fiókkal.

## <a name="next-steps"></a>További lépések

Az Azure AD-szerepkör ismertetését referenciáért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure ad-ben](directory-assign-admin-roles.md)