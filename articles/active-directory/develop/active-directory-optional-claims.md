---
title: Opcionális jogcímek megadása az Azure AD-alkalmazásokhoz
titleSuffix: Microsoft identity platform
description: Egyéni vagy további jogcímek hozzáadása a Microsoft Identity platform által kiadott SAML 2,0 és JSON web tokens (JWT) jogkivonatokhoz.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/24/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: ff3e2c9f989a6688e200a1c34e85ef3a22860840
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794675"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>Útmutató: opcionális jogcímek megadása az alkalmazás számára

Az alkalmazások fejlesztői használhatják az Azure AD-alkalmazásokban választható jogcímeket annak meghatározására, hogy mely jogcímeket szeretnék elküldeni az alkalmazásnak.

A következő választható jogcímeket használhatja:

- Válassza ki az alkalmazáshoz tartozó jogkivonatokban szerepeltetni kívánt további jogcímeket.
- Módosítsa a Microsoft Identity platform által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A standard jogcímek listájáért tekintse meg a [hozzáférési jogkivonat](access-tokens.md) és a [id_token](id-tokens.md) jogcím dokumentációját.

Míg a választható jogcímek a v 1.0 és a v 2.0 formátumú jogkivonatokban, valamint az SAML-jogkivonatokban is támogatottak, az értékük nagy részét az 1.0 és a v 2.0 közötti váltáskor adja meg. A 2.0-s [Microsoft Identity platform végpontjának](./v2-overview.md) egyik célja kisebb token-méret az ügyfelek optimális teljesítményének biztosítása érdekében. Ennek eredményeképpen számos, korábban a hozzáférési és azonosító jogkivonatban szereplő jogcím már nem található meg a v 2.0-s jogkivonatokban, és a kérést külön alkalmazási alapon kell megadnia.

**1. táblázat: alkalmazhatóság**

| Fiók típusa               | 1.0-s verziós tokenek | v 2.0-tokenek |
|----------------------------|-------------|-------------|
| Személyes Microsoft-fiók | N.A.         | Támogatott   |
| Azure AD-fiók           | Támogatott   | Támogatott   |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 és v 2.0 választható jogcímek készlete

Az alábbi listában a használni kívánt alkalmazások alapértelmezett választható jogcímei érhetők el. Ha egyéni opcionális jogcímeket szeretne hozzáadni az alkalmazáshoz, tekintse meg az alábbi [címtárszolgáltatás-bővítményeket](#configuring-directory-extension-optional-claims). Ha jogcímeket ad hozzá a **hozzáférési jogkivonathoz**, a jogcímek az alkalmazáshoz (webes API-hoz *) kért hozzáférési* jogkivonatokra vonatkoznak, az alkalmazás *által* kért jogcímekre nem. Függetlenül attól, hogy az ügyfél hogyan fér hozzá az API-hoz, a megfelelő információ szerepel az API-val való hitelesítéshez használt hozzáférési jogkivonatban.

> [!NOTE]
> Ezeknek a jogcímeknek a többsége megadható a JWTs for 1.0 és v 2.0 tokenekhez, de nem SAML-tokenekhez, kivéve, ha a jogkivonat típusa oszlopban szerepel. A fogyasztói fiókok a "felhasználói típus" oszlopban megjelölt jogcímek egy részhalmazát támogatják.  A felsorolt jogcímek többsége nem vonatkozik a felhasználói felhasználókra (nem rendelkezik Bérlővel, ezért `tenant_ctry` nem rendelkezik értékkel).

**2. táblázat: v 1.0 és v 2.0 opcionális jogcím-készlet**

| Név                       |  Leírás   | Jogkivonat típusa | Felhasználó típusa | Jegyzetek  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | A felhasználó utolsó hitelesítésének időpontja. Lásd: OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Az erőforrás-bérlő régiója | JWT        |           | |
| `sid`                      | Munkamenet-azonosító, amely a felhasználónkénti felhasználói kijelentkezéshez használatos. | JWT        |  Személyes és Azure AD-fiókok.   |         |
| `platf`                    | Eszközplatform    | JWT        |           | Olyan felügyelt eszközökre korlátozódik, amelyek ellenőrizhetik az eszköz típusát.|
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail származik      | JWT        |           |         |
| `verified_secondary_email` | A felhasználó SecondaryAuthoritativeEmail származik   | JWT        |           |        |
| `vnet`                     | A VNET megadására vonatkozó információk. | JWT        |           |      |
| `fwd`                      | IP-cím.| JWT    |   | Hozzáadja a kérelmező ügyfél eredeti IPv4-címe (egy VNET belül) |
| `ctry`                     | Felhasználó országa/régiója | JWT |  | Az Azure AD visszaadja a `ctry` választható jogcímet, ha a jelen van, és a mező értéke standard kétbetűs ország-vagy régiókód, például fr, JP, sz stb. |
| `tenant_ctry`              | Erőforrás-bérlő országa | JWT | | Ugyanaz, mint `ctry` a rendszergazda által a bérlői szinten beállítottak kivételével.  Standard kétbetűs értéknek is kell lennie. |
| `xms_pdl`             | Elsődleges adatelérési hely   | JWT | | A többszörös földrajzi bérlők esetében az előnyben részesített adatterület a felhasználó földrajzi régióját megjelenítő hárombetűs kód. További információ: [Azure ad Connect dokumentáció az előnyben részesített adatok helyéről](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md).<br/>Például: `APC` Ázsia és a csendes-óceáni térség. |
| `xms_pl`                   | Felhasználó által előnyben részesített nyelv  | JWT ||A felhasználó által választott nyelv, ha be van állítva. A saját bérlőtől származik, a vendég hozzáférési forgatókönyvekben. Formázott LL-CC ("en-us"). |
| `xms_tpl`                  | Bérlő által előnyben részesített nyelv| JWT | | Az erőforrás-bérlő előnyben részesített nyelve, ha be van állítva. Formázott LL ("en"). |
| `ztdid`                    | Nulla érintéses telepítési azonosító | JWT | | A [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) szolgáltatáshoz használt eszköz identitása |
| `email`                    | A felhasználó címezhető e-mail-címe, ha a felhasználó rendelkezik ilyennel.  | JWT, SAML | MSA, Azure AD | Alapértelmezés szerint ez az érték szerepel, ha a felhasználó vendég a bérlőben.  A felügyelt felhasználók (a bérlőn belüli felhasználók) esetében ezt a választható jogcímen keresztül kell kérni, vagy csak a 2.0-s verzióban az OpenID hatókörrel.  A felügyelt felhasználók esetében az e-mail-címet be kell állítani az [Office felügyeleti portálon](https://portal.office.com/adminportal/home#/users).|
| `acct`                | Felhasználói fiók állapota a bérlőben | JWT, SAML | | Ha a felhasználó tagja a bérlőnek, az érték a `0` . Ha vendég, az érték a `1` . |
| `groups`| Csoportos jogcímek opcionális formázása |JWT, SAML| |Az GroupMembershipClaims beállítással együtt használatos az [alkalmazás jegyzékfájljában](reference-app-manifest.md), amelyet is be kell állítani. Részletekért lásd az alábbi [csoportos jogcímeket](#configuring-groups-optional-claims) . A csoportok jogcímeivel kapcsolatos további információkért lásd: [csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md)
| `upn`                      | UserPrincipalName | JWT, SAML  |           | Bár ez a jogcím automatikusan szerepel, megadhatja opcionális jogcímként is, ha további tulajdonságokat szeretne csatolni a vendég felhasználói eset működésének módosításához.  |
| `idtyp`                    | Jogkivonat típusa   | JWT hozzáférési jogkivonatok | Speciális: csak az alkalmazáshoz tartozó hozzáférési jogkivonatokban |  Az érték az, `app` Ha a jogkivonat csak alkalmazási token. Ez a legpontosabb módszer egy API számára annak megállapítására, hogy a token alkalmazás-jogkivonat vagy alkalmazás + felhasználói jogkivonat-e.|

## <a name="v20-specific-optional-claims-set"></a>a 2.0-s verzióra vonatkozó nem kötelező jogcímek beállítása

Ezeket a jogcímeket mindig tartalmazza a v 1.0 Azure AD-jogkivonatok, de a nem tartalmazza a v 2.0-tokeneket, kivéve, ha erre kérték. Ezek a jogcímek csak a JWTs (azonosító jogkivonatok és hozzáférési tokenek) esetén érvényesek.

**3. táblázat: v 2.0 – csak opcionális jogcímek**

| JWT jogcím     | Név                            | Leírás                                | Jegyzetek |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-cím                      | Az ügyféltől bejelentkezett IP-cím.   |       |
| `onprem_sid`  | Helyszíni biztonsági azonosító |                                             |       |
| `pwd_exp`     | Jelszó lejárati ideje        | Az a dátum és idő, amikor a jelszó lejár. |       |
| `pwd_url`     | Jelszó URL-címének módosítása             | URL-cím, amelyet a felhasználó megkereshet a jelszavuk módosításához.   |   |
| `in_corp`     | Vállalati hálózaton belül        | Azt jelzi, hogy az ügyfél bejelentkezik-e a vállalati hálózatról. Ha nem, a rendszer nem tartalmazza a jogcímet.   |  Az MFA [megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -címeinek beállításai alapján.    |
| `family_name` | Vezetéknév                       | A felhasználó vezetéknevét, vezetéknevét vagy családjának nevét adja meg a felhasználói objektumban definiált módon. <br>"family_name": "Miller" | Támogatott a MSA és az Azure AD-ben. A `profile` hatókört igényli.   |
| `given_name`  | Utónév                      | A felhasználó első vagy "megadott" nevét adja meg a felhasználói objektumra vonatkozóan.<br>"given_name": "Frank"                   | Támogatott a MSA és az Azure AD-ben.  A `profile` hatókört igényli. |
| `upn`         | Felhasználó egyszerű neve | A username_hint paraméterrel használható felhasználó termékazonosító.  Nem tartós azonosító a felhasználó számára, és nem használható a legfontosabb adathoz. | A jogcím konfigurálásához tekintse meg az alábbi [további tulajdonságokat](#additional-properties-of-optional-claims) . A `profile` hatókört igényli.|

### <a name="additional-properties-of-optional-claims"></a>A választható jogcímek további tulajdonságai

Egyes választható jogcímek úgy konfigurálhatók, hogy megváltoztassák a jogcím visszaadásának módját. Ezek a további tulajdonságok többnyire a helyszíni alkalmazások különböző adatelérési elvárásokkal való áttelepítésének megkönnyítésére szolgálnak (például olyan ügyfelek számára, `include_externally_authenticated_upn_without_hash` akik nem kezelhetik a kivonatoló jeleket ( `#` ) az UPN-ben)

**4. táblázat: választható jogcímek konfigurálásának értékei**

| Tulajdonság neve  | További tulajdonságnév | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | Az SAML-és JWT-válaszokhoz, valamint a 1.0-s és a v 2.0-tokenekhez is használható. |
|                | `include_externally_authenticated_upn`  | Az erőforrás-bérlőben tárolt vendég UPN-t tartalmazza. Például: `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint a fenti, azzal a különbséggel, hogy a kivonatoló jeleket () a `#` rendszer aláhúzással () váltja le `_` , például: `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>További tulajdonságok – példa

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Ez a OptionalClaims objektum azt eredményezi, hogy a rendszer visszaadja az azonosító jogkivonatot az ügyfélnek, hogy tartalmazza a további otthoni Bérlővel és erőforrás-bérlői információkkal rendelkező UPN A `upn` jogcímek csak abban az esetben módosulnak a jogkivonatban, ha a felhasználó a bérlő egyik vendége (amely más identitásszolgáltató használ a hitelesítéshez).

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

> [!IMPORTANT]
> A hozzáférési jogkivonatok **mindig** az erőforrás jegyzékfájljának használatával jönnek létre, nem az ügyfél.  Tehát a kérelemben `...scope=https://graph.microsoft.com/user.read...` az erőforrás a Microsoft Graph API.  Így a hozzáférési jogkivonat a Microsoft Graph API-jegyzékfájl használatával jön létre, nem az ügyfél jegyzékfájlja.  Az alkalmazás jegyzékfájljának módosítása soha nem okoz jogkivonatot a Microsoft Graph API-nak a különböző megjelenéséhez.  Annak ellenőrzéséhez, hogy a `accessToken` módosítások érvényben vannak-e, igényeljen egy jogkivonatot az alkalmazáshoz, nem pedig egy másik alkalmazást.

Az alkalmazásra vonatkozó opcionális jogcímeket a felhasználói felület vagy az alkalmazás jegyzékfájlja segítségével konfigurálhatja.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.

**Választható jogcímek konfigurálása a felhasználói felületen keresztül:**

[![Bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció**elemet.
1. Válassza a **választható jogcím hozzáadása**lehetőséget.
1. Válassza ki a konfigurálni kívánt jogkivonat-típust.
1. Válassza ki a hozzáadandó választható jogcímeket.
1. Válassza a **Hozzáadás** elemet.

**Választható jogcímek konfigurálása az alkalmazás jegyzékfájlján keresztül:**

[![Bemutatja, hogyan konfigurálhat választható jogcímeket az alkalmazás jegyzékfájljának használatával](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. A **kezelés** szakaszban válassza a **jegyzékfájl**lehetőséget. Megnyílik egy web-alapú jegyzékfájl-szerkesztő, amely lehetővé teszi a jegyzékfájl szerkesztését. Másik lehetőségként a **Letöltés** lehetőséget választva a helyi gépen is szerkesztheti az alkalmazásjegyzéket, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra. Az alkalmazás jegyzékfájljának megismeréséhez tekintse meg az [Azure ad Application manifest ismertetése című cikket](reference-app-manifest.md).

    A következő Application manifest bejegyzés hozzáadja a auth_time, a IPADDR és az UPN opcionális jogcímeit az AZONOSÍTÓhoz, a hozzáféréshez és az SAML-jogkivonatokhoz.

    ```json
    "optionalClaims": {
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

2. Ha elkészült, válassza a **Mentés**lehetőséget. Most a megadott választható jogcímek szerepelni fognak az alkalmazás jogkivonatában.

### <a name="optionalclaims-type"></a>OptionalClaims típusa

Deklarálja az alkalmazás által kért választható jogcímeket. Egy alkalmazás konfigurálhat választható jogcímeket is, amelyek a biztonsági jogkivonat szolgáltatástól kapott három jogkivonat (azonosító token, hozzáférési jogkivonat, SAML 2 token) mindhárom típusában visszatérhetnek. Az alkalmazás különböző választható jogcímeket konfigurálhat az egyes token-típusokban való visszatéréshez. Az alkalmazás entitás OptionalClaims tulajdonsága egy OptionalClaims objektum.

**5. táblázat: OptionalClaims típusának tulajdonságai**

| Név          | Típus                       | Description                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Gyűjtemény (OptionalClaim) | A JWT azonosító jogkivonatában visszaadott választható jogcímek.     |
| `accessToken` | Gyűjtemény (OptionalClaim) | Az JWT hozzáférési jogkivonatban visszaadott választható jogcímek. |
| `saml2Token`  | Gyűjtemény (OptionalClaim) | Az SAML-jogkivonatban visszaadott választható jogcímek.       |

### <a name="optionalclaim-type"></a>OptionalClaim típusa

Egy alkalmazáshoz vagy egy egyszerű szolgáltatáshoz társított opcionális jogcímet tartalmaz. A [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) típus IdToken, AccessToken és saml2Token tulajdonsága a OptionalClaim gyűjteménye.
Ha egy adott jogcím támogatja, a OptionalClaim viselkedését a AdditionalProperties mező használatával is módosíthatja.

**6. táblázat: OptionalClaim típusának tulajdonságai**

| Név                   | Típus                    | Description                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcím neve.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A jogcím forrása (Directory-objektum). A bővítmény tulajdonságaiban előre definiált jogcímek és felhasználó által definiált jogcímek találhatók. Ha a forrás értéke null, a jogcím egy előre meghatározott opcionális jogcím. Ha a forrás értéke felhasználó, a Name (név) tulajdonság értéke a felhasználói objektum kiterjesztés tulajdonsága. |
| `essential`            | Edm.Boolean             | Ha az érték TRUE (igaz), akkor az ügyfél által megadott jogcím szükséges a végfelhasználó által kért feladat zökkenőmentes engedélyezési élményének biztosításához. Az alapértelmezett érték a hamis.                                                                                                                 |
| `additionalProperties` | Gyűjtemény (EDM. String) | A jogcím további tulajdonságai. Ha egy tulajdonság létezik ebben a gyűjteményben, a módosítja a Name (név) tulajdonságban megadott választható jogcím viselkedését.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>A címtár-bővítmény választható jogcímeinek konfigurálása

A standard opcionális jogcímek készleten kívül a jogkivonatokat is konfigurálhatja a bővítmények belefoglalásához. További információ: [Microsoft Graph extensionProperty dokumentációja](/graph/api/resources/extensionproperty?view=graph-rest-1.0).

A sémák és a nyitott bővítmények nem támogatottak a választható jogcímek esetében, csak a HRE stílusú könyvtárak bővítményei. Ez a funkció hasznos lehet az alkalmazás által használható további felhasználói adatok csatolásához – például egy további azonosító vagy fontos konfigurációs beállítás, amelyet a felhasználó beállított. Példaként tekintse meg az oldal alját.

> [!NOTE]
> A címtár-séma bővítményei egy Azure AD-only szolgáltatás. Ha az alkalmazás jegyzékfájlja egyéni kiterjesztést kér, és egy MSA-felhasználó bejelentkezik az alkalmazásba, ezek a bővítmények nem lesznek visszaadva.

### <a name="directory-extension-formatting"></a>Címtárszolgáltatás-bővítmény formázása

Ha az alkalmazás jegyzékfájljának használatával konfigurálhatja a címtár-bővítmény választható jogcímeit, használja a bővítmény teljes nevét (a következő formátumban: `extension_<appid>_<attributename>` ). Az értéknek `<appid>` meg kell egyeznie a jogcímet kérő alkalmazás azonosítójával.

A JWT belül ezeket a jogcímeket a következő formátumnév fogja kiadni:  `extn.<attributename>` .

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
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció** elemet.
1. Válassza a **csoportok hozzáadása** lehetőséget
1. Válassza ki a visszaadni kívánt csoportok típusát (**minden csoport**, **SecurityGroup**vagy **DirectoryRole**). A **minden csoport** beállítás magában foglalja az **SecurityGroup**, a **DirectoryRole**és a **DistributionList**
1. Nem kötelező: válassza ki az adott jogkivonat-típus tulajdonságait a csoportok jogcím értékének módosításához a helyszíni csoport attribútumain vagy a jogcím típusának a szerepkörre való módosításához.
1. Válassza a **Mentés** lehetőséget

**Csoportok konfigurálása választható jogcímek az alkalmazás jegyzékfájlján keresztül:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában található elem kiválasztásával
1. **Azure Active Directory** kiválasztása a bal oldali menüben
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában
1. A **kezelés** szakaszban válassza a **jegyzékfájl** elemet.
1. Adja hozzá a következő bejegyzést a jegyzékfájl-szerkesztő használatával:

   Az érvényes értékek a következők:

   - "All" (ez a beállítás magában foglalja az SecurityGroup, a DirectoryRole és a DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"

   Például:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Alapértelmezés szerint a csoport ObjectIDs a rendszer a csoportos jogcím értékét fogja kiállítani.  Ha módosítani szeretné a jogcím értékét, hogy a tartalmazza a helyszíni csoport attribútumait, vagy módosítani szeretné a jogcím típusát a szerepkörre, használja a OptionalClaims-konfigurációt az alábbiak szerint:

1. Adja meg a csoport neve konfigurációjának választható jogcímeit.

   Ha azt szeretné, hogy a tokenben lévő csoportok megjelenjenek a helyszíni AD-csoport attribútumai a választható jogcímek szakaszban, adja meg, hogy melyik jogkivonat-típust kívánja alkalmazni, a kért választható jogcím nevét és a kívánt további tulajdonságokat.  Több jogkivonat-típus is szerepelhet:

   - a OIDC azonosító token idToken
   - a OAuth hozzáférési token accessToken
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

**Példák:**

1) Csoportok kibocsátása csoport neveként a OAuth hozzáférési jogkivonatokban dnsDomainName\sAMAccountName formátumban

    **Felhasználói felület konfigurációja:**

    [![Bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Alkalmazás jegyzékfájljának bejegyzése:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) A netbiosDomain\sAMAccountName formátumban visszaadott csoportok nevének kibocsátása az SAML-és OIDC-azonosító jogkivonatokban lévő szerepkörök jogcímként

    **Felhasználói felület konfigurációja:**

    [![Bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Alkalmazás jegyzékfájljának bejegyzése:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Választható jogcímek – példa

Ebben a szakaszban áttekintheti a forgatókönyvet, amelyből megtudhatja, hogyan használhatja a választható jogcímek funkciót az alkalmazásához.
Több lehetőség is rendelkezésre áll az alkalmazás identitás-konfigurációjának tulajdonságainak frissítésére az opcionális jogcímek engedélyezéséhez és konfigurálásához:

- Használhatja a jogkivonat- **konfigurációs** felhasználói felületet (lásd az alábbi példát)
- Használhatja a **jegyzékfájlt** (lásd az alábbi példát). Olvassa el az [Azure ad Application manifest-dokumentum megismerése című dokumentumot](./reference-app-manifest.md) a jegyzékfájl bevezetésének első lépéseit ismertető dokumentumban.
- Olyan alkalmazást is írhat, amely a [Microsoft Graph API](/graph/use-the-api?context=graph%2fapi%2f1.0&view=graph-rest-1.0) -t használja az alkalmazás frissítéséhez. A Microsoft Graph API-referenciák útmutatójának [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) -típusa segíthet a választható jogcímek konfigurálásában.

**Példa**

Az alábbi példában a **jogkivonat-konfigurációs** felhasználói felület és a **jegyzékfájl** használatával adhat hozzá opcionális jogcímeket az alkalmazáshoz szánt hozzáféréshez, azonosítóhoz és SAML-jogkivonatokhoz. Különböző választható jogcímek lesznek hozzáadva az egyes tokenekhez, amelyeket az alkalmazás fogadni tud:

- Az azonosító tokenek mostantól a teljes űrlapon () is tartalmazzák az összevont felhasználók egyszerű felhasználónevét `<upn>_<homedomain>#EXT#@<resourcedomain>` .
- Az alkalmazáshoz tartozó más ügyfelek által igényelt hozzáférési jogkivonatok mostantól tartalmazzák a auth_time jogcímet
- Az SAML-tokenek mostantól tartalmazzák a skypeId Directory sémakezelő bővítményt (ebben a példában az alkalmazáshoz tartozó ab603c56068041afb2f6832e2a17e237). Az SAML-tokenek a Skype ID-t teszik elérhetővé `extension_skypeId` .

**Felhasználói felület konfigurációja:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.

1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.

1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk**lehetőséget.

1. Keresse meg azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában, majd válassza ki azt.

1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció**elemet.

1. Válassza a **választható jogcím hozzáadása**lehetőséget, válassza ki az **azonosító** token típusát, válassza az **egyszerű felhasználónév** lehetőséget a jogcímek listájából, majd válassza a **Hozzáadás**lehetőséget.

1. Válassza a **választható jogcím hozzáadása**lehetőséget, válassza ki a **hozzáférési** token típusát, válassza a **auth_time** elemet a jogcímek listájából, majd válassza a **Hozzáadás**lehetőséget.

1. A jogkivonat-konfiguráció áttekintése képernyőn válassza az **egyszerű felhasználónév**melletti ceruza ikont, válassza a **külsőleg hitelesített** váltógomb elemet, majd kattintson a **Mentés**gombra.

1. Válassza az **opcionális jogcím hozzáadása**lehetőséget, válassza ki az **SAML** -token típusát, válassza a **extn. skypeID** elemet a jogcímek listájából (csak akkor érvényes, ha létrehozott egy skypeID nevű Azure ad felhasználói objektumot), majd válassza a **Hozzáadás**lehetőséget.

    [![Bemutatja, hogyan konfigurálhat választható jogcímeket a felhasználói felület használatával](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Jegyzékfájl konfigurációja:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.
1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
1. Keresse meg azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában, majd válassza ki azt.
1. A **kezelés** szakaszban válassza a **jegyzékfájl** lehetőséget a beágyazott jegyzékfájl-szerkesztő megnyitásához.
1. A jegyzékfájlt közvetlenül szerkesztheti a szerkesztő használatával. A jegyzékfájl az [alkalmazás entitásának](./reference-app-manifest.md)sémáját követi, és a mentés után automatikusan formázza a jegyzékfájlt. Új elemek lesznek hozzáadva a `OptionalClaims` tulajdonsághoz.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
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

1. Amikor befejezte a jegyzékfájl frissítését **, a Mentés gombra kattintva** mentheti a jegyzékfájlt.

## <a name="next-steps"></a>Következő lépések

További információ az Azure AD által biztosított standard jogcímekről.

- [Azonosító jogkivonatok](id-tokens.md)
- [Hozzáférési jogkivonatok](access-tokens.md)
