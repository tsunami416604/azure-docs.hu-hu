---
title: "Jogcím-leképezés az Azure Active Directoryban (nyilvános előzetes verzió) |} Microsoft Docs"
description: "Ez a lap leképezési Azure Active Directory jogcímszolgáltatói ismerteti."
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: billmath
ms.openlocfilehash: 6f5ca44e08c783fdf22a14d71c56c3019cc2bb52
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>A jogcímek hozzárendelése az Azure Active Directoryban (nyilvános előzetes verzió)

>[!NOTE]
>Ez a szolgáltatás váltja fel, és írja felül a [testreszabási jogcímek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) ma kínált a portálon keresztül. Ha testre szabta a portálon kívül a Graph/PowerShell-módszer a ugyanahhoz az alkalmazáshoz a jelen dokumentumban szereplő jogcímeket, jogkivonatok ki, hogy alkalmazás figyelmen kívül hagyja a portálon megadottal.
Ebben a dokumentumban ismertetett módszerek használatával végzett konfigurációk nem jelenik meg a portálon.

Ez a szolgáltatás-k segítségével Bérlői rendszergazda testre szabhatja a jogcímek a kibocsátott jogkivonatokat egy adott alkalmazáshoz a bérlőben. A házirendek hozzárendelése jogcímek használata:

- Válassza ki, melyik jogkivonatok szerepelnek.
- Hozzon létre jogcímtípusokra vonatkozik, amelyeket már nem létezik.
- Válassza ki, vagy módosítsa az adott jogcímek kibocsátott adatainak forrását.

>[!NOTE]
>Ez a funkció jelenleg nyilvános előzetes verziójához. Készüljön visszaállítja, vagy távolítsa el a módosításokat. A szolgáltatás bármely Azure Active Directory (Azure AD) előfizetés nyilvános előzetes érhető el. Amikor a szolgáltatás általánosan elérhetővé válik, azonban a funkció az egyes funkcióit szükség lehet az Azure Active Directory premium előfizetéssel.

## <a name="claims-mapping-policy-type"></a>A jogcímek leképezési házirend típusa
Az Azure AD egy **házirend** objektum által jelképezett szabályok lépnek érvénybe, az egyes alkalmazások vagy a szervezeten belüli összes alkalmazást. Mindegyik házirendtípusnál struktúrája egy egyedi, majd alkalmazott, amelyhez hozzá vannak rendelve objektumok tulajdonságainak vannak beállítva.

A jogcím-házirend hozzárendelése egy olyan típusú **házirend** objektum, amely módosítja az adott alkalmazásokra vonatkozó kiállított jogkivonatokat a kibocsátott jogcímeket.

## <a name="claim-sets"></a>Jogcímszabály-készletek
Nincsenek bizonyos-készletek, amelyek meghatározzák, hogy mikor és hogyan használhatók a jogkivonatok jogcímeket.

### <a name="core-claim-set"></a>Alapvető jogcímkészlethez
A core jogcímek készletében lévő jogcímek minden jogkivonatban, függetlenül a házirend szerepelnek. Ezeket a jogcímeket is tartoznak, korlátozott, és nem módosítható.

### <a name="basic-claim-set"></a>Alapszintű jogcímek készletéhez
Az alapvető jogcím alapértelmezett jogkivonatokat (mellett a core jogcímkészlethez) által kibocsátott jogcímeket is tartalmaz. Ezeket a jogcímeket nincs megadva, vagy módosítja a házirendek hozzárendelése jogcímeket.

### <a name="restricted-claim-set"></a>Korlátozott jogcímkészlethez
Korlátozott jogcímek házirend használatával nem módosítható. Az adatforrás nem módosítható, és átalakítás nélkül alkalmazza ezeket a jogcímeket létrehozásakor.

#### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>1. táblázat: JSON webes jogkivonat (JWT) korlátozott jogcímek készletéhez
|Jogcím típusa (név)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|ACR|
|Aktor|
|actortoken|
|aio|
|altsecid|
|AMR|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|alkalmazásazonosító|
|appidacr|
|helyességi feltétel|
|at_hash|
|és|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|Másolatot kap|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|használható cnf paraméterrel|
|Kód|
|vezérlők|
|credential_keys|
|CSR|
|csr_type|
|eszközazonosító|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|e-mailben|
|végpont|
|enfpolids|
|Exp|
|expires_on|
|grant_type|
|Diagram|
|group_sids|
|Csoportok|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationInstant|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationMethod|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expiration|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expired|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Name|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/NameIdentifier|
|IAT|
|identityprovider|
|IDP|
|in_corp|
|Példány|
|IPADDR|
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
|Nonce|
|OID|
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
|szerepkörök|
|Hatókör|
|Szolgáltatáskapcsolódási pont|
|biztonsági azonosító|
|Aláírás|
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

#### <a name="table-2-security-assertion-markup-language-saml-restricted-claim-set"></a>2. táblázat: Security Assertion Markup Language (SAML) korlátozott jogcímek készletéhez
|Jogcím típusa (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expiration|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expired|
|http://schemas.microsoft.com/Identity/Claims/accesstoken|
|http://schemas.microsoft.com/Identity/Claims/openid2_id|
|http://schemas.microsoft.com/Identity/Claims/identityprovider|
|http://schemas.microsoft.com/Identity/Claims/objectidentifier|
|http://schemas.microsoft.com/Identity/Claims/PUID|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/NameIdentifier [MR1] |
|http://schemas.microsoft.com/Identity/Claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationInstant|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationMethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/Claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/groups|
|http://schemas.microsoft.com/Claims/groups.Link|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Role|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/Claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/Claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/Claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/Claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/Identity/Claims/Actor|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Authentication|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SID|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/UPN|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/GroupSID|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SPN|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/privatepersonalidentifier|
|http://schemas.microsoft.com/Identity/Claims/scope|

## <a name="claims-mapping-policy-properties"></a>A jogcímek leképezési házirend tulajdonságai
Egy házirend hozzárendelése a vezérlő melyik kibocsátott jogcímeket tulajdonságait, és ha az adatok származik. Ha nincs házirend van beállítva, a rendszer a core jogcímek készletében, az egyszerű jogcímek készletében és az alkalmazást úgy döntött, hogy fogadni választható jogcímeket tartalmazó jogkivonatokat állít ki.

### <a name="include-basic-claim-set"></a>Alapszintű jogcímkészlethez tartalmazza

**Karakterlánc:** IncludeBasicClaimSet

**Adattípus:** logikai (IGAZ vagy hamis)

**Összefoglalás:** Ez a tulajdonság határozza meg, hogy a házirend által érintett jogkivonatokba szerepel-e az egyszerű jogcímek készletébe. 

- Ha a házirend által érintett jogkivonatokba kibocsátott True értéke esetén az egyszerű jogcímek készletében lévő összes jogcímek. 
- Ha FALSE értéke esetén az egyszerű jogcímek készletében nem szerepelnek a jogkivonatokat, kivéve, ha külön-külön hozzáadásuk után a jogcím séma tulajdonságában ugyanabban a házirendben.

>[!NOTE] 
>A core jogcímek készletében lévő jogcímek egyikével sem található minden jogkivonatot, függetlenül attól, milyen Ez a tulajdonság értéke. 

### <a name="claims-schema"></a>Jogcímek séma

**Karakterlánc:** ClaimsSchema

**Adattípus:** JSON blob egy vagy több jogcím séma megadásával

**Összefoglalás:** Ez a tulajdonság határozza meg, milyen jogcímek szerepelnek a jogkivonatok hatással a házirendet, ezen felül az egyszerű jogcímek készletéhez, mind a core jogcímek készletéhez.
Minden jogcím séma bejegyzéshez ebben a tulajdonságban meghatározott bizonyos információkra szükség. Meg kell adnia, ahol az adatok származik (**érték** vagy **forrás vagy-azonosító pár**), és amely jogcímek, az adatok kibocsátott (**jogcím típusa**).

### <a name="claim-schema-entry-elements"></a>Jogcím sémaelemek-bejegyzés

**Érték:** érték elem statikus értéket határoz meg, a jogcímek kibocsátott kell az adatokat.

**Forrás vagy-azonosító pár:** a forrás- és azonosító elemek határozza meg, ahol az adatokat a jogcímek származik. 

A forráselem kell állítani a következők egyikét: 


- "user": a jogcím adatai a User objektum tulajdonság. 
- "alkalmazás": a jogcím adatai (ügyfél) alkalmazás szolgáltatásnév-tulajdonságok. 
- "erőforrás": a jogcím adatai egy tulajdonság az erőforrás-szolgáltatás egyszerű.
- "célközönség": a jogcím adatai a szolgáltatás egyszerű, amely a token (ügyfél vagy erőforrás egyszerű szolgáltatásnév) a célközönséget egy tulajdonság.
- "Vállalati": a jogcím adatai egy tulajdonság az erőforrás-bérlői vállalati objektumon.
- "átalakítása": a jogcím adatai a jogcímek átalakításáról (lásd a "Jogcím-átalakítás" a cikk későbbi részében). 

Ha a forrás átalakításában, a **TransformationID** elem szerepelnie kell a jogcímdefiníciót.

Az "ID" elem. azonosítja, hogy melyik tulajdonság a forrás az értéket ad meg a jogcímet. Az alábbi táblázat a forrás-értékek érvényes azonosító értékének.

#### <a name="table-3-valid-id-values-per-source"></a>3. táblázat: Érvényes azonosító értékek forrásonként
|Forrás|Azonosító|Leírás|
|-----|-----|-----|
|Felhasználó|Vezetéknév|Család neve|
|Felhasználó|givenName|Utónév|
|Felhasználó|DisplayName|Megjelenített név|
|Felhasználó|objektumazonosító|Objektumazonosító|
|Felhasználó|mail|E-mail cím|
|Felhasználó|userPrincipalName|Egyszerű felhasználónév|
|Felhasználó|Szervezeti egység|Részleg|
|Felhasználó|onpremisessamaccountname|A helyi Sam-fiók neve|
|Felhasználó|netbiosname|NetBIOS-név|
|Felhasználó|tartománynév|DNS-tartománynév|
|Felhasználó|onpremisesecurityidentifier|a helyi biztonsági azonosítója|
|Felhasználó|Cégnév|Szervezet neve|
|Felhasználó|streetAddress|Utca, házszám|
|Felhasználó|Irányítószám|Irányítószám|
|Felhasználó|preferredlanguange|Választott nyelv|
|Felhasználó|onpremisesuserprincipalname|a helyszíni egyszerű Felhasználónévvel|
|Felhasználó|mailnickname|Mail becenév|
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
|Felhasználó|othermail|Más E-mail|
|Felhasználó|Ország|Ország|
|Felhasználó|city|Város|
|Felhasználó|state|Állam|
|Felhasználó|Beosztás|Beosztás|
|Felhasználó|EmployeeID|Alkalmazott azonosítója|
|Felhasználó|facsimiletelephonenumber|Fax Telefonszám|
|alkalmazás, erőforrás, a célközönség|DisplayName|Megjelenített név|
|alkalmazás, erőforrás, a célközönség|kifogásolt|Objektumazonosító|
|alkalmazás, erőforrás, a célközönség|címkék|Szolgáltatás egyszerű címke|
|Vállalat|tenantcountry|Bérlő ország|

**TransformationID:** TransformationID elemhez meg kell adni, csak akkor, ha a forráselem "átalakítása" értékre van állítva.

- Ennek az elemnek meg kell egyeznie az "ID" elem. a átalakítása bejegyzés a **ClaimsTransformation** tulajdonság, amely meghatározza, hogyan generáljon a rendszer az adatokat a jogcímet.

**Jogcím típusa:** a **JwtClaimType** és **SamlClaimType** elemek határozza meg, amely jogcím, a jogcím-séma bejegyzés hivatkozik.

- A JwtClaimType tartalmaznia kell a JWTs felszabaduló a jogcím nevét.
- A SamlClaimType tartalmaznia kell a jogcím az SAML-jogkivonatokat kell kibocsátott URI.

>[!NOTE]
>A jogcím típusa elemek nevét és a korlátozott jogcímek készletében lévő jogcímek URI-azonosítók nem használható. További információkért lásd: a "Kivételek és korlátozások" című szakaszban található ebben a cikkben.

### <a name="claims-transformation"></a>Jogcím átalakítása

**Karakterlánc:** ClaimsTransformation

**Adattípus:** JSON blob egy vagy több átalakítása megadásával 

**Összefoglalás:** közös átalakítások alkalmazhat forrásadatok, létrehozza a kimeneti adatokat, a jogcímek sémában megadott jogcímek használják ezt a tulajdonságot.

**Azonosító:** használja az "ID" elem. a átalakítása bejegyzésre TransformationID jogcímek séma bejegyzésben. Ez az érték átalakítása tételek belül a házirend egyedinek kell lennie.

**TransformationMethod:** TransformationMethod elem azonosítja, mely a művelet létrehozza az adatokat a jogcímet.

Alapján kiválasztott módszert, amelynek bemenetekhez és kimenetekhez várt. Ezek használatával definiáltak a **InputClaims**, **InputParameters** és **OutputClaims** elemek.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>4. táblázat: Átalakítási metódusok és várt bemenetekhez és kimenetekhez
|TransformationMethod|Várt bemeneti|Várt kimenet|Leírás|
|-----|-----|-----|-----|
|Csatlakozás|karakterlánc1, karakterlánc2, elválasztó|outputClaim|Illesztések karakterláncok adjon meg egy elválasztó bejelentkezve használatával. Például: karakterlánc1: "foo@bar.com", karakterlánc2: "védőfal", az elválasztó: "." outputClaim eredményez: "foo@bar.com.sandbox"|
|ExtractMailPrefix|mail|outputClaim|Kibontja a helyi részét egy e-mail címet. Például: mail: "foo@bar.com" outputClaim eredményez: "foo". Ha @ nem bejelentkezési jelen, majd a orignal bemeneti karakterláncot ad vissza.|

**InputClaims:** InputClaims elemet használja az adatok egy jogcím séma bejegyzésből átadása átalakítás. Két attribútumot tartalmaz: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **ClaimTypeReferenceId** azonosító elem található a megfelelő bemeneti jogcímet a jogcím séma bejegyzés kapcsolódik. 
- **TransformationClaimType** adjon egy egyedi nevet a bemeneti szolgál. Ezt a nevet meg kell egyeznie az átalakítási metódus a várt bemenetek egyike.

**InputParameters:** konstans értéket átadni átalakítás InputParameters elemet használja. Két attribútumot tartalmaz: **érték** és **azonosító**.

- **Érték** átadni a tényleges konstans érték.
- **Azonosító** adjon egy egyedi nevet a bemeneti szolgál. Ezt a nevet meg kell egyeznie az átalakítási metódus a várt bemenetek egyike.

**OutputClaims:** OutputClaims elem átalakítás által létrehozott adatok tárolásához használja, és kötése a jogcím-séma bejegyzést. Két attribútumot tartalmaz: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **ClaimTypeReferenceId** , azonosító: a jogcím-séma bejegyzés található a megfelelő kimeneti jogcímek csatlakoztatva van.
- **TransformationClaimType** használatával adjon egyedi nevet a kimenetet. Ezt a nevet meg kell egyeznie a várt kimeneti az átalakítási metódus egyik.

### <a name="exceptions-and-restrictions"></a>Kivételek és korlátozások

**SAML NameID és egyszerű felhasználónév:** , amelyből forrás NameID és UPN értékek és a jogcímek átalakítása, amelyeknél engedélyezve van, attribútumait korlátozottak.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>5. táblázat: Attribútumok SAML NameID adatforrásként engedélyezett
|Forrás|Azonosító|Leírás|
|-----|-----|-----|
|Felhasználó|mail|E-mail cím|
|Felhasználó|userPrincipalName|Egyszerű felhasználónév|
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

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>6. táblázat: Átalakítási metódusok SAML NameID engedélyezett
|TransformationMethod|Korlátozások|
| ----- | ----- |
|ExtractMailPrefix|Nincs|
|Csatlakozás|A csatlakoztatni kívánt utótagot az erőforrás-bérlő ellenőrzött tartományt kell lennie.|

### <a name="custom-signing-key"></a>Egyéni aláírási kulcs
Egy egyéni aláírási kulcs a szolgáltatás a jogcím leképezési házirend érvénybe elsődleges objektumot hozzá kell rendelni. Minden olyan kiállított jogkivonatokat, amelyek rendelkeznek a házirend lett érinti ez a kulcs vannak aláírva. Alkalmazások jogkivonatok elfogadására kell konfigurálni a kulccsal aláírva. Ez biztosítja, hogy tudomásul vétele, hogy módosult-e a jogkivonatok által a jogcímek házirend leképezési hozta létre. Alkalmazások megakadályozza kártevő szereplője által létrehozott házirendek hozzárendelése jogcímeket.

### <a name="cross-tenant-scenarios"></a>Több-bérlős forgatókönyvek
Jogcímek házirendek hozzárendelése nem vonatkoznak a vendégfelhasználók számára. A Vendég felhasználó próbál csatlakozni egy alkalmazáshoz egy leképezése az egyszerű szolgáltatásnév rendelt házirend JOGCÍMEKKEL rendelkező és, ha az alapértelmezett tokent ad ki (a házirend nincs hatása).

## <a name="claims-mapping-policy-assignment"></a>Házirend-hozzárendelés leképezési jogcímek
Szolgáltatás egyszerű objektumok csak rendelhet hozzá házirendek hozzárendelése jogcímeket.

### <a name="example-claims-mapping-policies"></a>Példa jogcím-leképezés házirendek

Az Azure ad-ben több forgatókönyv is előfordulhatnak, ha testre szabhatja az adott szolgáltatás rendszerbiztonsági tagoknak jogkivonatokba kibocsátott jogcímeket. Ez a szakasz azt bízná néhány olyan gyakori forgatókönyvet, amelyik segíthet a leképezés típus jogcímek használata megfogható is.

#### <a name="prerequisites"></a>Előfeltételek
A következő példákban meg létrehozása, frissítése, hivatkozás és házirendek törlése szolgáltatás rendszerbiztonsági tagoknak. Ha most ismerkedik az Azure AD, azt javasoljuk, hogy további tudnivalók az Azure AD-bérlő beszerzése, mielőtt folytatná a példákat. 

A kezdéshez tegye a következőket:


1. Töltse le a legújabb [Azure AD PowerShell modult nyilvános előzetes](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Jelentkezzen be az Azure AD rendszergazdai fiókját a Connect parancs futtatásával. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenet indításához.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  A szervezet létrehozott összes házirend megtekintéséhez futtassa a következő parancsot. Azt javasoljuk, hogy a parancs futtatása után a következő helyzetekben, ellenőrizze, hogy a házirendek várt módon létrehozott legtöbb műveletet.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Példa: Hozzon létre, és hagyja ki ezt az alapszintű jogcímek egy egyszerű szolgáltatásnév a kiállított jogkivonatokat a házirend hozzárendelése.
Ebben a példában egy házirendet, amely az alapvető jogcímek eltávolítja a társított szolgáltatás rendszerbiztonsági tagok kiadott jogkivonatokat hoz létre.


1. Hozzon létre egy házirend leképezési jogcímeket. Ez a házirend, kapcsolódószolgáltatás-specifikus rendszerbiztonsági tagok, eltávolítja az alapvető jogcímek a tokenekből.
    1. A házirend létrehozásához futtassa a parancsot: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Az új házirend megtekintéséhez, és hogy beszerezze a szabályzatot ObjectId, futtassa a következő parancsot:
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  A házirend hozzárendelése a szolgáltatásnevet. Szükség az ObjectId azonosító, a szolgáltatás egyszerű beolvasása. 
    1.  A szervezet összes szolgáltatás rendszerbiztonsági tagok megtekintéséhez, Microsoft Graph lekérdezheti. Vagy az Azure AD Graph Explorerben, jelentkezzen be az Azure AD-fiókot.
    2.  Ha az egyszerű szolgáltatás objektumazonosító, a következő parancsot:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Példa: Hozzon létre, és egy házirend EmployeeID és TenantCountry magukban jogcímekként egy egyszerű szolgáltatásnév a kiállított jogkivonatokat a hozzárendelése.
Ebben a példában egy házirendet, amely hozzáadja a EmployeeID és TenantCountry csatolt szolgáltatásnevekről kiadott jogkivonatokat hoz létre. Az EmployeeID is ki lesz adva a SAML-jogkivonatokat és a JWTs neve jogcím típusaként. A TenantCountry kibocsátott, az ország jogcímtípus SAML-jogkivonatokat és JWTs is. Ebben a példában továbbra is a jogkivonatok beállítani az alapvető jogcímeket tartalmaznak.

1. Hozzon létre egy házirend leképezési jogcímeket. Ezt a házirendet, kapcsolódik az adott szolgáltatás rendszerbiztonsági tagok, hozzáadja a jogkivonatok EmployeeID és TenantCountry jogcímeket.
    1. A házirend létrehozásához futtassa a parancsot:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":" tenantcountry ","SamlClaimType":" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country ","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Az új házirend megtekintéséhez, és hogy beszerezze a szabályzatot ObjectId, futtassa a következő parancsot:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
2.  A házirend hozzárendelése a szolgáltatásnevet. Szükség az ObjectId azonosító, a szolgáltatás egyszerű beolvasása. 
    1.  A szervezet összes szolgáltatás rendszerbiztonsági tagok megtekintéséhez, Microsoft Graph lekérdezheti. Vagy az Azure AD Graph Explorerben, jelentkezzen be az Azure AD-fiókot.
    2.  Ha az egyszerű szolgáltatás objektumazonosító, a következő parancsot:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Példa: Hozzon létre, és rendelje hozzá egy egyszerű szolgáltatásnév a kiállított jogkivonatokat a jogcím-átalakítási használó házirend.
Ebben a példában egy házirendet, amely megfelelően kibocsát egy egyéni jogcím "JoinedData" társított szolgáltatás rendszerbiztonsági tagok kiadott JWTs hoz létre. A jogcím értéke a extensionattribute1 attribútum a user objektum ".sandbox" a tárolt adatok összeillesztésével lett létrehozva. Ebben a példában a jogkivonatok beállított alapvető jogcímeket kizárása azt.


1. Hozzon létre egy házirend leképezési jogcímeket. Ezt a házirendet, kapcsolódik az adott szolgáltatás rendszerbiztonsági tagok, hozzáadja a jogkivonatok EmployeeID és TenantCountry jogcímeket.
    1. A házirend létrehozásához futtassa a parancsot: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformation":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"Id":"string2","Value":"sandbox"},{"Id":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Az új házirend megtekintéséhez, és hogy beszerezze a szabályzatot ObjectId, futtassa a következő parancsot: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
2.  A házirend hozzárendelése a szolgáltatásnevet. Szükség az ObjectId azonosító, a szolgáltatás egyszerű beolvasása. 
    1.  A szervezet összes szolgáltatás rendszerbiztonsági tagok megtekintéséhez, Microsoft Graph lekérdezheti. Vagy az Azure AD Graph Explorerben, jelentkezzen be az Azure AD-fiókot.
    2.  Ha az egyszerű szolgáltatás objektumazonosító, a következő parancsot: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
