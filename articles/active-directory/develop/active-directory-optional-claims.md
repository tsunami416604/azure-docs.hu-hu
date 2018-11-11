---
title: Ismerje meg, hogyan választható jogcímek, az Azure AD-alkalmazás megadása |} A Microsoft Docs
description: Útmutató egyéni vagy további jogcímek, az Azure Active Directory által kibocsátott SAML 2.0 és a JSON Web tokenek (JWT) tokenek hozzáadásához.
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: 0983c2235fba0cacbda53208e5dcad5b2878619c
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345487"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app-public-preview"></a>Hogyan: Adja meg a választható jogcímeket, az Azure AD-alkalmazás (nyilvános előzetes verzió)

Ez a szolgáltatás-k segítségével az alkalmazásfejlesztők adja meg, milyen jogcímeket szeretnének biztosítani az alkalmazásokban a jogkivonatokban. Nem kötelező jogcímeket is használhatja:
- Válassza ki az alkalmazáshoz tartozó jogkivonatok foglalandó további jogcímek.
- Változtathatja meg bizonyos jogcímek, az Azure AD eredményül a jogkivonatokban.
- Adja hozzá, és egyéni jogcímek, az alkalmazás eléréséhez. 

> [!NOTE]
> Ez a lehetőség jelenleg nyilvános előzetes verzióban érhető el. Készüljön fel a módosítások visszavonására vagy eltávolítására. A szolgáltatás minden olyan Azure AD-előfizetéshez, a nyilvános előzetes verzióban érhető el. Amikor a szolgáltatás általánosan elérhetővé válik, azonban a szolgáltatást bizonyos aspektusainak szükség lehet egy Azure AD premium előfizetéssel.

Standard jogcímek és azok hogyan használhatók a tokenek listáját lásd: a [alapjait, az Azure AD által kiállított jogkivonatokban](v1-id-and-access-tokens.md). 

Az egyik a [az Azure AD v2.0-végpont](active-directory-appmodel-v2-overview.md) az ügyfelek által az optimális teljesítmény biztosítása érdekében kisebb token méretű. Ennek eredményeképpen korábban szerepelni fog a hozzáférési és azonosító-jogkivonatokat több jogcím már nem találhatók a 2.0-s verziójú jogkivonatokban, és kifejezetten a alkalmazásonkénti alapján kell kérni.

**1. táblázat: alkalmazhatósági**

| Fiók típusa | 1.0-s verziójú végpont | 2.0 verziójú végpont  |
|--------------|---------------|----------------|
| Személyes Microsoft-fiók  | NA – jegyek RPS ehelyett használt | A támogatás hamarosan elérhető |
| Azure AD-fiók          | Támogatott                          | Szem előtt a következőket támogatja |

> [!IMPORTANT]
> Mind a személyes fiókokat, és az Azure AD-t támogató alkalmazások (regisztrálva a [alkalmazás regisztrációs portál](https://apps.dev.microsoft.com)) választható jogcímek nem használható. Azonban csak az Azure ad v2.0-végpont használatával regisztrált alkalmazás kérheti le a nem kötelező jogcímeket a kért a jegyzékfájlban. Az Azure Portalon is használhatja a meglévő az alkalmazásjegyzék-szerkesztőben **alkalmazásregisztrációk** élmény a választható jogcím szerkesztése. Azonban ez a funkció még nem áll rendelkezésre az új alkalmazásjegyzék-szerkesztőben használatával **alkalmazásregisztrációk (előzetes verzió)** tapasztalható.

## <a name="standard-optional-claims-set"></a>Standard választható jogcímek készletébe

Az alkalmazásokkal való használatra alapértelmezés szerint elérhető nem kötelező jogcímek készlete alább láthatók. Az egyéni opcionális jogcímek az alkalmazáshoz adni, tekintse meg a [Címtárbővítmények](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions), az alábbi. Vegye figyelembe, hogy jogcímeket hozzáadásakor a **hozzáférési jogkivonat**, ez lesz érvényes hozzáférési jogkivonatok kért *a* az alkalmazás (webes API-k), azokat *által* az alkalmazást. Ez biztosítja, hogy az ügyfelet, az API elérése, függetlenül attól, hogy a megfelelő adatok már jelen vannak a hozzáférési jogkivonat hitelesíti a rendszer az API-t használják.

> [!NOTE]
> A legtöbb ezeket a jogcímeket is szerepelnek JWTs az 1.0-s verziója és a 2.0-s verziójú jogkivonatok, de nem SAML-jogkivonatokat, kivéve, ahol ennek ellenezőjét jelöltük a jogkivonat típusa oszlopban. Emellett bár nem kötelező jogcímek csak jelenleg támogatott az AAD-felhasználók, MSA támogatási ad hozzá. Amikor MSA választható jogcímeket támogatja a v2.0-végpont, a felhasználói adattípusú oszlop jogcím elérhető-e az AAD vagy az MSA-felhasználók esetében fog jelöl. 

**2. táblázat: Standard választható jogcímek készletéhez**

| Name (Név)                        | Leírás   | Jogkivonat típusa | Felhasználó típusa | Megjegyzések  |
|-----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Amikor a felhasználó legutóbbi hitelesített ideje. Lásd: OpenID Connect specifikációja.| JWT        |           |  |
| `tenant_region_scope`      | Az erőforrás-bérlő régió | JWT        |           | |
| `home_oid`                 | A vendégfelhasználók számára, a felhasználó bérlőjének a felhasználó Objektumazonosítóját.| JWT        |           | |
| `sid`                      | Munkamenet-azonosító, a munkamenet-felhasználó kijelentkezés használt. | JWT        |           |         |
| `platf`                    | Eszközplatform    | JWT        |           | Eszköztípus ellenőrizheti a felügyelt eszközökre korlátozva.|
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail forrása      | JWT        |           |         |
| `verified_secondary_email` | A felhasználó SecondaryAuthoritativeEmail forrása   | JWT        |           |        |
| `enfpolids`                | Kényszerített házirend azonosítók. A szabályzat az aktuális felhasználó értékelt azonosítók listáját. | JWT |  |  |
| `vnet`                     | VNET-adatok megadása. | JWT        |           |      |
| `fwd`                      | IP-cím.| JWT    |   | Felveszi az eredeti IPv4-címet a kérést küldő ügyfélnek (Ha egy virtuális hálózaton belül) |
| `ctry`                     | A felhasználó ország | JWT |           | Az Azure AD vissza a `ctry` jogcím nem kötelező, ha telepítve, és a jogcím értéke egy standard kétbetűs országkód, például FR, JP, SZ és így tovább. |
| `tenant_ctry`              | Erőforrás-bérlő ország | JWT | | |
| `xms_pdl`          | Az adatok előnyben részesített helye   | JWT | | A Multi-földrajzi bérlők számára ez az a 3 betűs kód megjelenítő melyik földrajzi régiót, a felhasználó szerepel. További részletekért tekintse meg a [adatainak előnyben részesített helye az Azure AD Connect dokumentáció](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation). <br> Például: `APC` Ázsia Csendes-óceáni számára. |
| `xms_pl`                   | A felhasználó preferált nyelvét  | JWT ||A felhasználó elsődleges nyelv, ha a beállítása. Származási hely a saját bérlőjén, a Vendég adathozzáférési forgatókönyvek esetében. R-CC formátumú ("en-us"). |
| `xms_tpl`                  | A bérlői elsődleges nyelv| JWT | | Az erőforrás-bérlő elsődleges nyelv, ha a beállítása. Formázott LL ("hu"). |
| `ztdid`                    | Beavatkozás nélküli telepítés azonosítója | JWT | | A használt eszközidentitás [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
|`email`                     | Címezhető e-mail a felhasználó, ha a felhasználó rendelkezik ilyennel.  | JWT, SAML | | Ha a felhasználó a bérlő Vendég Ez az érték alapértelmezés szerint tartalmazza.  Felügyelt felhasználók (amelyeket a bérlőn belül) azt kell kérik, ez nem kötelező a jogcím, vagy a 2.0-s verziójú csak, és az OpenID hatókörének.  Felügyelt felhasználók esetén az e-mail-cím formájában kell megadni a [Office rendszergazdai portál](https://portal.office.com/adminportal/home#/users).|  
| `acct`             | Felhasználói fiók állapota-bérlőben. | JWT, SAML | | Ha a felhasználó tagja a bérlő, a értéke `0`. A Vendég, ha az értéke `1`. |
| `upn`                      | UserPrincipalName claim. | JWT, SAML  |           | Bár ez a jogcím automatikusan tartalmazza, mint egy nem kötelező jogcím csatolni a Vendég felhasználói esetben viselkedésének módosítása további tulajdonságok megadhat. <br> További tulajdonságok: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash` |

### <a name="v20-optional-claims"></a>Nem kötelező jogcímek 2.0-s verzió

Ezeket a jogcímeket 1.0-s verziójú jogkivonatok mindig szerepel, de nem tartalmazza a 2.0-s verziójú jogkivonatokban, kivéve, ha a kért. Ezeket a jogcímeket (azonosító-jogkivonatokat és hozzáférési jogkivonatok) JWTs csak vonatkoznak. 

**3. táblázat: Csak V2.0 választható jogcímek**

| JWT-jogcím     | Name (Név)                            | Leírás                                | Megjegyzések |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | IP-cím                      | Az IP-címet az ügyfél a bejelentkezett.   |       |
| `onprem_sid`  | Helyi biztonsági azonosító |                                             |       |
| `pwd_exp`     | Jelszó lejárati ideje        | A dátumot/időt, amikor a jelszó lejár. |       |
| `pwd_url`     | Jelszó URL-Címének módosítása             | Egy URL-címet, amely a felhasználó ellátogathat a jelszó módosítására.   |       |
| `in_corp`     | Vállalati hálózaton belül        | Ha az ügyfél bejelentkezik a vállalati hálózatról jelek. Ha nem, a a jogcím lehetőség nem része.   |       |
| `nickname`    | Becenév.                        | A felhasználó, az első vagy utolsó külön további neve. |       |                                                                                                                |       |
| `family_name` | Vezetéknév                       | Az utolsó neve, Vezetéknév vagy felhasználó család neve biztosít az Azure AD-felhasználói objektum. <br>"family_name":"Miller" |       |
| `given_name`  | Utónév                      | Itt az első vagy az "adott" a felhasználó neve az Azure AD-felhasználói objektum vannak megadva.<br>"given_name": "Frank"                   |       |

### <a name="additional-properties-of-optional-claims"></a>Nem kötelező jogcímek további tulajdonságok

Egyes nem kötelező jogcímek konfigurálható megváltoztatni a jogcímet ad vissza. Ezek a további tulajdonságok vannak többnyire kódtáblázatokhoz használják a különböző adatokat elvárásainak a helyszíni alkalmazások migrálása érdekében (például `include_externally_authenticated_upn_without_hash` segít az ügyfeleknek, amely nem tudja kezelni a hashmarks a (`#`) UPN-jét)

**4. táblázat: Értékeit standard választható jogcím konfigurálása**

| Tulajdonság neve                                     | További tulajdonság neve                                                                                                             | Leírás |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |  A SAML- és a JWT-válaszok használható.        |
| | `include_externally_authenticated_upn`              | A Vendég UPN adatbázisában található az erőforrás-bérlő tartalmazza. Például: `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint a fenti, kivéve, amelyek a hashmarks (`#`) cserélése aláhúzásjeleket (`_`), például `foo_hometenant.com_EXT_@resourcetenant.com` |             

> [!Note]
>Adja meg a választható jogcím egy új tulajdonság nélkül nem változik meg a minden viselkedés – annak érdekében, hogy a jogkivonatban kiadott új jogcímet talál, a további tulajdonságok közül legalább hozzá kell adni. 

#### <a name="additional-properties-example"></a>További tulajdonságok példa

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

Ez OptionalClaims objektum okoz az azonosító jogkivonat, az ügyfél egy másik egyszerű felhasználónév a további saját bérlőjén és erőforrás-bérlő adatai tartalmazzák. Ez csak módosítja a `upn` jogcím a jogkivonatban, ha a felhasználó a Vendég a bérlőben (amely egy másik Identitásszolgáltatót használja a hitelesítéshez). 

## <a name="configuring-optional-claims"></a>Nem kötelező jogcím konfigurálása

Az alkalmazás nem kötelező jogcímek az alkalmazásjegyzéknek (lásd például alább) módosításával konfigurálhatja. További információkért lásd: a [ismertetése az Azure AD application manifest cikk](reference-app-manifest.md).

**Minta séma:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "auth_time", 
                   "essential": false
              }
        ],
 "accessToken": [ 
             {
                    "name": "ipaddr", 
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

### <a name="optionalclaims-type"></a>OptionalClaims típusa

Deklarálja a választható jogcímek, az alkalmazás által kért. Alkalmazás választható jogcímeket adja vissza a jogkivonatok (azonosító jogkivonat, access token, SAML 2 jogkivonat) három típusú megkaphatja a biztonsági jogkivonatokkal kapcsolatos szolgáltatástól származó konfigurálhatja. Az alkalmazás beállíthatja a választható minden típusú jogkivonatot adott vissza jogcímeket külön készletét. Az alkalmazás entitás OptionalClaims tulajdonság OptionalClaims objektum.

**5. táblázat: OptionalClaims tulajdonságait**

| Name (Név)        | Típus                       | Leírás                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | A gyűjtemény (OptionalClaim) | A nem kötelező a JWT-azonosító jogkivonat visszaküldött jogcímek. |
| `accessToken` | A gyűjtemény (OptionalClaim) | A JWT jogkivonat nem kötelező jogcímeket adja vissza. |
| `saml2Token`  | A gyűjtemény (OptionalClaim) | Az SAML-jogkivonat a választható jogcímeket adja vissza.   |

### <a name="optionalclaim-type"></a>OptionalClaim típusa

Tartalmaz egy választható jogcím egy alkalmazás vagy az egyszerű szolgáltatás társítva. IdToken accessToken és saml2Token tulajdonságait a [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) típus OptionalClaim gyűjteménye.
Ha az egy adott jogcím támogatja, a további tulajdonságok mező használatával OptionalClaim viselkedését is módosíthatja.

**6. táblázat: OptionalClaim tulajdonságait**

| Name (Név)                 | Típus                    | Leírás                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcímszabály neve.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A forrás (címtárobjektum) a jogcím. Előre meghatározott jogcím és a felhasználói jogcímeket bővítmény tulajdonságai vannak. Ha a forrás értéke null, a jogcím előre meghatározott jogcím nem kötelező. Ha a forrás értéke felhasználó, a name tulajdonság értéke az a felhasználói objektum bővítménytulajdonság. |
| `essential`            | Edm.Boolean             | Ha az értéke igaz, a jogcímek, az ügyfél által megadott az adott feladat, a felhasználó által kért egy gördülékeny hitelesítési élmény biztosításához szükséges. Az alapértelmezett értéke FALSE (hamis).                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Az igényt további tulajdonságai. Ha egy tulajdonság már létezik ebben a gyűjteményben, módosítja a a name tulajdonság nem kötelező jogcímek viselkedését.                                                                                                                                               |
## <a name="configuring-custom-claims-via-directory-extensions"></a>Egyéni jogcímek keresztül címtárbővítmények konfigurálása

Felül a standard szintű választható jogcímek készletébe jogkivonatok is konfigurálhatók a directory-sémabővítmények tartalmazzák (lásd a [Directory séma bővítményei cikk](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) további információt). Ez a funkció akkor hasznos, amelyek az alkalmazás használhatja – például további felhasználói adatokat, egy további azonosító vagy a fontos konfigurációs beállítás, amelyet a felhasználó be van állítva. 

> [!Note]
> Directory-sémabővítmények egy csak AAD-funkció,, így ha az alkalmazás jegyzékfájlja kérelmek egyedi bővítmény és a egy MSA felhasználó bejelentkezik az alkalmazás, ezek a bővítmények nem állítható vissza. 

### <a name="values-for-configuring-additional-optional-claims"></a>Értékeit további nem kötelező jogcím konfigurálása

A bővítményattribútumok, használja a bővítmény teljes nevére (a következő formátumban: `extension_<appid>_<attributename>`) az alkalmazásjegyzékben. A `<appid>` meg kell egyeznie a jogcím kér az alkalmazás azonosítója. 

A JWT belül ezeket a jogcímeket fog kibocsátott, a következő név formátumban: `extn.<attributename>`.

Az SAML-jogkivonatok belül ezeket a jogcímeket fog bocsátja ki az URI formátuma a következő: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Nem kötelező jogcímek példa

Ebben a szakaszban megtekintheti, hogyan használhatja a választható jogcímek szolgáltatást az alkalmazás egy forgatókönyvet is végig.
Több lehetőség van egy alkalmazás identitás konfiguráció engedélyezése és konfigurálása nem kötelező jogcímek tulajdonságait frissítési érhető el:
-   Az alkalmazásjegyzékben módosíthatja. Az alábbi példában ez a módszer használatával végezheti el a konfigurálást. Olvassa el a [ismertetése az Azure AD application manifest dokumentum](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) első bevezetést a jegyzékfájlban.
-   Akkor is használó alkalmazások írása a [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) frissíteni az alkalmazást. A [entitások és összetett típusok segédletben](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) az a Graph API-referencia útmutató segítségére lehet a nem kötelező jogcímek konfigurálása.

**Példa:** az alábbi példában egy alkalmazásjegyzékben jogcímalapú hozzáférés, az ID és a SAML hozzáadandó módosítják a tokenek számára az alkalmazás.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A hitelesítése után után válassza ki azt az oldal jobb felső sarokban válassza ki az Azure AD-bérlő.
1. Válassza ki **Alkalmazásregisztrációk** bal alsó oldalán.
1. Keresse meg az alkalmazás konfigurálása nem kötelező jogcímeket a listában, és kattintson rá.
1. Kattintson az alkalmazás oldaláról **Manifest** , nyissa meg a beágyazott alkalmazásjegyzék-szerkesztőben. 
1. Közvetlenül szerkesztheti a jegyzékfájlt a szerkesztő használatával. A jegyzékfájl követi a sémát a [alkalmazás entitás](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity), és formázza a jegyzékfájl egyszer mentve. Új elem megjelenik a `OptionalClaims` tulajdonság.

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
      Ebben az esetben másik választható jogcímek hozzáadott minden típusú jogkivonatot, amely az alkalmazás fogadhat. Az azonosító-jogkivonatokat mostantól tartalmazza a teljes képernyőn összevont felhasználók esetében az egyszerű Felhasználónevet (`<upn>_<homedomain>#EXT#@<resourcedomain>`). A hozzáférési tokenek kérő ügyfelek számára az alkalmazás most már tartalmazza a auth_time jogcímet. Az SAML-jogkivonatok mostantól tartalmazza a skypeId directory-séma kiterjesztését (ebben a példában az alkalmazás az alkalmazás csomagazonosítója ab603c56068041afb2f6832e2a17e237). Az SAML-jogkivonatok fogja elérhetővé tenni a Skype-azonosító, `extension_skypeId`.

1. Ha elkészült, a jegyzékfájl frissítése, kattintson a **mentése** a jegyzékfájl mentése

## <a name="next-steps"></a>További lépések

További információ az Azure AD által támogatott szabványos jogcímeket.

- [Azonosító-jogkivonatokat](id-tokens.md)
- [Hozzáférési jogkivonatok](access-tokens.md)
