---
title: Ismerje meg, hogyan biztosíthat opcionális jogcímeket az Azure AD-alkalmazáshoz | Microsoft Docs
description: Útmutató egyéni vagy további jogcímek hozzáadásához a Azure Active Directory által kiadott SAML 2,0 és JSON web tokens (JWT) jogkivonatokhoz.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6e097df21051019495b3bf9bb6c83cb3dba03c8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835328"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Útmutató: Opcionális jogcímek megadása az Azure AD-alkalmazáshoz

Az alkalmazások fejlesztői használhatják az Azure AD-alkalmazásokban választható jogcímeket annak meghatározására, hogy mely jogcímeket szeretnék elküldeni az alkalmazásnak. 

A következő választható jogcímeket használhatja:

- Válassza ki az alkalmazáshoz tartozó jogkivonatokban szerepeltetni kívánt további jogcímeket.
- Módosítsa az Azure AD által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A standard jogcímek listájáért tekintse meg a [hozzáférési jogkivonat](access-tokens.md) és a [id_token](id-tokens.md) -jogcímek dokumentációját. 

Míg a választható jogcímek a v 1.0 és a v 2.0 formátumú jogkivonatokban, valamint az SAML-jogkivonatokban is támogatottak, az értékük nagy részét az 1.0 és a v 2.0 közötti váltáskor adja meg. A 2.0-s [Microsoft Identity platform végpontjának](active-directory-appmodel-v2-overview.md) egyik célja kisebb token-méret az ügyfelek optimális teljesítményének biztosítása érdekében. Ennek eredményeképpen számos, korábban a hozzáférési és azonosító jogkivonatban szereplő jogcím már nem található meg a v 2.0-s jogkivonatokban, és a kérést külön alkalmazási alapon kell megadnia.

**1. táblázat: Alkalmazhatósági**

| Fiók típusa | 1\.0-s verziós tokenek | v 2.0-tokenek  |
|--------------|---------------|----------------|
| Személyes Microsoft-fiók  | –  | Támogatott |
| Azure AD-fiók      | Támogatott | Támogatott |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 és v 2.0 választható jogcímek készlete

Az alábbi listában a használni kívánt alkalmazások alapértelmezett választható jogcímei érhetők el. Ha egyéni opcionális jogcímeket szeretne hozzáadni az alkalmazáshoz, tekintse meg az alábbi [címtárszolgáltatás](#configuring-directory-extension-optional-claims)-bővítményeket. Ha jogcímeket ad hozzá a **hozzáférési**jogkivonathoz, akkor ez az alkalmazáshoz (webes API-hoz) kért hozzáférési jogkivonatokra vonatkozik, nem az alkalmazás *által* . Ez biztosítja, hogy az ügyfél ne férhessen hozzá az API-hoz, a megfelelő adatok szerepelnek az API-val való hitelesítéshez használt hozzáférési jogkivonatban.

> [!NOTE]
> Ezeknek a jogcímeknek a többsége megadható a JWTs for 1.0 és v 2.0 tokenekhez, de nem SAML-tokenekhez, kivéve, ha a jogkivonat típusa oszlopban szerepel. A fogyasztói fiókok a "felhasználói típus" oszlopban megjelölt jogcímek egy részhalmazát támogatják.  A felsorolt jogcímek többsége nem vonatkozik a felhasználói felhasználókra (nem rendelkezik Bérlővel `tenant_ctry` , ezért nem rendelkezik értékkel).  

**2. táblázat: v 1.0 és v 2.0 opcionális jogcím-készlet**

| Name (Név)                       |  Leírás   | Jogkivonat típusa | Felhasználó típusa | Megjegyzések  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | A felhasználó utolsó hitelesítésének időpontja. Lásd: OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Az erőforrás-bérlő régiója | JWT        |           | |
| `home_oid`                 | Vendég felhasználók számára a felhasználó saját bérlője objektumának AZONOSÍTÓját.| JWT        |           | |
| `sid`                      | Munkamenet-azonosító, amely a felhasználónkénti felhasználói kijelentkezéshez használatos. | JWT        |  Személyes és Azure AD-fiókok.   |         |
| `platf`                    | Eszközplatform    | JWT        |           | Olyan felügyelt eszközökre korlátozódik, amelyek ellenőrizhetik az eszköz típusát.|
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail származik      | JWT        |           |         |
| `verified_secondary_email` | A felhasználó SecondaryAuthoritativeEmail származik   | JWT        |           |        |
| `enfpolids`                | Kényszerített szabályzat-azonosítók. Az aktuális felhasználó számára kiértékelt szabályzat-azonosítók listája. | JWT |  |  |
| `vnet`                     | A VNET megadására vonatkozó információk. | JWT        |           |      |
| `fwd`                      | IP-cím.| JWT    |   | Hozzáadja a kérelmező ügyfél eredeti IPv4-címe (egy VNET belül) |
| `ctry`                     | Felhasználó országa | JWT |  | Az Azure ad visszaadja a `ctry` választható jogcímet, ha a jelen van, és a jogcím értéke szabványos kétbetűs országkód, például fr, JP, sz stb. |
| `tenant_ctry`              | Erőforrás-bérlő országa | JWT | | |
| `xms_pdl`          | Elsődleges adatelérési hely   | JWT | | A multi-geo bérlők esetében ez a 3 betűs kód, amely a felhasználó földrajzi régióját mutatja. További információ: [Azure ad Connect dokumentáció az előnyben részesített adatok helyéről](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Például: `APC` Ázsia és a csendes-óceáni térség. |
| `xms_pl`                   | Felhasználó által előnyben részesített nyelv  | JWT ||A felhasználó által választott nyelv, ha be van állítva. A saját bérlőtől származik, a vendég hozzáférési forgatókönyvekben. Formázott LL-CC ("en-us"). |
| `xms_tpl`                  | Bérlő által előnyben részesített nyelv| JWT | | Az erőforrás-bérlő előnyben részesített nyelve, ha be van állítva. Formázott LL ("en"). |
| `ztdid`                    | Nulla érintéses telepítési azonosító | JWT | | A [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) szolgáltatáshoz használt eszköz identitása |
| `email`                    | A felhasználó címezhető e-mail-címe, ha a felhasználó rendelkezik ilyennel.  | JWT, SAML | MSA, Azure AD | Alapértelmezés szerint ez az érték szerepel, ha a felhasználó vendég a bérlőben.  A felügyelt felhasználók számára (a bérlőn belül) ezt a választható jogcímen keresztül kell kérni, vagy csak a 2.0-s verzióban az OpenID hatókörrel.  A felügyelt felhasználók esetében az e-mail-címet be kell állítani az [Office felügyeleti portálon](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Csoportos jogcímek opcionális formázása |JWT, SAML| |Az GroupMembershipClaims beállítással együtt használatos az [alkalmazás jegyzékfájljában](reference-app-manifest.md), amelyet is be kell állítani. Részletekért lásd az alábbi [csoportos](#Configuring-group-optional claims) jogcímeket. További információ a csoportos jogcímek [konfigurálásáról: csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Felhasználói fiók állapota a bérlőben. | JWT, SAML | | Ha a felhasználó tagja a bérlőnek, az érték `0`a. Ha vendég, az érték `1`a. |
| `upn`                      | UserPrincipalName claim. | JWT, SAML  |           | Bár ez a jogcím automatikusan szerepel, megadhatja opcionális jogcímként is, ha további tulajdonságokat szeretne csatolni a vendég felhasználói eset működésének módosításához.  |

### <a name="v20-optional-claims"></a>v 2.0 választható jogcímek

Ezeket a jogcímeket mindig tartalmazza a v 1.0 Azure AD-jogkivonatok, de a nem tartalmazza a v 2.0-tokeneket, kivéve, ha erre kérték. Ezek a jogcímek csak a JWTs (azonosító jogkivonatok és hozzáférési tokenek) esetén érvényesek. 

**3. táblázat: v 2.0 – csak opcionális jogcímek**

| JWT jogcím     | Name (Név)                            | Leírás                                | Megjegyzések |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-cím                      | Az ügyféltől bejelentkezett IP-cím.   |       |
| `onprem_sid`  | Helyi biztonsági azonosító |                                             |       |
| `pwd_exp`     | Jelszó lejárati ideje        | Az a dátum és idő, amikor a jelszó lejár. |       |
| `pwd_url`     | Jelszó URL-címének módosítása             | URL-cím, amelyet a felhasználó megkereshet a jelszavuk módosításához.   |   |
| `in_corp`     | Vállalati hálózaton belül        | Azt jelzi, hogy az ügyfél bejelentkezik-e a vállalati hálózatról. Ha nem, a rendszer nem tartalmazza a jogcímet.   |  Az MFA [megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -címeinek beállításai alapján.    |
| `nickname`    | Becenév.                        | A felhasználó további neve, amely az utónévtől és az utónévtől elválasztva. | 
| `family_name` | Vezetéknév                       | A felhasználó vezetéknevét, vezetéknevét vagy családjának nevét adja meg a felhasználói objektumban definiált módon. <br>"family_name":"Miller" | Támogatott a MSA és az Azure AD-ben   |
| `given_name`  | Utónév                      | A felhasználó első vagy "megadott" nevét adja meg a felhasználói objektumra vonatkozóan.<br>"given_name": Frank                   | Támogatott a MSA és az Azure AD-ben  |
| `upn`         | Egyszerű felhasználónév | A username_hint paraméterrel használható felhasználó termékazonosító.  Nem tartós azonosító a felhasználó számára, és nem használható a legfontosabb adathoz. | A jogcím konfigurálásához tekintse meg az alábbi [további tulajdonságokat](#additional-properties-of-optional-claims) . |

### <a name="additional-properties-of-optional-claims"></a>A választható jogcímek további tulajdonságai

Egyes választható jogcímek úgy konfigurálhatók, hogy megváltoztassák a jogcím visszaadásának módját. Ezek a további tulajdonságok többnyire a helyszíni alkalmazások különböző adatelérési elvárásokkal való áttelepítésének megkönnyítésére szolgálnak `include_externally_authenticated_upn_without_hash` (például olyan ügyfelek számára, akik nem kezelhetik a kivonatoló jeleket (`#`) az UPN-ben)

**4. táblázat: Választható jogcímek konfigurálásának értékei**

| Tulajdonság neve  | További tulajdonságnév | Leírás |
|----------------|--------------------------|-------------|
| `upn`          |                          | Az SAML-és JWT-válaszokhoz, valamint a 1.0-s és a v 2.0-tokenekhez is használható. |
|                | `include_externally_authenticated_upn`  | Az erőforrás-bérlőben tárolt vendég UPN-t tartalmazza. Például: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint a fenti, azzal a különbséggel`#`, hogy a kivonatoló jeleket () a`_`rendszer aláhúzással () váltja le, például:`foo_hometenant.com_EXT_@resourcetenant.com` |

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

Ez a OptionalClaims objektum azt eredményezi, hogy az azonosító token visszakerül az ügyfélnek, hogy egy másik UPN-t is tartalmazzon a további Kezdőlap bérlői és erőforrás-bérlői információk Ez csak akkor változtatja `upn` meg a jogkivonatban szereplő jogcímet, ha a felhasználó a bérlő egyik vendége (amely más identitásszolgáltató használ a hitelesítéshez). 

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Az alkalmazás jegyzékfájljának módosításával az alkalmazáshoz választható jogcímeket is konfigurálhat (lásd az alábbi példát). További információért lásd az [Azure ad Application manifest ismertetése című cikket](reference-app-manifest.md).

> [!IMPORTANT]
> A hozzáférési jogkivonatok **mindig** az erőforrás jegyzékfájljának használatával jönnek létre, nem az ügyfél.  Tehát a kérelemben `...scope=https://graph.microsoft.com/user.read...` az erőforrás gráf.  Így a hozzáférési jogkivonat a Graph manifest használatával jön létre, nem az ügyfél jegyzékfájlja.  Az alkalmazás jegyzékfájljának módosítása soha nem eredményezi a gráfok eltérő megjelenésének jogkivonatait.  Annak ellenőrzéséhez, hogy a `accessToken` módosítások érvényben vannak-e, igényeljen egy jogkivonatot az alkalmazáshoz, nem pedig egy másik alkalmazást.  

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

### <a name="optionalclaims-type"></a>OptionalClaims típusa

Deklarálja az alkalmazás által kért választható jogcímeket. Egy alkalmazás konfigurálhat választható jogcímeket is, amelyek a biztonsági jogkivonat szolgáltatástól kapott három jogkivonat (azonosító token, hozzáférési jogkivonat, SAML 2 token) mindhárom típusában visszatérhetnek. Az alkalmazás különböző választható jogcímeket konfigurálhat az egyes token-típusokban való visszatéréshez. Az alkalmazás entitás OptionalClaims tulajdonsága egy OptionalClaims objektum.

**5. táblázat: OptionalClaims típusának tulajdonságai**

| Name (Név)        | Típus                       | Leírás                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Gyűjtemény (OptionalClaim) | A JWT azonosító jogkivonatában visszaadott választható jogcímek. |
| `accessToken` | Gyűjtemény (OptionalClaim) | Az JWT hozzáférési jogkivonatban visszaadott választható jogcímek. |
| `saml2Token`  | Gyűjtemény (OptionalClaim) | Az SAML-jogkivonatban visszaadott választható jogcímek.   |

### <a name="optionalclaim-type"></a>OptionalClaim típusa

Egy alkalmazáshoz vagy egy egyszerű szolgáltatáshoz társított opcionális jogcímet tartalmaz. A [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) típus IdToken, AccessToken és saml2Token tulajdonsága a OptionalClaim gyűjteménye.
Ha egy adott jogcím támogatja, a OptionalClaim viselkedését a AdditionalProperties mező használatával is módosíthatja.

**6. táblázat: OptionalClaim típusának tulajdonságai**

| Name (Név)                 | Típus                    | Leírás                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcím neve.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A jogcím forrása (Directory-objektum). A bővítmény tulajdonságaiban előre definiált jogcímek és felhasználó által definiált jogcímek találhatók. Ha a forrás értéke null, a jogcím egy előre meghatározott opcionális jogcím. Ha a forrás értéke felhasználó, a Name (név) tulajdonság értéke a felhasználói objektum kiterjesztés tulajdonsága. |
| `essential`            | Edm.Boolean             | Ha az érték TRUE (igaz), akkor az ügyfél által megadott jogcím szükséges a végfelhasználó által kért feladat zökkenőmentes engedélyezési élményének biztosításához. Az alapértelmezett értéke FALSE (hamis).                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | A jogcím további tulajdonságai. Ha egy tulajdonság létezik ebben a gyűjteményben, a módosítja a Name (név) tulajdonságban megadott választható jogcím viselkedését.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>A címtár-bővítmény választható jogcímeinek konfigurálása

A standard opcionális jogcímek beállítása mellett a tokeneket is konfigurálhatja a címtár-séma bővítményeinek belefoglalásához. További információ: [Directory sémakezelő bővítmények](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Ez a funkció hasznos lehet az alkalmazás által használható további felhasználói adatok csatolásához – például egy további azonosító vagy fontos konfigurációs beállítás, amelyet a felhasználó beállított. 

> [!Note]
> - A címtár-séma bővítményei egy csak Azure AD-szolgáltatás, így ha az alkalmazás jegyzékfájlja egyéni kiterjesztést kér, és egy MSA-felhasználó bejelentkezik az alkalmazásba, akkor ezeket a bővítményeket a rendszer nem adja vissza.
> - Az Azure AD opcionális jogcímei csak az Azure AD bővítménnyel működnek, és nem működnek együtt a Microsoft Graph Directory bővítménnyel. Mindkét API-nak `Directory.ReadWriteAll` engedélyre van szüksége, amely csak a rendszergazdák által adható meg.

### <a name="directory-extension-formatting"></a>Címtárszolgáltatás-bővítmény formázása

A bővítmény attribútumaihoz használja a bővítmény teljes nevét (formátum: `extension_<appid>_<attributename>`) az alkalmazás jegyzékfájljában. Az `<appid>` értéknek meg kell egyeznie a jogcímet kérő alkalmazás azonosítójával. 

A JWT belül ezeket a jogcímeket a következő formátumnév fogja kiadni: `extn.<attributename>`.

Az SAML-tokeneken belül ezek a jogcímek a következő URI-formátummal lesznek kibocsátva:`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Csoportos választható jogcímek konfigurálása

   > [!NOTE]
   > A helyi verzióról szinkronizált felhasználók és csoportok számára a csoportok nevének kiadásának lehetősége nyilvános előzetes verzió.

Ez a szakasz azokat a konfigurációs beállításokat ismerteti, amelyek a választható jogcímek területen az alapértelmezett csoport objectID a helyi Windows Active Directoryról szinkronizált attribútumokra vonatkozóan használt csoportok attribútumainak módosítására használhatók.

> [!IMPORTANT]
> További részletekért tekintse meg a [csoportos jogcímek konfigurálása alkalmazások számára az Azure ad-vel](../hybrid/how-to-connect-fed-group-claims.md) című témakört, beleértve a helyszíni attribútumok nyilvános előzetes verziójára vonatkozó fontos figyelmeztetéseket.

1. A portálon – > Azure Active Directory – > alkalmazás regisztrációi – > válassza az alkalmazás – > jegyzékfájlt

2. Csoporttagság-jogcímek engedélyezése a groupMembershipClaim módosításával

   Az érvényes értékek a következők:

   - Összes
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Példa:

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
   | **additionalProperties:** | További tulajdonságok listája.  Az érvényes beállítások a következők: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   A additionalProperties csak a "sam_account_name", a "dns_domain_and_sam_account_name", a "netbios_domain_and_sam_account_name" egyike szükséges.  Ha egynél több van jelen, az első használatban van, és minden más figyelmen kívül lesz hagyva.

   Egyes alkalmazások a szerepkör-jogcímben szereplő felhasználóra vonatkozó csoportos adatokat igényelnek.  Ha módosítani szeretné a jogcím típusát egy csoport jogcímen egy szerepkör-jogcímre, adja hozzá a "emit_as_roles" elemet a további tulajdonságokhoz.  A csoport értékeit a rendszer a szerepkör-jogcímben fogja kiállítani.

   > [!NOTE]
   > Ha a "emit_as_roles" minden olyan alkalmazási szerepkört használ, amelyhez a felhasználó hozzá van rendelve, nem jelenik meg a szerepkör-jogcímben.

**Példák:** Csoportok kibocsátása csoport neveként a OAuth hozzáférési jogkivonatokban dnsDomainName\sAMAccountName formátumban

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Az SAML-és OIDC-azonosító jogkivonatokban a netbiosDomain\sAMAccountName formátumban visszaadott csoportok nevének kibocsátása:

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
 }

 ```

## <a name="optional-claims-example"></a>Választható jogcímek – példa

Ebben a szakaszban áttekintheti a forgatókönyvet, amelyből megtudhatja, hogyan használhatja a választható jogcímek funkciót az alkalmazásához.
Több lehetőség is rendelkezésre áll az alkalmazás identitás-konfigurációjának tulajdonságainak frissítésére az opcionális jogcímek engedélyezéséhez és konfigurálásához:
-   Módosíthatja az alkalmazás jegyzékfájlját. Az alábbi példa ezt a módszert fogja használni a konfiguráció elvégzéséhez. Olvassa el az [Azure ad Application manifest-dokumentum megismerése című dokumentumot](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) a jegyzékfájl bevezetésének első lépéseit ismertető dokumentumban.
-   Olyan alkalmazást is írhat, amely a [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) használatával frissíti az alkalmazást. Az Graph API hivatkozási útmutatóban található [entitás és összetett típus hivatkozása](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) segítséget nyújt a választható jogcímek konfigurálásához.

**Példa:** Az alábbi példában egy alkalmazás jegyzékfájlját fogja módosítani, amely az alkalmazáshoz szánt hozzáférési, azonosító és SAML-tokenekhez ad hozzá jogcímeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában található elem kiválasztásával.
1. Válassza ki az **alkalmazás regisztrációját** a bal oldali oldalon.
1. Keresse meg azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni, és kattintson rá.
1. Az alkalmazás lapon kattintson a **manifest (jegyzékfájl** ) elemre a beágyazott jegyzékfájl-szerkesztő megnyitásához. 
1. A jegyzékfájlt közvetlenül szerkesztheti a szerkesztő használatával. A jegyzékfájl az [alkalmazás entitásának](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)sémáját követi, és a mentés után automatikusan formázza a jegyzékfájlt. Új elemek lesznek hozzáadva a `OptionalClaims` tulajdonsághoz.

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

    Ebben az esetben különböző választható jogcímek lettek hozzáadva az egyes tokenekhez, amelyeket az alkalmazás fogadni tud. Az azonosító tokenek mostantól a teljes űrlapon (`<upn>_<homedomain>#EXT#@<resourcedomain>`) is tartalmazzák az összevont felhasználók egyszerű felhasználónevét. Az alkalmazáshoz tartozó más ügyfelek által igényelt hozzáférési jogkivonatok mostantól a auth_time jogcímet is tartalmazzák. Az SAML-tokenek mostantól tartalmazzák a skypeId Directory sémakezelő bővítményt (ebben a példában az alkalmazáshoz tartozó ab603c56068041afb2f6832e2a17e237). Az SAML-tokenek a Skype ID-t `extension_skypeId`teszik elérhetővé.

1. Amikor befejezte a jegyzékfájl frissítését, kattintson a **Mentés** gombra a jegyzékfájl mentéséhez

## <a name="next-steps"></a>További lépések

További információ az Azure AD által biztosított standard jogcímekről.

- [AZONOSÍTÓ jogkivonatok](id-tokens.md)
- [Hozzáférési jogkivonatok](access-tokens.md)
