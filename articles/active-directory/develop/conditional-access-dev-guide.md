---
title: Feltételes hozzáférés az Azure Active Directory fejlesztői útmutatója
description: Fejlesztői útmutatás és az Azure AD feltételes hozzáférési forgatókönyvek
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
ms.author: celested
ms.reviewer: dadobali
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: c02f094def3828d0839025f4b7dea48ee64adcc8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543186"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Feltételes hozzáférés az Azure Active Directory fejlesztői útmutatója

Az Azure Active Directoryban (Azure AD) feltételes hozzáférés funkciójának kínál, amellyel az alkalmazás biztonságos és védelme a szolgáltatás számos módon. Feltételes hozzáférés lehetővé teszi a fejlesztők és a nagyvállalati vásárlók többféle, beleértve a szolgáltatások védelme érdekében:

* Multi-Factor Authentication
* Így csak az Intune-ban regisztrált eszközök adott szolgáltatásokhoz való hozzáférést
* Korlátozhatja a felhasználó tartózkodási helye és IP-címtartományok

A feltételes hozzáférés az összes funkciójáról további információkért lásd: [feltételes hozzáférés az Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Alkalmazások létrehozása az Azure ad fejlesztőknek Ez a cikk bemutatja, hogyan használhatja a feltételes hozzáférés, és is megismerkedhet a hatását, amelyek nem rendelkeznek erőforrásokhoz való hozzáférés felett, előfordulhat, hogy alkalmazza a feltételes hozzáférési szabályzatok. A cikk ismerteti a feltételes hozzáférés a alapú meghatalmazásos folyamat következményei webalkalmazások, Microsoft Graph elérése és API-k hívása is.

Ismerete [egyetlen](quickstart-v1-integrate-apps-with-azure-ad.md) és [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazásokat és [gyakori hitelesítési minták](authentication-scenarios.md) feltételezi.

## <a name="how-does-conditional-access-impact-an-app"></a>Milyen hatással van a feltételes hozzáférés egy alkalmazás?

### <a name="app-types-impacted"></a>Érintett alkalmazástípusok

Leggyakoribb esetekben feltételes hozzáférés nem változtatja meg az alkalmazás viselkedését, vagy a fejlesztőtől származó módosításokat igényel. Csak bizonyos esetekben a alkalmazás közvetett módon vagy beavatkozás nélkül tokent kér egy szolgáltatáshoz, amikor egy alkalmazásban szükséges kódmódosításokat "kihívások" feltételes hozzáférés kezelésére. Elképzelhető, hogy egyszerűen egy interaktív bejelentkezési kérelem végrehajtása.

Pontosabban a következő esetekben szükséges "kihívások" feltételes hozzáférés kezeléséhez szükséges kódot:

* Alkalmazások a alapú meghatalmazásos folyamat végrehajtása
* Az alkalmazások több szolgáltatásokhoz és erőforrásokhoz való hozzáférés
* Egyoldalas alkalmazások ADAL.js használatával
* A Web Apps erőforrás hívása

Feltételes hozzáférési szabályzatok alkalmazhatók az alkalmazást, de is alkalmazható egy webes API-t az alkalmazás hozzáfér. Feltételes hozzáférési szabályzat konfigurálásával kapcsolatos további tudnivalókért lásd: [a rövid útmutató: Többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés az adott alkalmazások](../conditional-access/app-based-mfa.md).

A forgatókönyvtől függően a nagyvállalati ügyfelek fiókazonosítóját vonatkoznak, és bármikor eltávolíthatja a feltételes hozzáférési szabályzatokat. Ahhoz, hogy az alkalmazás működését, amikor egy új házirendet alkalmaznak a "kérdés" kezelési megvalósításához szüksége. Az alábbi példák bemutatják, challenge kezelését.

### <a name="conditional-access-examples"></a>Feltételes hozzáférési példa

Egyes forgatókönyvekben feltételes hozzáférés kezelésére, míg mások működik, kódmódosítás szükséges. Az alábbiakban néhány forgatókönyv feltételes hozzáférés használata ehhez a multi-factor authentication szolgáltatás, amely a különbség a betekintést biztosít.

* Egy egybérlős iOS-alkalmazást hoz létre, és a alkalmazni a feltételes hozzáférési szabályzat. Az alkalmazás a felhasználó bejelentkezik, és nem igényelhet hozzáférést egy API-t. A felhasználó bejelentkezésekor automatikusan megnyílik, a szabályzatot, és a felhasználónak a multi-factor authentication (MFA) végrehajtása. 
* A középső réteg szolgáltatást használ egy alárendelt API eléréséhez natív alkalmazást fejleszt. A vállalat az alkalmazás használatával a nagyvállalati ügyfelek fiókazonosítóját alkalmazza a szabályzatot az alsóbb rétegbeli API-hoz. Ha egy végfelhasználó bejelentkezik, a natív alkalmazás a középső réteg hozzáférést kér, és elküldi. A középső réteg hajt végre a-meghatalmazásos folyamat az alsóbb rétegbeli API-hoz való hozzáférés kéréséhez. A "kérdés" jogcímek ezen a ponton a középső réteg számára. A középső réteg küld a kihívás az natív alkalmazás, amelynek meg kell felelnie azoknak a feltételes hozzáférési szabályzat.

#### <a name="microsoft-graph"></a>Microsoft Graph

A Microsoft Graph különleges szempontok rendelkezik, a feltételes hozzáférés környezetekben alkalmazások készítése során. Általában a feltételes hozzáférés mechanics úgy viselkedik, de a szabályzatok a felhasználók az alapul szolgáló adatokat kér az alkalmazás a diagramon fog alapulni. 

Pontosabban a hatóköröket a Microsoft Graph képviselik néhány adatkészletet, amely a alkalmazni szabályzatokat külön-külön is rendelkezhet. Feltételes hozzáférési szabályzatok hozzá van rendelve az adott adatkészleteket, mivel az Azure AD fog mögött Graph - adatok alapján feltételes hozzáférési házirendek betartatásához helyett a Graph-magát.

Például, ha az alkalmazás kéri a következő Microsoft Graph-hatókörök

```
scopes="Bookings.Read.All Mail.Read"
```

Egy alkalmazás Bookings és Exchange szabályzatokhoz teljesítéséhez felhasználók várható. Bizonyos hatókörökre előfordulhat, hogy leképezése több adatkészlet, ha engedélyezi a hozzáférést. 

### <a name="complying-with-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat betartása

Számos különböző alkalmazás-topológiákat a feltételes hozzáférési szabályzat lesz kiértékelve, amikor a munkamenet. Feltételes hozzáférési szabályzat működtethető alkalmazások és szolgáltatások közül, mint a a pont, amelyen meghívása a megvalósítani kívánt forgatókönyv erősen függ.

Amikor az alkalmazás megpróbálja elérni egy szolgáltatást, a feltételes hozzáférési szabályzattal együtt, akkor feltételes hozzáférési kihívást merülhetnek fel. Ez kihívást a kódolt a `claims` paraméter, amely a válasz az Azure ad-ből származik. Íme egy példa a kérdés paraméter: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A fejlesztők mutatták igénybe és fűzze hozzá egy új kérelmet az Azure AD-be. Ebben az állapotban passing kéri a végfelhasználó számára, amelyek szükségesek ahhoz a feltételes hozzáférési szabályzat bármely művelet elvégzésére. A következő esetekben a hiba, és hogyan lehet kigyűjteni a paraméter tulajdonságairól magyarázata.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="prerequisites"></a>Előfeltételek

Az Azure AD feltételes hozzáférés lehetővé teszi a [prémium szintű Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). További információ a licencelésről a követelmények a [licenccel nem rendelkező használati jelentés](../active-directory-conditional-access-unlicensed-usage-report.md). A fejlesztők csatlakozhat a [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), amely tartalmazza a nagyvállalati mobilitási csomag, amely magában foglalja a prémium szintű Azure AD-előfizetés ingyenes.

### <a name="considerations-for-specific-scenarios"></a>Bizonyos forgatókönyvek esetén megfontolandó szempontok

Feltételes hozzáférés forgatókönyvekben csak vonatkozik a következő információkat:

* Alkalmazások a alapú meghatalmazásos folyamat végrehajtása
* Az alkalmazások több szolgáltatásokhoz és erőforrásokhoz való hozzáférés
* Egyoldalas alkalmazások ADAL.js használatával

A következő részekben bemutatjuk a gyakori forgatókönyvek összetettebb. A működési elv középpontjában feltételes hozzáférési szabályzatok értékeli ki a szolgáltatás, amely rendelkezik a alkalmazni a feltételes hozzáférési szabályzat a jogkivonatot kért idő.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Forgatókönyv: Az alkalmazás végrehajtása a--meghatalmazásos folyamat

Ebben a forgatókönyvben azt végig a helyzet, amelyben egy natív alkalmazást meghív egy webes szolgáltatás és az API is. Ezután a szolgáltatásnak nincs-e ["he a-meghatalmazásos" folyamat egy alsóbb rétegbeli szolgáltatás hívásához. Ebben az esetben azt alkalmazott a feltételes hozzáférési szabályzatot az alárendelt szolgáltatás (Web API 2) és egy kiszolgáló/démon alkalmazások helyett inkább egy natív alkalmazást használ. 

![Az alkalmazás végrehajtása a alapú meghatalmazásos folyamat diagramja](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Webes API-t 1. a kezdeti jogkivonat kérése nem kéri a felhasználó a multi-factor authentication, a webes API-t 1 mindig nem ütközhet az alsóbb rétegbeli API-t. Webes API-t 1 próbál Web API 2--meghatalmazásos jogkivonat a felhasználó kérése, ha a kérés nem teljesíthető, mert a felhasználó nem jelentkezett be a multi-factor authentication szolgáltatás.

Az Azure AD néhány érdekes adatokkal HTTP-választ adja vissza:

> [!NOTE]
> Ebben a példában a multi-factor authentication hiba leírását, de nincs széles `interaction_required` lehetséges vonatkozó feltételes hozzáférést.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A webes API-1, hogy a hiba catch `error=interaction_required`, és visszaküldi a `claims` vonatkozó kérdést állít be az asztali alkalmazás. Az asztali alkalmazás ezen a ponton végezhet egy új `acquireToken()` meghívása, és fűzze hozzá a `claims`challenge további lekérdezési karakterlánc paraméterként. Az új kérelem a felhasználónak a többtényezős hitelesítést kell végezniük, majd az új jogkivonatot küld vissza a webes API-t 1 és a alapú meghatalmazásos folyamat befejezése.

Próbálja ki ebben a forgatókönyvben, tekintse meg a [.NET-Kódmintát](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ebben bemutatjuk, hogyan adnak át a jogcímeket kihívás vissza a webes API-t 1-től a natív alkalmazás, és hozhat létre egy új kérelmet az ügyfél-alkalmazáson belül.

## <a name="scenario-app-accessing-multiple-services"></a>Forgatókönyv: Az alkalmazás több szolgáltatást elérése

Ebben a forgatókönyvben azt végig a helyzet, amelyben webes alkalmazás hozzáfér a két szolgáltatás egyike rendelkezik hozzárendelt feltételes hozzáférési szabályzat is. Az alkalmazás logikáját, attól függően, amelyben az alkalmazás nem igényel a két webes szolgáltatásokhoz való hozzáférés elérési előfordulhat, hogy létezik. Ebben a forgatókönyvben a rendelés egy tokent kér a felhasználói élmény a fontos szerepet játszik.

Tegyük fel, A és B webszolgáltatás van, és a B webszolgáltatáshoz a alkalmazni feltételes hozzáférési szabályzat. A kezdeti interaktív hitelesítési kérelmek mindkét szolgáltatás jóváhagyási igényel, míg a feltételes hozzáférési szabályzat nem minden esetben szükséges. Webszolgáltatás B jogkivonatot kér az alkalmazás, ha a szabályzat hív, és A webszolgáltatások későbbi kérelmeket is sikeres a következőképpen.

![Az alkalmazás eléréséhez több-szolgáltatások folyamatábrája](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Azt is megteheti Ha az alkalmazás kezdetben a web service A tokent kér, a végfelhasználó nem hívja meg a feltételes hozzáférési szabályzat. Ez lehetővé teszi az alkalmazás fejlesztőjének szabályozhatja a végfelhasználói élményt, és nem kényszeríti a feltételes hozzáférési szabályzat minden olyan esetben meg kell hívni. A legbonyolultabb helyzet, ha az alkalmazás ezt követően webszolgáltatás b tokent kér Ezen a ponton a végfelhasználónak meg kell felelnie azoknak a feltételes hozzáférési szabályzat. Ha az alkalmazás próbál `acquireToken`, előfordulhat, hogy létrehozni (az alábbi ábrán szemléltetett) a következő hibával:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Az alkalmazás több szolgáltatást egy új jogkivonatot igénylő elérése](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Ha az alkalmazás nem használja az ADAL könyvtár, a jogkivonat beszerzése nem sikerült mindig újra megpróbálja interaktív módon. A interaktív kérelem esetén a végfelhasználónak van a lehetőséget, hogy megfeleljen a feltételes hozzáférés. Ez igaz, ha a kérés nem egy `AcquireTokenSilentAsync` vagy `PromptBehavior.Never` ebben az esetben az alkalmazás kell végrehajtania egy interaktív ```AcquireToken``` a végfelhasználás adni a lehetőséget a szabályzat ahhoz, hogy a kérelmet.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Forgatókönyv: Egyoldalas alkalmazás (SPA) ADAL.js használatával

Ebben a forgatókönyvben azt végig az esetben, ha van egy egyoldalas alkalmazás (SPA), a feltételes hozzáférés által védett webes API-hívás ADAL.js használatával. Ez egy egyszerű architektúra, de van néhány apró sajátosságaival szeretne feltételes hozzáférési körül fejlesztése során figyelembe kell venni.

ADAL.js, a jogkivonatok lekérésére néhány funkciók találhatók: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, és `acquireTokenRedirect(…)`.

* `login()` interaktív bejelentkezési kérés egy azonosító jogkivonat szerzi be, de nem szerezhető be a hozzáférési jogkivonatok az egyes szolgáltatásokhoz (beleértve a feltételes hozzáférés által védett webes API-k).
* `acquireToken(…)` Ezután segítségével csendes szerezzen be egy hozzáférési jogkivonatot, azaz a felhasználói felület semmilyen körülmények között nem szerepelnek.
* `acquireTokenPopup(…)` és `acquireTokenRedirect(…)` vannak is lehet interaktív módon igényelni az erőforrás-jogkivonat tehát, mindig bejelentkezési felhasználói felület megjelenítése.

Amikor egy alkalmazásnak egy webes API hívása egy hozzáférési jogkivonatot kell, akkor próbálja meg egy `acquireToken(…)`. Ha a jogkivonat munkamenet lejárt, vagy meg kell felelnie egy feltételes hozzáférési szabályzat, majd a *acquireToken* függvény meghiúsul, és az alkalmazás által használt `acquireTokenPopup()` vagy `acquireTokenRedirect()`.

![Egyoldalas alkalmazás használatával az ADAL folyamatábrája](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Vegyük végig egy példa a feltételes hozzáférési forgatókönyv. Csak a felhasználó betöltötte a webhelyen, és nem rendelkezik a munkamenet. Teljesítjük egy `login()` hívja, kaphat azonosító tokent a multi-factor authentication nélkül. Ezután a felhasználó eléri egy gombot, amely szükséges az alkalmazás adatokat kérjen a webes API-t. Az alkalmazást próbál meg végrehajtani egy `acquireToken()` hívás azonban sikertelen lesz, mivel a felhasználó nem végezte el még a multi-factor authentication és a feltételes hozzáférési szabályzatának igényeinek.

Az Azure AD küld vissza a következő HTTP-választ:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Az alkalmazás tényleges kell a `error=interaction_required`. Az alkalmazás ezután használhatja `acquireTokenPopup()` vagy `acquireTokenRedirect()` ugyanazon az erőforráson futó. A felhasználónak meg kell tennie a multi-factor Authentication hitelesítést. A felhasználó a multi-factor Authentication hitelesítés befejezése után az alkalmazás egy új hozzáférési jogkivonatot a kért erőforrás jelenik meg.

Próbálja ki ebben a forgatókönyvben, tekintse meg a [JS SPA--meghatalmazásos kódminta](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ez a kódminta a feltételes hozzáférési szabályzat és a webes API-t egy JS SPA bemutatásához ebben a forgatókönyvben a korábban regisztrált használja. Azt mutatja be megfelelően a jogcímek kihívás, és a webes API-hoz használható hozzáférési jogkivonatot kapjon. Azt is megteheti, az Általános kivétel [Angular.js kódminta](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) útmutató az Angular SPA-ALKALMAZÁSOKBA

## <a name="see-also"></a>Lásd még

* A képességekkel kapcsolatos további információkért lásd: [feltételes hozzáférés az Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* További Azure AD-mintakódok, lásd: [kódmintát GitHub-adattárat](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* További információ az ADAL SDK-t és a hozzáférés a dokumentációja: [könyvtár útmutató](active-directory-authentication-libraries.md).
* Több-bérlős forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [a felhasználók a több-bérlős minta használatával hogyan](howto-convert-app-to-be-multi-tenant.md).
