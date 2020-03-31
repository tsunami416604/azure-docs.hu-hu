---
title: REST API-jogcímek cseréje a B2C egyéni házirendben
titleSuffix: Azure AD B2C
description: Bevezetés egy Azure AD B2C felhasználói út létrehozásához, amely a RESTful szolgáltatásokkal kommunikál.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337414"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Rest API-jogcímek cseréjének integrálása az Azure AD B2C egyéni szabályzatába

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) alapjául épülő identitáskezelési keretrendszer egy felhasználói úton integrálható a RESTful API-kkal. Ez a cikk bemutatja, hogyan hozhat létre olyan felhasználói utazást , amely egy [RESTful technikai profil](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)használatával kommunikál egy RESTful szolgáltatással .

Az Azure AD B2C használatával hozzáadhatja saját üzleti logikáját egy felhasználói úthoz a saját RESTful szolgáltatás ának felhívásával. Az identitáskezelési keretrendszer adatokat küldhet és fogadhat a RESTful szolgáltatásból a jogcímek cseréje céljából. Megteheti például a következőt:

- **A felhasználói beviteli adatok ellenőrzése**. Ellenőrizheti például, hogy a felhasználó által megadott e-mail-cím szerepel-e az ügyfél adatbázisában, és ha nem, akkor hibát jelezhet.
- **A jogcímek feldolgozása**. Ha a felhasználó megírja a keresztnevét az összes kis- vagy az összes nagybetűs, a REST API formázhatja a nevet csak az első betű nagybetűvel, és visszaadja az Azure AD B2C.
- **A felhasználói adatok további integrálásával gazdagíthatja a vállalati üzletági alkalmazásokkal való integrációt.** A RESTful szolgáltatás kaphatja meg a felhasználó e-mail címét, lekérdezheti az ügyfél adatbázisát, és visszaadhatja a felhasználó hűségszámát az Azure AD B2C-nek. Ezután a visszatérési jogcímek tárolhatók a felhasználó Azure AD-fiókjában, kiértékelhető a következő vezénylési lépésekben, vagy a hozzáférési jogkivonat tartalmazza.
- **Egyéni üzleti logika futtatása**. Leküldéses értesítéseket küldhet, frissítheti a vállalati adatbázisokat, futtathat egy felhasználói áttelepítési folyamatot, kezelheti az engedélyeket, naplózhatja az adatbázisokat, és bármilyen más munkafolyamatot végrehajthat.

![ReSTful szolgáltatási kárrendezési csere diagramja](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>RESTful szolgáltatás hívása

Az interakció magában foglalja a REST API-jogcímek és az Azure AD B2C közötti jogcímek közötti jogcímek cseréjét. A RESTful szolgáltatásokkal való integrációt a következő módokon tervezheti meg:

- **Érvényesítéstechnikai profil**. A RESTful szolgáltatásra történő hívás a [megadott, saját érvényesítésű műszaki profil](self-asserted-technical-profile.md) [érvényesítési technikai profilján](validation-technical-profile.md) vagy a kijelzővezérlés [ellenőrző kijelző-vezérlésén](display-control-verification.md) belül történik . [display control](display-controls.md) Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, mielőtt a felhasználói út halad előre. Az érvényesítési technikai profillal a következőket teheti:

  - Jogcímek küldése a REST API-ba.
  - Ellenőrizze a jogcímeket, és jelenítsen meg egyéni hibaüzeneteket, amelyek megjelennek a felhasználó számára.
  - Jogcímek visszaküldése a REST API-ból a következő vezénylési lépésekre.

- **Követelések cseréje**. A közvetlen jogcímek cseréje úgy konfigurálható, hogy a REST API technikai profilját közvetlenül a felhasználói út vezénylési lépéséről hívja [meg.](userjourneys.md) Ez a meghatározás a következőkre korlátozódik:

  - Jogcímek küldése a REST API-ba.
  - Ellenőrizze a jogcímeket, és egyéni hibaüzeneteket küldaz alkalmazásnak.
  - Jogcímek visszaküldése a REST API-ból a következő vezénylési lépésekre.

Rest API-hívást adhat hozzá az egyéni szabályzat által meghatározott felhasználói út bármely lépésében. Meghívhat például egy REST API-t:

- Bejelentkezés közben, közvetlenül az Azure AD B2C érvényesíti a hitelesítő adatokat.
- Közvetlenül a bejelentkezés után.
- Mielőtt az Azure AD B2C új fiókot hoz létre a címtárban.
- Miután az Azure AD B2C új fiókot hoz létre a címtárban.
- Mielőtt az Azure AD B2C kiad egy hozzáférési jogkivonatot.

![Érvényesítéstechnikai profilgyűjtemény](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Adatok küldése

A [RESTful technikai profilban](restful-technical-profile.md)az elem tartalmazza a `InputClaims` RESTful szolgáltatásnak küldő jogcímek listáját. A jogcím nevét leképezheti a RESTful szolgáltatásban definiált névre, alapértelmezett értéket állíthat be, és [jogcímfeloldókat](claim-resolver-overview.md)használhat.

A SendClaimsIn attribútum használatával konfigurálhatja, hogy a bemeneti jogcímek hogyan kerüljön elküldésre a RESTful jogcímek szolgáltatójának. Lehetséges értékek:

- **Törzs**, a HTTP POST kéréstörzsben JSON formátumban elküldve.
- **form**, amelyet a HTTP POST kéréstörzsében egy "&" külön kulcsérték-formátumban küldenek.
- **Fejléc**, amelyet a HTTP GET kérelem fejlécében küld.
- **QueryString**, a HTTP GET kérés lekérdezési karakterláncában elküldve.

Ha a **Törzs** beállítás konfigurálva van, a REST API technikai profil lehetővé teszi, hogy egy összetett JSON-tartalom küldése egy végpontra. További információ: [JSON-hasznos adat küldése.](restful-technical-profile.md#send-a-json-payload)

## <a name="receiving-data"></a>Adatok fogadása

A `OutputClaims` [RESTful technikai profil](restful-technical-profile.md) eleme a REST API által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy le kell képeznie a házirendben definiált jogcím nevét a REST API-ban definiált névhez. A REST API-identitásszolgáltató által vissza nem adott jogcímeket is megadhat, feltéve, hogy beállítja a DefaultValue attribútumot.

A RESTful jogcímszolgáltató által elemzött kimeneti jogcímek mindig elvárják, hogy egy lapos JSON-törzs választ elemez, például:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

A kimeneti jogcímeknek a következőkre kell hasonlítaniuk:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Beágyazott JSON-törzsválasz elemzéséhez állítsa a ResolveJsonPathsInJsonTokens metaadatait true értékre. A kimeneti jogcímben állítsa a PartnerClaimType-ot a kimenetre kívánt JSON elérési út elemre.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


A kimeneti jogcímeknek a következőkre kell kinézniük:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Biztonsági szempontok

A REST API-végpontot védeni kell, hogy csak a hitelesített ügyfelek kommunikálhassanak vele. A REST API-nak HTTPS-végpontot kell használnia. Állítsa az AuthenticationType metaadatait az alábbi hitelesítési módszerek egyikére:

- **Az ügyféltanúsítvány** az ügyféltanúsítvány-hitelesítés használatával korlátozza a hozzáférést. Csak a megfelelő tanúsítványokkal rendelkező szolgáltatások férhetnek hozzá az API-hoz. Az ügyféltanúsítványt egy Azure AD B2C-házirendkulcsban tárolja. További információ a RRÓL, hogyan [biztosíthatja a RESTful szolgáltatást ügyféltanúsítványok használatával.](secure-rest-api.md#https-client-certificate-authentication)
- **Az alapszintű** biztosítja a REST API-t a HTTP alapszintű hitelesítéssel. Csak ellenőrzött felhasználók, beleértve az Azure AD B2C, hozzáférhetnek az API-t. A felhasználónév és a jelszó az Azure AD B2C-házirendkulcsok tárolja. Ismerje meg, hogyan [biztosíthatja a RESTful szolgáltatásokat a HTTP alapfokú hitelesítés használatával.](secure-rest-api.md#http-basic-authentication)
- **A tulajdonos** korlátozza a hozzáférést egy ügyfél OAuth2 hozzáférési jogkivonathasználatával. A hozzáférési jogkivonat egy Azure AD B2C-szabályzatkulcstárolja. További információ a rról, hogyan [biztosíthatja a RESTful szolgáltatást a bemutatóra szóló token használatával.](secure-rest-api.md#oauth2-bearer-authentication)

## <a name="rest-api-platform"></a>REST API platform
A REST API bármely platformon alapulhat, és bármilyen programozási nyelven írható, feltéve, hogy biztonságos, és a [RESTful technikai profilban](restful-technical-profile.md)meghatározott jogcímeket küldhet és fogadhat.

## <a name="localize-the-rest-api"></a>A REST API honosítása
A RESTful technikai profilban érdemes lehet elküldeni az aktuális munkamenet nyelvét/területi beállítását, és szükség esetén felvetni egy honosított hibaüzenetet. A [jogcímfeloldó](claim-resolver-overview.md)használatával környezeti jogcímet küldhet, például a felhasználói nyelvet. A következő példa egy RESTful technikai profilt mutat be, amely ezt a forgatókönyvet mutatja be.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Hibaüzenetek kezelése

Előfordulhat, hogy a REST API-nak hibaüzenetet kell visszaadnia, például : "A felhasználó nem található a CRM rendszerben." Hiba esetén a REST API-nak HTTP 409-es hibaüzenetet (Ütközésválasz-állapotkód) kell visszaadnia. További információt a [RESTful technikai profiljában](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message)talál.

Ez csak úgy érhető el, ha egy REST API technikai profilt hív meg egy érvényesítési technikai profilból. Ez lehetővé teszi a felhasználó számára, hogy javítsa ki az adatokat az oldalon, és futtassa az érvényesítési újra az oldal benyújtását követően.

Http 409-es válasz szükséges a vezénylési lépésen belüli későbbi érvényesítési technikai profilok feldolgozásának megakadályozásához.

Ha egy REST API technikai profilra hivatkozik közvetlenül egy felhasználói útról, a felhasználó a megfelelő hibaüzenettel visszairányítja a függő entitás alkalmazáshoz.

## <a name="publishing-your-rest-api"></a>A REST API közzététele

A REST API-szolgáltatáshoz intézett kérelem az Azure AD B2C-kiszolgálókról származik. A REST API-szolgáltatást közzé kell tenni egy nyilvánosan elérhető HTTPS-végponton. A REST API-hívások egy Azure-adatközpont IP-címéről érkeznek.

Tervezze meg a REST API-szolgáltatást és annak alapjául szolgáló összetevőket (például az adatbázist és a fájlrendszert) magas rendelkezésre állásúnak.

## <a name="next-steps"></a>További lépések

A RESTful technikai profil használatára vonatkozó példákat az alábbi cikkekben talál:

- [Forgatókönyv: Rest API-jogcímek cseréjének integrálása az Azure AD B2C felhasználói útba a felhasználói bevitel érvényesítéseként](custom-policy-rest-api-claims-validation.md)
- [Forgatókönyv: REST API-jogcímek cseréje az Azure Active Directory B2C egyéni szabályzataihoz](custom-policy-rest-api-claims-validation.md)
- [A REST API-szolgáltatások biztonságossá tétele](secure-rest-api.md)
- [Hivatkozás: RESTful műszaki profil](restful-technical-profile.md)