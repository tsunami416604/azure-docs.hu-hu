---
title: Opcionális jogcímek biztosítása az Azure AD-alkalmazásokhoz | Azure
titleSuffix: Microsoft identity platform
description: Egyéni vagy további jogcímek hozzáadása az Azure Active Directory által kiadott SAML 2.0 és JSON webtokenek (JWT) jogkivonatokhoz.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 3/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 23d83b59c510f2565b2f66f78dad56c9c9592dd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136517"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Útmutató: Opcionális jogcímek biztosítása az Azure AD-alkalmazáshoz

Az alkalmazásfejlesztők az Azure AD-alkalmazásokban választható jogcímeket használhatnak annak meghatározásához, hogy mely jogcímekre szeretnének az alkalmazásukba küldött jogcímekben. 

A választható jogcímek a következőkre használhatók:

- Válassza ki a további jogcímeket, amelyeket az alkalmazás jogkivonataiban is fel szeretne tüntetni.
- Módosíthatja bizonyos jogcímek viselkedését, amelyeket az Azure AD jogkivonatokban ad vissza.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A szabványos jogcímek listáját a [hozzáférési jogkivonat](access-tokens.md) ban találja, és [id_token](id-tokens.md) jogcímdokumentációjában. 

Míg a választható jogcímek mind a v1.0- és a v2.0 formátumú jogkivonatok, valamint az SAML-jogkivonatok, ők biztosítják a legtöbb értékük, ha a v1.0-ról a 2.0-s. A [v2.0 Microsoft identity platform végpont](active-directory-appmodel-v2-overview.md) egyik célja a kisebb jogkivonat-méretek az ügyfelek optimális teljesítményének biztosítása érdekében. Ennek eredményeképpen a hozzáférésben és az azonosító jogkivonatokban korábban szereplő számos jogcím már nem szerepel a 2.0-s jogkivonatokban, és külön alkalmazásonként kell kérni őket.

**1. táblázat: Alkalmazhatóság**

| Fiók típusa | 1.0-s kulcs | 2.0-s kulcs  |
|--------------|---------------|----------------|
| Személyes Microsoft-fiók  | N/A  | Támogatott |
| Azure AD-fiók      | Támogatott | Támogatott |

## <a name="v10-and-v20-optional-claims-set"></a>1.0-s és 2.0-s sorozat választható jogcímek

Az alkalmazások számára alapértelmezés szerint elérhető választható jogcímek készlete az alábbiakban látható. Az alkalmazáshoz egyéni választható jogcímeket a Címtárbővítmények (Directory [Extensions) (Címtárbővítmények) (Címtárbővítmények) (Címtárbővítmények) (Címtárbővítmények) (Címtárbővítmények) (Címtárbővítmények) (Címtár](#configuring-directory-extension-optional-claims) Jogcímek hozzáadásakor a **hozzáférési jogkivonatot,** a jogcímek az alkalmazáshoz kért *by* hozzáférési jogkivonatokra (webes *API-ra)* vonatkoznak, nem pedig az alkalmazás által kért jogcímekre. Nem számít, hogy az ügyfél hogyan fér hozzá az API-hoz, a megfelelő adatok jelen vannak a hozzáférési jogkivonatban, amely az API-n való hitelesítéshez használatos.

> [!NOTE]
> Ezek a jogcímek többsége szerepelhet jwts a v1.0 és a v2.0 tokenek, de nem SAML-jogkivonatok, kivéve, ha a Token type oszlopban szerepel. Az fogyasztói fiókok támogatják ezeknek a jogcímeknek a "Felhasználó típusa" oszlopban megjelölt részhalmazát.  A felsorolt jogcímek nagy része nem vonatkozik a fogyasztói `tenant_ctry` felhasználókra (nincs bérlő, így nincs értéke).  

**2. táblázat: v1.0 és v2.0 választható jogcímkészlet**

| Név                       |  Leírás   | Token típusa | Felhasználó típusa | Megjegyzések  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | A felhasználó utolsó hitelesítésének időpontja. Lásd: OpenID Connect specifikáció.| Jwt        |           |  |
| `tenant_region_scope`      | Az erőforrás-bérlő régiója | Jwt        |           | |
| `home_oid`                 | Vendég felhasználók számára a felhasználó objektumazonosítója a felhasználó otthoni bérlőjében.| Jwt        |           | |
| `sid`                      | Munkamenet-azonosító, amely munkamenetenkénti felhasználói kijelentkezéshez használatos. | Jwt        |  Személyes és Azure AD-fiókok.   |         |
| `platf`                    | Eszközplatform    | Jwt        |           | Az eszköztípust ellenőrző felügyelt eszközökre korlátozva.|
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail-éból származik      | Jwt        |           |         |
| `verified_secondary_email` | A felhasználó másodlagos szerzőjeE-ből származik   | Jwt        |           |        |
| `enfpolids`                | Kényszerített házirend-azonosítók. Az aktuális felhasználó számára kiértékelt házirendazonosítók listája. | Jwt |  |  |
| `vnet`                     | VNET-megadható információ. | Jwt        |           |      |
| `fwd`                      | IP-cím.| Jwt    |   | A kérelmező ügyfél eredeti IPv4-címének hozzáadása (virtuális hálózaton belül) |
| `ctry`                     | A felhasználó országa | Jwt |  | Az Azure AD visszaadja a `ctry` választható jogcímet, ha jelen van, és a jogcím értéke egy szabványos kétbetűs országkód, például FR, JP, SZ és így tovább. |
| `tenant_ctry`              | Erőforrás-bérlő országa | Jwt | | |
| `xms_pdl`          | Előnyben részesített adatok helye   | Jwt | | A Multi-Geo bérlők, az előnyben részesített adatok helye a hárombetűs kód, amely a felhasználó földrajzi régióban található. További információt az [Azure AD Connect az előnyben részesített adatok helyéről szóló dokumentációjában](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)talál.<br/>Például: `APC` az ázsiai és csendes-óceáni térség esetében. |
| `xms_pl`                   | Felhasználó által előnyben részesített nyelv  | Jwt ||A felhasználó által előnyben részesített nyelv, ha be van állítva. Az otthoni bérlőtől származik, vendéghozzáférési forgatókönyvekben. Formázott LL-CC ("en-us"). |
| `xms_tpl`                  | Bérlő által előnyben részesített nyelv| Jwt | | Az erőforrás-bérlő előnyben részesített nyelve, ha be van állítva. Formázott LL ("en"). |
| `ztdid`                    | Nulla érintéses telepítési azonosító | Jwt | | A [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) eszközidentitása |
| `email`                    | A címezhető e-mail a felhasználó, ha a felhasználó rendelkezik ilyen.  | JWT, SAML | MSA, Azure Hirdetés | Ez az érték alapértelmezés szerint szerepel, ha a felhasználó a bérlő vendége.  Felügyelt felhasználók (a bérlőn belüli felhasználók) számára ezt a választható jogcím, vagy csak a 2.0-s vagy az OpenID hatókörön keresztül kell kérni.  Felügyelt felhasználók esetén az e-mail címet az [Office felügyeleti portálon](https://portal.office.com/adminportal/home#/users)kell beállítani.| 
| `groups`| A csoportos jogcímek választható formázása |JWT, SAML| |Az [alkalmazásjegyzékBen](reference-app-manifest.md)a GroupMembershipClaims beállítással együtt használatos, amelyet szintén be kell állítani. További részletek: Az alábbi [Csoportjogcímek.](#configuring-groups-optional-claims) A csoportjogcímekről a [Csoportjogcímek konfigurálása című](../hybrid/how-to-connect-fed-group-claims.md) témakörben talál további információt.
| `acct`             | A felhasználók fiók állapota a bérlőben. | JWT, SAML | | Ha a felhasználó a bérlő tagja, `0`az érték . Ha vendég, az érték `1`. |
| `upn`                      | UserPrincipalName jogcím. | JWT, SAML  |           | Bár ez a jogcím automatikusan szerepel, megadhatja azt választható jogcímként, hogy további tulajdonságokat csatoljon a vendégfelhasználói esetben a viselkedésének módosításához.  |

## <a name="v20-specific-optional-claims-set"></a>2.0-specifikus opcionális jogcímkészlet

Ezek a jogcímek mindig szerepelnek a v1.0-s Azure AD-jogkivonatok, de nem tartalmazza a 2.0-s jogkivonatok, ha kérik. Ezek a jogcímek csak jwt-k (azonosító jogkivonatok és hozzáférési jogkivonatok) esetén alkalmazhatók. 

**3. táblázat: csak 2.0-os v2.0-ra vonatkozó választható jogcímek**

| JWT követelés     | Név                            | Leírás                                | Megjegyzések |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-cím                      | Az az IP-cím, amelyről az ügyfél bejelentkezett.   |       |
| `onprem_sid`  | Helyszíni biztonsági azonosító |                                             |       |
| `pwd_exp`     | Jelszó lejárati ideje        | A jelszó lejáratának dátuma. |       |
| `pwd_url`     | Jelszó URL-címének módosítása             | Olyan URL-cím, amelyet a felhasználó felkeres a jelszó módosításához.   |   |
| `in_corp`     | Vállalati hálózaton belül        | Jelzi, ha az ügyfél a vállalati hálózatról jelentkezik be. Ha nem, a követelés nem szerepel.   |  Az MFA megbízható IP-beállításaialapján. [trusted IPs](../authentication/howto-mfa-mfasettings.md#trusted-ips)    |
| `nickname`    | Felhasználónév                        | A felhasználó további neve. A becenév elkülönül a vezeték- vagy keresztnévtől. A `profile` hatókört igényli.| 
| `family_name` | Vezetéknév                       | Megadja a felhasználó vezetéknevét, vezetéknevét vagy családnevét a felhasználói objektumban meghatározottak szerint. <br>"family_name":"Miller" | MsA és Azure AD támogatja. A `profile` hatókört igényli.   |
| `given_name`  | Utónév                      | Megadja a felhasználó első vagy "megadott" nevét a felhasználói objektumon beállítottan.<br>"given_name": "Frank"                   | Az MSA és az Azure AD támogatja.  A `profile` hatókört igényli. |
| `upn`         | Felhasználó egyszerű neve | A felhasználó azonosítóazonosítója, amely a username_hint paraméterrel használható.  Nem tartós azonosító a felhasználó számára, és nem használható kulcsadatokhoz. | A jogcím konfigurálása [további tulajdonságokat](#additional-properties-of-optional-claims) alább talál. A `profile` hatókört igényli.|

### <a name="additional-properties-of-optional-claims"></a>A választható jogcímek további tulajdonságai

Néhány választható jogcímek konfigurálható a jogcím visszaadási módjának módosítására. Ezeket a további tulajdonságokat többnyire a különböző adatelvárásokkal rendelkező `include_externally_authenticated_upn_without_hash` helyszíni alkalmazások áttelepítésének elősegítésére`#`használják (például olyan ügyfelekkel, akik nem tudják kezelni a kivonatjeleket ( ) az upn-ban)

**4. táblázat: A választható jogcímek konfigurálásának értékei**

| Tulajdonság neve  | További tulajdonságnév | Leírás |
|----------------|--------------------------|-------------|
| `upn`          |                          | Saml- és JWT-válaszokhoz, valamint az 1.0-s és 2.0-s és a 2.0-s jogkivonatokhoz is használható. |
|                | `include_externally_authenticated_upn`  | Tartalmazza a vendég UPN az erőforrás-bérlőben tárolt. Például: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint fent, kivéve,`#`hogy a kivonatjelek`_`( ) helyébe aláhúzásjel ( ), például`foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Példa további tulajdonságokra

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

Ez a OptionalClaims objektum hatására az ügyfélnek visszaadott azonosító jogkivonat egy upn jogcímet tartalmaz a további otthoni bérlői és erőforrás-bérlői adatokkal. A `upn` jogcím csak akkor változik meg a jogkivonatban, ha a felhasználó a bérlő vendége (amely egy másik IDP-t használ a hitelesítéshez). 

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

> [!IMPORTANT]
> A hozzáférési jogkivonatok **mindig** az erőforrás jegyzékfájlja, nem pedig az ügyfél használatával jönnek létre.  Tehát a `...scope=https://graph.microsoft.com/user.read...` kérelemben az erőforrás a Microsoft Graph API.So in the request the resource is the Microsoft Graph API.  Így a hozzáférési jogkivonat a Microsoft Graph API-jegyzékfájl használatával jön létre, nem az ügyfél jegyzékfájljával.  Az alkalmazás jegyzékfájljának módosítása soha nem eredményezi a Microsoft Graph API-hoz másképp en jelenik meg jogkivonatokat.  Annak ellenőrzése érdekében, hogy a `accessToken` módosítások érvényben vannak, kérjen jogkivonatot az alkalmazáshoz, ne egy másik alkalmazáshoz.  


Az alkalmazás választható jogcímeket a felhasználói felületen vagy az alkalmazásjegyzéken keresztül konfigurálhatja.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **Kezelés** szakaszban válassza **az Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez a nem kötelező jogcímeket konfigurálni szeretné a listában.

**Választható jogcímek konfigurálása a felhasználói felületen keresztül:**

[![A választható jogcímek konfigurálása a felhasználói felület használatával](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. A **Kezelés** szakaszban válassza **a Token konfigurációja (előzetes verzió)** lehetőséget.
2. Válassza **a Választható jogcím hozzáadása**lehetőséget.
3. Válassza ki a konfigurálni kívánt tokentípust.
4. Válassza ki a hozzáadni kívánt választható jogcímeket.
5. Kattintson a **Hozzáadás** gombra.

**Választható jogcímek konfigurálása az alkalmazásjegyzéken keresztül:**

[![A választható jogcímek konfigurálása az alkalmazásjegyzék használatával](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. A **Kezelés csoportban** válassza a **Jegyzékfájl lehetőséget.** Megnyílik egy webalapú jegyzékszerkesztő, amely lehetővé teszi a jegyzékfájl szerkesztését. Másik lehetőségként a **Letöltés** lehetőséget választva a helyi gépen is szerkesztheti az alkalmazásjegyzéket, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra. Az alkalmazásjegyzékről az Azure [AD-alkalmazásjegyzék ismertetése című cikkben olvashat bővebben.](reference-app-manifest.md)

    A következő alkalmazásjegyzék-bejegyzés hozzáadja a auth_time, ipaddr és upn választható jogcímeket az azonosítóhoz, a hozzáféréshez és az SAML-jogkivonatokhoz.

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
                            "essential": false
                       },
                       {
                            "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                            "source": "user", 
                            "essential": false
                       }
               ]
           }
    ```

2. Amikor végzett, kattintson a **Mentés** gombra. Most a megadott választható jogcímek szerepelni fog nak az alkalmazás jogkivonatai.    


### <a name="optionalclaims-type"></a>Választható jogcímek típusa

Deklarálja az alkalmazás által kért választható jogcímeket. Egy alkalmazás konfigurálhatja a választható jogcímeket, hogy vissza kell adni a három típusú jogkivonat (azonosító jogkivonat, hozzáférési jogkivonat, SAML 2 jogkivonat) a biztonsági jogkivonat szolgáltatásból kaphat. Az alkalmazás konfigurálhatja az egyes jogkivonat-típusokhoz visszaadandó választható jogcímek különböző készletét. Az Application entitás OptionalClaims tulajdonsága egy OptionalClaims objektum.

**5. táblázat: OptionalClaims típusú tulajdonságok**

| Név        | Típus                       | Leírás                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Gyűjtemény (OptionalClaim) | A JWT-azonosító jogkivonatban visszaadott választható jogcímek. |
| `accessToken` | Gyűjtemény (OptionalClaim) | A JWT-hozzáférési jogkivonatban visszaadott választható jogcímek. |
| `saml2Token`  | Gyűjtemény (OptionalClaim) | Az SAML-jogkivonatban visszaadott választható jogcímek.   |

### <a name="optionalclaim-type"></a>OptionalClaim típus

Egy alkalmazáshoz vagy egyszerű szolgáltatáshoz társított választható jogcímet tartalmaz. Az [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) típus idToken, accessToken és saml2Token tulajdonságai az OptionalClaim gyűjtemény.
Ha egy adott jogcím támogatja, az OptionalClaim viselkedését is módosíthatja a További tulajdonságok mező használatával.

**6. táblázat: OptionalClaim típustulajdonságai**

| Név                 | Típus                    | Leírás                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcím neve.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A jogcím forrása (címtárobjektuma). Vannak előre definiált jogcímek és a felhasználó által definiált jogcímek a bővítmény tulajdonságaiból. Ha a forrásérték null értékű, a jogcím egy előre definiált választható jogcím. Ha a forrásérték felhasználó, a name tulajdonság értéke a felhasználói objektum bővítmény tulajdonsága. |
| `essential`            | Edm.Boolean             | Ha az érték igaz, az ügyfél által megadott jogcím szükséges a végfelhasználó által kért feladat zökkenőmentes engedélyezési élményének biztosításához. Az alapértelmezett érték a hamis.                                                                                                             |
| `additionalProperties` | Gyűjtemény (Edm.String) | A követelés további tulajdonságai. Ha egy tulajdonság létezik ebben a gyűjteményben, módosítja a névtulajdonságban megadott nem kötelező jogcím viselkedését.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Címtárbővítmény választható jogcímek konfigurálása

A szabványos választható jogcímkészlet mellett a jogkivonatokat is konfigurálhatja bővítményekre. További információt [a Microsoft Graph extensionProperty dokumentációjában](https://docs.microsoft.com/graph/api/resources/extensionproperty?view=graph-rest-1.0) talál – vegye figyelembe, hogy a sémát és a nyitott bővítményeket nem támogatják a választható jogcímek, csak az AAD-Graph stílusú címtárbővítmények. Ez a funkció akkor hasznos, ha az alkalmazás által használható további felhasználói adatokat szeretne csatolni – például egy további azonosítót vagy a felhasználó által beállított fontos konfigurációs beállítást. Lásd az oldal alján egy példa.

> [!NOTE]
> - A címtárséma-bővítmények csak Azure AD-szolgáltatás, így ha az alkalmazás jegyzékfájlja egyéni bővítményt kér, és egy MSA-felhasználó bejelentkezik az alkalmazásba, ezek a bővítmények nem kerülnek vissza.

### <a name="directory-extension-formatting"></a>Könyvtárbővítmény formázása

Ha a címtárbővítmény választható jogcímeit az alkalmazásjegyzék használatával konfigurálja, `extension_<appid>_<attributename>`használja a bővítmény teljes nevét (formátumban: ). A `<appid>` jogcímkérő alkalmazás azonosítójának meg kell egyeznie. 

A JWT-n belül ezeket a jogcímeket `extn.<attributename>`a következő névformátummal adjuk ki: .

Az SAML-jogkivonatokon belül ezek a jogcímek a következő URI formátumban kerülnek kibocsátásra:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Csoportok konfigurálása választható jogcímek konfigurálása

   > [!NOTE]
   > A helyszíni adatokból szinkronizált felhasználók és csoportok csoportnevek et a Nyilvános előzetes verzió képes kibocsátni.

Ez a szakasz a csoportjogcímekben használt csoportjogcímekben használt csoportattribútumok alapértelmezett csoportobjektum-azonosítóból a helyszíni Windows Active Directoryból szinkronizált attribútumokra történő módosításához használt konfigurációs beállításokat ismerteti. A felhasználói felületen vagy az alkalmazásjegyzéken keresztül konfigurálhatja az alkalmazás választható jogcímeket.

> [!IMPORTANT]
> További részletekért, beleértve a helyszíni attribútumokból származó csoportjogcímek nyilvános előzetes verziójának fontos kikötéseit, olvassa [el a Csoportjogcímek konfigurálása az Alkalmazásokhoz az Azure AD-vel című témakört.](../hybrid/how-to-connect-fed-group-claims.md)

**A csoportok választható jogcímek konfigurálása a felhasználói felületen keresztül:**
1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. A hitelesítés t követően válassza ki az Azure AD-bérlőt a lap jobb felső sarkából kiválasztva.
1. Válassza ki az **Azure Active Directoryt** a bal oldali menüből
1. A **Kezelés** csoportban válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza ki azt az alkalmazást, amelyhez nem kötelező jogcímeket szeretne konfigurálni a listában.
1. A **Kezelés** szakaszban válassza a **Token konfigurációja (előzetes verzió) lehetőséget.**
2. Válassza **a Csoportok hozzáadása jogcím lehetőséget.**
3. Válassza ki a visszaadandó csoporttípusokat (**Minden csoport**, **SecurityGroup**vagy **DirectoryRole**). A **Minden csoport beállítás** tartalmazza a **SecurityGroup**, **DirectoryRole**és **DistributionList lehetőséget.**
4. Nem kötelező: kattintson az adott jogkivonat típusának tulajdonságaira a csoportok jogcímértékének módosításához, hogy az helyszíni csoportattribútumokat tartalmazzon, vagy módosítsa a jogcímtípusát szerepkörre.
5. Kattintson a **Mentés gombra**

**Csoportok konfigurálása választható jogcímek konfigurálása az alkalmazásjegyzéken keresztül:**
1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. A hitelesítés t követően válassza ki az Azure AD-bérlőt a lap jobb felső sarkából kiválasztva.
1. Válassza ki az **Azure Active Directoryt** a bal oldali menüből
1. Válassza ki azt az alkalmazást, amelyhez nem kötelező jogcímeket szeretne konfigurálni a listában.
1. A **Kezelés csoportban** válassza a **Manifest (Jegyzékfájl) lehetőséget.**
3. Adja hozzá a következő bejegyzést a jegyzékszerkesztővel:

   Az érvényes értékek a következők:

   - "All" (ez a beállítás tartalmazza a SecurityGroup, a DirectoryRole és a DistributionList listát)
   - "Biztonsági csoport"
   - "DirectoryRole"

   Példa:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Alapértelmezés szerint a csoport objectid-jai megjelennek a csoport jogcímértékében.  Ha módosítani szeretné a jogcímértékét, hogy az a helyszíni csoport attribútumait tartalmazza, vagy hogy a jogcímtípusát szerepkörre módosítsa, használja az OptionalClaims konfigurációt az alábbiak szerint:

3. Állítsa be a csoportnév konfigurációjának választható jogcímeit.

   Ha azt szeretné, hogy a jogkivonatban lévő csoportok tartalmazzák a helyszíni AD-csoport attribútumait a választható jogcímek szakaszban adja meg, hogy melyik jogkivonat típusára kell alkalmazni a választható jogcímet, a kért nem kötelező jogcím nevét és a kívánt további tulajdonságokat.  Több tokentípus is felsorolható:

   - idToken az OIDC-azonosító tokenhez
   - accessToken az OAuth hozzáférési jogkivonathoz
   - Saml2Token SAML tokenekhez.

   > [!NOTE]
   > A Saml2Token típus az SAML1.1 és az SAML2.0 formátumú tokenekre egyaránt vonatkozik.

   Minden megfelelő jogkivonat-típus, módosítsa a csoportok jogcím a manifest OptionalClaims szakasz ának használatára. Az OptionalClaims séma a következő:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Választható jogcímséma | Érték |
   |----------|-------------|
   | **név:** | "Csoportoknak" kell lenniük. |
   | **Forrás:** | Nem használt. Null kihagyása vagy megadása |
   | **Alapvető:** | Nem használt. Hamis kihagyás vagy megadása |
   | **továbbitulajdonságok:** | A további tulajdonságok listája.  Érvényes opciók: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   A további tulajdonságokban csak egy "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" szükséges.  Ha egynél több van jelen, az elsőt használja a rendszer, és a többi figyelmen kívül hagyja.

   Egyes alkalmazások csoportadatokat igényelnek a szerepkörjogcímben szereplő felhasználóról.  Ha a jogcímtípusát csoportjogcímről szerepkörjogcímre szeretné módosítani, adja hozzá a "emit_as_roles" értéket a további tulajdonságokhoz.  A csoportértékek a szerepkörjogcímleg kerülnek kibocsátásra.

   > [!NOTE]
   > Ha a "emit_as_roles" kifejezést használja, a felhasználó hozzárendelt alkalmazásszerepkörei nem jelennek meg a szerepkörjogcímhelyen

**Példák:**

1) Csoportok at bocsátanak ki csoportnévként oauth hozzáférési tokenekben dnsDomainName\sAMAccountName formátumban

    
    **Felhasználói felület konfigurációja:**

    [![A választható jogcímek konfigurálása a felhasználói felület használatával](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Alkalmazásjegyzék-bejegyzés:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) NetbiosDomain\sAMAccountName formátumban visszaküldendő csoportnevek et bocsát ki az SAML és OIDC id tokenek szerepkörjogcímigényeként

    **Felhasználói felület konfigurációja:**

    [![A választható jogcímek konfigurálása a felhasználói felület használatával](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Alkalmazásjegyzék-bejegyzés:**
    
    ```json
        "optionalClaims": {
        "saml2Token": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }],
        "idToken": [{
            "name": "groups",
            "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
        }]
    ``` 
     

## <a name="optional-claims-example"></a>Választható jogcímek példa

Ebben a szakaszban egy forgatókönyv, amely bemutatja, hogyan használhatja az alkalmazás választható jogcímkezelési szolgáltatás.
Az alkalmazás identitáskonfigurációjának tulajdonságainak frissítéséhez több lehetőség is rendelkezésre áll a választható jogcímek engedélyezéséhez és konfigurálásához:
-    Használhatja a **token konfiguráció (előzetes)** felhasználói felület (lásd az alábbi példát)
-    Használhatja a **jegyzékfájl (lásd** az alábbi példát). Olvassa el [az Azure AD alkalmazásjegyzék-dokumentum ismertetése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) először a jegyzékfájl bemutatását.
-   Az is lehetséges, hogy írjon egy alkalmazást, amely a [Microsoft Graph API-t](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) használja az alkalmazás frissítéséhez. A Microsoft Graph API referenciaútmutatójában található [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) típus segíthet a választható jogcímek konfigurálásában.

**Példa:** Az alábbi példában a **token konfiguráció (előzetes verzió)** felhasználói felület és **a manifest** használatával választható jogcímeket adhat hozzá az alkalmazáshoz szánt hozzáférési, azonosító és SAML-jogkivonatokhoz. Az alkalmazás által fogadott jogkivonat-típusokhoz különböző választható jogcímek lesznek hozzáadva:
-    Az azonosító tokenek mostantól a teljes formában (`<upn>_<homedomain>#EXT#@<resourcedomain>`) tartalmazzák az összevont felhasználók számára az UPN-t.
-    A hozzáférési jogkivonatokat, amelyeket más ügyfelek kérnek ehhez az alkalmazáshoz, mostantól a auth_time jogcím
-    Az SAML-tokenek mostantól a skypeId könyvtárséma-bővítményt tartalmazzák (ebben a példában az alkalmazás azonosítója ab603c56068041afb2f6832e2a17e237). Az SAML-tokenek a Skype-azonosítót a . `extension_skypeId`

**Felhasználói felület konfigurációja:**

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)

1. A hitelesítés után válassza ki az Azure AD-bérlőt a lap jobb felső sarkából való kiválasztásával.

1. Válassza ki az **Azure Active Directory** a bal oldali menüben.

1. A **Kezelés** csoportban válassza az **Alkalmazásregisztrációk lehetőséget.**

1. Keresse meg azt az alkalmazást, amelyhez a választható jogcímeket konfigurálni szeretné a listában, és kattintson rá.

1. A **Kezelés** csoportban kattintson a **Token konfigurációja (előzetes verzió)** elemre.

1. Válassza **a Választható jogcím hozzáadása**lehetőséget, válassza ki az **azonosítótoken** típusát, válassza ki a **upn** elemet a jogcímek listájából, majd kattintson a **Hozzáadás gombra.**

1. Válassza **a Választható jogcím hozzáadása**lehetőséget, válassza a **Hozzáférési** jogkivonat típusát, válassza a **auth_time** elemet a jogcímek listájából, majd kattintson a **Hozzáadás gombra.**

1. A Token konfigurációja áttekintése képernyőn kattintson a **felsorakulék**melletti ceruza ikonra, kattintson a **Külsőleg hitelesített** váltóra, majd a **Mentés gombra.**

1. Válassza **a Választható jogcím hozzáadása**lehetőséget, válassza ki az **SAML-jogkivonat** típusát, válassza **az extn.skypeID fájlt** a jogcímek listájából (csak akkor alkalmazható, ha létrehozott egy SkypeID nevű Azure AD felhasználói objektumot), majd kattintson a **Hozzáadás gombra.**

    [![A választható jogcímek konfigurálása a felhasználói felület használatával](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Jegyzékfájl konfigurációja:**
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A hitelesítés után válassza ki az Azure AD-bérlőt a lap jobb felső sarkából való kiválasztásával.
1. Válassza ki az **Azure Active Directory** a bal oldali menüben.
1. Keresse meg azt az alkalmazást, amelyhez a választható jogcímeket konfigurálni szeretné a listában, és kattintson rá.
1. A **Kezelés csoportban** kattintson a **Jegyzékfájl** elemre a szövegközi jegyzékszerkesztő megnyitásához.
1. Közvetlenül szerkesztheti a jegyzékfájlt ezzel a szerkesztővel. A jegyzékfájl az [Alkalmazás entitás](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)sémáját követi, és a mentés után automatikusan formázza a jegyzékfájlt. A `OptionalClaims` tulajdonság új elemekkel egészül ki.

    ```json
            "optionalClaims": {
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
        ``` 


1. When you're finished updating the manifest, click **Save** to save the manifest.

## Next steps

Learn more about the standard claims provided by Azure AD.

- [ID tokens](id-tokens.md)
- [Access tokens](access-tokens.md)
