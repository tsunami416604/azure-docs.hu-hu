---
title: Fejlesztői útmutató az Azure Active Directory feltételes hozzáféréséhez
description: Fejlesztői útmutató és forgatókönyvek az Azure AD feltételes hozzáférés és a Microsoft identitásplatform.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 03/16/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: aae1b8aa27363e8f1d3c72d3934146c47b0cf2c9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535893"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Fejlesztői útmutató az Azure Active Directory feltételes hozzáféréséhez

Az Azure Active Directory (Azure AD) feltételes hozzáférés funkciója számos olyan módon kínál, amelyaz alkalmazás védelmére és egy szolgáltatás védelmére használható. A feltételes hozzáférés lehetővé teszi a fejlesztők és a vállalati ügyfelek számára, hogy számos módon védhessék meg a szolgáltatásokat, többek között:

* Multi-Factor Authentication
* Lehetővé teszi, hogy csak az Intune-ban regisztrált eszközök férjenek hozzá bizonyos szolgáltatásokhoz
* A felhasználói helyek és IP-tartományok korlátozása

A feltételes hozzáférés teljes képességeiről további információt a Feltételes hozzáférés az Azure Active Directoryban című [témakörben talál.](../active-directory-conditional-access-azure-portal.md)

Az Azure AD-hez alkalmazásokat fejlesztő fejlesztők számára ez a cikk bemutatja, hogyan használhatja a feltételes hozzáférést, és megtudhatja, hogy milyen hatással van az olyan erőforrások elérésének, amelyek nem rendelkeznek azokkal a feltételes hozzáférési szabályzatokkal kapcsolatos szabályok alkalmazásával. A cikk azt is vizsgálja, hogy milyen következményekkel jár a feltételes hozzáférés a folyamat nevében, a webalkalmazások, a Microsoft Graph elérése és az API-k hívása.

Az [egy-](quickstart-register-app.md) és [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazások és a [közös hitelesítési minták](authentication-scenarios.md) ismerete feltételezhető.

> [!NOTE]
> A funkció használatához Egy Azure AD Premium P1 licenc szükséges. A követelményeinek leginkább megfelelő licenc kiválasztásáról lásd [az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásait összehasonlító cikket](https://azure.microsoft.com/pricing/details/active-directory/).
> A [Microsoft 365 Vállalati licenccel](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek is hozzáférhetnek a feltételes hozzáférés funkcióihoz.

## <a name="how-does-conditional-access-impact-an-app"></a>Milyen hatással van a feltételes hozzáférés egy alkalmazásra?

### <a name="app-types-impacted"></a>Érintett alkalmazástípusok

A legtöbb esetben a feltételes hozzáférés nem módosítja az alkalmazás viselkedését, és nem igényel módosításokat a fejlesztőtől.Csak bizonyos esetekben, amikor egy alkalmazás közvetve vagy csendben kér egy jogkivonatot egy szolgáltatáshoz, az alkalmazás kódmódosításokat igényel a feltételes hozzáférés "kihívásainak" kezeléséhez.Lehet, hogy olyan egyszerű, mint egy interaktív bejelentkezési kérelem végrehajtása.

Pontosabban a következő forgatókönyvek szükséges kódot kezelni feltételes hozzáférés "kihívások":

* A folyamat nevében végző alkalmazások
* Több szolgáltatást/erőforrást elérő alkalmazások
* Egyoldalas alkalmazások az MSAL.js használatával
* Erőforrást hívó webalkalmazások

Feltételes hozzáférési szabályzatok alkalmazhatók az alkalmazásra, de az alkalmazás által elért webes API-ra is alkalmazhatók. A feltételes hozzáférési szabályzat konfigurálásáról a [Rövid útmutató: Többfunkciós szolgáltatás megkövetelése bizonyos alkalmazásokhoz az Azure Active Directory feltételes hozzáféréssel](../conditional-access/app-based-mfa.md)című témakörben olvashat bővebben.

A forgatókönyvtől függően a vállalati ügyfelek bármikor alkalmazhatják és eltávolíthatják a feltételes hozzáférési házirendeket. Ahhoz, hogy az alkalmazás az új szabályzat alkalmazásakor is működjön, végre kell hajtania a "kihívás" kezelését. Az alábbi példák a kihívások kezelését szemléltetik.

### <a name="conditional-access-examples"></a>Példák feltételes hozzáférésre

Egyes esetekben kódmódosításokat igényelnek a feltételes hozzáférés kezeléséhez, míg mások úgy működnek, ahogy van. Íme néhány forgatókönyv feltételes hozzáférés használatával többtényezős hitelesítés, amely némi betekintést nyújt a különbség.

* Egybérlős iOS-alkalmazást hoz létre, és feltételes hozzáférési szabályzatot alkalmaz. Az alkalmazás aláírja a felhasználót, és nem kér hozzáférést egy API-hoz. Amikor a felhasználó bejelentkezik, a rendszer automatikusan meghívja a házirendet, és a felhasználónak többtényezős hitelesítést (MFA) kell végrehajtania.
* Natív alkalmazást hoz fel, amely egy középső szintű szolgáltatást használ egy alsóbb rétegbeli API eléréséhez. Az alkalmazást használó vállalati ügyfél házirendet alkalmaz az alsóbb rétegbeli API-ra. Amikor egy végfelhasználó bejelentkezik, a natív alkalmazás hozzáférést kér a középső réteghez, és elküldi a jogkivonatot. A középső réteg a folyamat nevében hajt végre hozzáférést az alsóbb rétegbeli API-hoz. Ezen a ponton a "kihívás" jogcímek jelennek meg a középső réteg. A középső réteg visszaküldi a kihívást a natív alkalmazásnak, amelynek meg kell felelnie a feltételes hozzáférési szabályzatnak.

#### <a name="microsoft-graph"></a>Microsoft Graph

A Microsoft Graph speciális szempontokat is figyelembe vett a feltételes hozzáférési környezetben történő alkalmazások létrehozásakor. Általában a feltételes hozzáférés mechanikája ugyanúgy viselkedik, de a felhasználók által látott szabályzatok az alkalmazás által a grafikonról kért alapul szolgáló adatokon alapulnak.

Pontosabban az összes Microsoft Graph hatókör olyan adatkészletet jelöl, amely egyedileg alkalmazhatja a házirendeket. Mivel a feltételes hozzáférési szabályzatok az adott adatkészletekhez vannak rendelve, az Azure AD feltételes hozzáférési szabályzatokat kényszerít a Graph mögötti adatok alapján – nem pedig magát a Graphot.

Ha például egy alkalmazás a következő Microsoft Graph-hatóköröket kéri,

```
scopes="Bookings.Read.All Mail.Read"
```

Az alkalmazások elvárhatják, hogy a felhasználók teljesítsék a Foglalások és az Exchange összes irányelvét. Egyes hatókörök több adatkészletre is leképezhetők, ha hozzáférést biztosítanak.

### <a name="complying-with-a-conditional-access-policy"></a>Feltételes hozzáférési házirend betartása

Több különböző alkalmazástopológiák esetén a rendszer a munkamenet létrehozásakor kiértékeli a feltételes hozzáférési szabályzatot. A feltételes hozzáférési szabályzat az alkalmazások és szolgáltatások részletessége alapján működik, a meghívás i.

Amikor az alkalmazás feltételes hozzáférési szabályzattal próbál hozzáférni egy szolgáltatáshoz, feltételes hozzáférési kihívással szembesülhet. Ez a kihívás az `claims` Azure AD válaszában megjelenő paraméterben van kódolva. Íme egy példa erre a kihívás paraméterre:

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A fejlesztők igénybe vehetik ezt a kihívást, és hozzáfűzhetik egy új kérelemhez az Azure AD-hez. Az állapot átadása arra kéri a végfelhasználót, hogy hajtson végre minden olyan műveletet, amely a feltételes hozzáférési házirendnek való megfeleléshez szükséges. A következő esetekben a hiba sajátosságait és a paraméter kinyerésének módját ismerteti.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="prerequisites"></a>Előfeltételek

Az Azure AD feltételes hozzáférés az [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)szolgáltatása. A [Microsoft 365 Vállalati licenccel](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek is hozzáférhetnek a feltételes hozzáférés funkcióihoz.

### <a name="considerations-for-specific-scenarios"></a>Konkrét forgatókönyvekkel kapcsolatos szempontok

A következő információk csak a következő feltételes hozzáférési forgatókönyvekben érvényesek:

* A folyamat nevében végző alkalmazások
* Több szolgáltatást/erőforrást elérő alkalmazások
* Egyoldalas alkalmazások az MSAL.js használatával

A következő szakaszok az összetettebb gyakori forgatókönyveket ismertetik. Az alapvető működési elv feltételes hozzáférési szabályzatok kiértékelése abban az időben a jogkivonat ot kéri a szolgáltatás, amely egy feltételes hozzáférési szabályzatot alkalmazott.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Eset: Az alkalmazás a folyamat nevében hajtja végre a folyamatot

Ebben a forgatókönyvben végigvezetjük az esetet, amelyben egy natív alkalmazás webszolgáltatást/API-t hív meg. Ez a szolgáltatás viszont a "nevében" folyamatot végzi egy alsóbb rétegbeli szolgáltatás hívásához. A mi esetünkben a feltételes hozzáférési szabályzatot alkalmaztuk az alsóbb rétegbeli szolgáltatásra (Web API 2), és egy natív alkalmazást használunk, nem pedig egy kiszolgálót/démonalkalmazást.

![A folyamatábrát végző alkalmazás](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

A webes API 1 kezdeti tokenkérelme nem kéri a végfelhasználót a többtényezős hitelesítésre, mivel előfordulhat, hogy a Web API 1 nem mindig éri el az alsóbb rétegbeli API-t. Ha a Webes API 1 megpróbál tokeneket kérni a felhasználó nevében a Webes API 2-höz, a kérés sikertelen lesz, mivel a felhasználó nem jelentkezett be többtényezős hitelesítéssel.

Az Azure AD http-választ ad vissza néhány érdekes adattal:

> [!NOTE]
> Ebben az esetben ez egy többtényezős hitelesítési hiba leírása, `interaction_required` de a feltételes hozzáféréshez tartozó számos lehetséges lehetőség van.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A Webes API 1-ben `error=interaction_required`elkapjuk a `claims` hibát , és visszaküldjük a kihívást az asztali alkalmazásnak. Ezen a ponton az asztali alkalmazás `acquireToken()` új hívást `claims`kezdeményezhet, és a kihívást további lekérdezési karakterlánc-paraméterként fűzheti hozzá. Ez az új kérelem megköveteli, hogy a felhasználó többtényezős hitelesítést hajtson végre, majd küldje vissza az új jogkivonatot a Webes API 1-nek, és fejezze be a folyamat nevében.

Ebben a forgatókönyvben a [.NET kódminta a](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access)következőben is megjelenik. Bemutatja, hogyan adja át a jogcímkérést a web API 1-ről a natív alkalmazásra, és hogyan hozhat létre egy új kérelmet az ügyfélalkalmazáson belül.

## <a name="scenario-app-accessing-multiple-services"></a>Eset: Alkalmazás elérése több szolgáltatás

Ebben a forgatókönyvben végigvezetjük azt az esetet, amelyben egy webalkalmazás két olyan szolgáltatáshoz fér hozzá, amelyek közül az egyikhez feltételes hozzáférési szabályzat van rendelve. Az alkalmazás logikájától függően előfordulhat, hogy létezik egy elérési út, amelyben az alkalmazás nem igényel hozzáférést mindkét webszolgáltatáshoz. Ebben a forgatókönyvben a jogkivonat kérésének sorrendje fontos szerepet játszik a végfelhasználói élményben.

Tegyük fel, hogy az A és B webszolgáltatással rendelkezünk, és a B webszolgáltatás feltételes hozzáférési szabályzatot alkalmaz. Bár a kezdeti interaktív hitelesítési kérelem mindkét szolgáltatás beleegyezését igényli, a feltételes hozzáférési szabályzat nem minden esetben szükséges. Ha az alkalmazás jogkivonatot kér a B webszolgáltatáshoz, akkor a szabályzat meghívása történik, és az A webszolgáltatás későbbi kérései is sikeresek lesznek az alábbiak szerint.

![Több szolgáltatásból álló folyamatdiagram alkalmazáshoz való hozzáférés](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Másik lehetőségként, ha az alkalmazás először kér egy jogkivonatot az A webszolgáltatáshoz, a végfelhasználó nem hívja meg a feltételes hozzáférési szabályzatot. Ez lehetővé teszi, hogy az alkalmazás fejlesztője szabályozhassa a végfelhasználói élményt, és ne kényszerítse a feltételes hozzáférési szabályzat meghívását minden esetben. A trükkös eset az, ha az alkalmazás ezt követően kér egy jogkivonatot a B webszolgáltatáshoz. Ezen a ponton a végfelhasználónak meg kell felelnie a feltételes hozzáférési szabályzatnak. Amikor az alkalmazás `acquireToken`megpróbálja, a következő hibát okozhatja (az alábbi ábrán látható):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Új jogkivonatot kérő alkalmazás több szolgáltatás elérése](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Ha az alkalmazás az MSAL-könyvtárat használja, a jogkivonat beszerzésének sikertelenségét a rendszer mindig interaktívmódon próbálja meg újra. Az interaktív kérelem esetén a végfelhasználónak lehetősége van a feltételes hozzáférésnek való megfelelésre. Ez igaz, kivéve, `AcquireTokenSilentAsync` `PromptBehavior.Never` ha a kérelem egy vagy ```AcquireToken``` ebben az esetben az alkalmazásnak kell végrehajtania egy interaktív kérést, hogy a végfelhasználó a lehetőséget, hogy megfeleljenek a szabályzatnak.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Eset: Egyoldalas alkalmazás (SPA) az MSAL.js használatával

Ebben a forgatókönyvben végighaladunk az eseten, amikor egy egyoldalas alkalmazás (SPA) használatával MSAL.js feltételes hozzáféréssel védett webes API-t hív. Ez egy egyszerű architektúra, de van néhány árnyalatok, amelyeket figyelembe kell venni, amikor a fejlődő körül feltételes hozzáférés.

Az MSAL.js-ben van néhány olyan függvény, `acquireTokenSilent(...)` `acquireTokenPopup(…)`amely `acquireTokenRedirect(…)`jogkivonatokat szerez be: `loginPopup()`, , , és .

* `loginPopup()`Egy azonosító jogkivonatot szerez be egy interaktív bejelentkezési kérelemen keresztül, de nem kap hozzáférési jogkivonatokat semmilyen szolgáltatáshoz (beleértve a feltételes hozzáféréssel védett webes API-t is).
* `acquireTokenSilent(…)`ezután használható a hozzáférési jogkivonat csendes megszerzésére, ami azt jelenti, hogy semmilyen körülmények között nem jeleníti meg a felhasználói felületet.
* `acquireTokenPopup(…)`és `acquireTokenRedirect(…)` mindkettő interaktívan kérhet egy jogkivonatot egy erőforráshoz, ami azt jelenti, hogy mindig bejelentkezési felhasználói felületet jelenítenek meg.

Ha egy alkalmazásnak hozzáférési jogkivonatra van szüksége `acquireTokenSilent(…)`egy webes API hívásához, megkísérli a. Ha a jogkivonat-munkamenet lejárt, vagy meg kell felelnünk egy feltételes hozzáférési szabályzatnak, akkor a *acquireToken* függvény sikertelen lesz, és az alkalmazás a vagyat `acquireTokenPopup()` `acquireTokenRedirect()`használja.

![Egyoldalas alkalmazás MSAL-folyamatdiagramhasználatával](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Mutassunk be egy példát a feltételes hozzáférés forgatókönyvével. A végfelhasználó most landolt az oldalon, és nincs munkamenet. Hívást hajtunk végre, `loginPopup()` többtényezős hitelesítés nélkül kapunk egy azonosító jogkivonatot. Ezután a felhasználó megnyom egy gombot, amely megköveteli, hogy az alkalmazás adatokat kérjen egy webes API-ból. Az alkalmazás megpróbál `acquireTokenSilent()` hívást végrehajtani, de sikertelen, mivel a felhasználó még nem hajtott végre többtényezős hitelesítést, és meg kell felelnie a feltételes hozzáférési szabályzatnak.

Az Azure AD a következő HTTP-választ küldi vissza:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Az alkalmazásunknak `error=interaction_required`el kell kapnia a . Az alkalmazás ezután `acquireTokenPopup()` `acquireTokenRedirect()` használhatja vagy használhatja az ugyanazon az erőforráson. A felhasználó nak többtényezős hitelesítést kell végeznie. Miután a felhasználó befejezte a többtényezős hitelesítést, az alkalmazás friss hozzáférési jogkivonatot kap a kért erőforráshoz.

Ennek a forgatókönyvnek a kipróbálásához tekintse meg a [JS SPA A kódminta nevében](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Ez a kódminta a feltételes hozzáférés-házirendet és a JS SPA-nál korábban regisztrált webes API-t használja a forgatókönyv bemutatásához. Bemutatja, hogyan kell megfelelően kezelni a jogcímkihívás, és kap egy hozzáférési jogkivonatot, amely használható a webes API-t. Alternatív megoldásként, pénztár az általános [Angular.js kód minta](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) útmutatást egy angular SPA

## <a name="see-also"></a>Lásd még

* A képességekről a Feltételes hozzáférés az Azure Active Directoryban című témakörben olvashat [bővebben.](/azure/active-directory/conditional-access/overview)
* További Azure AD-kódminták, lásd: [minták.](sample-v2-code.md)
* Az MSAL SDK-król és a referenciadokumentáció eléréséről a [Microsoft hitelesítési könyvtár ának áttekintése](msal-overview.md)című témakörben olvashat bővebben.
* Ha többet szeretne megtudni a több-bérlős forgatókönyvekről, olvassa [el a Felhasználók bejelentkezése a több-bérlős minta használatával című témakört.](howto-convert-app-to-be-multi-tenant.md)
* További információ a [feltételes hozzáférésről és az IoT-alkalmazásokhoz való hozzáférés biztosításáról.](/azure/architecture/example-scenario/iot-aad/iot-aad)
