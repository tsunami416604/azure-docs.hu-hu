---
title: Opcionális jogcímek megadása az Azure AD-alkalmazásokhoz | Azure
titleSuffix: Microsoft identity platform
description: Egyéni vagy további jogcímek hozzáadása a Azure Active Directory által kiállított SAML 2,0 és JSON web tokens (JWT) jogkivonatokhoz.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/08/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 9ea3388cb65b18c093ffff3ec8b8c9f2764ef189
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300068"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Útmutató: opcionális jogcímek megadása az Azure AD-alkalmazáshoz

Az alkalmazások fejlesztői használhatják az Azure AD-alkalmazásokban választható jogcímeket annak meghatározására, hogy mely jogcímeket szeretnék elküldeni az alkalmazásnak. 

A következő választható jogcímeket használhatja:

- Válassza ki az alkalmazáshoz tartozó jogkivonatokban szerepeltetni kívánt további jogcímeket.
- Módosítsa az Azure AD által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A standard jogcímek listájáért tekintse meg a [hozzáférési jogkivonat](access-tokens.md) és a [id_token](id-tokens.md) jogcím dokumentációját. 

Míg a választható jogcímek a v 1.0 és a v 2.0 formátumú jogkivonatokban, valamint az SAML-jogkivonatokban is támogatottak, az értékük nagy részét az 1.0 és a v 2.0 közötti váltáskor adja meg. A 2.0-s [Microsoft Identity platform végpontjának](active-directory-appmodel-v2-overview.md) egyik célja kisebb token-méret az ügyfelek optimális teljesítményének biztosítása érdekében. Ennek eredményeképpen számos, korábban a hozzáférési és azonosító jogkivonatban szereplő jogcím már nem található meg a v 2.0-s jogkivonatokban, és a kérést külön alkalmazási alapon kell megadnia.

**1. táblázat: alkalmazhatóság**

| Fiók típusa | 1\.0-s verziós tokenek | v 2.0-tokenek  |
|--------------|---------------|----------------|
| Személyes Microsoft-fiók  | N.A.  | Támogatott |
| Azure AD-fiók      | Támogatott | Támogatott |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 és v 2.0 választható jogcímek készlete

Az alábbi listában a használni kívánt alkalmazások alapértelmezett választható jogcímei érhetők el. Ha egyéni opcionális jogcímeket szeretne hozzáadni az alkalmazáshoz, tekintse meg az alábbi [címtárszolgáltatás-bővítményeket](#configuring-directory-extension-optional-claims). Ha jogcímeket ad hozzá a **hozzáférési jogkivonathoz**, a jogcímek az alkalmazáshoz (webes API-hoz *) kért hozzáférési* jogkivonatokra vonatkoznak, az alkalmazás *által* kért jogcímekre nem. Függetlenül attól, hogy az ügyfél hogyan fér hozzá az API-hoz, a megfelelő információ szerepel az API-val való hitelesítéshez használt hozzáférési jogkivonatban.

> [!NOTE]
> Ezeknek a jogcímeknek a többsége megadható a JWTs for 1.0 és v 2.0 tokenekhez, de nem SAML-tokenekhez, kivéve, ha a jogkivonat típusa oszlopban szerepel. A fogyasztói fiókok a "felhasználói típus" oszlopban megjelölt jogcímek egy részhalmazát támogatják.  A felsorolt jogcímek többsége nem vonatkozik a felhasználói felhasználókra (nem rendelkeznek Bérlővel, így `tenant_ctry` nem rendelkezik értékkel).  

**2. táblázat: v 1.0 és v 2.0 opcionális jogcím-készlet**

| Name (Név)                       |  Leírás   | Jogkivonat típusa | Felhasználó típusa | Megjegyzések  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | A felhasználó utolsó hitelesítésének időpontja. Lásd: OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Az erőforrás-bérlő régiója | JWT        |           | |
| `home_oid`                 | Vendég felhasználók számára a felhasználó saját bérlője objektumának AZONOSÍTÓját.| JWT        |           | |
| `sid`                      | Munkamenet-azonosító, amely a felhasználónkénti felhasználói kijelentkezéshez használatos. | JWT        |  Személyes és Azure AD-fiókok.   |         |
| `platf`                    | Eszköz platformja    | JWT        |           | Olyan felügyelt eszközökre korlátozódik, amelyek ellenőrizhetik az eszköz típusát.|
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail származik      | JWT        |           |         |
| `verified_secondary_email` | A felhasználó SecondaryAuthoritativeEmail származik   | JWT        |           |        |
| `enfpolids`                | Kényszerített szabályzat-azonosítók. Az aktuális felhasználó számára kiértékelt szabályzat-azonosítók listája. | JWT |  |  |
| `vnet`                     | A VNET megadására vonatkozó információk. | JWT        |           |      |
| `fwd`                      | IP-cím.| JWT    |   | Hozzáadja a kérelmező ügyfél eredeti IPv4-címe (egy VNET belül) |
| `ctry`                     | Felhasználó országa | JWT |  | Az Azure AD a `ctry` opcionális jogcímet adja vissza, ha a jelen van, és a jogcím értéke szabványos kétbetűs országkód, például FR, JP, SZ stb. |
| `tenant_ctry`              | Erőforrás-bérlő országa | JWT | | |
| `xms_pdl`          | Elsődleges adatelérési hely   | JWT | | A többszörös földrajzi bérlők esetében az előnyben részesített adatterület a felhasználó földrajzi régióját megjelenítő hárombetűs kód. További információ: [Azure ad Connect dokumentáció az előnyben részesített adatok helyéről](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Például: `APC` a Ázsia és a Csendes-óceáni térséghoz. |
| `xms_pl`                   | Felhasználó által előnyben részesített nyelv  | JWT ||A felhasználó által választott nyelv, ha be van állítva. A saját bérlőtől származik, a vendég hozzáférési forgatókönyvekben. Formázott LL-CC ("en-us"). |
| `xms_tpl`                  | Bérlő által előnyben részesített nyelv| JWT | | Az erőforrás-bérlő előnyben részesített nyelve, ha be van állítva. Formázott LL ("en"). |
| `ztdid`                    | Nulla érintéses telepítési azonosító | JWT | | A [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) szolgáltatáshoz használt eszköz identitása |
| `email`                    | A felhasználó címezhető e-mail-címe, ha a felhasználó rendelkezik ilyennel.  | JWT, SAML | MSA, Azure AD | Alapértelmezés szerint ez az érték szerepel, ha a felhasználó vendég a bérlőben.  A felügyelt felhasználók (a bérlőn belüli felhasználók) esetében ezt a választható jogcímen keresztül kell kérni, vagy csak a 2.0-s verzióban az OpenID hatókörrel.  A felügyelt felhasználók esetében az e-mail-címet be kell állítani az [Office felügyeleti portálon](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Csoportos jogcímek opcionális formázása |JWT, SAML| |Az GroupMembershipClaims beállítással együtt használatos az [alkalmazás jegyzékfájljában](reference-app-manifest.md), amelyet is be kell állítani. Részletekért lásd az alábbi [csoportos jogcímeket](#configuring-groups-optional-claims) . A csoportok jogcímeivel kapcsolatos további információkért lásd: [csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Felhasználói fiók állapota a bérlőben. | JWT, SAML | | Ha a felhasználó tagja a bérlőnek, az érték `0`. Ha vendég, az érték `1`. |
| `upn`                      | UserPrincipalName claim. | JWT, SAML  |           | Bár ez a jogcím automatikusan szerepel, megadhatja opcionális jogcímként is, ha további tulajdonságokat szeretne csatolni a vendég felhasználói eset működésének módosításához.  |

## <a name="v20-specific-optional-claims-set"></a>a 2.0-s verzióra vonatkozó nem kötelező jogcímek beállítása

Ezeket a jogcímeket mindig tartalmazza a v 1.0 Azure AD-jogkivonatok, de a nem tartalmazza a v 2.0-tokeneket, kivéve, ha erre kérték. Ezek a jogcímek csak a JWTs (azonosító jogkivonatok és hozzáférési tokenek) esetén érvényesek. 

**3. táblázat: v 2.0 – csak opcionális jogcímek**

| JWT jogcím     | Name (Név)                            | Leírás                                | Megjegyzések |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-cím                      | Az ügyféltől bejelentkezett IP-cím.   |       |
| `onprem_sid`  | Helyszíni biztonsági azonosító |                                             |       |
| `pwd_exp`     | Jelszó lejárati ideje        | Az a dátum és idő, amikor a jelszó lejár. |       |
| `pwd_url`     | Jelszó URL-címének módosítása             | URL-cím, amelyet a felhasználó megkereshet a jelszavuk módosításához.   |   |
| `in_corp`     | Vállalati hálózaton belül        | Azt jelzi, hogy az ügyfél bejelentkezik-e a vállalati hálózatról. Ha nem, a rendszer nem tartalmazza a jogcímet.   |  Az MFA [megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -címeinek beállításai alapján.    |
| `nickname`    | Becenév                        | A felhasználó további neve. A becenév elkülönül az utónévtől vagy az utónévtől. | 
| `family_name` | Vezetéknév                       | A felhasználó vezetéknevét, vezetéknevét vagy családjának nevét adja meg a felhasználói objektumban definiált módon. <br>"family_name":"Miller" | Támogatott a MSA és az Azure AD-ben   |
| `given_name`  | Keresztnév                      | A felhasználó első vagy "megadott" nevét adja meg a felhasználói objektumra vonatkozóan.<br>"given_name": "Frank"                   | Támogatott a MSA és az Azure AD-ben  |
| `upn`         | Felhasználó egyszerű neve | A username_hint paraméterrel használható felhasználó termékazonosító.  Nem tartós azonosító a felhasználó számára, és nem használható a legfontosabb adathoz. | A jogcím konfigurálásához tekintse meg az alábbi [további tulajdonságokat](#additional-properties-of-optional-claims) . |

### <a name="additional-properties-of-optional-claims"></a>A választható jogcímek további tulajdonságai

Egyes választható jogcímek úgy konfigurálhatók, hogy megváltoztassák a jogcím visszaadásának módját. Ezek a további tulajdonságok többnyire a helyszíni alkalmazások különböző adatelérési elvárásokkal való áttelepítésének megkönnyítésére szolgálnak (például `include_externally_authenticated_upn_without_hash` segít az egyszerű felhasználónévben nem kezelhető kivonatoló jeleket (`#`okat) használó ügyfelek számára.

**4. táblázat: választható jogcímek konfigurálásának értékei**

| Tulajdonság neve  | További tulajdonságnév | Leírás |
|----------------|--------------------------|-------------|
| `upn`          |                          | Az SAML-és JWT-válaszokhoz, valamint a 1.0-s és a v 2.0-tokenekhez is használható. |
|                | `include_externally_authenticated_upn`  | Az erőforrás-bérlőben tárolt vendég UPN-t tartalmazza. Például: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint a fenti, azzal a különbséggel, hogy a kivonatoló jeleket (`#`) az aláhúzás (`_`) váltja fel, például `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>További tulajdonságok – példa

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

Ez a OptionalClaims objektum azt eredményezi, hogy az azonosító token visszakerül az ügyfélnek, hogy egy másik UPN-t is tartalmazzon a további Kezdőlap bérlői és erőforrás-bérlői információk A `upn` jogcímet csak akkor változtatja meg a tokenben, ha a felhasználó a bérlő egyik vendége (amely más IDENTITÁSSZOLGÁLTATÓ használ a hitelesítéshez). 

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

> [!IMPORTANT]
> A hozzáférési jogkivonatok **mindig** az erőforrás jegyzékfájljának használatával jönnek létre, nem az ügyfél.  Tehát a kérelemben `...scope=https://graph.microsoft.com/user.read...` az erőforrás a Microsoft Graph API.  Így a hozzáférési jogkivonat a Microsoft Graph API-jegyzékfájl használatával jön létre, nem az ügyfél jegyzékfájlja.  Az alkalmazás jegyzékfájljának módosítása soha nem okoz jogkivonatot a Microsoft Graph API-nak a különböző megjelenéséhez.  Annak ellenőrzéséhez, hogy a `accessToken` változásai érvényesek-e, igényeljen egy jogkivonatot az alkalmazáshoz, nem pedig egy másik alkalmazást.  

Az alkalmazásra vonatkozó opcionális jogcímeket a felhasználói felület vagy az alkalmazás jegyzékfájlja segítségével konfigurálhatja.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg és válassza ki a **Azure Active Directory**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.

**Választható jogcímek konfigurálása a felhasználói felületen keresztül:**

[![bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció (előzetes verzió)** lehetőséget.
2. Válassza a **választható jogcím hozzáadása**lehetőséget.
3. Válassza ki a konfigurálni kívánt jogkivonat-típust.
4. Válassza ki a hozzáadandó választható jogcímeket.
5. Kattintson az **Hozzáadás** parancsra.

**Választható jogcímek konfigurálása az alkalmazás jegyzékfájlján keresztül:**

[![bemutatja, hogyan konfigurálhat választható jogcímeket az alkalmazás jegyzékfájljának használatával](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. A **kezelés** szakaszban válassza a **jegyzékfájl**lehetőséget. Megnyílik egy web-alapú jegyzékfájl-szerkesztő, amely lehetővé teszi a jegyzékfájl szerkesztését. Másik lehetőségként a **Letöltés** lehetőséget választva a helyi gépen is szerkesztheti az alkalmazásjegyzéket, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra. Az alkalmazás jegyzékfájljának megismeréséhez tekintse meg az [Azure ad Application manifest ismertetése című cikket](reference-app-manifest.md).

    A következő Application manifest bejegyzés hozzáadja a auth_time, a IPADDR és az UPN opcionális jogcímeit az AZONOSÍTÓhoz, a hozzáféréshez és az SAML-jogkivonatokhoz.

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

2. Amikor végzett, kattintson a **Mentés** gombra. Most a megadott választható jogcímek szerepelni fognak az alkalmazás jogkivonatában.    


### <a name="optionalclaims-type"></a>OptionalClaims típusa

Deklarálja az alkalmazás által kért választható jogcímeket. Egy alkalmazás konfigurálhat választható jogcímeket is, amelyek a biztonsági jogkivonat szolgáltatástól kapott három jogkivonat (azonosító token, hozzáférési jogkivonat, SAML 2 token) mindhárom típusában visszatérhetnek. Az alkalmazás különböző választható jogcímeket konfigurálhat az egyes token-típusokban való visszatéréshez. Az alkalmazás entitás OptionalClaims tulajdonsága egy OptionalClaims objektum.

**5. táblázat: OptionalClaims típusának tulajdonságai**

| Name (Név)        | Típus                       | Leírás                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Gyűjtemény (OptionalClaim) | A JWT azonosító jogkivonatában visszaadott választható jogcímek. |
| `accessToken` | Gyűjtemény (OptionalClaim) | Az JWT hozzáférési jogkivonatban visszaadott választható jogcímek. |
| `saml2Token`  | Gyűjtemény (OptionalClaim) | Az SAML-jogkivonatban visszaadott választható jogcímek.   |

### <a name="optionalclaim-type"></a>OptionalClaim típusa

Egy alkalmazáshoz vagy egy egyszerű szolgáltatáshoz társított opcionális jogcímet tartalmaz. A [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) típus IdToken, AccessToken és saml2Token tulajdonsága a OptionalClaim gyűjteménye.
Ha egy adott jogcím támogatja, a OptionalClaim viselkedését a AdditionalProperties mező használatával is módosíthatja.

**6. táblázat: OptionalClaim típusának tulajdonságai**

| Name (Név)                 | Típus                    | Leírás                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcím neve.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A jogcím forrása (Directory-objektum). A bővítmény tulajdonságaiban előre definiált jogcímek és felhasználó által definiált jogcímek találhatók. Ha a forrás értéke null, a jogcím egy előre meghatározott opcionális jogcím. Ha a forrás értéke felhasználó, a Name (név) tulajdonság értéke a felhasználói objektum kiterjesztés tulajdonsága. |
| `essential`            | Edm.Boolean             | Ha az érték TRUE (igaz), akkor az ügyfél által megadott jogcím szükséges a végfelhasználó által kért feladat zökkenőmentes engedélyezési élményének biztosításához. Az alapértelmezett értéke FALSE (hamis).                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | A jogcím további tulajdonságai. Ha egy tulajdonság létezik ebben a gyűjteményben, a módosítja a Name (név) tulajdonságban megadott választható jogcím viselkedését.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>A címtár-bővítmény választható jogcímeinek konfigurálása

A standard opcionális jogcímek készleten kívül a jogkivonatokat is konfigurálhatja a bővítmények belefoglalásához. Ez a funkció hasznos lehet az alkalmazás által használható további felhasználói adatok csatolásához – például egy további azonosító vagy fontos konfigurációs beállítás, amelyet a felhasználó beállított. Példaként tekintse meg az oldal alját.

> [!NOTE]
> - A címtár-séma bővítményei egy csak Azure AD-szolgáltatás, így ha az alkalmazás jegyzékfájlja egyéni kiterjesztést kér, és egy MSA-felhasználó bejelentkezik az alkalmazásba, akkor ezeket a bővítményeket a rendszer nem adja vissza.

### <a name="directory-extension-formatting"></a>Címtárszolgáltatás-bővítmény formázása

Ha az alkalmazás jegyzékfájljának használatával konfigurálhatja a címtár-bővítmény választható jogcímeit, használja a bővítmény teljes nevét (a következő formátumban: `extension_<appid>_<attributename>`). A `<appid>`nak meg kell egyeznie a jogcímet kérő alkalmazás azonosítójával. 

A JWT belül ezeket a jogcímeket a következő formátumnév fogja kiadni: `extn.<attributename>`.

Az SAML-tokeneken belül ezek a jogcímek a következő URI-formátummal lesznek kibocsátva: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Csoportok konfigurálása választható jogcímek

   > [!NOTE]
   > A helyi verzióról szinkronizált felhasználók és csoportok számára a csoportok nevének kiadásának lehetősége nyilvános előzetes verzió.

Ez a szakasz azokat a konfigurációs beállításokat ismerteti, amelyek a választható jogcímek területen az alapértelmezett csoport objectID a helyi Windows Active Directoryról szinkronizált attribútumokra vonatkozóan használt csoportok attribútumainak módosítására használhatók. A felhasználói felület vagy az alkalmazás jegyzékfájlja segítségével az alkalmazáshoz választható jogcímeket is konfigurálhat.

> [!IMPORTANT]
> További részletek: az Azure AD-ben az [alkalmazások csoportos jogcímei jogcímeinek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md)című témakörben olvashat bővebben.

**Csoportok konfigurálása választható jogcímek a felhasználói felületen keresztül:**
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában található elem kiválasztásával
1. **Azure Active Directory** kiválasztása a bal oldali menüben
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk**
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció (előzetes verzió) lehetőséget.**
2. Válassza a **csoportok hozzáadása** lehetőséget
3. Válassza ki a visszaadni kívánt csoportok típusát (**minden csoport**, **SecurityGroup**vagy **DirectoryRole**). A **minden csoport** beállítás magában foglalja az **SecurityGroup**, a **DirectoryRole**és a **DistributionList**
4. Nem kötelező: kattintson az adott token típusának tulajdonságaira a csoportok jogcím értékének a helyszíni csoport attribútumain való tárolásához vagy a jogcím típusának a szerepkörre való módosításához.
5. Kattintson a **Mentés** gombra.

**Csoportok konfigurálása választható jogcímek az alkalmazás jegyzékfájlján keresztül:**
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában található elem kiválasztásával
1. **Azure Active Directory** kiválasztása a bal oldali menüben
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában
1. A **kezelés** szakaszban válassza a **jegyzékfájl** elemet.
3. Adja hozzá a következő bejegyzést a jegyzékfájl-szerkesztő használatával:

   Az érvényes értékek a következők:

   - "All" (ez a beállítás magában foglalja az SecurityGroup, a DirectoryRole és a DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   Például:

    ```json
        "groupMembershipClaims": "SecurityGroup"
    ```

   Alapértelmezés szerint a csoport ObjectIDs a rendszer a csoportos jogcím értékét fogja kiállítani.  Ha módosítani szeretné a jogcím értékét, hogy a tartalmazza a helyszíni csoport attribútumait, vagy módosítani szeretné a jogcím típusát a szerepkörre, használja a OptionalClaims-konfigurációt az alábbiak szerint:

3. Adja meg a csoport neve konfigurációjának választható jogcímeit.

   Ha azt szeretné, hogy a tokenben lévő csoportok megjelenjenek a helyszíni AD-csoport attribútumai a választható jogcímek szakaszban, adja meg, hogy melyik jogkivonat-típust kívánja alkalmazni, a kért választható jogcím nevét és a kívánt további tulajdonságokat.  Több jogkivonat-típus is szerepelhet:

   - a OIDC azonosító token idToken
   - a OAuth/OIDC hozzáférési token accessToken
   - SAML-tokenek Saml2Token.

   > [!NOTE]
   > A Saml2Token típusa SAML 1.1 és SAML 2.0 formátumú jogkivonatokra is vonatkozik.

   Minden releváns jogkivonat-típus esetében módosítsa a csoportok jogcímet a jegyzékfájl OptionalClaims szakaszának használatára. A OptionalClaims séma a következő:

    ```json
       {
       "name": "groups",
       "source": null,
       "essential": false,
       "additionalProperties": []
       }
    ```

   | Választható jogcímek sémája | Érték |
   |----------|-------------|
   | **név:** | "Groups" értéknek kell lennie |
   | **forrás** | Nincs használatban. Kihagyás vagy a Null érték meghatározása |
   | **alapvető** | Nincs használatban. Kihagyás vagy a hamis meghatározása |
   | **additionalProperties:** | További tulajdonságok listája.  Az érvényes beállítások: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   A additionalProperties-ben a "sam_account_name", a "dns_domain_and_sam_account_name", a "netbios_domain_and_sam_account_name" értéknek csak az egyike szükséges.  Ha egynél több van jelen, az első használatban van, és minden más figyelmen kívül lesz hagyva.

   Egyes alkalmazások a szerepkör-jogcímben szereplő felhasználóra vonatkozó csoportos adatokat igényelnek.  Ha módosítani szeretné a jogcím típusát egy csoport jogcímen egy szerepkör-jogcímre, adja hozzá a "emit_as_roles" értéket a további tulajdonságokhoz.  A csoport értékeit a rendszer a szerepkör-jogcímben fogja kiállítani.

   > [!NOTE]
   > Ha a "emit_as_roles" minden olyan alkalmazási szerepkört használ, amelyet a felhasználóhoz rendeltek, nem jelennek meg a szerepkör-jogcímben

**Példák**

1) Csoportok kibocsátása csoport neveként a OAuth hozzáférési jogkivonatokban dnsDomainName\sAMAccountName formátumban

    
    **Felhasználói felület konfigurációja:**

    [![bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)


    **Alkalmazás jegyzékfájljának bejegyzése:**
    ```json
        "optionalClaims": {
            "accessToken": [{
            "name": "groups",
            "additionalProperties": ["dns_domain_and_sam_account_name"]
            }]
        }
    ```

 
    
2) A netbiosDomain\sAMAccountName formátumban visszaadott csoportok nevének kibocsátása az SAML-és OIDC-azonosító jogkivonatokban lévő szerepkörök jogcímként

    **Felhasználói felület konfigurációja:**

    [![bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Alkalmazás jegyzékfájljának bejegyzése:**
    
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
     

## <a name="optional-claims-example"></a>Választható jogcímek – példa

Ebben a szakaszban áttekintheti a forgatókönyvet, amelyből megtudhatja, hogyan használhatja a választható jogcímek funkciót az alkalmazásához.
Több lehetőség is rendelkezésre áll az alkalmazás identitás-konfigurációjának tulajdonságainak frissítésére az opcionális jogcímek engedélyezéséhez és konfigurálásához:
-    Használhatja a **jogkivonat-konfiguráció (előzetes verzió)** felhasználói felületét (lásd az alábbi példát)
-    Használhatja a **jegyzékfájlt** (lásd az alábbi példát). Olvassa el az [Azure ad Application manifest-dokumentum megismerése című dokumentumot](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) a jegyzékfájl bevezetésének első lépéseit ismertető dokumentumban.
-   Olyan alkalmazást is írhat, amely a [Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2F1.0&view=graph-rest-1.0) -t használja az alkalmazás frissítéséhez. A Microsoft Graph API-referenciák útmutatójának [OptionalClaims](https://docs.microsoft.com/graph/api/resources/optionalclaims?view=graph-rest-1.0) -típusa segíthet a választható jogcímek konfigurálásában.

**Példa:** Az alábbi példában a **jogkivonat-konfiguráció (előzetes verzió)** felhasználói felület és a **jegyzékfájl** használatával adhat hozzá opcionális jogcímeket az alkalmazáshoz szánt hozzáféréshez, azonosítóhoz és SAML-jogkivonatokhoz. Különböző választható jogcímek lesznek hozzáadva az egyes tokenekhez, amelyeket az alkalmazás fogadni tud:
-    Az azonosító tokenek mostantól a teljes űrlapon (`<upn>_<homedomain>#EXT#@<resourcedomain>`) is tartalmazzák az összevont felhasználók egyszerű felhasználónevét.
-    Az alkalmazáshoz tartozó más ügyfelek által igényelt hozzáférési jogkivonatok mostantól tartalmazzák a auth_time jogcímet
-    Az SAML-tokenek mostantól tartalmazzák a skypeId Directory sémakezelő bővítményt (ebben a példában az alkalmazáshoz tartozó ab603c56068041afb2f6832e2a17e237). Az SAML-tokenek `extension_skypeId`ként teszik elérhetővé a Skype-azonosítót.

**Felhasználói felület konfigurációja:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.

1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.

1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk**lehetőséget.

1. Keresse meg azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni, és kattintson rá.

1. A **kezelés** szakaszban kattintson a **jogkivonat-konfiguráció (előzetes verzió)** elemre.

1. Válassza a **választható jogcím hozzáadása**lehetőséget, válassza ki az **azonosító** token típusát, válassza az **egyszerű felhasználónév** lehetőséget a jogcímek listájából, majd kattintson a **Hozzáadás**gombra.

1. Válassza a **választható jogcím hozzáadása**lehetőséget, válassza ki a **hozzáférési** token típusát, válassza a **auth_time** elemet a jogcímek listájából, majd kattintson a **Hozzáadás**gombra.

1. A jogkivonat-konfiguráció áttekintése képernyőn kattintson az **egyszerű felhasználónév**elem melletti ceruza ikonra, kattintson a **külsőleg hitelesített** váltógomb elemre, majd a **Mentés**gombra.

1. Válassza az **opcionális jogcím hozzáadása**lehetőséget, válassza ki az **SAML** -token típusát, válassza a **extn. skypeID** elemet a jogcímek listájából (csak akkor érvényes, ha létrehozott egy skypeID nevű Azure ad felhasználói objektumot), majd kattintson a **Hozzáadás**gombra.

    [![bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Jegyzékfájl konfigurációja:**
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.
1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
1. Keresse meg azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni, és kattintson rá.
1. A **kezelés** szakaszban kattintson a **manifest (jegyzékfájl** ) elemre a beágyazott jegyzékfájl-szerkesztő megnyitásához.
1. A jegyzékfájlt közvetlenül szerkesztheti a szerkesztő használatával. A jegyzékfájl az [alkalmazás entitásának](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)sémáját követi, és a mentés után automatikusan formázza a jegyzékfájlt. Új elemek lesznek hozzáadva a `OptionalClaims` tulajdonsághoz.

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
