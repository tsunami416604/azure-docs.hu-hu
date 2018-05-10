---
title: Felhasználói hozzáférés az Azure AD B2C kezelése |} Microsoft Docs
description: Megtudhatja, hogyan kiskorúak azonosítása, dátum születési és ország adatok gyűjtése és az alkalmazás az Azure AD B2C használatával beolvasása a használati feltételek elfogadását.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.openlocfilehash: c44135a3069966b14d8760e4daa009ab8d39ccca
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Felhasználói hozzáférés az Azure AD B2C kezelése

Ez a cikk bemutatja, hogyan kezelheti a felhasználói hozzáférés az Azure Active Directory (AD) B2C használatával alkalmazások. Az alkalmazás kezelési tartalmazza:

- Kiskorúak azonosítja, és az alkalmazás-hozzáférés szabályozásáról
- Az alkalmazások használatára kiskorúak megkövetelését beleegyezést
- A felhasználó a születési és ország adatgyűjtés
- Rögzítésével feltételeinek szerződés és változástábla hozzáférésének korlátozásához használja

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>Ez a cikk a GDPR a kötelezettségeit támogatásához használható információkat biztosít. Ha további általános információt GDPR van szüksége, tekintse meg a [GDPR szakasz a szolgáltatás megbízható portál](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="control-minor-access"></a>Kisebb elérés

Alkalmazások és a szervezetek dönthetnek úgy, hogy használja az alkalmazások és szolgáltatások a célközönség nem irányuló kiskorúak blokkolása. Azt is megteheti alkalmazások és a szervezetek dönthetnek úgy, hogy fogadja el a kiskorúak és ezt követően a szülői jóváhagyása kezelése, és biztosításához megengedett lép kiskorúak az üzleti szabályok által előírtaknak és szabályozás által engedélyezett. 

Ha a felhasználók egy kisebb azonosítottak, majd a felhasználói folyamat az Azure AD B2C lehet beállítani a három lehetőség közül:

- **Egy aláírt JWT id_token küldi vissza az alkalmazás** – a felhasználó regisztrálva van a könyvtárban, és egy jogkivonatot adott vissza az alkalmazásba. Az alkalmazás ezután folytatja az üzleti szabályok használata. Például az alkalmazás egy szülői jóváhagyása folyamat folytathatja. Ehhez úgy dönt, hogy fogadni a **ageGroup** és **consentProvidedForMinor** jogcímek az alkalmazásból.
- **Az aláírás nélküli JSON-tokent küldeni az alkalmazás** -az Azure AD B2C értesíti az alkalmazást, hogy a felhasználó egy kisebb és a felhasználó beleegyezését állapotának tartalmazza. Az alkalmazás ezután folytatja az üzleti szabályok használata. A JSON-jogkivonat nem fejeződik be az alkalmazás sikeres hitelesítés. Az alkalmazás kell feldolgozni a nem hitelesített felhasználó alapján a jogcímeket, a a JSON tokenben, amelyek magukban foglalhatják **neve**, **e-mail**, **ageGroup**, és **consentProvidedForMinor**.
- **A felhasználó** – Ha a felhasználó egy kisebb, és nincs megadva szülői jóváhagyása.  Az Azure AD B2C majd jelenthet, képernyőn a felhasználónak, amelyben arról tájékoztatja a blokkolja.  Nincs tokent ad ki, hozzáférés le van tiltva, és a felhasználói fiók nem jön létre a regisztrációs út. Ehhez adjon meg egy megfelelő HTML/CSS tartalomlapon tájékoztatja a felhasználói és a jelen megfelelő beállításokat. Az alkalmazás új regisztrációk semmilyen további műveletet van szükség.

## <a name="get-parental-consent"></a>Szülői jóváhagyása beolvasása

Attól függően, hogy az alkalmazás szabályozás beleegyezést lehet szükség, mint felnőtt ellenőrzi a felhasználó által engedélyezhető.  Az Azure AD B2C nem biztosít egy személy életkora ellenőrizze és majd egy kisebb szülői hozzájárulását ellenőrzött felnőtt élményt.  Ez a felület meg kell adni az alkalmazás vagy egy másik szolgáltató.

A következő egy példa egy felhasználói folyamat gyűjtésének szülői jóváhagyása:

1. Egy [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) művelet a felhasználó egy kisebb azonosítja, és a felhasználó adatait jeleníti meg az alkalmazás egy aláíratlan JSON-jogkivonatot formájában.
2. Az alkalmazás dolgozza fel a JSON-jogkivonatot, és látható, a képernyőn a kisebb, amely értesíti, hogy szülői jóváhagyása szükséges, és online szülő beleegyezését kéri. 
3. Az Azure AD B2C jeleníti meg a bejelentkezési használatában, amelyek használhatók a szokásos módon jelentkezhet be, és egy jogkivonatot állít ki az alkalmazás tartalmazza a beállított **legalAgeGroupClassification = "minorWithParentalConsent"** az alkalmazás gyűjti a e-mail cím a szülő és a rendszer ellenőrzi a szülő használja a megbízható forrás, például egy nemzeti azonosító office, licenc-ellenőrzési vagy igazoló hitelkártya felnőtt. Ha sikeres, az alkalmazás kéri a kisebb is bejelentkezhet az Azure AD B2C felhasználói folyamat. Ha a hozzájárulásukat adják meg lett tagadva (pl. **legalAgeGroupClassification = "minorWithoutParentalConsent"**, az Azure AD B2C az alkalmazásba hozzájárulás folyamat újraindításához a JSON-jogkivonat (nem a bejelentkezési) adja vissza. Is szükség lehet testre szabhatja a felhasználói folyamat, amellyel felnőttet vagy egy kisebb hozzáférést nyerhetnek a kisebb fiók regisztrációs kódja küld a másodlagos e-mail címet, vagy a felnőtt e-mail cím rekord.
4. Az alkalmazás egy lehetőséget kínál a beleegyezése visszavonásához kisebb.
5. A kisebb vagy a felnőtt visszavonja jóváhagyását, ha az Azure AD Graph API segítségével módosítható **consetProvidedForMinor** való **megtagadva**. Másik lehetőségként az alkalmazás dönthetnek úgy, hogy egy kisebb, amelynek hozzájárulási visszavonták törlése. Is szükség lehet a felhasználói folyamat, amelynek során a hitelesített kisebb (vagy a kisebb fiókkal szülő) vissza tudja vonni a hozzájárulási testreszabása. Az Azure AD B2C-rekordok **consentProvidedForMinor** , **megtagadva**.

További információ a **legalAgeGroupClassification**, **consentProvidedForMinor**, és **ageGroup**, lásd: [felhasználói erőforrástípus](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user). Az egyéni attribútumok kapcsolatos további információkért lásd: [egyéni attribútumok használata gyűjthet a felhasználókról](active-directory-b2c-reference-custom-attr.md). Amikor az Azure AD Graph API-jával kiterjesztett attribútumok, például a "extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth" attribútum hosszú verzióját kell használni: "2011-01-01T00:00:00Z"

## <a name="gather-date-of-birth-and-country-data"></a>Gyűjtse össze a születési idejét és ország adatok dátuma

Alkalmazások az Azure AD B2C az összes felhasználó regisztrálás során a születési idejét (DOB) és a ország gyűjtését hivatkozhatnak. Ha DOB vagy ország-információk még nem létezik, azt is kérni a felhasználó a következő hitelesítési (bejelentkezés) út során. Felhasználók nem fognak tudni DOB megadása nélkül folytatja és ország információkat. Az ország és a megadott DOB alapján, az Azure AD B2C határozza meg, ha az egyes számít-e az adott ország szabályozási előírások szerint egy kisebb. 

A testre szabott felhasználói folyamat DOB gyűjthet és ország információkat és használja az Azure AD B2C jogcím-e az átalakítás meghatározásához a **ageGroup** és megőrizni az eredmény (vagy az ország és DOB adatokat közvetlenül megőrzéséhez) a címtárban.

A következő lépések bemutatják az kiszámításához használt logikai **ageGroup** születési dátum:

1. Próbálja a ország talált az országkódot a listában. Ha nem található az ország, visszaállni **alapértelmezett**.
2. Ha a **MinorConsent** csomópont megtalálható-e az ország elem:  <br>a. A legkorábbi dátum, amely a felhasználó kellett volna figyelembe kell venni a felnőtt születtek kiszámításához. Példa: születési dátumát: 3/14/2015 és **MinorConsent** 18-ra, a minimális születési dátumát lenne 3/14/2000.
    <br>b. Hasonlítsa össze a tényleges születési dátumot minimális születési dátumát. Ha a minimális születési dátumát van, a felhasználó születési dátum előtt, a számítás vissza **kisebb** a kor csoport számítási.
3. Ha a **MinorNoConsentRequired** csomópont megtalálható-e az ország elem, a 2a. Ismételje meg a lépéseket és a 2b. az érték használatával **MinorNoConsentRequired**. 2b kimenete adja vissza **MinorNoConsentRequired** Ha minimális születési dátum előtt a felhasználó születési dátumát. 
4. Ha sem a számítások TRUE értéket adott vissza, a számítás vissza **felnőtt**.

Ha egy alkalmazás megbízhatóan összegyűjtött DOB vagy más módon országos adatok, az alkalmazás használhat a GRAPH API-t az információ a felhasználói rekordjának frissítése érdekében. Példa:

- Ha egy felhasználó ismert, hogy felnőtt, frissítse a directory attribútum **ageGroup** értékkel rendelkező **felnőtt**.
- Ha egy felhasználó ismert, hogy egy kisebb, frissítse a directory attribútum **ageGroup** értékkel rendelkező **kisebb** és **consentProvidedForMinor** szükség szerint.

DOB adatgyűjtést kapcsolatos további információkért lásd: [használata az Azure AD B2C átjáró kora](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Használati feltételek rögzítése

Amikor az alkalmazás fejlesztése, általában rögzítése belül az alkalmazások, amelyekre nem, a használati feltételek felhasználói elfogadását, vagy csak kisebb részvétel a felhasználói könyvtárból.  Lehetséges, azonban az Azure AD B2C használandó felhasználói flow gyűjtse össze a használati feltételek a szerződést, korlátozza a hozzáférést, kivéve, ha elfogadási kap, és elfogadását kényszerítéséhez érintő változásokról a használati feltételek alapján utolsó elfogadásától és a legújabb verziójában dátumának a használati feltételeket az n.

**Használati feltételek** tartalmazhat "Hozzájárul az adatok megosztása a harmadik felek számára."  Ezeket a feltételeket a felhasználó pozitív elfogadását előfordulhat, hogy technikailag összegyűjteni, kombinált, vagy lehet, hogy a felhasználó képes fogadni, egyet, és nem a másik attól függően, hogy helyi szabályozásainak megfelelően és az üzleti szabályok.

A használati feltételek kezelésére szolgáló képességek a következő lépésekből áll:

1. A használati feltételeket és a Graph API és a kiterjesztett attribútumok elfogadásától rekord elfogadását. Ehhez mindkét beépített és egyéni felhasználói adatfolyamok használatával. Javasoljuk, hogy hozzon létre, és használjon a **extension_termsOfUseConsentDateTime** és **extension_termsOfUseConsentVersion** attribútumok.
2. Hozzon létre egy "Elfogadja használati feltételek" című szükséges jelölőnégyzetet, és jegyezze fel az eredmény regisztráció során. Ehhez mindkét beépített és egyéni felhasználói adatfolyamok használatával.
3. Az Azure AD B2C tárolja a használati és beleegyezése feltételeit. A Graph API-val használható minden olyan felhasználó állapotának lekérdezése a mellék attribútum rögzítse a válaszban olvasásával, például olvassa el **termsOfUseTestUpdateDateTime**. Ehhez mindkét beépített és egyéni felhasználói adatfolyamok használatával.
4. A használati feltételek legújabb verzióját a Date elfogadásától összehasonlításával használata a frissített feltételek elfogadására van szükség. Ezt megteheti csak egy egyéni felhasználói folyamat használatával. A kiterjesztett attribútum használata a **extension_termsOfUseConsentDateTime** és a jogcímek, az értékének összevetése **termsOfUseTextUpdateDateTime**, ha az elfogadás régi, akkor egy új elfogadási kényszerítése Ellenkező esetben önálló magas képernyő, letiltja a hozzáférést házirend logikája használatával.
5. Használati frissített feltételek elfogadásának megkövetelése összehasonlítva az utolsó elfogadott verziószámot elfogadási verziószáma. Ezt megteheti csak egy egyéni felhasználói folyamat használatával. A kiterjesztett attribútum használata a **extension_termsOfUseConsentDateTime** és a jogcímek, az értékének összevetése **extension_termsOfUseConsentVersion**, ha az elfogadás régi, akkor egy új elfogadási kényszerítése Ellenkező esetben önálló magas képernyő, letiltja a hozzáférést házirend logikája használatával.

Használjon hozzájárulási feltételeinek rögzítése is jelennek meg a felhasználó csoportban a következő esetekben:

- Új felhasználó regisztrál. A használati feltételek megjelennek a hozzájárulási eredmény tárolódik.
- A felhasználó van jelentkezik be, és már korábban elfogadta a legújabb vagy aktív feltételeit a hozzájárulásukat adják. A használati feltételek nem jelennek meg.
- A felhasználó bejelentkezik van, és már nem fogadta el a használati feltételek legújabb vagy aktív kapcsolatos jóváhagyásról. A használati feltételek megjelennek a hozzájárulási eredmény tárolódik.
- A felhasználó akkor jelentkezik be, és már fogadták el egy régebbi használati feltételeket, amelyet most egy újabb verzióra frissít a hozzájárulásukat adják. A használati feltételek megjelennek a hozzájárulási eredmény tárolódik.

A következő kép bemutatja a javasolt felhasználói folyamat:

![elfogadó felhasználó folyamata](./media/manage-user-access/user-flow.png) 

A következő egy példa egy dátum és idő alapján a jogcímek használata hozzájárulási feltételeinek:

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

A következő egy példa egy verziót, attól függően feltételeit. a jogcímek használata hozzájárulási:

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

- Útmutató törlése és a felhasználói adatok exportálása [felhasználói adatok kezelése](manage-user-data.md)
