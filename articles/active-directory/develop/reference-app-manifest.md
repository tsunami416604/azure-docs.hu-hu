---
title: Az Azure Active Directory alkalmazásjegyzék ismertetése
description: Az Azure Active Directory alkalmazásjegyzék részletes lefedettsége, amely egy alkalmazás identitáskonfigurációját képviseli egy Azure AD-bérlőben, és az OAuth-engedélyezés, a hozzájárulási élmény és egyebek megkönnyítésére szolgál.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 9f2ed6ea8cc75e2ee72f15c14f3de7bb8bf8cef6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450881"
---
# <a name="azure-active-directory-app-manifest"></a>Az Azure Active Directory alkalmazás jegyzékfájlja

Az alkalmazásjegyzék tartalmazza egy alkalmazásobjektum összes attribútumának definícióját a Microsoft identity platformon. Az alkalmazásobjektum frissítésének mechanizmusaként is szolgál. Az alkalmazás entitásról és annak sémájáról a [Graph API Application entitás dokumentációjában](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)olvashat bővebben.

Az alkalmazások attribútumait az Azure Portalon vagy programozott módon konfigurálhatja a [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) vagy a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)használatával. Vannak azonban olyan esetek, ahol az alkalmazásjegyzéket kell szerkeszteni az alkalmazás attribútumának konfigurálásához. Ezek a forgatókönyvek a következőket biztosítják:

* Ha regisztrálta az alkalmazást Azure AD több-bérlős és személyes Microsoft-fiókként, nem módosíthatja a támogatott Microsoft-fiókokat a felhasználói felületen. Ehelyett az alkalmazásjegyzék-szerkesztőt kell használnia a támogatott fióktípus módosításához.
* Ha meg kell adnia az alkalmazás által támogatott engedélyeket és szerepköröket, módosítania kell az alkalmazásjegyzéket.

## <a name="configure-the-app-manifest"></a>Az alkalmazásjegyzék konfigurálása

Az alkalmazásjegyzék konfigurálása:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza ki az **Azure Active Directory** szolgáltatást.
1. Válassza **az Alkalmazásregisztrációk lehetőséget.**
1. Válassza ki a konfigurálni kívánt alkalmazást.
1. Az alkalmazás **Áttekintés** lapján válassza az **Alkalmazásjegyzék** szakaszt. Megnyílik egy webalapú jegyzékszerkesztő, amely lehetővé teszi a jegyzékfájl szerkesztését a portálon belül. Szükség esetén a **Letöltés** lehetőséget választva helyileg szerkesztheti a jegyzékfájlt, majd az Upload segítségével újra **alkalmazhatja** azt az alkalmazásra.

## <a name="manifest-reference"></a>Jegyzékfájl hivatkozása

Ez a szakasz az alkalmazásjegyzékben található attribútumokat ismerteti.

### <a name="id-attribute"></a>id attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| id | Sztring |

A címtárban lévő alkalmazás egyedi azonosítója. Ez az azonosító nem az az azonosító, amelyet az alkalmazás azonosítására használnak bármely protokolltranzakcióban. Az objektum címtárlekérdezésekben való hivatkozására szolgál.

Példa:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| accessTokenAcceptedVersion | Érvényteleníthető Int32 |

Megadja az erőforrás által várt hozzáférési jogkivonat-verziót. Ez a paraméter módosítja a hozzáférési jogkivonat kéréséhez használt végponttól vagy ügyféltől függetlenül előállított JWT verzióját és formátumát.

A használt végpontot ( 1.0 vagy 2.0) az ügyfél választja ki, és csak a id_tokens verzióját érinti. Az erőforrásokat explicit `accesstokenAcceptedVersion` módon kell konfigurálni a támogatott hozzáférési jogkivonat-formátum jelzésére.

A lehetséges `accesstokenAcceptedVersion` értékek: 1, 2 vagy null. Ha az érték null, ez a paraméter alapértelmezés szerint 1, amely megfelel a v1.0 végpont.

Ha `signInAudience` `AzureADandPersonalMicrosoftAccount`van , az `2`értéknek a nak kell lennie.

Példa:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| Kiegészítők | Gyűjtemény |

Olyan egyéni viselkedést határoz meg, amelyet egy fogyasztó szolgáltatás egy alkalmazás adott környezetben történő hívására használhat. A fájlfolyamok megjelenítésére alkalmas alkalmazások `addIns` például beállíthatják a "FileHandler" funkció tulajdonságát. Ez a paraméter lehetővé teszi, hogy az Olyan szolgáltatások, mint az Office 365, meghívja az alkalmazást egy olyan dokumentum kontextusában, amelyen a felhasználó dolgozik.

Példa:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| engedélyező Nyilvánosügyfél | Logikai |

Megadja a tartalék alkalmazás típusát. Az Azure AD alapértelmezés szerint a replyUrlsWithType alkalmazástípusból következtet. Vannak bizonyos forgatókönyvek, ahol az Azure AD nem tudja meghatározni az ügyfélalkalmazás típusát. Például az egyik ilyen forgatókönyv a [ROPC-folyamat,](https://tools.ietf.org/html/rfc6749#section-4.3) ahol a HTTP-kérelem URL-átirányítás nélkül történik). Ezekben az esetekben az Azure AD fogja értelmezni az alkalmazás típusát a tulajdonság értéke alapján. Ha ez az érték igaz, a tartalék alkalmazás típusa nyilvános ügyfélként van beállítva, például egy mobileszközön futó telepített alkalmazás. Az alapértelmezett érték hamis, ami azt jelenti, hogy a tartalék alkalmazás típusa bizalmas ügyfél, például webalkalmazás.

Példa:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>availableToOtherTenants attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| availableToOtherTenants | Logikai |

Állítsa igaz, ha az alkalmazás más bérlőkkel van megosztva; ellenkező esetben hamis.

> [!NOTE]
> Ez az attribútum csak az **alkalmazásregisztrációk (Örökölt)** élményben érhető el. Az `signInAudience` [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élményben.

### <a name="appid-attribute"></a>appId attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| appId | Sztring |

Az Azure AD által egy alkalmazáshoz rendelt alkalmazás egyedi azonosítóját adja meg.

Példa:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| appRoles alkalmazás | Gyűjtemény |

Az alkalmazás által deklarálható szerepkörök gyűjteményét adja meg. Ezek a szerepkörök hozzárendelhetők a felhasználókhoz, csoportokhoz vagy szolgáltatásnévi tagokhoz. További példákat és információkat az [Alkalmazásszerepkörök hozzáadása az alkalmazásban című témakörben talál, és fogadja őket a jogkivonatban.](howto-add-app-roles-in-azure-ad-apps.md)

Példa:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>displayName attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| displayName | Sztring |

Az alkalmazás megjelenítendő neve.

> [!NOTE]
> Ez az attribútum csak az **alkalmazásregisztrációk (Örökölt)** élményben érhető el. Az `name` [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élményben.

### <a name="errorurl-attribute"></a>errorUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| errorUrl | Sztring |

Támogatott.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
|groupMembershipClaims (csoporttagságjogcímek) | Sztring |

Konfigurálja `groups` a felhasználó vagy az OAuth 2.0 hozzáférési jogkivonatban kiadott jogcímet, amelyet az alkalmazás elvár. Az attribútum beállításához használja az alábbi érvényes karakterláncértékek egyikét:

- `"None"`
- `"SecurityGroup"`(biztonsági csoportok és Azure AD-szerepkörök esetén)
- `"All"`(ez lefogja az összes biztonsági csoportok, terjesztési csoportok és az Azure AD címtárszerepkörök, amelyek a bejelentkezett felhasználó tagja.

Példa:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>kezdőlap attribútuma

| Kulcs | Érték típusa |
| :--- | :--- |
| Honlap |Sztring |

Az alkalmazás honlapjának URL-címe.

> [!NOTE]
> Ez az attribútum csak az **alkalmazásregisztrációk (Örökölt)** élményben érhető el. Az `signInUrl` [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élményben.

### <a name="objectid-attribute"></a>objectId attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
|objectId | Sztring |

A címtárban lévő alkalmazás egyedi azonosítója.

Ez csak az **alkalmazásregisztrációk (Legacy)** élményben érhető el. Az `id` [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élményben.

Példa:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>optionalClaims attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| választható jogcímek | Sztring |

A biztonsági jogkivonat-szolgáltatás által adott alkalmazáshoz visszaadott opcionális jogcímek.

Jelenleg a személyes fiókokat és az Azure AD-t (az alkalmazásregisztrációs portálon keresztül regisztrált) támogató alkalmazások nem használhatnak választható jogcímeket. Azonban a v2.0-végpont használatával regisztrált alkalmazások csak az Azure AD-hez a manifesztben kért opcionális jogcímek. További információ: [Választható jogcímek](active-directory-optional-claims.md).

Példa:

```json
    "optionalClaims": null,
```



### <a name="identifieruris-attribute"></a>identifierUris attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| identifierUris | Karakterlánc-tömb |

Felhasználó által definiált URI(k), amelyek egyedileg azonosítják a webalkalmazást az Azure AD-bérlőn belül, vagy egy ellenőrzött egyéni tartományban, ha az alkalmazás több-bérlős.

Példa:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| információs url-címek | Sztring |

Megadja az alkalmazás szolgáltatási feltételeire és adatvédelmi nyilatkozatára mutató hivatkozásokat. A szolgáltatási feltételek és az adatvédelmi nyilatkozat a felhasználói hozzájárulási élményen keresztül jelennek meg a felhasználók számára. További információ: [Útmutató: Szolgáltatási feltételek hozzáadása és adatvédelmi nyilatkozat a regisztrált Azure AD-alkalmazásokhoz.](howto-add-terms-of-service-privacy-statement.md)

Példa:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| keyCredentials | Gyűjtemény |

Az alkalmazáshoz rendelt hitelesítő adatokra, karakterlánc-alapú megosztott titkos kulcsokra és X.509-tanúsítványokra mutató hivatkozásokat tartalmaz. Ezek a hitelesítő adatok a hozzáférési jogkivonatok kérésekor használatosak (amikor az alkalmazás inkább ügyfélként működik, mint erőforrás).

Példa:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| knownClientApplications | Karakterlánc-tömb |

A hozzájárulás kötegeléséhez használható, ha olyan megoldással rendelkezik, amely két részből áll: egy ügyfélalkalmazásból és egy egyéni webes API-alkalmazásból. Ha megadja az ügyfélalkalmazás alkalmazásazonosítóját ebben az értékben, a felhasználónak csak egyszer kell hozzájárulnia az ügyfélalkalmazáshoz. Az Azure AD tudni fogja, hogy az ügyfél beleegyezése implicit módon a webes API-hoz való hozzájárulás. Automatikusan üzembe foglalja a szolgáltatásnévugyanazt az ügyfél és a webes API-t egy időben. Mind az ügyfél, mind a web API-alkalmazás regisztrálva kell lennie ugyanabban a bérlőben.

Példa:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| logoUrl | Sztring |

Csak olyan írásvédett érték, amely a CDN URL-címére mutat a portálon feltöltött embléma elemre.

Példa:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| logoutUrl | Sztring |

Az alkalmazásból kijelentkező URL-cím.

Példa:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>name attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| név | Sztring |

Az alkalmazás megjelenítendő neve.

Példa:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2AllowImplicitFlow | Logikai |

Itt adható meg, hogy a webalkalmazás kérhet-e OAuth2.0 implicit folyamat-hozzáférési jogkivonatokat. Az alapértelmezett érték hamis. Ez a jelző böngészőalapú alkalmazásokhoz, például a JavaScript egyoldalas alkalmazásokhoz használatos. További információért `OAuth 2.0 implicit grant flow` írja be a tartalomjegyzékbe az implicit folyamattal kapcsolatos témaköröket.

Példa:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Logikai |

Itt adható meg, hogy a webalkalmazás kérhet-e OAuth2.0 implicit folyamatazonosító jogkivonatokat. Az alapértelmezett érték hamis. Ez a jelző böngészőalapú alkalmazásokhoz, például a JavaScript egyoldalas alkalmazásokhoz használatos.

Példa:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2Engedélyek | Gyűjtemény |

Az OAuth 2.0 engedélyható körök gyűjteményét adja meg, amelyeket a webes API (erőforrás) alkalmazás az ügyfélalkalmazások számára elérhetővé. Ezek az engedélyható körök a jóváhagyás során adhatók meg az ügyfélalkalmazásoknak.

Példa:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2RequiredPostResponse | Logikai |

Itt adható meg, hogy az OAuth 2.0-s tokenkérelmek részeként az Azure AD engedélyezi-e a POST-kérelmeket, szemben a GET-kérelmekkel. Az alapértelmezett érték hamis, amely azt adja meg, hogy csak a GET-kérelmek engedélyezettek legyenek.

Példa:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| parentalControlSettings (szülői felügyelet– Beállítások | Sztring |

- `countriesBlockedForMinors`meghatározza azokat az országokat, amelyekben az alkalmazás kiskorúak számára le van tiltva.
- `legalAgeGroupRule`meghatározza az alkalmazás felhasználóira vonatkozó korcsoportszabályt. `Allow`Beállítható , `RequireConsentForPrivacyServices`, `RequireConsentForMinors` `RequireConsentForKids`, `BlockMinors`vagy .  

Példa:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| passwordCredentials | Gyűjtemény |

Lásd a `keyCredentials` szálláshely leírását.

Példa:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| preAuthorizedApplications | Gyűjtemény |

Felsorolja az alkalmazásokat és a kért engedélyeket az implicit hozzájáruláshoz. Az alkalmazáshoz való hozzájáruláshoz rendszergazda szükséges. a preAuthorizedApplications nem követeli meg a felhasználótól, hogy hozzájáruljon a kért engedélyekhez. A preAuthorizedApplications listában felsorolt engedélyekhez nincs szükség a felhasználó beleegyezésére. A preAuthorizedApplications ben nem szereplő további kért engedélyek azonban felhasználói jóváhagyást igényelnek.

Példa:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>publicClient attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| nyilvános ügyfél | Logikai|

Itt adható meg, hogy az alkalmazás nyilvános ügyfél-e (például mobileszközön futó telepített alkalmazás). 

Ez a tulajdonság csak az **alkalmazásregisztrációk (Legacy)** felületén érhető el. Az `allowPublicClient` [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élményben.

### <a name="publisherdomain-attribute"></a>publisherDomain attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| publisherDomain | Sztring |

Az alkalmazás ellenőrzött közzétevői tartománya. Csak olvasható.

Példa:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>replyUrls attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| replyUrls | Sztringtömb |

Ez a többértékű tulajdonság tartalmazza a regisztrált redirect_uri értékek listáját, amelyeket az Azure AD elfogad célként a jogkivonatok visszaküldésekor.

Ez a tulajdonság csak az **alkalmazásregisztrációk (Legacy)** felületén érhető el. Az `replyUrlsWithType` [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élményben.

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| replyUrlsWithType | Gyűjtemény |

Ez a többértékű tulajdonság tartalmazza a regisztrált redirect_uri értékek listáját, amelyeket az Azure AD elfogad célként a jogkivonatok visszaküldésekor. Minden URI-értéknek tartalmaznia kell egy társított alkalmazástípus-értéket. A támogatott típusértékek a következők:

- `Web`
- `InstalledClient`

További információ: [replyUrl korlátozások és korlátozások](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

Példa:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| requiredResourceAccess | Gyűjtemény |

Dinamikus hozzájárulással `requiredResourceAccess` a rendszergazdai hozzájárulási és a felhasználói hozzájárulási élményt a statikus beleegyezést használó felhasználók számára hajtja. Ez a paraméter azonban nem hajt a felhasználói hozzájárulási élményt az általános esetben.

- `resourceAppId`annak az erőforrásnak az egyedi azonosítója, amelyhez az alkalmazásnak hozzáférésre van szüksége. Ennek az értéknek meg kell egyeznie a célerőforrás-alkalmazásban deklarált alkalmazásazonosítóval.
- `resourceAccess`Egy olyan tömb, amely felsorolja az OAuth2.0 engedélyhatóköröket és alkalmazásszerepköröket, amelyeket az alkalmazás a megadott erőforrásból igényel. A `id` megadott `type` erőforrások értékeit és értékeit tartalmazza.

Példa:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| samlMetadataUrl | Sztring |

Az alkalmazás SAML-metaadatainak URL-címe.

Példa:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| jelInUrl | Sztring |

Megadja az alkalmazás kezdőlapjának URL-címét.

Példa:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| signInAudience | Sztring |

Itt adható meg, hogy milyen Microsoft-fiókoktámogatottak az aktuális alkalmazáshoz. A támogatott értékek a következők:
- `AzureADMyOrg`- Microsoft-munkahelyi vagy iskolai fiókkal rendelkező felhasználók a szervezet Azure AD-bérlőjében (például egyetlen bérlő)
- `AzureADMultipleOrgs`- Bármely szervezet Azure AD-bérlőjében Microsoft-munkahelyi vagy iskolai fiókkal rendelkező felhasználók (például több-bérlős)
- `AzureADandPersonalMicrosoftAccount`- Személyes Microsoft-fiókkal, illetve munkahelyi vagy iskolai fiókkal rendelkező felhasználók bármely szervezet Azure AD-bérlőjében
- `PersonalMicrosoftAccount`- Személyes fiókok, amelyek az Xbox és a Skype szolgáltatásokba való bejelentkezéshez használatosak.

Példa:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>címkék attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| címkét | Karakterlánc-tömb  |

Egyéni karakterláncok, amelyek az alkalmazás kategorizálására és azonosítására használhatók.

Példa:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Gyakori problémák

### <a name="manifest-limits"></a>Manifesztani-korlátozások

Az alkalmazásjegyzék több attribútummal rendelkezik, amelyeket gyűjteménynek neveznek; például appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess és oauth2Permissions. A teljes alkalmazásjegyzéken belül a gyűjtemények összes bejegyzéseinek száma összesen 1200. Ha korábban 100 átirányítási URI-t adott meg az alkalmazásjegyzékben, akkor csak 1100 fennmaradó bejegyzés marad az összes többi gyűjteményben, amelyek együttesen alkotják a jegyzéket.

> [!NOTE]
> Abban az esetben, ha megpróbál hozzáadni több mint 1200 bejegyzést az alkalmazás jegyzékfájljában, akkor a következő hibaüzenet jelenhet **meg: "Nem sikerült frissíteni az xxxxxx alkalmazást. Hiba részletei: A jegyzékfájl mérete túllépte a korlátot. Csökkentse az értékek számát, és próbálkozzon újra a kéréssel."**

### <a name="unsupported-attributes"></a>Nem támogatott attribútumok

Az alkalmazásjegyzék az alapul szolgáló alkalmazásmodell sémáját jelöli az Azure AD-ben. Az alapul szolgáló séma fejlődésével a jegyzékszerkesztő időről időre frissül, hogy tükrözze az új sémát. Ennek eredményeképpen új attribútumok jelenhetnek meg az alkalmazásjegyzékben. Ritkán előfordulhat, hogy szintaktikai vagy szemantikai változás fordul elő a meglévő attribútumokban, vagy olyan attribútumot talál, amely korábban létezett, és már nem támogatott. Például új attribútumokat fog látni az [alkalmazásregisztrációkban,](https://go.microsoft.com/fwlink/?linkid=2083908)amelyek más néven ismertek az alkalmazásregisztrációk (Örökölt) élményben.

| Alkalmazásregisztrációk (örökölt)| Alkalmazásregisztrációk           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Ezeknek az attribútumoknak a leírását lásd a [jegyzékfájl hivatkozási](#manifest-reference) szakaszában.

Egy korábban letöltött jegyzékfeltöltéskor az alábbi hibák egyike jelenhet meg. Ez a hiba valószínűleg azért, mert a jegyzékszerkesztő most már támogatja a séma újabb verzióját, amely nem egyezik meg a feltölteni kívánt verzióval.

* "Nem sikerült frissíteni az xxxxxx alkalmazást. Hiba részletei: Érvénytelen objektumazonosító "undefined". []."
* "Nem sikerült frissíteni az xxxxxx alkalmazást. Hiba részletei: Egy vagy több megadott tulajdonságérték érvénytelen. []."
* "Nem sikerült frissíteni az xxxxxx alkalmazást. Hiba részletei: Nem szabad beállítani availableToOtherTenants ebben az api verzió frissítésre. []."
* "Nem sikerült frissíteni az xxxxxx alkalmazást. Hiba részletei: A "replyUrls" tulajdonság frissítése nem engedélyezett ehhez az alkalmazáshoz. Használja inkább a "replyUrlsWithType" tulajdonságot. []."
* "Nem sikerült frissíteni az xxxxxx alkalmazást. Hiba részletei: Típusnév nélküli érték található, és nem áll rendelkezésre várt típus. A modell megadásakor a hasznos adat minden értékének olyan típussal kell rendelkeznie, amely a tartalomban megadható, a hívó kifejezetten, vagy implicit módon a szülőértékből következtethet. []"

Ha a következő hibák valamelyikét látja, a következő műveleteket javasoljuk:

1. Az attribútumokat egyenként szerkesztheti a jegyzékszerkesztőben egy korábban letöltött jegyzék feltöltése helyett. A [jegyzékfájl hivatkozási](#manifest-reference) táblája segítségével megismerheti a régi és az új attribútumok szintaxisát és szemantikáját, így sikeresen szerkesztheti az önt érdeklő attribútumokat. 
1. Ha a munkafolyamat megköveteli, hogy mentse a jegyzékek a forrástárban későbbi használatra, javasoljuk, hogy a mentett jegyzékek a tárházban az egyik látható az **alkalmazás regisztrációs** élményt.

## <a name="next-steps"></a>További lépések

* Az alkalmazás és az egyszerű szolgáltatásobjektum(ok) közötti kapcsolatról további információt az [Azure AD alkalmazás- és egyszerű szolgáltatásobjektumai című témakörben](app-objects-and-service-principals.md)talál.
* Tekintse meg a [Microsoft identity platform fejlesztői szószedet](developer-glossary.md) definícióit néhány alapvető Microsoft identity platform fejlesztői koncepciók.

A következő megjegyzések szakaszban visszajelzést adhat, amely segít a tartalom finomításában és alakításában.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
