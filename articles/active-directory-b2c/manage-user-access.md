---
title: Felhasználói hozzáférés kezelése az Azure Active Directory B2C könyvtárában | Microsoft dokumentumok
description: Ismerje meg, hogyan azonosíthatja a kiskorúakat, gyűjtheti a születési dátumát és az ország-/régióadatokat, és hogyan kaphatja el a használati feltételeket az alkalmazásban az Azure AD B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f04a3fea3801f917a3ae4aced04ef3824d1cfa82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184519"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Felhasználói hozzáférés kezelése az Azure Active Directory B2C-ben

Ez a cikk ismerteti, hogyan kezelheti a felhasználói hozzáférést az alkalmazások hoz az Azure Active Directory B2C (Azure AD B2C) használatával. Az alkalmazás hozzáférés-kezelése a következőket tartalmazza:

- Kiskorúak azonosítása és az alkalmazáshoz való felhasználói hozzáférés szabályozása.
- A kiskorúak szülői beleegyezésének megkövetelése az alkalmazások használatához.
- Születési és ország-/régióadatok gyűjtése a felhasználóktól.
- Használati szerződés rögzítése és a hozzáférés kiforgatása.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kisebb hozzáférés vezérlése

Az alkalmazások és szervezetek dönthetnek úgy, hogy letiltják a kiskorúakat a nem erre a közönségre irányuló alkalmazások és szolgáltatások használatában. Másik lehetőségként az alkalmazások és szervezetek dönthetnek úgy, hogy elfogadják a kiskorúakat, és ezt követően kezelik a szülői beleegyezést, és az üzleti szabályok által meghatározott és a szabályozás által megengedett élményt nyújtanak a kiskorúak számára.

Ha egy felhasználó kiskorúként van azonosítva, az Azure AD B2C felhasználói folyamatát a következő három lehetőség egyikére állíthatja be:

- **Aláírt JWT id_token visszaküldése az alkalmazásnak:** A felhasználó regisztrálva van a címtárban, és egy jogkivonatot ad vissza az alkalmazásnak. Az alkalmazás ezután üzleti szabályok alkalmazásával folytatódik. Például az alkalmazás folytathatja a szülői beleegyezési folyamatot. A módszer használatához válassza ki, hogy megkapja az **ageGroup** és **consentProvidedForMinor** jogcímek az alkalmazásból.

- **Aláíratlan JSON-jogkivonat küldése az alkalmazásnak:** Az Azure AD B2C értesíti az alkalmazást, hogy a felhasználó kiskorú, és a felhasználó szülői beleegyezésének állapotát adja meg. Az alkalmazás ezután üzleti szabályok alkalmazásával folytatódik. A JSON-jogkivonat nem hajt végre sikeres hitelesítést az alkalmazással. Az alkalmazásnak fel kell dolgoznia a nem hitelesített felhasználót a JSON-jogkivonatban szereplő jogcímek nek megfelelően, amelyek tartalmazhatják a **nevet,** **az e-mailt,** **az ageGroup-ot**és a **consentProvidedForMinor-t.**

- **A felhasználó letiltása:** Ha egy felhasználó kiskorú, és a szülői hozzájárulás nem lett megadva, az Azure AD B2C értesítheti a felhasználót, hogy le vannak tiltva. Nincs jogkivonat kiadása, a hozzáférés le van tiltva, és a felhasználói fiók nem jön létre a regisztrációs út során. Az értesítés megvalósításához meg kell adnia egy megfelelő HTML/CSS tartalomlapot, hogy tájékoztassa a felhasználót és bemutassa a megfelelő beállításokat. Az új lajstromozási kérelemhez nincs szükség további intézkedésre.

## <a name="get-parental-consent"></a>Szülői beleegyezés beszerezni

Az alkalmazásszabályozástól függően előfordulhat, hogy a szülői beleegyezést egy felnőttként ellenőrzött felhasználónak kell megadnia. Az Azure AD B2C nem nyújt élményt az egyén életkorának ellenőrzéséhez, és lehetővé teszi egy ellenőrzött felnőtt számára, hogy szülői beleegyezést adjon egy kiskorúnak. Ezt a felhasználói élményt az alkalmazásnak vagy egy másik szolgáltatónak kell biztosítania.

A következő példa a szülői beleegyezés összegyűjtésére szolgáló felhasználói folyamat:

1. A [Microsoft Graph API-művelet](https://docs.microsoft.com/graph/use-the-api) azonosítja a felhasználót, mint egy kisebb, és visszaadja a felhasználói adatokat az alkalmazás egy aláíratlan JSON-jogkivonat formájában.

2. Az alkalmazás feldolgozza a JSON-jogkivonatot, és egy képernyőt jelenít meg a kiskorú számára, értesítve őket arról, hogy szülői hozzájárulás szükséges, és a szülő beleegyezését kéri online.

3. Az Azure AD B2C egy bejelentkezési utat jelenít meg, amelybe a felhasználó normál módon bejelentkezhet, és egy jogkivonatot ad ki az alkalmazásnak, amely a **legalAgeGroupClassification = "minorWithParentalConsent"** értékre van állítva. Az alkalmazás összegyűjti a szülő e-mail címét, és ellenőrzi, hogy a szülő felnőtt-e. Ehhez megbízható forrást használ, például egy nemzeti azonosító irodát, licencellenőrzést vagy hitelkártya-igazolást. Ha az ellenőrzés sikeres, az alkalmazás kéri a kiskorút, hogy jelentkezzen be az Azure AD B2C felhasználói folyamat használatával. Ha a hozzájárulás megtagadva (például ha **legalAgeGroupClassification = "minorWithoutParentalConsent"**), az Azure AD B2C egy JSON-jogkivonatot (nem bejelentkezést) ad vissza az alkalmazásnak a jóváhagyási folyamat újraindításához. A felhasználói folyamat ot tetszés szerint testre szabhatja, hogy egy kiskorú vagy egy felnőtt visszaszerezhesse a hozzáférést egy kiskorú fiókjához, ha regisztrációs kódot küld a kiskorú e-mail címére vagy a felnőtt e-mail címére.

4. Az alkalmazás lehetőséget biztosít a kiskorú számára a hozzájárulás visszavonására.

5. Amikor a kiskorú vagy a felnőtt visszavonja a beleegyezést, a Microsoft Graph API használható a **consentProvidedForMinor** **megtagadásának módosítására.** Másik lehetőségként az alkalmazás dönthet úgy, hogy töröl egy olyan kiskorút, akinek a beleegyezését visszavonták. A felhasználói folyamat ot igény szerint testre is szabhatja, hogy a hitelesített kiskorú (vagy a kiskorú fiókját használó szülő) visszavonhassa a hozzájárulást. Az Azure AD B2C rekordok **consentProvidedForMinor** **megtagadva.**

A legalAgeGroupClassification , **consentProvidedForMinor**és agegroup ( **legalAgeGroupClassification**, consentProvidedForMinor , agegroup – és **agegroup**– című témakörben [található](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Az egyéni attribútumokról további információt az Egyéni attribútumok használata a [fogyasztókkal kapcsolatos információk gyűjtésére című témakörben talál.](user-flow-custom-attributes.md) Ha a kiterjesztett attribútumokat a Microsoft Graph API használatával címezi, az attribútum hosszú verzióját kell használnia, például *a extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth:* *: 2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Születési dátum és ország-/régióadatok összegyűjtése

Az alkalmazások támaszkodhatnak az Azure AD B2C-re, hogy összegyűjtsék a születési dátum (DOB) és az ország/régió adatait az összes felhasználótól a regisztráció során. Ha ez az információ még nem létezik, az alkalmazás kérheti azt a felhasználótól a következő hitelesítési (bejelentkezési) út során. A felhasználók nem folytathatják a doktom és az ország/régió adatainak megadása nélkül. Az Azure AD B2C az adatok alapján határozza meg, hogy az adott személy/régió szabályozási szabványai szerint az adott személy kiskorúnak minősül-e.

Egy testre szabott felhasználói folyamat összegyűjtheti a DOB- és ország-/régióadatokat, és az Azure AD B2C-jogcímek átalakításával meghatározhatja az **ageGroup-ot,** és megőrizheti az eredményt (vagy közvetlenül megőrizheti a DOB és az ország/régió adatait) a címtárban.

A következő lépések az **ageGroup** kiszámításához használt logikát mutatják be a felhasználó születési dátumától számítva:

1. Próbálja megtalálni az országot a listában szereplő országkód szerint. Ha az ország nem található, visszakell lépnie az **Alapértelmezett**értékre.

2. Ha a **MinorConsent** csomópont jelen van az országelemben:

    a. Számítsa ki azt a dátumot, amikor a felhasználónak felnőttnek kell lennie. Ha például az aktuális dátum 2015. **MinorConsent**

    b. Hasonlítsa össze a minimális születési dátumot a tényleges születési dátummal. Ha a minimális születési dátum a felhasználó születési dátuma előtt van, a számítás korcsoportszámításként **a Kiskorú** értéket adja vissza.

3. Ha a **MinorNoConsentRequired** csomópont jelen van az országelemben, ismételje meg a 2a és a 2b lépéseket a **MinorNoConsentRequired**érték használatával. A 2b kimenete **minornoconsentrequired** értéket ad vissza, ha a minimális születési dátum a felhasználó születési dátuma előtt van.

4. Ha egyik számítás sem ad vissza igaz értéket, akkor a számítás **felnőtt**értéket ad vissza.

Ha egy alkalmazás más módszerekkel megbízhatóan gyűjtötte össze a DOB- vagy ország-/régióadatokat, az alkalmazás a Graph API segítségével frissítheti a felhasználói rekordot ezekkel az információkkal. Példa:

- Ha a felhasználóról ismert, hogy felnőtt, frissítse az **ageGroup** könyvtárattribútumot **felnőtt**értékkel.
- Ha egy felhasználóról ismert, hogy kiskorú, frissítse az **ageGroup** könyvtárattribútumot **Minor** értékkel, és adja meg a **consentProvidedForMinor**értéket.

A DOB-adatok gyűjtéséről az [Age gating használata az Azure AD B2C-ben című témakörben](basic-age-gating.md)talál további információt.

## <a name="capture-terms-of-use-agreement"></a>Használati feltételek rögzítési feltételei

Az alkalmazás fejlesztése során rendszerint rögzíti, hogy a felhasználók elfogadják a használati feltételeket az alkalmazásaikban, a felhasználói címtárban való részvétel nélkül, vagy csak kisebb mértékben. Lehetőség van azonban egy Azure AD B2C felhasználói folyamat használatára, hogy összegyűjtse a felhasználó által elfogadott használati feltételeket, korlátozza a hozzáférést, ha az elfogadás nem teljesül, és a használati feltételek jövőbeli módosításainak elfogadását a legutóbbi elfogadás dátuma és a a használati feltételek legújabb verzióját.

**A használati feltételek** között szerepelhet a "Hozzájárulás az adatok harmadik felekkel való megosztásához". A helyi előírásoktól és az üzleti szabályoktól függően összegyűjtheti, hogy a felhasználó elfogadja a két feltételt, vagy engedélyezheti, hogy a felhasználó elfogadja az egyik feltételt, és ne a másikat.

Az alábbi lépések a használati feltételek kezelésének módját ismertetik:

1. Jegyezze fel a felhasználási feltételek elfogadását és az elfogadás dátumát a Graph API és a kiterjesztett attribútumok használatával. Ehhez beépített és egyéni felhasználói folyamatok használatával is megteheti. Javasoljuk, hogy hozza létre és használja a **extension_termsOfUseConsentDateTime** és **extension_termsOfUseConsentVersion** attribútumokat.

2. Hozzon létre egy "Használati feltételek elfogadása" címkével ellátott kötelező jelölőnégyzetet, és rögzítse az eredményt a regisztráció során. Ehhez beépített és egyéni felhasználói folyamatok használatával is megteheti.

3. Az Azure AD B2C tárolja a használati feltételeket és a felhasználó elfogadását. A Graph API segítségével lekérdezheti bármely felhasználó állapotát a válasz rögzítéséhez használt bővítményattribútum (például a **termsOfUseTestUpdateDateTime)** olvasásával. Ehhez beépített és egyéni felhasználói folyamatok használatával is megteheti.

4. A frissített használati feltételek elfogadásának megkövetelése az elfogadás dátumának a használati feltételek legújabb verziójának dátumával való összehasonlításával. A dátumokat csak egyéni felhasználói folyamat használatával hasonlíthatja össze. Használja a kiterjesztett **attribútumot extension_termsOfUseConsentDateTime**, és hasonlítsa össze az értéket a **termsOfUseTextUpdateDateTime**jogcímével. Ha az elfogadás régi, kényszerítse az új elfogadást egy saját érvényesítésű képernyő megjelenítésével. Ellenkező esetben tiltsa le a hozzáférést a házirendlogikával.

5. A frissített használati feltételek elfogadásának megkövetelése az elfogadás verziószámának a legújabb elfogadott verziószámmal való összehasonlításával. A verziószámokat csak egyéni felhasználói folyamat használatával lehet összehasonlítani. Használja a kiterjesztett attribútumot **extension_termsOfUseConsentDateTime**, és hasonlítsa össze az értéket a **extension_termsOfUseConsentVersion**jogcímével . Ha az elfogadás régi, kényszerítse az új elfogadást egy saját érvényesítésű képernyő megjelenítésével. Ellenkező esetben tiltsa le a hozzáférést a házirendlogikával.

A használati feltételek elfogadását a következő esetekben rögzítheti:

- Új felhasználó regisztrál. Megjelennek a használati feltételek, és az elfogadási eredmény tárolásra kerül.
- Egy felhasználó olyan bejelentkezéssel jelentkezik be, aki korábban elfogadta a legújabb vagy aktív használati feltételeket. A használati feltételek nem jelennek meg.
- Egy olyan felhasználó jelentkezik be, aki még nem fogadta el a legújabb vagy aktív használati feltételeket. Megjelennek a használati feltételek, és az elfogadási eredmény tárolásra kerül.
- Egy felhasználó bejelentkezik, aki már elfogadta a használati feltételek régebbi verzióját, amely most a legújabb verzióra frissül. Megjelennek a használati feltételek, és az elfogadási eredmény tárolásra kerül.

Az alábbi képen látható az ajánlott felhasználói folyamat:

![Folyamatábra-diagram az ajánlott elfogadási felhasználói folyamatot ábrázoló ábrán](./media/manage-user-access/user-flow.png)

A következő példa egy DateTime alapú használati feltétel hozzájárulása a reklamációban:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

A következő példa egy verzióalapú használati feltétel hozzájárulása a jogcímben:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>További lépések

- A felhasználói adatok törléséről és exportálásáról a [Felhasználói adatok kezelése (Manage user data) (Felhasználói adatok kezelése) témakörben](manage-user-data.md)olvashat.
- Például egyéni házirend, amely megvalósítja a használati feltételek et, lásd: [A B2C IEF egyéni házirend - Regisztráció és bejelentkezés a "Használati feltételek" üzenet.](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou)
