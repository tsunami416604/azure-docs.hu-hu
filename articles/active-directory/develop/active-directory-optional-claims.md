---
title: Útmutató az Azure AD alkalmazás választható jogcímeket biztosítson |} Microsoft Docs
description: Kézikönyv a SAML 2.0 és a JSON Web Tokens (JWT) tokenek Azure Active Directory által kiadott jogcímeket egyéni vagy további hozzáadásához.
documentationcenter: na
author: hpsin
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/15/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: f9cc4f900428e1337fc9b9d428879d6527c60017
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="optional-claims-in-azure-ad-preview"></a>Nem kötelező jogcímek, az Azure AD (előzetes verzió)

Ez a szolgáltatás alkalmazásfejlesztők adja meg, melyik jogcímeket a cég ehhez az alkalmazáshoz jogkivonatokba használt. A választható jogcímek használata:
-   Válassza ki ahhoz, hogy szerepeljen a jogkivonatok az alkalmazás további jogcímeket.
-   Módosíthatja az egyes jogcímek, az Azure AD visszaadó jogkivonatokba működését.
-   Adja hozzá, és egyéni jogcímekként az alkalmazás eléréséhez. 

> [!Note]
> Ez a funkció jelenleg nyilvános előzetes verziójához. Készüljön visszaállítja, vagy távolítsa el a módosításokat. A szolgáltatás bármely Azure AD-előfizetés nyilvános előzetes érhető el. Amikor a szolgáltatás általánosan elérhetővé válik, azonban a funkció az egyes funkcióit szükség lehet az Azure AD premium előfizetéssel.

Standard jogcímeket, és hogyan használhatók a jogkivonatok listájáért lásd: a [az Azure AD által kiállított jogkivonatokat alapjait](active-directory-token-and-claims.md). 

Egyik a célja, a [az Azure AD v2.0-végponttól](active-directory-appmodel-v2-overview.md) ügyfelek teljesítményének kisebb token méretű van.  Ennek eredményeképpen több jogcímek korábban és tartalmazza a hozzáférési azonosító-jogkivonatokat már nem szerepelnek a v2.0-jogkivonatok és -alkalmazásonként meg kell kérni a.  

**1. táblázat: alkalmazhatósági**

| Fiók típusa | Végpont 1.0-s verzió                      | V2.0-végponttól  |
|--------------|------------------------------------|----------------|
| MSA          | N/A - RPS jegyek használja helyette | Támogatás hamarosan |
| AAD          | Támogatott                          | Támogatott      |

## <a name="standard-optional-claims-set"></a>Standard választható jogcímek készletébe
A választható alkalmazások is használhatnak alapértelmezés szerint elérhető jogcímek készlete alább láthatók.  Az alkalmazás egyéni választható jogcímeket adhatnak hozzá, lásd: [Címtárbővítmények](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions), az alábbi. 

> [!Note]
>A legtöbb, ezeket a jogcímeket tartalmazhat JWTs, de nem SAML-jogkivonatokat, kivéve ahol ennek ellenezőjét jelöltük a jogkivonat típusa oszlopban.  Emellett opcionális jogcímek csak támogatottak az AAD-felhasználókat jelenleg, MSA támogatási adja hozzá.  Ha az msa-t támogatja a v2.0-végpontra a választható jogcímeket, a felhasználó oszlop lesz jelöl, ha egy jogcímet érhető el az AAD vagy az msa-t a felhasználók.  

**2. táblázat: Standard választható jogcímek készletéhez**

| Name (Név)                     | Leírás                                                                                                                                                                                     | A jogkivonat típusa | Felhasználó típusa | Megjegyzések                                                                                                                                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `auth_time`                | Amikor a felhasználó utoljára hitelesített idő.  Lásd: OpenID Connect specifikációi.                                                                                                                                | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_region_scope`      | Az erőforrás-bérlő régió                                                                                                                                                                   | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `signin_state`             | Jelentkezzen be a jogcím állapota                                                                                                                                                                             | JWT        |           | 6 visszatérési értékek, mint a jelzők:<br> "dvc_mngd": eszköz felügyelete<br> "dvc_cmp": eszköz esetében ez engedélyezett<br> "dvc_dmjd": eszköz a tartományhoz<br> "dvc_mngd_app": MDM segítségével felügyelt eszköz<br> "inknownntwk": eszköz egy ismert hálózaton belül van.<br> "kmsi": tartsa meg aláírt használták. <br> |
| `controls`                 | Típushoz jogcím, a feltételes hozzáférési házirendeket tartat munkamenet vezérlők tartalmazó.                                                                                                       | JWT        |           | 3 értéket:<br> "app_res": a részletes korlátozásokat kényszerítéséhez kell az alkalmazást. <br> "ca_enf": a feltételes hozzáférés kényszerítési a rendszer, és továbbra is szükséges. <br> "no_cookie": Ez a token nem elegendő a böngészőben a cookie-nak az exchange. <br>                              |
| `home_oid`                 | A vendégfelhasználók számára, a felhasználó otthoni bérlői felhasználói objektum azonosítója.                                                                                                                           | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `sid`                      | Munkamenet-azonosító, munkamenet-felhasználó signout használatos.                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `platf`                    | Eszközplatform                                                                                                                                                                                 | JWT        |           | Ellenőrizheti az eszköztípus felügyelt eszközökre korlátozza.                                                                                                                                                                                                                              |
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail forrása                                                                                                                                               | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `verified_secondary_email` | A felhasználó SecondaryAuthoritativeEmail forrása                                                                                                                                             | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `enfpolids`                | Érvényben lévő házirend azonosítók. A házirend az aktuális felhasználó értékelt azonosítók listáját.                                                                                                         | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `vnet`                     | Virtuális hálózat megadása adatokat.                                                                                                                                                                     | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `fwd`                      | IP-címet.  (Ha belül egy VNETET) kérő ügyfél eredeti IPv4-cím hozzáadása                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `ctry`                     | Felhasználó ország                                                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_ctry`              | Erőforrás-bérlő ország                                                                                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `upn`                      | UserPrincipalName claim.  Bár a jogcím automatikusan települ, adja meg azt a Vendég felhasználói esetben viselkedésének módosítása további tulajdonságok csatolni egy választható jogcímként. | JWT, SAML  |           | További tulajdonságok: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash`                                                                                                                                                                 |
### <a name="v20-optional-claims"></a>Nem kötelező jogcímek 2.0-s verzió
Ezeket a jogcímeket 1.0-s verziójú jogkivonatok mindig szerepelnek, de v2.0 jogkivonatok törlődnek, kivéve, ha a kért.  Ezeket a jogcímeket csak alkalmazhatók JWTs (azonosító-jogkivonatokat és hozzáférési jogkivonatok).  

**3. táblázat: Csak V2.0 választható jogcímek**

| JWT jogcím     | Name (Név)                            | Leírás                                                                                                                    | Megjegyzések |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | IP-cím                      | Az IP-címet az ügyfél a bejelentkezett.                                                                                      |       |
| `onprem_sid`  | A helyi biztonsági azonosítója |                                                                                                                                |       |
| `pwd_exp`     | Jelszó lejárati ideje        | A jelszó lejárati dátum és idő.                                                                                    |       |
| `pwd_url`     | Jelszó URL-cím módosítása             | Egy URL-címet, amely a felhasználói által felkereshető módosíthatják jelszavukat.                                                                        |       |
| `in_corp`     | Vállalati hálózaton belül        | Ha az ügyfél bejelentkezik a vállalati hálózatról jeleket. Ha nem, a jogcím nincs megadva                     |       |
| `nickname`    | Nickname                        | A felhasználó, a Vezetéknév vagy utónév megadva külön kiegészítő nevét.                                                             |       |                                                                                                                |       |
| `family_name` | Vezetéknév                       | Az utolsó nevét, Vezetéknév vagy családnév felhasználó biztosít az Azure AD-felhasználói objektum. <br>"family_name":"Miller" |       |
| `given_name`  | Utónév                      | Az első biztosít vagy az "adott" a felhasználó nevét, az Azure AD-felhasználói objektum vannak megadva.<br>"given_name": "Frank"                   |       |

### <a name="additional-properties-of-optional-claims"></a>A választható jogcímek további tulajdonságok

Néhány választható jogcímek beállítható úgy, hogy megváltoztassák a módját a jogcímet ad vissza.  A további tulajdonságok többnyire segítségével többféle elvárásainak a helyszíni alkalmazások áttelepítése (például `include_externally_authenticated_upn_without_hash` olyan ügyfelek esetében, nem tudja kezelni a hashmarks segítségével (`#`) a megadott egyszerű felhasználónév)

**4. táblázat: Értékeit szabványos választható jogcím konfigurálása**

| Tulajdonság neve                                     | További tulajdonság neve                                                                                                             | Leírás |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |             |
| | `include_externally_authenticated_upn`              | A Vendég a erőforrás bérlő tárolt UPN tartalmazza.  Például: `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint a fenti, kivéve, amelyek a hashmarks (`#`) váltják fel aláhúzásjelek (`_`), például `foo_hometenant.com_EXT_@resourcetenant.com` |             

> [!Note]
>Adja meg a választható jogcím egy további tulajdonság nélkül nem változtatja meg minden olyan esetben – a token által kiadott új jogcímet talál, a további tulajdonságok közül legalább egy fel kell venni. 


#### <a name="additional-properties-example"></a>További tulajdonságok példa:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

A OptionalClaims objektum okoz a azonosító token küld vissza az ügyfélnek a további otthoni bérlői és bérlői erőforrásadatok egy másik upn tartalmazza.  

## <a name="configuring-optional-claims"></a>Nem kötelező jogcím konfigurálása

Nem kötelező jogcímek, az alkalmazás az alkalmazás jegyzékében (lásd például az alábbi) módosításával konfigurálhatja. További információkért lásd: a [ismertetése a Azure AD application manifest cikk](active-directory-application-manifest.md).

**A minta séma:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "upn", 
                   "essential": false, 
                   "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ],
 "accessToken": [ 
             {
                    "name": "auth_time", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>OptionalClaims Type

A választható jogcímek, az alkalmazás által kért deklarálja. Az alkalmazás minden jogkivonatok (azonosító token, hozzáférési jogkivonat, SAML 2 jogkivonat) három típusú visszaküldendő választható jogcímeket, a biztonsági jogkivonatokkal kapcsolatos szolgáltatástól fogadhatnak konfigurálhatja. Az alkalmazás is beállíthat különböző választható jogcímek vissza kell helyezni az egyes lexikális elem típusa. Az alkalmazás entitás OptionalClaims tulajdonság OptionalClaims objektum.

**5. táblázat: OptionalClaims típus tulajdonságokat.**

| Name (Név)        | Típus                       | Leírás                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | A gyűjtemény (OptionalClaim) | A JWT-Azonosítót jogkivonatban visszaadott választható jogcímeket.     |
| `accessToken` | A gyűjtemény (OptionalClaim) | A választható jogcímeket a JWT jogkivonatot adott vissza. |
| `saml2Token`  | A gyűjtemény (OptionalClaim) | A választható jogcímeket a SAML-jogkivonatot adott vissza.       |


### <a name="optionalclaim-type"></a>OptionalClaim típusa

Egy nem kötelező jogcím egy alkalmazás vagy a szolgáltatás egyszerű társított tartalmazza. A idToken accessToken és saml2Token tulajdonságainak a [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) típus OptionalClaim gyűjteménye.
Ha egy adott jogcím támogatja, a további tulajdonságok mező OptionalClaim viselkedését is módosíthatja.

**6. táblázat: OptionalClaim típus tulajdonságokat.**

| Name (Név)                 | Típus                    | Leírás                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcím nevét.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A forrás (könyvtárobjektum) a jogcím. Előre meghatározott jogcím és a felhasználói jogcímek bővítmény tulajdonságai vannak. Ha az érték null, a választható előre meghatározott jogcím nincs. Ha az érték felhasználói, a name tulajdonság értéke a user objektum a bővített tulajdonság. |
| `essential`            | Edm.Boolean             | Értéke true, ha a jogcímek, az ügyfél által megadott szükség az adott feladat, a végfelhasználó által kért gördülékeny hitelesítési élményt nyújtsanak. Az alapértelmezett értéke hamis.                                                                                                                 |
| `additionalProperties` | Collection (Edm.String) | A jogcím további tulajdonságok. Ha egy tulajdonság szerepel a gyűjteményben, módosítja a name tulajdonság a nem kötelező jogcímek viselkedését.                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>Egyéni jogcím használatával címtárbővítmények konfigurálása

Mellett a szabványos választható jogcímek készletébe jogkivonatok is konfigurálható directory-séma bővítményei tartalmazza (lásd: a [Directory séma bővítményei cikk](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) olvashat).  Ez a szolgáltatás akkor hasznos, amellyel az alkalmazás – például további felhasználói adatokat, egy további azonosítója vagy fontos konfigurációs beállítás, amely a felhasználó. 

> [!Note]
> Directory-séma bővítményei újdonsága egy csak aad-ben, így ha az alkalmazás jegyzékében kérelmek egy egyéni bővítményt, és az msa-t a felhasználó bejelentkezik az az alkalmazás, ezek a bővítmények nem jeleníti meg. 

### <a name="values-for-configuring-additional-optional-claims"></a>További választható jogcímek konfigurálásához értékek 

Kiterjesztési attribútumot használja a teljes nevet, a bővítmény (a formátumban: `extension_<appid>_<attributename>`) az alkalmazásjegyzékben. A `<appid>` meg kell egyeznie a kérelmet a követelés azonosítója. 

A JWT belül ezeket a jogcímeket fog kibocsátott, a következő név formátumban: `extn.<attributename>`.

Belül a SAML-jogkivonatokat ezeket a jogcímeket kibocsátott a következő URI-formátumú lehet: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Nem kötelező jogcímek – példa

Ebben a szakaszban egy olyan forgatókönyvet, hogy hogyan használható a választható jogcímek funkció az alkalmazás keresztül is ismerteti.
Több lehetőség áll rendelkezésre az alkalmazás identitáskonfigurációs engedélyezése és konfigurálása nem kötelező jogcímek tulajdonságok frissítése:
-   Módosíthatja az alkalmazás jegyzékében. Az alábbi példa fogja ezt a módszert a konfigurálás elvégzéséhez. Olvassa el a [ismertetése az Azure AD alkalmazás jegyzékdokumentum](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) megismerkedhet a jegyzék az első.
-   Akkor is, amely használja az alkalmazás a [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) frissíteni az alkalmazást. A [entitás és a komplex típus hivatkozás](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) a Graph API-referencia az útmutató segítséget a választható jogcímek konfigurálásánál.

**Példa:** az alábbi példában egy alkalmazás jegyzékfájlja való hozzáférést, a azonosítója és a SAML jogcímeket adhatnak hozzá fog módosítani az alkalmazás szánt jogkivonatokat.
1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Ön már hitelesítése után válassza ki az Azure AD-bérlő el az oldal jobb felső sarkában.
3.  Válassza ki **Azure AD-bővítményben** a bal oldali navigációs panelen, majd kattintson a **App regisztrációk**.
4.  Választható jogcímeket adja meg a listában, kattintson a kívánt alkalmazás megkeresése.
5.  Az alkalmazás lapon kattintson a **Manifest** a jegyzék beágyazott-szerkesztő megnyitásához. 
6.  Közvetlenül szerkesztheti a jegyzéket a szerkesztő. A jegyzékfájl követi a sémát a [alkalmazás entitás](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity), és a jegyzék egyszer mentett automatikus formátumok. Új elemets nem kerülnek be a `OptionalClaims` tulajdonság.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      Ebben az esetben másik választható jogcímek hozzáadott minden típusú jogkivonatot, amely az alkalmazás fogadhat. Az azonosító-jogkivonatokat mostantól tartalmazza a teljes formátumot az összevont felhasználók UPN (`<upn>_<homedomain>#EXT#@<resourcedomain>`). A hozzáférési jogkivonatok most fog kapni a auth_time jogcímet. A SAML-jogkivonatokat mostantól tartalmazza a skypeId directory-séma kiterjesztését (ebben a példában az alkalmazás-azonosító az alkalmazás azonos ab603c56068041afb2f6832e2a17e237).  A SAML-jogkivonatokat megmutatják a Skype-azonosító szerint `extension_skypeId`.

7.  Ha végzett a jegyzékfájl frissítése, kattintson a **mentése** a jegyzékfájl mentése


## <a name="related-content"></a>Kapcsolódó tartalom
* További információ a [szabványos jogcímek](active-directory-token-and-claims.md) az Azure AD által biztosított. 
