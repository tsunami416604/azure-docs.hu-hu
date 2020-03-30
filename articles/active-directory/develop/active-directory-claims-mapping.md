---
title: Az Azure AD-bérlői alkalmazás jogcímének testreszabása (PowerShell)
titleSuffix: Microsoft identity platform
description: Ez a lap az Azure Active Directory jogcím-hozzárendelését ismerteti.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 49860504da8dd2a1b994a23a24df95f59c959c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263191"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Útmutató: A bérlőben lévő adott alkalmazás hoz egy adott alkalmazáshoz kibocsátott jogkivonatokban kibocsátott jogcímek testreszabása (előzetes verzió)

> [!NOTE]
> Ez a funkció felváltja és felülírja a portálon keresztül ma kínált [jogcímek testreszabását.](active-directory-saml-claims-customization.md) Ugyanebben az alkalmazásban, ha a portál használatával a portál használatával a jelen dokumentumban részletezett Graph/PowerShell metódus testreszabása kor, az adott alkalmazáshoz kiadott jogkivonatok figyelmen kívül hagyja a konfigurációt a portálon. A dokumentumban részletezett módszerekkel végzett konfigurációk nem jelennek meg a portálon.

Ezt a szolgáltatást a bérlői rendszergazdák használják a bérlői rendszergazdák által kibocsátott jogcímekben a bérlőben egy adott alkalmazás jogkivonataiban kibocsátott jogcímek testreszabásához. A jogcímleképezési házirendek a következőkre használhatók:

- Válassza ki, hogy mely jogcímek szerepelnek a jogkivonatokban.
- Olyan jogcímtípusok létrehozása, amelyek még nem léteznek.
- Az egyes jogcímekben kibocsátott adatok forrásának kiválasztása vagy módosítása.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. Készüljön fel a módosítások visszavonására vagy eltávolítására. A funkció bármely Azure Active Directory (Azure AD) előfizetésben érhető el nyilvános előzetes verzióban. Azonban, ha a funkció általánosan elérhetővé válik, a funkció bizonyos aspektusai azure AD prémium előfizetést igényelhetnek. Ez a szolgáltatás támogatja a WS-Fed, SAML, OAuth és OpenID Connect protokollok jogcímleképezési házirendjeinek konfigurálását.

## <a name="claims-mapping-policy-type"></a>Jogcímleképezési házirend típusa

Az Azure AD-ben egy **szabályzatobjektum** az egyes alkalmazásokra vagy a szervezet összes alkalmazására kényszerített szabályok készletét jelöli. Minden házirendtípus nak egyedi struktúrája van, és olyan tulajdonságokat tartalmaz, amelyeket aprogram azokra az objektumokra alkalmaz, amelyekhez hozzá vannak rendelve.

A jogcímleképezési **Policy** házirend olyan házirend-objektum, amely módosítja az adott alkalmazásokhoz kiadott jogkivonatokban kibocsátott jogcímeket.

## <a name="claim-sets"></a>Követeléskészletek

Vannak bizonyos jogcímek, amelyek meghatározzák, hogyan és mikor használják a jogkivonatokat.

| Jogcímkészlet | Leírás |
|---|---|
| Alapvető jogcímkészlet | A házirendtől függetlenül minden jogkivonatban jelen vannak. Ezek a jogcímek is korlátozottnak minősülnek, és nem módosíthatók. |
| Alapjogcímkészlet | Tartalmazza a jogkivonatok (az alapvető jogcímkészleten kívül) alapértelmezés szerint kibocsátott jogcímeket. Az alapjogcímeket a jogcímleképezési házirendek használatával hagyhatja ki vagy módosíthatja. |
| Korlátozott jogcímkészlet | Házirenddel nem módosítható. Az adatforrás nem módosítható, és a jogcímek létrehozásakor nem lesz átalakítás. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>1. táblázat: JSON Web Token (JWT) korlátozott jogcímkészlet

| Jogcím típusa (név) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Színész |
| actortoken |
| Aio |
| altsecid |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| Appid |
| appidacr |
| Állítás |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| vezérlők |
| credential_keys |
| Csr |
| csr_type |
| Deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| Exp |
| expires_on |
| grant_type |
| Grafikon |
| group_sids |
| csoportok |
| csoportok |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identitásszolgáltató |
| Idp |
| in_corp |
| Példány |
| ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| névazonosító |
| nbf |
| netbios_name |
| nonce között |
| Oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| jelszó |
| platf között |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| Erőforrás |
| Szerepet |
| roles |
| scope |
| Scp |
| Sid |
| Aláírás |
| signin_state |
| src1 |
| src2 |
| Al |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| Tid |
| tokenAutologonEnabled |
| megbízhatóderende |
| unique_name |
| Upn |
| user_setting_sync_url |
| felhasználónév |
| Uti |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>2. táblázat: SAML korlátozott jogcímkészlet

| Jogcím típusa (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Jogcímleképezési házirend tulajdonságai

A jogcímek kibocsátásának és az adatok származásának szabályozásához használja a jogcímleképezési házirend tulajdonságait. Ha egy házirend nincs beállítva, a rendszer olyan jogkivonatokat ad ki, amelyek tartalmazzák az alapvető jogcímkészletet, az alapjogcímkészletet és az alkalmazás által választott [választható jogcímeket.](active-directory-optional-claims.md)

### <a name="include-basic-claim-set"></a>Alapjogcímkészlet belefoglalása

**Karakterlánc:** IncludeBasicClaimSet

**Adattípus:** Logikai (igaz vagy hamis)

**Összefoglaló:** Ez a tulajdonság határozza meg, hogy az alapszintű jogcímkészlet szerepel-e a házirend által érintett jogkivonatokban.

- Ha értéke Igaz, az alapszintű jogcímkészletben lévő összes jogcím a házirend által érintett jogkivonatokban kerül kibocsátásra. 
- Ha értéke Hamis, az alapjogcímkészletben lévő jogcímek nem szerepelnek a jogkivonatokban, kivéve, ha azok külön-külön kerülnek hozzáadásra ugyanazon házirend jogcímséma tulajdonságában.

> [!NOTE] 
> Az alapvető jogcímkészletben lévő jogcímek minden jogkivonatban jelen vannak, függetlenül attól, hogy ez a tulajdonság mire van beállítva. 

### <a name="claims-schema"></a>Jogcímséma

**Karakterlánc:** ClaimsSchema

**Adattípus:** JSON-blob egy vagy több jogcímséma-bejegyzéssel

**Összefoglaló:** Ez a tulajdonság határozza meg, hogy mely jogcímek vannak jelen a szabályzat által érintett jogkivonatokban, az alapjogcímkészlet en és az alapvető jogcímkészleten kívül.
A tulajdonságban definiált jogcímséma-bejegyzésekhez bizonyos adatokra van szükség. Adja meg, hogy az adatok honnan származnak (**Érték** vagy **Forrás/Azonosító pár**), és melyek állítják, hogy az adatokat a rendszer (**Jogcímtípusa )** adja meg.

### <a name="claim-schema-entry-elements"></a>Jogcímséma-bejegyzési elemek

**Érték:** Az Érték elem egy statikus értéket határoz meg a jogcímben kibocsátandó adatként.

**Forrás/azonosító pár:** A Forrás és az Azonosító elemek határozzák meg, hogy a jogcímben lévő adatok honnan származnak. 

Állítsa a Forrás elemet az alábbi értékek egyikére: 

- "user": A jogcímben lévő adatok a User objektum egyik tulajdonsága. 
- "alkalmazás": A jogcímben lévő adatok az alkalmazás (ügyfél) szolgáltatásnév tulajdonsága. 
- "erőforrás": A jogcímben lévő adatok az erőforrás-szolgáltatásnév tulajdonsága.
- "közönség": A jogcímben lévő adatok a szolgáltatásnév olyan tulajdonsága, amely a jogkivonat (az ügyfél vagy az erőforrás-szolgáltatásegyszerű) közönsége.
- "vállalat": A jogcímben lévő adatok az erőforrás-bérlő vállalati objektumának egy tulajdonsága.
- "átalakítás": A jogcím ben szereplő adatok a jogcímek átalakításából származnak (lásd a cikk "Jogcímek átalakítása" című szakaszát).

Ha a forrás átalakítás, a **TransformationID** elemet is bele kell foglalni ebbe a jogcímdefinícióba.

Az azonosító elem azonosítja, hogy a forrásmelyik tulajdonságadja meg a jogcím értékét. Az alábbi táblázat a Forrás minden egyes értékére érvényes azonosítóértékeket sorolja fel.

#### <a name="table-3-valid-id-values-per-source"></a>3. táblázat: Érvényes azonosítóértékek forrásonként

| Forrás | ID (Azonosító) | Leírás |
|-----|-----|-----|
| Felhasználó | surname | Családi név |
| Felhasználó | givenname | utónév; |
| Felhasználó | Displayname | Megjelenítendő név |
| Felhasználó | tárgyiobjektum | ObjectID |
| Felhasználó | Levelezés | E-mail-cím |
| Felhasználó | userprincipalname (felhasználóprincipalname) | Felhasználó egyszerű neve |
| Felhasználó | Részleg|Részleg|
| Felhasználó | helyszíni samaccountname | Helyszíni SAM-fiók neve |
| Felhasználó | netbiosname| NetBios-név |
| Felhasználó | dnsdomainname | DNS-tartománynév |
| Felhasználó | helyszíni biztonsági azonosító | Helyszíni biztonsági azonosító |
| Felhasználó | cégnév| Szervezetnév |
| Felhasználó | utcacím | Utca, házszám |
| Felhasználó | irányítószám | Irányítószám |
| Felhasználó | preferredlanguange | Előnyben részesített nyelv |
| Felhasználó | onpremisesuserprincipalname | Helyszíni upn |
| Felhasználó | mailbecenév | E-mail becenév |
| Felhasználó | extensionattribute1 | 1. bővítmény attribútuma |
| Felhasználó | extensionattribute2 | 2. bővítmény attribútuma |
| Felhasználó | kiterjesztett attribútum3 | 3. bővítmény attribútuma |
| Felhasználó | extensionattribute4 | 4. bővítmény attribútuma |
| Felhasználó | extensionattribute5 | 5. bővítmény attribútuma |
| Felhasználó | extensionattribute6 | 6. bővítmény attribútuma |
| Felhasználó | extensionattribute7 | Extension Attribútum 7 |
| Felhasználó | kiterjesztett attribútum8 | 8. bővítmény attribútuma |
| Felhasználó | extensionattribute9 | Extension Attribútum 9 |
| Felhasználó | extensionattribute10 | Extension Attribútum 10 |
| Felhasználó | extensionattribute11 | Extension Attribútum 11 |
| Felhasználó | extensionattribute12 | Extension Attribútum 12 |
| Felhasználó | kiterjesztett attribútum13 | Extension Attribútum 13 |
| Felhasználó | extensionattribute14 | Extension Attribútum 14 |
| Felhasználó | kiterjesztettattribútum15 | Extension Attribútum 15 |
| Felhasználó | othermail (egyébmail) | Egyéb levelek |
| Felhasználó | ország | Ország |
| Felhasználó | city | Város |
| Felhasználó | state | Állapot |
| Felhasználó | állásjogcím | Beosztás |
| Felhasználó | alkalmazott | Alkalmazott azonosítója |
| Felhasználó | faxszám | Fax telefonszáma |
| alkalmazás, erőforrás, közönség | Displayname | Megjelenítendő név |
| alkalmazás, erőforrás, közönség | Kifogásolta | ObjectID |
| alkalmazás, erőforrás, közönség | címkét | Szolgáltatáscímke |
| Vállalat | bérlőország | Bérlő országa |

**TransformationID:** A TransformationID elemet csak akkor kell megadni, ha a Forrás elem "transzformáció" lesz.

- Ennek az elemnek meg kell egyeznie a **ClaimsTransformation** tulajdonság átalakítási bejegyzésének azonosítóelemével, amely meghatározza a jogcím adatainak létrehozásának módját.

**Jogcím típusa:** A **JwtClaimType** és **a SamlClaimType** elemek határozzák meg, hogy melyik jogcímsére hivatkozik ez a jogcímséma-bejegyzés.

- A JwtClaimType típusnak tartalmaznia kell a JWT-kben kibocsátandó jogcím nevét.
- A SamlClaimType-nak tartalmaznia kell az SAML-jogkivonatokban kibocsátandó jogcím URI-ját.

> [!NOTE]
> A korlátozott jogcímkészletben szereplő jogcímek neve és URI-ja nem használható a jogcímtípus-elemekhez. További információt a cikk későbbi, "Kivételek és korlátozások" című részében talál.

### <a name="claims-transformation"></a>Jogcím-átalakítás

**Karakterlánc:** Jogcímátalakítás

**Adattípus:** JSON blob egy vagy több átalakítási bejegyzéssel 

**Összefoglaló:** Ezzel a tulajdonsággal általános átalakításokat alkalmazhat a forrásadatokra, hogy létrehozza a jogcímsémében megadott jogcímek kimeneti adatait.

**Azonosító:** Az ID-elem segítségével hivatkozzon erre az átalakítási bejegyzésre az Átalakítási azonosító jogcímek sémája bejegyzésben. Ennek az értéknek egyedinek kell lennie a házirenden belüli minden egyes transzformációs bejegyzéshez.

**TransformationMethod:** A TransformationMethod elem azonosítja, hogy melyik művelet et hajtja végre a jogcím adatainak létrehozásához.

A választott módszer alapján bemenetek és kimenetek készlete várható. Adja meg a bemenetek és kimenetek segítségével **InputClaims**, **InputParameters** és **OutputClaims** elemek.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>4. táblázat: Átalakítási módszerek és várható betáplálások és outputok

|TransformationMetódus|Várt bemenet|Várt kimenet|Leírás|
|-----|-----|-----|-----|
|Csatlakozás|string1, string2, elválasztó|outputClaim|A bemeneti karakterláncokat egy elválasztó val illeszti össze a kettő között. Például: string1:"foo@bar.com" , string2:"sandbox" , elválasztó:"." kimenetifoo@bar.com.sandboxigény:" "|
|ExtractMailPrefix|Levelezés|outputClaim|Az e-mail cím helyi részének kibontása. Például: mail:"foo@bar.com" " outputClaim:"foo" eredményt ad. Ha \@ nincs jel jelen, akkor az eredeti bemeneti karakterlánc ad vissza, ahogy van.|

**InputClaims:** Az InputClaims-elem használatával adja át a jogcímséma-bejegyzésből származó adatokat egy átalakításnak. Két attribútuma van: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **A ClaimTypeReferenceId** a jogcímséma-bejegyzés azonosítóelemével van összekapcsolva a megfelelő bemeneti jogcím megkereséséhez. 
- **A TransformationClaimType** segítségével egyedi nevet adhat a bemenetnek. Ennek a névnek meg kell egyeznie az átalakítási módszer várt bemeneteinek egyikével.

**InputParameters:** Az InputParameters elem használatával állandó értéket ad át egy átalakításnak. Két attribútuma van: **Érték** és **Azonosító**.

- **Az érték** az átadandó tényleges állandó érték.
- **Az azonosító** segítségével egyedi nevet adhat a bemenetnek. A névnek meg kell egyeznie az átalakítási módszer várt bemeneteinek egyikével.

**OutputClaims:** Egy OutputClaims elem használatával lenyomva tartani az átalakítás által létrehozott adatokat, és kösse egy jogcímséma bejegyzéshez. Két attribútuma van: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **A ClaimTypeReferenceId** a jogcímséma-bejegyzés azonosítójával csatlakozik a megfelelő kimeneti jogcím megkereséséhez.
- **A TransformationClaimType** segítségével egyedi nevet adhat a kimenetnek. A névnek meg kell egyeznie az átalakítási módszer várt kimeneteinek egyikével.

### <a name="exceptions-and-restrictions"></a>Kivételek és korlátozások

**SAML nameID és UPN:** Azok az attribútumok, amelyekből a NameID és az UPN-értékeket beszerzi, valamint az engedélyezett jogcímátalakítások korlátozottak. Lásd az 5.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>5. táblázat: Az SAML NameID adatforrásaként engedélyezett attribútumok

|Forrás|ID (Azonosító)|Leírás|
|-----|-----|-----|
| Felhasználó | Levelezés|E-mail-cím|
| Felhasználó | userprincipalname (felhasználóprincipalname)|Felhasználó egyszerű neve|
| Felhasználó | helyszíni samaccountname|Helyszíni Sam-fiók neve|
| Felhasználó | alkalmazott|Alkalmazott azonosítója|
| Felhasználó | extensionattribute1 | 1. bővítmény attribútuma |
| Felhasználó | extensionattribute2 | 2. bővítmény attribútuma |
| Felhasználó | kiterjesztett attribútum3 | 3. bővítmény attribútuma |
| Felhasználó | extensionattribute4 | 4. bővítmény attribútuma |
| Felhasználó | extensionattribute5 | 5. bővítmény attribútuma |
| Felhasználó | extensionattribute6 | 6. bővítmény attribútuma |
| Felhasználó | extensionattribute7 | Extension Attribútum 7 |
| Felhasználó | kiterjesztett attribútum8 | 8. bővítmény attribútuma |
| Felhasználó | extensionattribute9 | Extension Attribútum 9 |
| Felhasználó | extensionattribute10 | Extension Attribútum 10 |
| Felhasználó | extensionattribute11 | Extension Attribútum 11 |
| Felhasználó | extensionattribute12 | Extension Attribútum 12 |
| Felhasználó | kiterjesztett attribútum13 | Extension Attribútum 13 |
| Felhasználó | extensionattribute14 | Extension Attribútum 14 |
| Felhasználó | kiterjesztettattribútum15 | Extension Attribútum 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>6. táblázat: Az SAML NameID-hez engedélyezett átalakítási módszerek

| TransformationMetódus | Korlátozások |
| ----- | ----- |
| ExtractMailPrefix | None |
| Csatlakozás | Az egyesített utótagnak az erőforrás-bérlő ellenőrzött tartományának kell lennie. |

### <a name="custom-signing-key"></a>Egyéni aláíró kulcs

Egyéni aláíró kulcsot kell hozzárendelni az egyszerű szolgáltatásobjektumhoz a jogcímleképezési házirend érvénybe léptetéséhez. Ez biztosítja annak nyugtázását, hogy a jogkivonat-leképezési házirend létrehozója módosította a jogkivonatokat, és megvédi az alkalmazásokat a rosszindulatú szereplők által létrehozott jogcímleképezési házirendektől. Egyéni aláíró kulcs hozzáadásához az Azure PowerShell-parancsmag `new-azureadapplicationkeycredential` használatával szimmetrikus kulcshitelesítő adatokat hozhat létre az alkalmazásobjektumhoz. Az Azure PowerShell-parancsmagról további információt az [Új-AzureADApplicationKeyCredential című](https://docs.microsoft.com/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0)témakörben talál.

Azoknak az alkalmazásoknak, amelyeken engedélyezve `appid={client_id}` van a jogcímhozzárendelés, az [OpenID Connect metaadat-kérelmeikhez](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)való hozzáfűzéssel kell érvényesíteniük a tokenaláíró kulcsokat. Az alábbiakban az OpenID Connect metaadat-dokumentum formátumát kell használnia: 

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Több-bérlős forgatókönyvek

A jogcímleképezési házirendek nem vonatkoznak a vendégfelhasználókra. Ha egy vendégfelhasználó megpróbál hozzáférni egy alkalmazáshoz egy jogcímleképezési házirendrendelt a szolgáltatásnév, az alapértelmezett jogkivonat ot ad ki (a szabályzat nincs hatása).

## <a name="claims-mapping-policy-assignment"></a>Jogcímhozzárendelési házirend-hozzárendelés

A jogcímleképezési házirendek csak egyszerű szolgáltatásobjektumokhoz rendelhetők hozzá.

### <a name="example-claims-mapping-policies"></a>Példa jogcímleképezési házirendek

Az Azure AD-ben számos forgatókönyv lehetséges, ha testre szabhatja az adott szolgáltatástagok jogkivonatokban kibocsátott jogcímeket. Ebben a szakaszban néhány gyakori forgatókönyvet vezetünk be, amelyek segíthetnek megérteni a jogcímleképezési házirend-típus használatát.

#### <a name="prerequisites"></a>Előfeltételek

Az alábbi példákban hozzon létre, frissítse, csatolja és törölje a szolgáltatásnévházirendeket. Ha most ismerkedik az Azure AD-vel, azt javasoljuk, hogy [ismerje meg, hogyan szerezhet be egy Azure AD-bérlőt,](quickstart-create-new-tenant.md) mielőtt továbblépne ezekkel a példákkal.

Első lépésként tegye a következőket:

1. Töltse le a legújabb [Azure AD PowerShell-modul nyilvános előzetes verziójú kiadását.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Futtassa a Connect parancsot az Azure AD-rendszergazdai fiókba való bejelentkezéshez. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. A szervezetben létrehozott összes házirend megtekintéséhez futtassa a következő parancsot. Azt javasoljuk, hogy futtassa ezt a parancsot a legtöbb művelet után a következő esetekben, annak ellenőrzéséhez, hogy a szabályzatok a várt módon jönnek létre.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Példa: Hozzon létre és rendeljen hozzá egy szabályzatot, amely kihagyja az egyszerű jogcímeket a szolgáltatásnévnek kiadott jogkivonatokból.

Ebben a példában hozzon létre egy szabályzatot, amely eltávolítja az alapszintű jogcímkészlet et a csatolt szolgáltatásnévi tagok által kiadott jogkivonatokból.

1. Jogcímleképezési házirend létrehozása. Ez a szabályzat, adott szolgáltatásnévi tagokhoz kapcsolódik, eltávolítja az alapszintű jogcímkészletet a jogkivonatokból.
   1. A házirend létrehozásához futtassa a következő parancsot: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Az új házirend megtekintéséhez és a házirend ObjectId bekéséhez futtassa a következő parancsot:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. A szolgáltatásnév ObjectId azonosítóját is be kell szereznie.
   1. A szervezet összes szolgáltatásnévének megtekintéséhez [lekérdezheti a Microsoft Graph API-t.](/graph/traverse-the-graph) Vagy a [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)jelentkezzen be az Azure AD-fiókjába.
   2. Ha a szolgáltatásnév ObjectId azonosítóját adja meg, futtassa a következő parancsot:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Példa: Hozzon létre és rendeljen hozzá egy szabályzatot, amely tartalmazza az EmployeeID-t és a TenantCountry-t jogcímként egy egyszerű szolgáltatásnak kiadott jogkivonatban.

Ebben a példában létrehoz egy szabályzatot, amely hozzáadja az EmployeeID és tenantCountry a csatolt szolgáltatásnevek által kiadott jogkivonatokat. Az EmployeeID névjogcím-típusként kerül kiadva mind az SAML-jogkivonatokban, mind a JWT-kben. A TenantCountry az SAML-jogkivonatokban és a JWT-kben is az országjogcímtípusaként kerül kibocsátásra. Ebben a példában továbbra is a jogkivonatokban beállított alapszintű jogcímeket foglaljuk be.

1. Jogcímleképezési házirend létrehozása. Ez a szabályzat, adott szolgáltatásnévi tagokhoz kapcsolódik, hozzáadja az EmployeeID és tenantCountry jogcímeket a jogkivonatokhoz.
   1. A házirend létrehozásához futtassa a következő parancsot:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Az új házirend megtekintéséhez és a házirend ObjectId bekéséhez futtassa a következő parancsot:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. A szolgáltatásnév ObjectId azonosítóját is be kell szereznie. 
   1. A szervezet összes szolgáltatásnévének megtekintéséhez [lekérdezheti a Microsoft Graph API-t.](/graph/traverse-the-graph) Vagy a [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)jelentkezzen be az Azure AD-fiókjába.
   2. Ha a szolgáltatásnév ObjectId azonosítóját adja meg, futtassa a következő parancsot:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Példa: Olyan szabályzat létrehozása és hozzárendelése, amely jogcímátalakítást használ az egyszerű szolgáltatásnak kiadott jogkivonatokban

Ebben a példában létrehoz egy szabályzatot, amely egyéni jogcímet bocsát ki a jwt-k számára, amelyeket összekapcsolt szolgáltatásneveknek adnak ki. Ez a jogcím olyan értéket tartalmaz, amelyet a".sandbox" felhasználói objektum extensionattribute1 attribútumában tárolt adatok összekapcsolásával hoznak létre. Ebben a példában kizárjuk a jogkivonatokban beállított alapszintű jogcímeket.

1. Jogcímleképezési házirend létrehozása. Ez a szabályzat, adott szolgáltatásnévi tagokhoz kapcsolódik, hozzáadja az EmployeeID és tenantCountry jogcímeket a jogkivonatokhoz.
   1. A házirend létrehozásához futtassa a következő parancsot:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Az új házirend megtekintéséhez és a házirend ObjectId bekéséhez futtassa a következő parancsot: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. A szolgáltatásnév ObjectId azonosítóját is be kell szereznie. 
   1. A szervezet összes szolgáltatásnévének megtekintéséhez [lekérdezheti a Microsoft Graph API-t.](/graph/traverse-the-graph) Vagy a [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)jelentkezzen be az Azure AD-fiókjába.
   2. Ha a szolgáltatásnév ObjectId azonosítóját adja meg, futtassa a következő parancsot: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Lásd még

Az SAML-jogkivonatban az Azure Portalon keresztül kiadott jogcímek testreszabásáról a [Hogyan: Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz](active-directory-saml-claims-customization.md) című témakörben olvashat.
