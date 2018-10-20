---
title: Az Azure AD-bérlő (nyilvános előzetes verzió) egy adott alkalmazáshoz tartozó jogkivonatok a kibocsátott jogcímek testreszabása
description: Ezen a lapon jogcímtársítások az Azure Active Directory ismerteti.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin, jeedes
ms.openlocfilehash: dbe903f502cbd1b96b34b47aed09c52104995b54
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466205"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Útmutató: a bérlőben (előzetes verzió) egy adott alkalmazáshoz tartozó jogkivonatok kibocsátott jogcímek testreszabása

> [!NOTE]
> Ez a szolgáltatás váltja fel, és hatályon kívül helyez a [jogcímek testreszabása](active-directory-saml-claims-customization.md) jelenleg elérhető a portálon keresztül. A ugyanazt az alkalmazást Ha testre szabhatja a jogcímek, az ebben a dokumentumban részletes Graph vagy PowerShell-módszer mellett a portál használatával jogkivonatok ki, hogy alkalmazás figyelmen kívül hagyja a konfiguráció a portálon. Ebben a dokumentumban ismertetett módszerek használatával végzett konfigurációk nem tükröződnek a portálon.

Ez a funkció segítségével bérlői rendszergazdák által a jogkivonatokban bérlőben egy adott alkalmazáshoz kibocsátott jogcímek testreszabása. A jogcím-hozzárendelési szabályzatokat használhatja:

- Válassza ki, melyik jogcímeket jogkivonatok szerepelnek.
- Hozzon létre a jogcímtípusok, amely még nem léteznek.
- Válassza ki, vagy módosítsa az adott jogcímeket bocsátja ki az adatforrást.

> [!NOTE]
> Ez a lehetőség jelenleg nyilvános előzetes verzióban érhető el. Készüljön fel a módosítások visszavonására vagy eltávolítására. A szolgáltatás bármely Azure Active Directory (Azure AD) az előfizetésben a nyilvános előzetes verzióban érhető el. Amikor a szolgáltatás általánosan elérhetővé válik, azonban a szolgáltatást bizonyos aspektusainak szükség lehet egy Azure AD premium előfizetéssel. Ez a funkció konfigurálását jogcím a szabályzat-hozzárendelés támogatja a WS-Fed, SAML, OAuth és OpenID Connect protokollok.

## <a name="claims-mapping-policy-type"></a>Jogcímek társítása a házirend típusa

Az Azure AD-ben egy **házirend** objektum képviseli, az egyes alkalmazásokra, illetve a szervezeten belüli összes alkalmazás által kényszerített szabályai. Minden házirend rendelkezik egy egyedi szerkezetűek, és a egy sor tulajdonságból, majd alkalmazott objektumok, amelyek hozzá vannak rendelve.

A jogcím-szabályzat hozzárendelése egy olyan típusú **házirend** objektum, amely módosítja az egyes alkalmazásoknak kiállított jogkivonatokban bocsásson ki jogcímeket.

## <a name="claim-sets"></a>Jogcímszabály-készletek

Vannak bizonyos csoportok, amelyek meghatározzák, hogy mikor és hogyan használhatók a jogkivonatok jogcímek.

| Jogcímek készletéhez | Leírás |
|---|---|
| Core jogcímek készletéhez | Függetlenül a szabályzat minden jogkivonatban jelen. Ezeket a jogcímeket is számítanak korlátozott, és nem módosítható. |
| Alapszintű jogcímek készletéhez | A jogkivonatok (mellett a core jogcímkészlethez) alapértelmezés szerint vannak kibocsátott jogcímeket tartalmaz. Nincs megadva, vagy alapszintű jogcímek módosítása a jogcímtársítások szabályzatok használatával. |
| Korlátozott jogcímek készletéhez | Nem módosítható a csoportházirend használatával. Az adatforrás nem lehet módosítani, és átalakítás nélkül alkalmazza ezeket a jogcímeket készítésekor. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>1. táblázat: JSON webes jogkivonat (JWT) korlátozott jogcímek készletéhez

|Jogcím típusa (név)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|aktor|
|actortoken|
|aio-ra|
|altsecid|
|AMR|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|alkalmazásazonosító|
|appidacr|
|assertion|
|at_hash|
|AUD|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|cnf|
|Kód|
|vezérlők|
|credential_keys|
|CSR-fájl|
|csr_type|
|az eszközazonosító|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|e-mail|
|endpoint|
|enfpolids|
|Exp|
|expires_on|
|grant_type|
|gráf|
|group_sids|
|csoportok|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier|
|IAT|
|identitásszolgáltató|
|identitásszolgáltató|
|in_corp|
|példány|
|IPcím|
|isbrowserhostedapp|
|iss|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|NBF|
|netbios_name|
|egyszeri|
|objektumazonosító|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|jelszó|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|PUID azonosítója|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|erőforrás|
|szerepkör|
|roles|
|scope|
|Szolgáltatáskapcsolódási pont|
|biztonsági azonosító|
|aláírás|
|signin_state|
|src1|
|src2|
|Sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|TID|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|egyszerű felhasználónév|
|user_setting_sync_url|
|felhasználónév|
|uti|
|ver|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

### <a name="table-2-saml-restricted-claim-set"></a>2. táblázat: SAML korlátozott jogcímek készletéhez

|Jogcím típusa (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.microsoft.com/identity/claims/accesstoken|
|http://schemas.microsoft.com/identity/claims/openid2_id|
|http://schemas.microsoft.com/identity/claims/identityprovider|
|http://schemas.microsoft.com/identity/claims/objectidentifier|
|http://schemas.microsoft.com/identity/claims/puid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1] |
|http://schemas.microsoft.com/identity/claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groups|
|http://schemas.microsoft.com/claims/groups.link|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/role|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier|
|http://schemas.microsoft.com/identity/claims/scope|

## <a name="claims-mapping-policy-properties"></a>Jogcímek társítása a házirend tulajdonságai

Szabályozhatja a rendszer kibocsátott jogcímek és az adatok forrását, használja a jogcímek társítása a házirend tulajdonságait. Ha egy szabályzat nincs beállítva, a rendszer problémák jogkivonatok, amelyek tartalmazzák az alapvető jogcím beállított, az egyszerű jogcímek készletéhez és bármilyen [választható jogcímek](active-directory-optional-claims.md) , amely az alkalmazás úgy döntött, hogy fogadni.

### <a name="include-basic-claim-set"></a>Alapszintű jogcímkészlethez belefoglalása

**Karakterlánc:** IncludeBasicClaimSet

**Adattípus:** logikai érték (IGAZ vagy hamis)

**Összefoglalás:** Ez a tulajdonság határozza meg, hogy az egyszerű jogcímek készletében szerepel-e a jogkivonatokban Ez a szabályzat által érintett. 

- Ha igaz értékű, az egyszerű jogcímek készletében lévő összes jogcímek a jogkivonatokban, a szabályzat által érintett kibocsátása megtörténik. 
- Ha az értéke HAMIS, az egyszerű jogcímek készletében lévő jogcímek nem szerepelnek a jogkivonatokat, kivéve, ha ugyanaz a szabályzat jogcímek séma tulajdonságában külön-külön kerül.

> [!NOTE] 
> A core jogcímek készletében lévő jogcímek szerepelnek minden jogkivonatot, függetlenül attól, milyen Ez a tulajdonság értéke. 

### <a name="claims-schema"></a>Jogcímek séma

**Karakterlánc:** ClaimsSchema

**Adattípus:** JSON-blobját és egy vagy több jogcím séma

**Összefoglalás:** Ez a tulajdonság határozza meg, milyen jogcímek szerepelnek a jogkivonatokat, az alapszintű jogcímkészlethez emellett a szabályzat által érintett, és az alapvető jogcímek készletéhez.
Minden egyes jogcím séma bejegyzés meghatározott ezt a tulajdonságot bizonyos információra szükség. Adja meg az adatok forrását (**érték** vagy **adatforrás és az azonosító pár**), és amely az adatok jogcím többszöröseként (**jogcím típusa**).

### <a name="claim-schema-entry-elements"></a>Jogcím-sémájában található elemek bejegyzés

**Érték:** érték elem egy állandó érték határozza meg az adatokat a jogcímet bocsátja ki.

**Adatforrás és az azonosító pár:** a forrás- és azonosító elemek határozza meg, ahol az adatokat a jogcímek forrása. 

Állítsa be a forrás elem a következő értékek egyikére: 

- "user": a jogcím adatai egy tulajdonságot a User objektum. 
- "alkalmazás": a jogcím adatai egy egyszerű alkalmazás (ügyfél) szolgáltatás tulajdonságára. 
- "erőforrás": a jogcím adatai a szolgáltatás egyszerű erőforrás-tulajdonságok.
- "célközönség": a jogcím adatai a szolgáltatásnevet, amely a közönség a jogkivonat (az ügyfél vagy az erőforrás egyszerű szolgáltatásnevének) tulajdonsága.
- "Vállalati": a jogcím adatai egy tulajdonság az erőforrás-bérlői vállalati objektumon.
- "átalakítás": a jogcím adatai a jogcímek átalakításáról (lásd a "Jogcím-átalakítás" szakaszt a cikk későbbi részében).

Ha a forrás átalakítási a **TransformationID** elemének szerepelnie kell a jogcím-definíció is.

Az azonosító elem azonosítja, mely tulajdonság a forrás az értéket ad meg a jogcímet. Az alábbi táblázat a forrás minden egyes érték érvényes azonosító értékét.

#### <a name="table-3-valid-id-values-per-source"></a>3. táblázat: Érvényes azonosító értéket, forrás

|Forrás|ID (Azonosító)|Leírás|
|-----|-----|-----|
|Felhasználó|Vezetéknév|Család neve|
|Felhasználó|givenName|utónév;|
|Felhasználó|DisplayName|Megjelenítendő név|
|Felhasználó|objektumazonosító|ObjectID|
|Felhasználó|levelezés|E-mail-cím|
|Felhasználó|userPrincipalName|Felhasználó egyszerű neve|
|Felhasználó|Szervezeti egység|Részleg|
|Felhasználó|onpremisessamaccountname|A helyi Sam-fiók neve|
|Felhasználó|netbiosname|NetBios-név|
|Felhasználó|dnsdomainname|DNS-tartománynév|
|Felhasználó|onpremisesecurityidentifier|a helyszíni biztonsági azonosítója|
|Felhasználó|Cégnév|Szervezet neve|
|Felhasználó|streetAddress|Utca, házszám|
|Felhasználó|Irányítószám|Irányítószám|
|Felhasználó|preferredlanguange|Elsődleges nyelv|
|Felhasználó|onpremisesuserprincipalname|a helyszíni egyszerű Felhasználónévvel|
|Felhasználó|mailnickname|Levelezési becenév|
|Felhasználó|extensionAttribute1|Mellék attribútum 1|
|Felhasználó|extensionattribute2|Mellék attribútum 2|
|Felhasználó|extensionattribute3|Mellék attribútum 3|
|Felhasználó|extensionattribute4|Mellék attribútum 4|
|Felhasználó|extensionattribute5|Mellék attribútum 5|
|Felhasználó|extensionattribute6|Mellék attribútum 6|
|Felhasználó|extensionattribute7|Mellék attribútum 7|
|Felhasználó|extensionattribute8|Mellék attribútum 8|
|Felhasználó|extensionattribute9|Mellék attribútum 9|
|Felhasználó|extensionattribute10|Mellék attribútum 10|
|Felhasználó|extensionattribute11|Mellék attribútum 11|
|Felhasználó|extensionattribute12|Mellék attribútum 12|
|Felhasználó|extensionattribute13|Mellék attribútum 13|
|Felhasználó|extensionattribute14|Mellék attribútum 14|
|Felhasználó|extensionattribute15|Mellék attribútum 15|
|Felhasználó|othermail|Egyéb E-mail|
|Felhasználó|Ország|Ország|
|Felhasználó|city|Város|
|Felhasználó|state|Állapot|
|Felhasználó|Beosztás|Beosztás|
|Felhasználó|EmployeeID|Alkalmazott azonosítója|
|Felhasználó|facsimiletelephonenumber|Fax telefonszáma|
|alkalmazás, erőforrás, célközönség|DisplayName|Megjelenítendő név|
|alkalmazás, erőforrás, célközönség|hozzáadást|ObjectID|
|alkalmazás, erőforrás, célközönség|tags|Egyszerű szolgáltatás címke|
|Vállalat|tenantcountry|Bérlő országában|

**TransformationID:** a TransformationID elemhez meg kell adni, csak akkor, ha a forrás elem "átalakítás" értékre van állítva.

- Ez az elem átalakítása bejegyzését azonosító eleme, meg kell egyeznie a **ClaimsTransformation** tulajdonság, amely meghatározza, hogyan generáljon a rendszer az adatokat a jogcímet.

**: Jogcímtípus** a **JwtClaimType** és **SamlClaimType** elemek határozza meg, amely jogcímet a jogcím-séma bejegyzés hivatkozik.

- A JwtClaimType JWTs bocsátjuk rendelkezésre a jogcím nevét kell tartalmaznia.
- A SamlClaimType tartalmaznia kell az URI azonosító a jogcím az SAML-jogkivonatok bocsátjuk rendelkezésre.

> [!NOTE]
> A jogcím típusa elemek nevét és a korlátozott jogcímek készletében lévő jogcímek, az URI-k nem használható. További információkért lásd a "Kivételek és korlátozások" szakaszt a cikk későbbi részében.

### <a name="claims-transformation"></a>Jogcím-átalakítás

**Karakterlánc:** ClaimsTransformation

**Adattípus:** JSON-blobját, és egy vagy több átalakítása 

**Összefoglalás:** ezt a tulajdonságot használja az olyan gyakori átalakítást alkalmazni a forrásadatokat, a jogcímek sémában megadott jogcímek a kimeneti adatok előállításához.

**ID:** az azonosító elem segítségével az átalakítást a tételben szereplő TransformationID jogcímek séma tétel hivatkozhat. Ez az érték belül a házirend minden átalakítási bejegyzés esetében egyedinek kell lennie.

**TransformationMethod:** TransformationMethod elem azonosítja, hogy melyik műveletet hajtja végre a jogcím az adatok létrehozásához.

A kiválasztott módszert alapján, bemenetek és kimenetek várható. A bemenetek és kimenetek megadása segítségével a **InputClaims**, **InputParameters** és **OutputClaims** elemeket.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>4. táblázat: Átalakítás módszerek és a várt bemenetek és kimenetek

|TransformationMethod|Várt bemenet|Várt kimenet|Leírás|
|-----|-----|-----|-----|
|Csatlakozás|Szöveg1, karakterlánc2, elválasztó|kimeneti jogcím|Illesztések karakterláncok adjon meg a kettő között az elválasztó használatával. Például: szöveg1: "foo@bar.com", karakterlánc2: "védőfal mögött", elválasztó: "." kimeneti jogcím eredményezi: "foo@bar.com.sandbox"|
|ExtractMailPrefix|levelezés|kimeneti jogcím|Kinyeri egy e-mail-címet a helyi részét. Például: mail: "foo@bar.com" kimeneti jogcím eredményezi: "foo". Ha nincs \@ bejelentkezési jelen, akkor az eredeti a bemeneti karakterláncot ad vissza, mert.|

**InputClaims:** InputClaims elem használatával az adatokat átadni egy jogcím-séma bejegyzést az átalakítást. Két attribútum rendelkezik: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **ClaimTypeReferenceId** a jogcím séma bejegyzés található a megfelelő bemeneti jogcím azonosítója elemmel csatlakozik. 
- **TransformationClaimType** adjon meg egy egyedi nevet a bemeneti szolgál. Ezt a nevet meg kell egyeznie az átalakítási metódus a várt bemenetek egyike.

**InputParameters:** állandó értéket adnak át egy átalakítás InputParameters elem használatával. Két attribútum rendelkezik: **érték** és **azonosító**.

- **Érték** átadni a tényleges konstans érték.
- **ID** adjon meg egy egyedi nevet a bemeneti szolgál. A névnek egyeznie kell az átalakítási metódus a várt bemenetek egyike.

**OutputClaims:** OutputClaims elem használja egy átalakítás által létrehozott adatok tárolására, és hozzákötését egy jogcím-séma bejegyzést. Két attribútum rendelkezik: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **ClaimTypeReferenceId** azonosítójú, a jogcím-séma bejegyzés található a megfelelő kimeneti jogcímek tartományhoz csatlakozik.
- **TransformationClaimType** adjon meg egy egyedi nevet a kimenetre szolgál. A névnek egyeznie kell a várt kimeneti az átalakítási mód közül.

### <a name="exceptions-and-restrictions"></a>Kivételek és korlátozások

**Az SAML NameID- és egyszerű felhasználónév:** , amelyről forrás a NameID és UPN értékeket, és a jogcímek átalakítása, amelyeknél engedélyezve van, az attribútumok korlátozva. Tekintse meg az 5 és 6 engedélyezett értékeit tábla.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>5. táblázat: Attribútumok engedélyezett adatforrásként SAML NameID

|Forrás|ID (Azonosító)|Leírás|
|-----|-----|-----|
|Felhasználó|levelezés|E-mail-cím|
|Felhasználó|userPrincipalName|Felhasználó egyszerű neve|
|Felhasználó|onpremisessamaccountname|A helyi Sam-fiók neve|
|Felhasználó|EmployeeID|Alkalmazott azonosítója|
|Felhasználó|extensionAttribute1|Mellék attribútum 1|
|Felhasználó|extensionattribute2|Mellék attribútum 2|
|Felhasználó|extensionattribute3|Mellék attribútum 3|
|Felhasználó|extensionattribute4|Mellék attribútum 4|
|Felhasználó|extensionattribute5|Mellék attribútum 5|
|Felhasználó|extensionattribute6|Mellék attribútum 6|
|Felhasználó|extensionattribute7|Mellék attribútum 7|
|Felhasználó|extensionattribute8|Mellék attribútum 8|
|Felhasználó|extensionattribute9|Mellék attribútum 9|
|Felhasználó|extensionattribute10|Mellék attribútum 10|
|Felhasználó|extensionattribute11|Mellék attribútum 11|
|Felhasználó|extensionattribute12|Mellék attribútum 12|
|Felhasználó|extensionattribute13|Mellék attribútum 13|
|Felhasználó|extensionattribute14|Mellék attribútum 14|
|Felhasználó|extensionattribute15|Mellék attribútum 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>6. táblázat: Átalakítás módszerek SAML NameID engedélyezett

|TransformationMethod|Korlátozások|
| ----- | ----- |
|ExtractMailPrefix|None|
|Csatlakozás|Az utótag csatlakoztatott folyamatban van az erőforrás-bérlőkulcs ellenőrzött tartományt kell lennie.|

### <a name="custom-signing-key"></a>Egyéni kulcs aláírása

Egyéni aláíró kulcs hozzá kell rendelni egy jogcímtársítások házirend érvénybe a szolgáltatásnév-objektum. Az egyéni aláírókulccsal aláírt kiadott összes jogkivonat, a szabályzat által lett érintő, és fogadja el a jogkivonatok alkalmazásokat kell konfigurálni a aláírókulccsal aláírt. Ez biztosítja, hogy a jogkivonatok létrehozója a jogcímek társítása a házirend által módosítva lett, és úgy védi az alkalmazásokat kártékony szándékú felhasználók által létrehozott házirendek hozzárendelése jogcímekből származó nyugtázása.

### <a name="cross-tenant-scenarios"></a>Több-bérlős forgatókönyvek

Vendégfelhasználók jogcímtársítások szabályzatok nem vonatkoznak. Amennyiben a Vendég felhasználó egy jogcímtársítások a szolgáltatásnévhez hozzárendelt szabályzat az alkalmazás eléréséhez, az alapértelmezett jogkivonat kiadott (a házirend nem lesz hatása).

## <a name="claims-mapping-policy-assignment"></a>Jogcímtársítások szabályzat-hozzárendelés

Szabályzatok hozzárendelése jogcímek csak egyszerű szolgáltatási objektumok lehet hozzárendelni.

### <a name="example-claims-mapping-policies"></a>A példában a szabályzat-hozzárendelés jogcímek

Az Azure AD-ben számos forgatókönyv akkor lehet, ha testre szabhatja a tokenek adott szolgáltatásnevek bocsátja ki jogcímeket. Ez a szakasz ismerteti azokat a lépéseket néhány olyan gyakori helyzetet, amelyek segítségével hogyan használhatja a jogcímek társítása a házirend típusát bonyolultnak keresztül.

#### <a name="prerequisites"></a>Előfeltételek

A következő példákban, létrehozása, frissítése, hivatkozás és házirendek a szolgáltatásnevek törlése. Ha most ismerkedik az Azure ad-ben, azt javasoljuk, hogy Ön [ismerje meg az Azure AD-bérlő beszerzése](quickstart-create-new-tenant.md) ezekben a példákban a folytatás előtt.

A kezdéshez kövesse az alábbi lépéseket:

1. Töltse le a legújabb [Azure AD PowerShell-modul nyilvános előzetes kiadás](https://www.powershellgallery.com/packages/AzureADPreview).
1. A Connect paranccsal jelentkezzen be az Azure AD rendszergazdai fiókot. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenet indításához.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
1. A szervezetben létrehozott összes szabályzat megtekintéséhez futtassa a következő parancsot. Azt javasoljuk, hogy a parancs futtatása után a következő esetekben, ellenőrizze, hogy a szabályzatok a várt módon létrehozott legtöbb művelet.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Példa: Szabályzat létrehozása és hozzárendelése egy üresen hagyni az alapszintű jogcímeket egy egyszerű szolgáltatás a kiállított jogkivonatokban.
Ebben a példában létrehozott egy szabályzatot, amely az alapvető jogcímek készletében távolít el a társított szolgáltatás rendszerbiztonsági tagok kiállított jogkivonatokban.

1. Hozzon létre egy jogcímek társítása a házirend. Ez a házirend-specifikus társított szolgáltatás egyszerű, az alapszintű jogcímek a jogkivonatok eltávolítja.
    1. A szabályzat létrehozásához a következő parancs futtatásával: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Az új házirend megtekintéséhez, és hogy lekérje a házirendet ObjectId, futtassa a következő parancsot:
    
     ``` powershell
    Get-AzureADPolicy
    ```
1. A szabályzat hozzárendelése az egyszerű szolgáltatást. Is kell objectid azonosítóját, a szolgáltatás egyszerű beolvasása. 
    1.  A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti a Microsoft Graph. Másik lehetőségként az Azure AD Graph Explorer, jelentkezzen be az Azure AD-fiókot.
    2.  Ha objectid azonosítóját, az egyszerű szolgáltatás rendelkezik, futtassa a következő parancsot:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Példa: Szabályzat létrehozása és hozzárendelése egy, az EmployeeID és TenantCountry jogcímként szerepeljenek a szolgáltatásnévvel való kiállított jogkivonatokban

Ebben a példában létrehozott egy szabályzatot, amely az EmployeeID és TenantCountry ad hozzá a társított szolgáltatás rendszerbiztonsági tagok kiállított jogkivonatokban. Az EmployeeID kibocsátott SAML-jogkivonatok és JWTs neve jogcím típusaként. Az ország jogcímtípus, a SAML-jogkivonatok és JWTs a TenantCountry áll rendelkezésre. Ebben a példában továbbra is az alapszintű, állítsa be a jogkivonatokat a jogcímeket tartalmaznak.

1. Hozzon létre egy jogcímek társítása a házirend. Ez a szabályzat adott szolgáltatásnevek, kapcsolódó jogkivonatok az EmployeeID és TenantCountry jogcímeket hozzáadja.
    1. A szabályzat létrehozásához a következő parancs futtatásával:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
    ```
    
    2. Az új házirend megtekintéséhez, és hogy lekérje a házirendet ObjectId, futtassa a következő parancsot:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
1. A szabályzat hozzárendelése az egyszerű szolgáltatást. Is kell objectid azonosítóját, a szolgáltatás egyszerű beolvasása. 
    1.  A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti a Microsoft Graph. Másik lehetőségként az Azure AD Graph Explorer, jelentkezzen be az Azure AD-fiókot.
    2.  Ha objectid azonosítóját, az egyszerű szolgáltatás rendelkezik, futtassa a következő parancsot:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Példa: Szabályzat létrehozása és hozzárendelése egy a tokenek egy egyszerű szolgáltatásnak egy jogcím-átalakítás használó

Ebben a példában létrehozott egy szabályzatot, amely egy egyéni jogcím "JoinedData" társított szolgáltatás rendszerbiztonsági tagok kiadott JWTs bocsát ki. Ez a jogcím a extensionattribute1 attribútum a user objektum ".sandbox" a tárolt adatok lett létrehozva értéket tartalmaz. Ebben a példában Elzárkózunk beállítása a jogkivonatok az alapszintű jogcímeket.

1. Hozzon létre egy jogcímek társítása a házirend. Ez a szabályzat adott szolgáltatásnevek, kapcsolódó jogkivonatok az EmployeeID és TenantCountry jogcímeket hozzáadja.
    1. A szabályzat létrehozásához a következő parancs futtatásával: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy" 
    ```
    
    2. Az új házirend megtekintéséhez, és hogy lekérje a házirendet ObjectId, futtassa a következő parancsot: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
1. A szabályzat hozzárendelése az egyszerű szolgáltatást. Is kell objectid azonosítóját, a szolgáltatás egyszerű beolvasása. 
    1.  A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti a Microsoft Graph. Másik lehetőségként az Azure AD Graph Explorer, jelentkezzen be az Azure AD-fiókot.
    2.  Ha objectid azonosítóját, az egyszerű szolgáltatás rendelkezik, futtassa a következő parancsot: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
