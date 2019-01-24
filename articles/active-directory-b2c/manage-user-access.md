---
title: Az Azure Active Directory B2C felhasználói hozzáférés felügyelete |} A Microsoft Docs
description: Ismerje meg, hogyan kiskorúak azonosítása, dátum születési idő és az országot adatok gyűjtése és használati feltételek elfogadása beszerzése az alkalmazás Azure AD B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 982587fa7da41ea1de5fd11bb054f87039596da1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852031"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C felhasználói hozzáférés felügyelete

Ez a cikk ismerteti az alkalmazásokhoz való felhasználói hozzáférés felügyelete Azure Active Directory (Azure AD) B2C használatával. Az alkalmazás hozzáférés-kezelés a következőket tartalmazza:

- Kiskorúak azonosítása, és az alkalmazásokhoz való felhasználói hozzáférés szabályozása.
- Szülői hozzájárulást kiskorúaknak ahhoz, hogy az alkalmazásokat.
- Születési idő és az országot adatokat gyűjt a felhasználók.
- A használati feltételek szerződés rögzítése és változástábla hozzáférésének korlátozásához.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kisebb hozzáférés-vezérlése

Alkalmazások és a szervezetek dönthetnek arról, hogy letiltja az alkalmazások és szolgáltatások, a célközönség nem szánt kiskorúak. Azt is megteheti alkalmazások és a szervezetek dönthetnek úgy kiskorúak fogadja el, és ezt követően a szülői beleegyezési kezelése és megengedett élményeket nyújthat kiskorúaknak üzleti szabályok által, és engedélyezi a rendelet. 

Ha egy felhasználó egy kisebb azonosítottak, állíthatja be a felhasználói folyamatot az Azure AD B2C-vel három lehetőség közül:

- **Egy aláírt JWT id_token küldése az alkalmazásnak**: A felhasználónak regisztrálva van a címtárban, és a egy jogkivonatot adott vissza az alkalmazásba. Az alkalmazás ezután folytatja az üzleti szabályok alkalmazásával. Például az alkalmazás folytathatja a szülői beleegyezési folyamat. Fogadásához válassza ezt a módszert használja, a **korcsoport** és **consentProvidedForMinor** jogcímek az alkalmazásból.

- **Az előjel nélküli JSON-jogkivonat küldhet az alkalmazáshoz**: Az Azure AD B2C az alkalmazás értesítést küld az, hogy a felhasználó egy kisebb, és biztosítja a szülői jóváhagyás a felhasználó állapotát. Az alkalmazás ezután folytatja az üzleti szabályok alkalmazásával. A JSON-jogkivonat nem fejeződik be az alkalmazás a sikeres hitelesítés. Az alkalmazás fel kell dolgoznia a jogcím szerepel a jogkivonatban JSON, amely szerint a nem hitelesített felhasználó **neve**, **e-mail**, **korcsoport**, és **consentProvidedForMinor**.

- **Felhasználó letiltása**: Ha egy felhasználó egy kisebb, és nem lett megadva a szülői hozzájárulást kér, az Azure AD B2C-vel is értesíti a felhasználót, hogy ő blokkolva van. Nem jogkivonat kiadott, hozzáférés le lesz tiltva, és a felhasználói fiók nem jön létre egy regisztrációs utazás közben. Alkalmazza ezt az értesítést, adjon meg egy megfelelő HTML/CSS tartalomlap tájékoztatja a felhasználó és a jelen a megfelelő beállításokat. Az alkalmazás új regisztrációs semmilyen további műveletet van szüksége.

## <a name="get-parental-consent"></a>Szülői beleegyezési beolvasása

Szülői beleegyezési alkalmazás rendelet, függően szükség lehet a felnőtt annak ellenőrzése, felhasználó által nyújtott. Az Azure AD B2C nem biztosít egy személy életkora ellenőrzése és a egy ellenőrzött felnőtt kiskorú szülői beleegyezési biztosítani, majd engedélyezése. Ez a tapasztalat kell adni az alkalmazás vagy egy másik szolgáltató által.

A következő egy felhasználói folyamat szülői beleegyezési ötvözve egy példát:

1. Egy [Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog) művelet a felhasználó egy kisebb azonosítja, és a felhasználói adatokat ad vissza az alkalmazás egy előjel nélküli JSON-jogkivonat formájában.

2. Az alkalmazás dolgozza fel a JSON-jogkivonatot, és a képernyő látható, a kisebb értesíteni az őt szülői hozzájárulásra szükség, és a hozzájárulás kérése egy szülő online. 

3. Az Azure AD B2C mutatja be az út, hogy a felhasználó bejelentkezhet általában és kiad egy jogkivonatot, amely tartalmazza az alkalmazáshoz **legalAgeGroupClassification = "minorWithParentalConsent"**. Az alkalmazás gyűjti a szülő e-mail-címét, és ellenőrzi, hogy a szülő felnőtt. Ehhez egy megbízható forrásból, például egy nemzeti azonosító office, licencellenőrzési vagy megvalósíthatósági példában hitelkártya használ. Ha az ellenőrzés sikeres, az alkalmazás kéri, jelentkezzen be az Azure AD B2C felhasználói folyamat használatával a kisebb. Ha a rendszer megtagadja a hozzájárulási (például, ha **legalAgeGroupClassification = "minorWithoutParentalConsent"**), Azure AD B2C visszaadja egy JSON-token (nem a bejelentkezési) az alkalmazást, hogy indítsa újra a jóváhagyási folyamatot. Úgy is igény szerint testre szabhatja a felhasználói folyamatot, hogy egy kisebb vagy a felnőtt újra hozzáférést kaphatnak egy kisebb fiók regisztrációs kód küld a másodlagos e-mail cím vagy a felnőtt e-mail-címét a rekordot.

4. Az alkalmazás egy lehetőséget kínál, a kisebb beleegyezése visszavonásához.

5. A kisebb vagy a felnőtt visszavonja a hozzájárulását, amikor az Azure AD Graph API segítségével módosíthatja **consentProvidedForMinor** való **megtagadva**. Az alkalmazás választhatja azt is megteheti, törölni egy kisebb, amelynek hozzájárulási byl odvolán. Úgy is igény szerint testre szabhatja a felhasználói folyamatot, hogy a hitelesített (vagy szülő által használt fiók a kisebb) kisebb vissza tudja vonni hozzájárulását. Az Azure AD B2C-rekordok **consentProvidedForMinor** , **megtagadva**.

További információ **legalAgeGroupClassification**, **consentProvidedForMinor**, és **korcsoport**, lásd: [felhasználói erőforrás típusa](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Egyéni attribútumok kapcsolatos további információkért lásd: [egyéni attribútumok használata a felhasználók adatainak gyűjtésére](active-directory-b2c-reference-custom-attr.md). Kiterjesztett attribútumok oldja meg az Azure AD Graph API-val, amikor például kell használnia az attribútum a hosszú verzió *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-country-data"></a>Gyűjtse össze a születési idő és az ország-adatok dátuma

Alkalmazások az Azure AD B2C-vel a születési dátum (DOB) és ország információkat gyűjtsön a minden felhasználó regisztrációja során hivatkozhatnak. Ha ezek az információk még nem létezik, az alkalmazás kérheti a felhasználótól a következő hitelesítési (bejelentkezik) utazás közben. Felhasználók saját DOB megadása nélkül nem lehet folytatni és ország információkat. Az Azure AD B2C az információk segítségével határozza meg, hogy az egyes van-e a szabályozási normák terén, az adott ország szerint kiskorúnak számít. 

Testre szabott felhasználói folyamat DOB gyűjthet és ország információkat, és használja az Azure AD B2C-vel jogcím-e meghatározásához átalakítás a **korcsoport** és megőrzéséhez az eredmény (vagy megőrizni az DOB és ország adatokat közvetlenül) a címtárban.

A következő lépések bemutatják, hogy a rendszer kiszámítja a logika **korcsoport** , a felhasználó születési dátuma:

1. Próbálja meg megkeresni az ország szerint az országkódot a listában. Ha nem található az ország, visszatér **alapértelmezett**.

2. Ha a **MinorConsent** csomópont szerepel az ország elem:

    a. A dátum, amelyen a felhasználó kell születtek figyelembe kell venni a felnőtt kiszámítása. Például, ha az aktuális dátum későbbi, 2015. március 14 és **MinorConsent** 18-ra, a születési dátum nem később mint 2000 március 14 kell lennie.

    b. Hasonlítsa össze a tényleges születési dátumot a minimális születési dátumát. A minimális születési dátum-e a felhasználó születési dátum előtt, a számítás adja vissza **kisebb** korcsoport számítását.

3. Ha a **MinorNoConsentRequired** csomópont szerepel az ország elem, a 2a. Ismételje meg a lépéseket és a 2b származó értékkel **MinorNoConsentRequired**. 2b kimenete adja vissza **MinorNoConsentRequired** Ha a minimális születési dátum a felhasználó születési dátum előtt. 

4. Ha sem számítási igaz értéket ad vissza, a számítás eredménye **felnőtt**.

Ha egy alkalmazás megbízhatóan összegyűjtött DOB vagy más módszerekkel országos adatok, az alkalmazás használatával a Graph API a felhasználói rekord frissítse ezt az információt. Példa:

- Ha egy felhasználó felnőtt életkorhoz ismert, frissítse a directory-attribútum **korcsoport** értékkel **felnőtt**.
- Ha egy felhasználó ismert, hogy egy kisebb, frissítse a directory-attribútum **korcsoport** értékkel **kisebb** és **consentProvidedForMinor**, ha szükséges.

Adatgyűjtés DOB kapcsolatos további információkért lásd: [használja a korhatáralapú hozzáférés az Azure AD B2C-vel](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Használati feltételek rögzítése

Az alkalmazás fejlesztésekor parancsmagokéval rögzítése nem rendelkező alkalmazásokba használati feltételek felhasználói elfogadását, vagy csak kisebb való részvétel a felhasználói könyvtárból. Lehetőség azonban egy Azure AD B2C felhasználói folyamat gyűjtse össze a használati feltételek elfogadása a felhasználó, ha elfogadási nincs engedélyezve a hozzáférés korlátozása, és a használati feltételeket, a legújabb elfogadása és a dátumot, az a dátum alapján jövőbeli módosításai elfogadása kényszerítése a  a használati feltételek legújabb verzióját.

**Használati feltételek** is tartalmazhat "Beleegyezik abba, hogy adatokat megoszthatja harmadik féllel." Helyi szabályozásokról és az üzleti szabályok függően gyűjthet mindkét kombinált feltételek elfogadása a felhasználó, vagy engedélyezheti a felhasználót, hogy egy feltétel, a másik nem fogadja el.

Az alábbi lépések bemutatják, hogyan kezelheti a használati feltételek:

1. A Graph API-t és a kiterjesztett attribútumok segítségével történő rögzítéséhez a rájuk a használati feltételeket és a elfogadásának dátumát. Ezt is a beépített és egyéni felhasználói folyamatok használatával teheti meg. Javasoljuk, hogy hozzon létre, és használja a **extension_termsOfUseConsentDateTime** és **extension_termsOfUseConsentVersion** attribútumok.

2. Hozzon létre egy szükséges "Fogadja el felhasználási feltételek" feliratú jelölőnégyzetet, és jegyezze fel az eredmény a regisztráció során. Ezt is a beépített és egyéni felhasználói folyamatok használatával teheti meg.

3. Az Azure AD B2C tárolja a használati használata szerződés és a felhasználói elfogadását. Használhatja a Graph API lekérdezéséhez bármely felhasználó állapotának a mellék attribútum, amellyel a válasz rögzítése olvassa el (például Olvasás **termsOfUseTestUpdateDateTime**). Ezt is a beépített és egyéni felhasználói folyamatok használatával teheti meg.

4. Frissített használati feltételek elfogadása szükséges a használati feltételek legújabb verzióját Date elfogadásától összehasonlításával. Csak egyéni felhasználói folyamat segítségével összehasonlíthatja a dátumok. A kiterjesztett attribútum használata a **extension_termsOfUseConsentDateTime**, és hasonlítsa össze a jogcímek, az érték **termsOfUseTextUpdateDateTime**. Ha az elfogadás régi, kényszerítheti egy új elfogadási önellenőrzött képernyő megjelenítésével. Ellenkező esetben a házirend logikája blokkolni a hozzáférést.

5. Frissített használati feltételek elfogadása szükséges össze a legutóbb elfogadott verziószámot, elfogadásának verziószáma. Csak egyéni felhasználói folyamat segítségével összehasonlíthatja a verziószámok. A kiterjesztett attribútum használata a **extension_termsOfUseConsentDateTime**, és hasonlítsa össze a jogcímek, az érték **extension_termsOfUseConsentVersion**. Ha az elfogadás régi, kényszerítheti egy új elfogadási önellenőrzött képernyő megjelenítésével. Ellenkező esetben a házirend logikája blokkolni a hozzáférést.

Rögzítheti a feltételek használata elfogadási alatt a következő esetekben:

- Új felhasználó jelentkezik. A használati feltételek jelennek meg, és az elfogadási eredmény tárolja.
- Egy felhasználó jelentkezik be, akik korábban elfogadta a legújabb vagy aktív használati feltételeket. A használati feltételek nem jelennek meg.
- A felhasználó bejelentkezik ki nem már fogadta el a legújabb vagy aktív használati feltételeket. A használati feltételek jelennek meg, és az elfogadási eredmény tárolja.
- A felhasználó bejelentkezik már elfogadta, akik egy régebbi verzióját a használati feltételeket, amelyek most frissülnek, hogy a legújabb verzióra. A használati feltételek jelennek meg, és az elfogadási eredmény tárolja.

Az alábbi képen látható a javasolt felhasználói folyamatot:

![Felhasználói folyamat elfogadása](./media/manage-user-access/user-flow.png) 

Az alábbiakban látható egy példa egy dátum és idő alapján használati feltételekhez tartozó jóváhagyások egy jogcím:

```
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

Az alábbiakban látható egy példa egy verziót, attól függően használati feltételekhez tartozó jóváhagyások a jogcím:

```
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

- Törölje, és a felhasználói adatok exportálása kapcsolatban lásd: [felhasználói adatokat](manage-user-data.md).
