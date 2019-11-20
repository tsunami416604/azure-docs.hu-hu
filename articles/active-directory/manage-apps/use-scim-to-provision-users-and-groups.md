---
title: SCIM-felhasználó kiépítés Azure Active Directorysal | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy SCIM-végpontot, hogyan integrálhatja a SCIM API-t a Azure Active Directoryval, és megkezdheti a felhasználók és csoportok kiépítésének automatizálását az alkalmazásokba.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8bb9b507763c935ab244c42584120a279063954
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195452"
---
# <a name="scim-user-provisioning-with-azure-active-directory-azure-ad"></a>SCIM-felhasználó kiépítés Azure Active Directory (Azure AD)

Ez a cikk azt ismerteti, hogyan használható a rendszer a tartományok közötti Identitáskezelés kezelésére ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)) a felhasználók és csoportok alkalmazásba való kiépítésének és megszüntetésének automatizálására. A SCIM-specifikáció általános felhasználói sémát biztosít az üzembe helyezéshez. Az olyan összevonási szabványokkal együtt, mint az SAML vagy az OpenID Connect, a SCIM teljes körű, szabványokon alapuló megoldást biztosít a rendszergazdáknak a hozzáférés-kezeléshez.

A SCIM a két végpont szabványosított definíciója: egy/Users-végpont és egy/groups-végpont. Általános REST-műveleteket használ az objektumok létrehozásához, frissítéséhez és törléséhez, valamint egy előre definiált sémát az általános attribútumok, például a csoportnév, a Felhasználónév, az utónév, a vezetéknév és az e-mailek számára. A SCIM 2,0 REST API-t kínáló alkalmazások csökkenthetik vagy megszüntethetik a saját felhasználói felügyeleti API-k használatával végzett munkát. A megfelelő SCIM-ügyfelek például megtudhatják, hogyan hozhat létre egy JSON-objektum HTTP-BEJEGYZÉSét a/Users-végponton egy új felhasználói bejegyzés létrehozásához. Ahelyett, hogy némileg eltérő API-ra lenne szüksége ugyanahhoz az alapszintű műveletekhez, a SCIM szabványnak megfelelő alkalmazások azonnal kihasználhatják a meglévő ügyfeleket, eszközöket és kódokat. 

![Kiépítés az Azure AD-ből egy SCIM-val rendelkező alkalmazásba](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

A SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) által meghatározott általános felhasználói objektum sémája és REST API-k lehetővé teszik az identitás-szolgáltatók és az alkalmazások könnyebb integrálását. Az SCIM-végpontot felépítő alkalmazás-fejlesztők az egyéni munka nélkül is integrálhatók bármely SCIM-kompatibilis ügyféllel.

Az alkalmazások kiépítésének automatizálásához az Azure AD SCIM-kompatibilis SCIM-végpontot kell létrehoznia és integrálnia. A felhasználók és csoportok alkalmazásba való kiépítés elindításához hajtsa végre az alábbi lépéseket. 
    
  * **[1. lépés: a felhasználói és a csoport sémájának megtervezése.](#step-1-design-your-user-and-group-schema)** Azonosítsa az alkalmazás igényeinek megfelelő objektumokat és attribútumokat, és határozza meg, hogyan képezik le az Azure AD SCIM implementációja által támogatott felhasználói és csoportosítási sémákat.

  * **[2. lépés: az Azure AD SCIM implementációjának ismertetése.](#step-2-understand-the-azure-ad-scim-implementation)** Ismerje meg, hogyan valósítja meg az Azure AD SCIM-ügyfelet, és modellezheti a SCIM protokoll kérelmek kezelését és válaszait.

  * **[3. lépés: hozzon létre egy SCIM-végpontot.](#step-3-build-a-scim-endpoint)** A végpontnak 2,0-kompatibilisnek kell lennie az Azure AD kiépítési szolgáltatással való SCIM. Lehetőség van arra, hogy a Microsoft Common Language Infrastructure (CLI) kódtárait és a kód mintáit használja a végpont létrehozásához. Ezek a minták kizárólag referenciára és tesztelésre szolgálnak; azt javasoljuk, hogy az éles alkalmazással való függőséget ne használja.

  * **[4. lépés: a SCIM-végpont integrálása az Azure AD SCIM-ügyféllel.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Ha a szervezet olyan külső alkalmazást használ, amely megvalósítja az Azure AD által támogatott SCIM 2,0-profilt, akkor azonnal elkezdheti a felhasználók és csoportok kiépítésének és megszüntetésének automatizálását.

  * **[5. lépés: az alkalmazás közzététele az Azure AD Application Galleryben.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Megkönnyíti az ügyfelek számára az alkalmazás felderítését és a kiépítés egyszerű konfigurálását. 

![A SCIM-végpont Azure AD-vel való integrálásának lépései](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>1\. lépés: a felhasználó és a csoport sémájának megtervezése

Minden alkalmazáshoz különböző attribútumok szükségesek egy felhasználó vagy csoport létrehozásához. Az integráció megkezdéséhez azonosítsa az alkalmazás által igényelt objektumokat (felhasználókat, csoportokat) és attribútumokat (név, felettes, beosztás stb.). Ezután az alábbi táblázat segítségével megismerheti, hogy az alkalmazás által igényelt attribútumok hogyan képezhetők le az Azure AD-ben és a SCIM RFC-ben található attribútumokra. Vegye figyelembe, hogy [testreszabhatja](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) az attribútumok leképezését az Azure ad és a scim végpontja között. 

A felhasználói erőforrásokat a séma azonosítója azonosítja, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, amely a protokoll specifikációjában szerepel: https://tools.ietf.org/html/rfc7643.  Az Azure AD-beli felhasználók attribútumainak alapértelmezett hozzárendelése a felhasználói erőforrások attribútumaihoz az 1. táblázatban található.  

Felhasználóicsoport-erőforrások azonosítja a séma azonosító `urn:ietf:params:scim:schemas:core:2.0:Group`. A 2. táblázat az Azure AD-beli csoportok attribútumainak alapértelmezett leképezését jeleníti meg a csoport erőforrásainak attribútumaiban.

Vegye figyelembe, hogy nem kell támogatnia a felhasználókat és a csoportokat, sem az összes alább látható attribútumot. Ezek arra utalnak, hogy az Azure AD-beli attribútumok gyakran a SCIM protokoll tulajdonságaira vannak leképezve.  

### <a name="table-1-default-user-attribute-mapping"></a>1\. táblázat: Alapértelmezett felhasználói attribútumleképezés

| Az Azure Active Directory-felhasználó | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktív |
| displayName |displayName |
| Telefax-TelephoneNumber |phoneNumbers [típus eq "fax"] .value |
| givenName |name.givenName |
| Beosztás |Cím |
| mail |e-mailek [típus eq "work"] .value |
| mailNickname |externalId |
| kezelő |kezelő |
| mobil |phoneNumbers [típus eq "mobileszköz"] .value |
| objectId |ID (Azonosító) |
| Irányítószám |.postalCode címek [típus eq "work"] |
| proxy-Addresses |e-mailek [Írja be a eq "egyéb"]. Érték |
| fizikai-kézbesítési-OfficeName |a címek [Írja be a eq "egyéb"]. Formázott |
| streetAddress |.streetAddress címek [típus eq "work"] |
| Vezetéknév |name.familyName |
| Telefonszám – |phoneNumbers [típus eq "work"] .value |
| felhasználó-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>2\. táblázat: Alapértelmezett csoport attribútumleképezés

| Azure Active Directory-csoport | urn: IETF: params: scim: sémák: Core: 2.0: Group |
| --- | --- |
| displayName |externalId |
| mail |e-mailek [típus eq "work"] .value |
| mailNickname |displayName |
| tag |tag |
| objectId |ID (Azonosító) |
| proxyAddresses |e-mailek [Írja be a eq "egyéb"]. Érték |

## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>2\. lépés: az Azure AD SCIM implementációjának ismertetése
> [!IMPORTANT]
> Az Azure AD-SCIM implementációjának viselkedését legutóbb 2018. december 18-án frissítették. További információ a változásokról: [SCIM 2,0 protokoll megfelelősége az Azure ad felhasználói kiépítési szolgáltatáshoz](application-provisioning-config-problem-scim-compatibility.md).

Ha olyan alkalmazást hoz létre, amely támogatja a SCIM 2,0 felhasználói Management API-t, ez a szakasz részletesen ismerteti az Azure AD SCIM-ügyfél megvalósításának módját. Azt is bemutatja, hogyan modellezheti a SCIM-kérelmek kezelését és válaszait. A SCIM-végpont implementálása után tesztelheti azt az előző szakaszban leírt eljárással.

Az [SCIM 2,0 protokoll specifikációja](http://www.simplecloud.info/#Specification)keretében az alkalmazásnak meg kell felelnie a következő követelményeknek:

* Támogatja a felhasználók létrehozását és opcionálisan csoportokat is, [az scim protokoll 3,3](https://tools.ietf.org/html/rfc7644#section-3.3). szakaszának megfelelően.  
* Támogatja a javítási kérelmekkel rendelkező felhasználók vagy csoportok módosítását [a scim protokoll 3.5.2. szakasza](https://tools.ietf.org/html/rfc7644#section-3.5.2)szerint.  
* A támogatja a korábban létrehozott felhasználók vagy csoportok ismert erőforrásának beolvasását, [a scim protokoll 3.4.1-es szakaszának](https://tools.ietf.org/html/rfc7644#section-3.4.1)megfelelően.  
* Támogatja a felhasználók vagy csoportok lekérdezését [a scim protokoll 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2). szakaszának megfelelően.  Alapértelmezés szerint a felhasználókat a `id` kéri le, és lekérdezi a `username` és a `externalid`, és a csoportokat `displayName`lekérdezi.  
* Támogatja a felhasználó azonosító és kezelő általi lekérdezését az SCIM protokoll 3.4.2. szakaszának megfelelően.  
* Támogatja a csoportok lekérdezését azonosító és tag szerint, a SCIM protokoll 3.4.2. szakaszának megfelelően.  
* Elfogad egyetlen tulajdonosi jogkivonatot az Azure AD hitelesítéséhez és engedélyezéséhez az alkalmazáshoz.

Az Azure AD-vel való kompatibilitás érdekében kövesse az alábbi általános irányelveket a SCIM-végpontok megvalósításához:

* `id` az összes erőforráshoz szükséges tulajdonság. Minden erőforrást visszaadó válasznak biztosítania kell, hogy minden erőforrás rendelkezik ezzel a tulajdonsággal, kivéve a nulla taggal rendelkező `ListResponse`.
* A lekérdezési/szűrési kérelemre adott válasznak mindig `ListResponse`nak kell lennie.
* A csoportok nem kötelezőek, de csak akkor támogatottak, ha a SCIM implementációja támogatja a javítási kérelmeket.
* A javítás válaszában nem szükséges a teljes erőforrás belefoglalása.
* Microsoft Azure AD csak a következő operátorokat használja:  
    - `eq`
    - `and`
* Nincs szükség a kis-és nagybetűk megkülönböztetésére a SCIM szerkezeti elemein, különösen a javítási `op` a https://tools.ietf.org/html/rfc7644#section-3.5.2ban meghatározott működési értékeket. Az Azure AD az "op" értékeket `Add`, `Replace`és `Remove`ként bocsátja ki.
* Microsoft Azure AD egy véletlenszerűen kiválasztott felhasználó és csoport beolvasását kéri a végpont és a hitelesítő adatok érvényességének biztosításához. Emellett a [Azure Portal](https://portal.azure.com) **tesztelési kapcsolati** folyamatának részeként is végrehajtja. 
* Azt az attribútumot, amely alapján az erőforrásokat le lehet kérdezni, megfelelő attribútumként kell beállítani az alkalmazásban a [Azure Portalban](https://portal.azure.com). További információ: a [felhasználói kiépítési attribútumok társításának testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-deprovisioning"></a>Felhasználók kiépítése és megszüntetése

A következő ábra azokat az üzeneteket mutatja be, amelyeket Azure Active Directory küld egy SCIM szolgáltatásnak az alkalmazás identitás-tárolójában lévő felhasználó életciklusának kezeléséhez.  

![megjeleníti a felhasználó kiépítésének és megszüntetésének folyamatát][4]<br/>
*4. ábra: a felhasználó üzembe helyezése és megszüntetési folyamata*

### <a name="group-provisioning-and-deprovisioning"></a>Csoport kiépítése és megszüntetése

A csoport kiépítése és megszüntetése nem kötelező. A implementált és a engedélyezést követően az alábbi ábra azokat az üzeneteket mutatja be, amelyeket az Azure AD a SCIM szolgáltatásnak küld egy csoport életciklusának kezeléséhez az alkalmazás identitás-tárolójában.  Ezek az üzenetek két módon különböznek a felhasználók üzeneteitől:

* A csoportok beolvasására irányuló kérések azt határozzák meg, hogy a tagok attribútumot ki kell zárni a kérelemre válaszként megadott összes erőforrásból.  
* Határozza meg, hogy rendelkezik-e a hivatkozási attribútum egy adott értéket a kérelmek azok a tag attribútum kapcsolatban.  

![megjeleníti a csoport kiépítésének és megszüntetésének menetét][5]<br/>
*5. ábra: csoport kiépítése és megszüntetési folyamata*

### <a name="scim-protocol-requests-and-responses"></a>SCIM és válaszok
Ez a szakasz példákat tartalmaz az Azure AD SCIM-ügyfél által kibocsátott SCIM-kérelmekre, és példa erre a várt válaszokra. A legjobb eredmény érdekében az alkalmazásnak az ilyen formátumú kérelmek kezelésére és a várt válaszokat kell kibocsátania.

> [!IMPORTANT]
> Ha szeretné megtudni, hogyan és mikor bocsátja ki az Azure AD-beli felhasználói kiépítési szolgáltatást az alábbiakban ismertetett műveleteket, tekintse meg, [Mi történik a felhasználó](user-provisioning.md#what-happens-during-provisioning)üzembe helyezése során?

[Felhasználói műveletek](#user-operations)
  - [Felhasználó létrehozása](#create-user) ( / [Válasz](#response)[kérése](#request) )
  - [Felhasználó beolvasása](#get-user) ( / [Válasz](#response-1)[kérése](#request-1) )
  - [Felhasználó lekérdezése lekérdezéssel](#get-user-by-query) ( / [Válasz](#response-2)[kérése](#request-2) )
  - Felhasználó lekérése [lekérdezéssel – nulla eredmények](#get-user-by-query---zero-results) (
/ [Válasz](#response-3)[kérése](#request-3) )
  - [Felhasználó frissítése [többértékű tulajdonságok]](#update-user-multi-valued-properties) ( /  [Válasz](#response-4)[kérése](#request-4) )
  - [Felhasználó frissítése [egyértékű tulajdonságok]](#update-user-single-valued-properties) (
/ [Válasz](#response-5)[kérése](#request-5) ) 
  - [Felhasználó törlése](#delete-user) ( / 
[Válasz](#response-6)[kérése](#request-6) )

[Csoportosítási műveletek](#group-operations)
  - [Csoport létrehozása](#create-group) ( / [Válasz](#response-7) [kérése](#request-7) )
  - [Csoport beolvasása](#get-group) ( / [Válasz](#response-8) [kérése](#request-8) )
  - [Csoport beolvasása DisplayName alapján](#get-group-by-displayname) ( / [Válasz](#response-9)[kérése](#request-9) )
  - [Csoport frissítése [nem tag attribútumok]](#update-group-non-member-attributes) (/
 [Válasz](#response-10)[kérése](#request-10) )
  - [Frissítési csoport [Tagok hozzáadása]](#update-group-add-members) ( /
[Válasz](#response-11) [kérése](#request-11) )
  - [Csoport frissítése [tagok eltávolítása]](#update-group-remove-members) ( /
[Válasz](#response-12) [kérése](#request-12) ) (
  - [Csoport törlése](#delete-group) ( /
[Válasz](#response-13)[kérése](#request-13) )

### <a name="user-operations"></a>Felhasználói műveletek

* A felhasználókat `userName` vagy `email[type eq "work"]` attribútumok alapján lehet lekérdezni.  

#### <a name="create-user"></a>Felhasználó létrehozása

###### <a name="request"></a>Kérés

*/Users közzététele*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Válasz

*HTTP/1.1 201 létrehozva*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Felhasználó beolvasása

###### <a name="request-1"></a>Kérelem
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Válasz (felhasználó található)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Kérés
*/Users/5171a35d82074e068ce2 beolvasása* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Válasz (a felhasználó nem található. Vegye figyelembe, hogy a részletek nem szükségesek, csak az állapot.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Felhasználó lekérése lekérdezés alapján

##### <a name="request-2"></a>Kérelem

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Válasz

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Felhasználó lekérése lekérdezéssel – nulla eredmények

##### <a name="request-3"></a>Kérelem

*/Users beolvasása? Filter = userName EQ "nem létező felhasználó"*

##### <a name="response-3"></a>Válasz

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Felhasználó frissítése [többszörös értékű tulajdonságok]

##### <a name="request-4"></a>Kérelem

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Válasz

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Felhasználó frissítése [egyértékű tulajdonságok]

##### <a name="request-5"></a>Kérelem

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Válasz

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Felhasználó törlése

##### <a name="request-6"></a>Kérelem

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Válasz

*HTTP/1.1 204 nincs tartalom*

### <a name="group-operations"></a>Csoportosítási műveletek

* A csoportokat mindig üres tagok listájával kell létrehozni.
* A csoportokat a `displayName` attribútum kérdezheti le.
* Ha frissíteni szeretne a csoport-javítási kérelemre, egy *HTTP 204* -es verzióra van szükség a válaszban. Ha egy törzset ad vissza, az összes tag listája nem ajánlott.
* Nem szükséges a csoport összes tagjának visszaküldését támogatni.

#### <a name="create-group"></a>Csoport létrehozása

##### <a name="request-7"></a>Kérelem

*/Groups közzététele HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Válasz

*HTTP/1.1 201 létrehozva*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Csoport lekérése

##### <a name="request-8"></a>Kérelem

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response-8"></a>Válasz
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Csoport beolvasása displayName alapján

##### <a name="request-9"></a>Kérelem
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>Válasz

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Csoport frissítése [nem tag attribútumok]

##### <a name="request-10"></a>Kérelem

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Válasz

*HTTP/1.1 204 nincs tartalom*

### <a name="update-group-add-members"></a>Csoport frissítése [Tagok hozzáadása]

##### <a name="request-11"></a>Kérelem

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Válasz

*HTTP/1.1 204 nincs tartalom*

#### <a name="update-group-remove-members"></a>Csoport frissítése [tagok eltávolítása]

##### <a name="request-12"></a>Kérelem

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Válasz

*HTTP/1.1 204 nincs tartalom*

#### <a name="delete-group"></a>Csoport törlése

##### <a name="request-13"></a>Kérelem

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Válasz

*HTTP/1.1 204 nincs tartalom*

## <a name="step-3-build-a-scim-endpoint"></a>3\. lépés: SCIM-végpont létrehozása

Ha olyan SCIM webszolgáltatást hoz létre, amely a Azure Active Directory felülettel rendelkezik, engedélyezheti az automatikus felhasználók üzembe helyezését gyakorlatilag bármely alkalmazáshoz vagy identitás-tárolóhoz.

Itt látható, hogyan működik:

1. Az Azure AD egy Microsoft. SystemForCrossDomainIdentityManagement nevű közös nyelvi infrastruktúra (CLI) függvénytárat biztosít, amely tartalmazza az alább ismertetett kód-mintákat. A rendszerintegrátorok és fejlesztők ezt a függvénytárat használhatják olyan SCIM-alapú webszolgáltatás-végpontok létrehozására és üzembe helyezésére, amelyek az Azure AD-t bármely alkalmazás identitás-tárolójához összekapcsolhatják.
2. A szabványos felhasználói sémájából leképezhető a felhasználó séma- és az alkalmazás által megkövetelt protokollt a web service-ben megvalósított leképezések. 
3. A végponti URL-cím regisztrálva van egy egyéni alkalmazást az alkalmazás katalógusában részeként az Azure AD-ben.
4. Ez az alkalmazás az Azure AD-felhasználók és csoportok vannak hozzárendelve. A hozzárendelés során a rendszer egy várólistába helyezi a szinkronizálást a célalkalmazás számára. A szinkronizálási folyamat kezelésére a várólista 40 percenként fut.

### <a name="code-samples"></a>Kódminták

Ahhoz, hogy ez a folyamat egyszerűbb legyen, meg kell adni a SCIM-webszolgáltatási végpontot és az automatikus kiépítés bemutatására szolgáló [kódot](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) . A minta olyan szolgáltató, amely a felhasználókat és csoportokat jelölő, vesszővel tagolt értékeket tartalmazó sorokat tart fenn.

**Előfeltételek**

* A Visual Studio 2013-as vagy újabb
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* Windows gép, amely támogatja az ASP.NET-keretrendszer 4.5-ös verzióját az SCIM-végpontként kell használni. A gépnek elérhetőnek kell lennie a felhőből.
* [Azure AD Premium érvényes próbaverziójával vagy licencével verziójával egy Azure-előfizetés](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Első lépések

A legegyszerűbb módja egy SCIM-végpontot, amely az Azure ad-ből kiépítési kérelmeket fogadhat megvalósításához készíthet és helyezhet üzembe a kódminta, amely a kiépített felhasználók egy vesszővel tagolt (CSV) fájl.

#### <a name="to-create-a-sample-scim-endpoint"></a>Minta SCIM-végpont létrehozása

1. Töltse le a kódcsomag minta: [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Tömörítse ki a csomagot, és helyezze el a Windows-gépen C:\AzureAD-BYOA-Provisioning-Samples\ például egy helyen.
1. Ebben a mappában nyissa meg a FileProvisioning\Host\FileProvisioningService.csproj projektre a Visual Studióban.
1. Válassza az **eszközök** > **NuGet Package Manager** > **csomagkezelő konzolt**, és hajtsa végre a következő parancsokat a FileProvisioningService projekthez a megoldás hivatkozásainak megoldásához:

   ```powershell
    Update-Package -Reinstall
   ```

1. A FileProvisioningService projekt buildjének elkészítéséhez.
1. Indítsa el a parancssort alkalmazást a Windows (rendszergazdaként), és használja a **cd** paranccsal lépjen be a **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**mappát.
1. Futtassa a következő parancsot, és cserélje le a `<ip-address>`t a Windows rendszerű gép IP-címére vagy tartománynevére:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. A Windows alatt a **Windows beállításai** > **hálózati & Internetbeállítások**területen válassza a **Windows tűzfal** > **Speciális beállítások**elemet, és hozzon létre egy **bejövő szabályt** , amely engedélyezi a bejövő hozzáférést a 9000-es porthoz.
1. Ha a Windows rendszerű gép egy útválasztó mögött található, az útválasztót úgy kell konfigurálni, hogy a hálózati hozzáférés fordítását a 9000-as portja és a 9000-es portja között futtassa a Windows-gépen. Ez a konfiguráció szükséges ahhoz, hogy az Azure AD hozzáférjen ehhez a végponthoz a felhőben.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>A minta SCIM-végpont regisztrálása az Azure AD-ben

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com). 
1. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
1. Válassza az **+ új alkalmazás** > **az összes** > **nem Gallery-alkalmazás**lehetőséget.
1. Adja meg az alkalmazás nevét, majd kattintson a **Hozzáadás** elemre az alkalmazás-objektum létrehozásához. A létrehozott alkalmazás objektum célja a célalkalmazással kívánja kell való üzembe helyezést, majd egyszeri bejelentkezést a, és nem csak az SCIM-végpont jelölésére.
1. Az App Management képernyőn válassza a **kiépítés** lehetőséget a bal oldali panelen.
1. Az a **Kiépítési mód** menüjében válassza **automatikus**.    
1. Az a **bérlői URL-cím** mezőben adja meg az alkalmazás SCIM-végpont URL-CÍMÉT. Például: https://api.contoso.com/scim/

1. Ha az SCIM-végpont egy OAuth tulajdonosi jogkivonat egy Azure AD-től eltérő kiállítótól van szüksége, majd másolja a szükséges OAuth tulajdonosi jogkivonat a választható **titkos jogkivonat** mező. Ha ez a mező üresen marad, az Azure AD az Azure AD-ből kiállított OAuth-tulajdonosi jogkivonatot tartalmaz minden kéréssel. Az Azure AD-t identitás-szolgáltatóként használó alkalmazások ellenőrizhetik ezt az Azure AD-kiállított jogkivonatot.
1. Válassza a **kapcsolat tesztelése** lehetőséget, hogy Azure Active Directory próbáljon csatlakozni az scim-végponthoz. Ha a kísérlet sikertelen, a rendszer hibaüzenetet jelenít meg.  

    > [!NOTE]
    > A SCIM a nem létező felhasználóhoz tartozó végpontot **kérdezi** le, véletlenszerű GUID azonosítóval, amely az Azure ad-konfigurációban kiválasztott egyező tulajdonság. A várt helyes válasz a HTTP 200 OK egy üres SCIM ListResponse-üzenettel

1. Ha az alkalmazáshoz való kapcsolódási kísérlet sikeres, kattintson a **Save (Mentés** ) gombra a rendszergazdai hitelesítő adatok mentéséhez.
1. Az a **leképezések** szakaszban, a két választható eljáráscsoport attribútumleképezések: egy a felhasználói objektumok és a egy csoport objektumainak. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználókat és csoportokat a frissítési műveleteket az alkalmazásban. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.
1. A **beállítások**, a **hatókör** mező határozza meg, hogy mely felhasználók és az or csoportok vannak szinkronizálva. Válassza a **"csak a hozzárendelt felhasználók** és csoportok szinkronizálása (ajánlott) lehetőséget, hogy csak a **felhasználók és csoportok** lapon hozzárendelt felhasználókat és csoportokat szinkronizálja.
1. A konfiguráció befejezése után állítsa be a **kiépítési állapotot** **a**következőre:.
1. Válassza a **Mentés** lehetőséget az Azure ad-kiépítési szolgáltatás elindításához.
1. Ha csak a hozzárendelt felhasználókat és csoportokat szinkronizálja (ajánlott), akkor ügyeljen arra, hogy a **felhasználók és csoportok** lapot jelölje ki, és rendelje hozzá a szinkronizálni kívánt felhasználókat vagy csoportokat.

A kezdeti ciklus megkezdése után a bal oldali panelen kiválaszthatja a **naplókat** a folyamat figyeléséhez, amely a kiépítési szolgáltatás által az alkalmazáson végzett összes műveletet megjeleníti. Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](check-status-user-account-provisioning.md).

Az utolsó lépés a minta ellenőrzése, hogy nyissa meg a TargetFile.csv fájlt a Windows-gépen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug mappában. A kiépítési folyamat futtatása után a fájl részleteit az összes hozzárendelt, és üzembe helyezett felhasználók és csoportok jeleníti meg.

### <a name="development-libraries"></a>Fejlesztési kódtárak

Segítségével hozhatja létre saját webes szolgáltatás, amely megfelel az SCIM-specifikációnak, először ismerkedjen meg az alábbi kódtárak segítségével felgyorsíthatja a fejlesztési folyamatot a Microsoft által biztosított:

* Kódtárak nyelvi infrastruktúra (CLI) alapján az infrastruktúrát, például a C# nyelv használatra érhetők el. Ezen könyvtárak egyike, a Microsoft. SystemForCrossDomainIdentityManagement. Service, deklarál egy, a Microsoft. SystemForCrossDomainIdentityManagement. IProvider felületet, amely az alábbi ábrán látható. A kódtárakat használó fejlesztők implementálják ezt az illesztőfelületet olyan osztállyal, amely általános, szolgáltatóként is hivatkozható. A kódtárak lehetővé teszik, hogy a fejlesztő olyan webszolgáltatást helyezzen üzembe, amely megfelel a SCIM specifikációjának. A webszolgáltatás a Internet Information Serviceson vagy bármely végrehajtható CLI-szerelvényen belül is üzemeltethető. Kérelem alakítja át a rendszer a szolgáltató metódusokkal, amelyeket a fejlesztő néhány ügyfélidentitás-tárolóval, a művelethez használandó lenne programozott hívásokat.
  
   ![Részletezés: A szolgáltató módszereire irányuló kérések lefordítása][3]
  
* [Express route-kezelők](https://expressjs.com/guide/routing.html) elemzéséhez node.js kérelem objektumok-hívás (megfelelően az SCIM-specifikáció szerint), amely elérhető végrehajtott egy node.js webes szolgáltatás.

### <a name="building-a-custom-scim-endpoint"></a>Egyéni SCIM-végpont létrehozása

A CLI-kódtárakat használó fejlesztők bármely végrehajtható CLI-szerelvényben vagy Internet Information Services belül tárolhatják a szolgáltatásaikat. Itt látható mintakód a címen egy végrehajtható v sestavení szolgáltatás üzemeltetéséhez http://localhost:9000: 

```csharp
 private static void Main(string[] arguments)
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
   new DevelopersMonitor();
 Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
   new DevelopersProvider(arguments[1]);
 Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
 try
 {
     webService = new WebService(monitor, provider);
     webService.Start("http://localhost:9000");

     Console.ReadKey(true);
 }
 finally
 {
     if (webService != null)
     {
         webService.Dispose();
         webService = null;
     }
 }
 }

 public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
 {
 private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
 private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

 public WebService(
   Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
   Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
 {
     this.monitor = monitoringBehavior;
     this.provider = providerBehavior;
 }

 public override IMonitor MonitoringBehavior
 {
     get
     {
         return this.monitor;
     }

     set
     {
         this.monitor = value;
     }
 }

 public override IProvider ProviderBehavior
 {
     get
     {
         return this.provider;
     }

     set
     {
         this.provider = value;
     }
 }
 }
```

Ez a szolgáltatás egy HTTP címet és a kiszolgáló hitelesítési tanúsítvánnyal kell rendelkeznie, amelynek a legfelső szintű hitelesítésszolgáltatót az alábbiak egyike a következő nevekkel: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* A VeriSign szolgáltatótól
* WoSign

Olyan kiszolgálói hitelesítési tanúsítványt a hálózati rendszerhéj segédprogrammal Windows gazdagépen köthető, port:

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

Itt a certhash argumentumhoz megadott érték a tanúsítvány ujjlenyomatát a megadott alkalmazásazonosító értéke egy tetszőleges globálisan egyedi azonosítóját.  

A szolgáltatás Internet Information Serviceson belüli üzemeltetéséhez a fejlesztő egy indítási nevű osztályt hoz létre a szerelvény alapértelmezett névterében.  Íme egy példa az ilyen osztály: 

```csharp
 public class Startup
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

 public Startup()
 {
     Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
       new DevelopersMonitor();
     Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
       new DevelopersProvider();
     this.starter = 
       new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
         provider, 
         monitor);
 }

 public void Configuration(
   Owin.IAppBuilder builder) // Defined in Owin.dll.  
 {
     this.starter.ConfigureApplication(builder);
 }
 }
```

### <a name="handling-endpoint-authentication"></a>Kezelési végpont hitelesítés

Az Azure Active Directoryból kérések az OAuth 2.0 tulajdonosi jogkivonat tartalmazzák.   A kérést fogadó bármely szolgáltatásnak hitelesítenie kell a kibocsátót úgy, hogy Azure Active Directory a várt Azure Active Directory bérlő számára, hogy hozzáférjen a Azure Active Directory Graph webszolgáltatáshoz.  A jogkivonatban a kiállítót egy ISS jogcím azonosítja, például az "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Ebben a példában a jogcím értéke, https://sts.windows.net, azonosítja Azure Active Directoryt a kiállítóként, míg a relatív cím szegmens, a cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, annak a Azure Active Directory-bérlőnek az egyedi azonosítója, amelynek a jogkivonatát kiállították. A jogkivonat célközönsége lesz az alkalmazás sablon-azonosítója a katalógusban. Az összes egyéni alkalmazáshoz tartozó 8adf8e6e-67b2-4cf2-a259-e3dc5476c621-azonosító. A katalógusban szereplő alkalmazások sablonjának azonosítója változó. Vegye fel a kapcsolatot a ProvisioningFeedback@microsoft.com kérdéseivel a Gallery-alkalmazáshoz tartozó Application Template-AZONOSÍTÓval kapcsolatban. Az egyetlen bérlőben regisztrált összes alkalmazás ugyanazon `iss` jogcímet kaphat SCIM-kérésekkel.

A SCIM szolgáltatás létrehozásához a Microsoft által biztosított CLI Azure Active Directory-kódtárakat használó fejlesztők a Microsoft. Owin. Security. ActiveDirectory csomag használatával hitelesíthetők a következő lépésekkel: 

Először a szolgáltatónál implementálja a Microsoft. SystemForCrossDomainIdentityManagement. IProvider. StartupBehavior tulajdonságot azáltal, hogy egy metódust ad vissza, amikor a szolgáltatás elindul: 

```csharp
  public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
  {
    get
    {
      return this.OnServiceStartup;
    }
  }

  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
    System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
  {
  }
```

Ezután adja hozzá a következő kódot ehhez a metódushoz, hogy bármilyen kérést adjon a szolgáltatásnak egy adott bérlő számára Azure Active Directory által kiállított jogkivonattal, az Azure AD Graph webszolgáltatáshoz való hozzáféréshez: 

```csharp
  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
    System.Web.Http.HttpConfiguration HttpConfiguration configuration)
  {
    // IFilter is defined in System.Web.Http.dll.  
    System.Web.Http.Filters.IFilter authorizationFilter = 
      new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

    // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
    // System.IdentityModel.Token.Jwt.dll.
    SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
      new TokenValidationParameters()
      {
        ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
      };

    // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
    // Microsoft.Owin.Security.ActiveDirectory.dll
    Microsoft.Owin.Security.ActiveDirectory.
    WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
      new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
      TokenValidationParameters = tokenValidationParameters,
      Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                    // identifier for this one.  
    };

    applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
  }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>A felhasználók kiépítésének és megszüntetésének kezelési felépítése

***1. példa. A szolgáltatás lekérdezése egy megfelelő felhasználó számára***

Az Azure Active Directory az Azure ad-ben a felhasználó mailNickname attribútum értéke egyező externalId attribútumértékkel rendelkező felhasználó számára a szolgáltatás lekérdezi. A lekérdezés Hypertext Transfer Protocol (HTTP) kérelemként van kifejezve, mint például az a példa, amelyben a jyoung egy, a Azure Active Directory felhasználójának egy mailNickname-mintája.

>[!NOTE]
> Ez csak példa. Nem minden felhasználó rendelkezik mailNickname attribútummal, és a felhasználó értéke nem lehet egyedi a címtárban. Továbbá a megfeleltetéshez használt attribútum (amely ebben az esetben a externalId) az [Azure ad attribútum-hozzárendelésekben](customize-application-attributes.md)konfigurálható.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Ha a szolgáltatás a Microsoft által a SCIM Services megvalósításához biztosított CLI-könyvtárakkal lett létrehozva, akkor a rendszer lefordítja a kérést a szolgáltatás szolgáltatójának lekérdezési módjára.  Itt van ez a módszer aláírása: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
   Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
   string correlationIdentifier);
```

Íme a Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters felület definíciója: 

```csharp
 public interface IQueryParameters: 
   Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
     System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
     { get; }
 }

 public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
   system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
   { get; }
   System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
   { get; }
   string SchemaIdentifier 
   { get; }
 }
```

```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
```

Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a szolgáltató lekérdezési metódus hívása.  Itt van ez a módszer aláírása: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

  System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
    Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
    string correlationIdentifier);
```

Íme a Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters felület definíciója: 

```csharp
  public interface IQueryParameters: 
    Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
      System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
      { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
    system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
    { get; }
    System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
    { get; }
    string SchemaIdentifier 
    { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
  {
      Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
        { get; set; }
      string AttributePath 
        { get; } 
      Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
        { get; }
      string ComparisonValue 
        { get; }
  }

  public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
  {
      Equals
  }
```

A lekérdezés egy felhasználó a externalId attribútum egy megadott értékkel a következő mintát a lekérdezés metódusnak átadott argumentumok értékei a következők: 
* a paraméterek. AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* a paraméterek. AlternateFilters.ElementAt(0). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
* a paraméterek. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

***2. példa. Felhasználó kiépítése***

Ha egy olyan felhasználó számára, aki egy externalId attribútum értékkel egyezik, amely megfelel a felhasználó mailNickname attribútumának, nem ad vissza felhasználót, majd Azure Active Directory kéri, hogy a szolgáltatás kiépítse a felhasználónak megfelelő felhasználót. Azure Active Directory.  Íme egy példa a kérés: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

A SCIM Services megvalósításához a Microsoft által biztosított CLI-kódtárak lefordítják a kérést a szolgáltatás szolgáltatójának létrehozási módjára.  A Create metódus az aláírás rendelkezik:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

Az egy felhasználó kiépítésére irányuló kérést az erőforrás argumentum értéke egy példányát a Microsoft.SystemForCrossDomainIdentityManagement. A Microsoft.SystemForCrossDomainIdentityManagement.Schemas könyvtárban meghatározott Core2EnterpriseUser osztály.  Ha a felhasználó kiépítésére irányuló kérést sikeres, a metódus végrehajtásának várhatóan a Microsoft.SystemForCrossDomainIdentityManagement egy példányát adja vissza. Az újonnan létrehozott felhasználó egyedi azonosítója az azonosító tulajdonsága értékét Core2EnterpriseUser osztály.  

***3. példa. Felhasználó aktuális állapotának lekérdezése*** 

Frissíteni egy felhasználó által az SCIM fronted identitás tárolóban található ismert, Azure Active Directory által a szolgáltatás egy kérelmet, mint például az, hogy a felhasználó aktuális állapotának kér folytatódik: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

A Microsoft által a SCIM-szolgáltatások megvalósítására szolgáló CLI-kódtárak használatával létrehozott szolgáltatás a kérést a szolgáltatás szolgáltatójának lekérési módszerére hívja le.  Íme a podpis metody olvashatók be:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
 // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
 // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
    Retrieve(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
        parameters, 
        string correlationIdentifier);

 public interface 
   Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
     IRetrievalParameters
     {
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
         ResourceIdentifier 
           { get; }
 }
 public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
 {
     string Identifier 
       { get; set; }
     string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
       { get; set; }
 }
```

A példában a felhasználó aktuális állapotának beolvasására irányuló kérelem, az értékeket a paraméterek argumentum értékeként megadott objektum tulajdonságai a következők: 
  
* Azonosító: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

***4. példa. A frissítendő Reference attribútum értékének lekérdezése*** 

Ha frissíteni kell egy hivatkozási attribútumot, akkor Azure Active Directory lekérdezi a szolgáltatást annak meghatározására, hogy a szolgáltatás által elindított, az identitás-tárolóban lévő Reference attribútum aktuális értéke már megegyezik-e az Azure Active-beli attribútum értékével Directory. A felhasználóknak, amelyek a jelenlegi érték lekérdezése követi, így egyetlen attribútuma a vezetői attribútumához. Íme egy példa kérést megállapítására, hogy a kezelő attribútum egy adott felhasználói objektum jelenleg jogosult-e a megadott érték: 

Ha a szolgáltatás a Microsoft által a SCIM Services megvalósításához biztosított CLI-könyvtárakkal lett létrehozva, akkor a rendszer lefordítja a kérést a szolgáltatás szolgáltatójának lekérdezési módjára. A paraméterek argumentum értékeként megadott objektum tulajdonságainak értéke a következők: 
  
* a paraméterek. AlternateFilters.Count: 2
* a paraméterek. AlternateFilters.ElementAt(x). AttributePath: "ID"
* a paraméterek. AlternateFilters.ElementAt(x). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
* paraméterek. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* a paraméterek. AlternateFilters.ElementAt(y). AttributePath: "manager"
* a paraméterek. AlternateFilters.ElementAt(y). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
* paraméterek. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* a paraméterek. RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Itt az x index értéke 0 is lehet, az y index értéke pedig 1, vagy az x értéke 1, az y értéke pedig 0, a Filter lekérdezési paraméter kifejezésének sorrendjétől függően.   

***5. példa. A felhasználó frissítése az Azure AD-ből egy SCIM-szolgáltatásba*** 

Íme egy példa egy kérést az Azure Active Directoryból frissíteni egy felhasználó egy SCIM-szolgáltatáshoz: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

A Microsoft Common Language infrastruktúra kódtárak SCIM-szolgáltatások végrehajtásának lenne a kérelem lefordítása a szolgáltató frissítési módszer hívása. Itt látható a frissítés metódus aláírása: 

```csharp
  // System.Threading.Tasks.Tasks and 
  // System.Collections.Generic.IReadOnlyCollection<T>
  // are defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
  // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

  System.Threading.Tasks.Task Update(
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
    string correlationIdentifier);

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
  {
  Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
    PatchRequest 
      { get; set; }
  Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
    ResourceIdentifier 
      { get; set; }        
  }

  public class PatchRequest2: 
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
  {
  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
      Operations
      { get;}

  public void AddOperation(
    Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
  }

  public class PatchOperation
  {
  public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
    Name
    { get; set; }

  public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
    Path
    { get; set; }

  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
    { get; }

  public void AddValue(
    Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
  }

  public enum OperationName
  {
    Add,
    Remove,
    Replace
  }

  public interface IPath
  {
    string AttributePath { get; }
    System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
    Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
  }

  public class OperationValue
  {
    public string Reference
    { get; set; }

    public string Value
    { get; set; }
  }
```

A példában egy kérelem egy felhasználó frissítéséhez a javítás argumentum értékeként megadott objektum rendelkezik a tulajdonságok értékeit: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (Mint PatchRequest2 PatchRequest). Operations.Count: 1
* (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). OperationName: OperationName.Add
* (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Path.AttributePath: "manager"
* (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.Count: 1
* (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.ElementAt(0). Referencia: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.ElementAt(0). Érték: 2819c223-7f76-453a-919d-413861904646

***6. példa. Felhasználó kiépítése***

Az Azure AD egy olyan kérelmet küld, amely egy SCIM-szolgáltatás által elküldött identitás-tárolóból kiépíti a felhasználót, például a következőt:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a Delete metódust a szolgáltató hívása.   Ez a módszer a aláírással rendelkezik: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
  System.Threading.Tasks.Task Delete(
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
      resourceIdentifier, 
    string correlationIdentifier);
```

A resourceIdentifier argumentum értékeként megadott objektum a következő tulajdonság értékeit írja be a felhasználó megszüntetésére irányuló kérelem példájában: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4\. lépés: a SCIM-végpont integrálása az Azure AD SCIM-ügyféllel

Az Azure AD beállítható úgy, hogy automatikusan kiépítse a hozzárendelt felhasználókat és csoportokat olyan alkalmazásokhoz, amelyek a [SCIM 2,0 protokoll](https://tools.ietf.org/html/rfc7644)egy adott profilját implementálják. A profil sajátosságait a [2. lépés: az Azure ad scim implementációjának ismertetése című témakör](#step-2-understand-the-azure-ad-scim-implementation)ismerteti.

Ellenőrizze az alkalmazás szolgáltatója vagy utasítások, ezek a követelmények való kompatibilitás érdekében az alkalmazás szolgáltatója dokumentációját.

> [!IMPORTANT]
> Az Azure AD SCIM implementációja az Azure AD-beli felhasználói kiépítési szolgáltatásra épül, amelynek célja, hogy folyamatosan szinkronizálja a felhasználókat az Azure AD és a célalkalmazás között, és a szabványos műveletek nagyon specifikus készletét implementálja. Fontos megérteni ezeket a viselkedéseket az Azure AD SCIM-ügyfél működésének megismerése érdekében. További információ: [Mi történik a felhasználó üzembe helyezése során?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Első lépések

Ebben a cikkben leírt az SCIM-profil támogató alkalmazások csatlakoztathatók az Azure Active Directoryhoz a az Azure AD alkalmazáskatalógusában "katalógusban nem szereplő alkalmazás" funkciójával. A csatlakozás után a Azure ad-ben minden 40 perces, ahol azt lekérdezi az alkalmazás SCIM végpont hozzárendelt felhasználók és csoportok, és létrehozza vagy módosítja őket a hozzárendelés részletei alapján futtatja a szinkronizálási folyamat.

**SCIM használatát támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com). Vegye figyelembe, hogy a [fejlesztői programra](https://developer.microsoft.com/office/dev-program) való feliratkozással a P2-licenccel rendelkező Azure Active Directory ingyenes próbaverzióját érheti el
2. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
3. Válassza az **+ új alkalmazás** > **az összes** > **nem Gallery-alkalmazás**lehetőséget.
4. Adja meg az alkalmazás nevét, majd kattintson a **Hozzáadás** elemre az alkalmazás-objektum létrehozásához. A rendszer hozzáadja az új alkalmazást a vállalati alkalmazások listájához, és megnyílik az alkalmazás-felügyeleti képernyőjén.

   ![képernyőképen az Azure AD-alkalmazás katalógusa látható][1]<br/>
   *2. ábra: Azure AD-alkalmazás katalógusa*

5. Az App Management képernyőn válassza a **kiépítés** lehetőséget a bal oldali panelen.
6. Az a **Kiépítési mód** menüjében válassza **automatikus**.

   ![példa: az alkalmazás kiépítési lapja a Azure Portal][2]<br/>
   *3. ábra: kiépítés konfigurálása a Azure Portalban*

7. Az a **bérlői URL-cím** mezőben adja meg az alkalmazás SCIM-végpont URL-CÍMÉT. Például: https://api.contoso.com/scim/
8. Ha az SCIM-végpont egy OAuth tulajdonosi jogkivonat egy Azure AD-től eltérő kiállítótól van szüksége, majd másolja a szükséges OAuth tulajdonosi jogkivonat a választható **titkos jogkivonat** mező. Ha ez a mező üresen marad, az Azure AD az Azure AD-ből kiállított OAuth-tulajdonosi jogkivonatot tartalmaz minden kéréssel. Az Azure AD-t identitás-szolgáltatóként használó alkalmazások ellenőrizhetik ezt az Azure AD-kiállított jogkivonatot. 
   > [!NOTE]
   > Ezt a mezőt ***nem*** ajánlott üresen hagyni, és az Azure ad által generált jogkivonatra támaszkodni. Ez a lehetőség elsősorban tesztelési célokra használható.
9. Válassza a **kapcsolat tesztelése** lehetőséget, hogy Azure Active Directory próbáljon csatlakozni az scim-végponthoz. Ha a kísérlet sikertelen, a rendszer hibaüzenetet jelenít meg.  

    > [!NOTE]
    > A SCIM a nem létező felhasználóhoz tartozó végpontot **kérdezi** le, véletlenszerű GUID azonosítóval, amely az Azure ad-konfigurációban kiválasztott egyező tulajdonság. A várt helyes válasz a HTTP 200 OK, amely üres SCIM ListResponse-üzenettel rendelkezik.

10. Ha az alkalmazáshoz való kapcsolódási kísérlet sikeres, kattintson a **Save (Mentés** ) gombra a rendszergazdai hitelesítő adatok mentéséhez.
11. A **leképezések** szakaszban két választható [attribútum-hozzárendelés](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)közül választhat: egyet a felhasználói objektumokhoz és egyet a csoport objektumaihoz. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználókat és csoportokat a frissítési műveleteket az alkalmazásban. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    > [!NOTE]
    > Igény szerint letilthatja a csoportobjektumokhoz, tiltsa le a "csoport" leképezési szinkronizálása.

12. A **Beállítások**területen a **hatókör** mező határozza meg, hogy mely felhasználók és csoportok legyenek szinkronizálva. Jelölje be a **csak a hozzárendelt felhasználók** és csoportok szinkronizálása (javasolt) lehetőséget, hogy csak a **felhasználók és csoportok** lapon hozzárendelt felhasználókat és csoportokat szinkronizálja.
13. A konfiguráció befejezése után állítsa be a **kiépítési állapotot** **a**következőre:.
14. Válassza a **Mentés** lehetőséget az Azure ad-kiépítési szolgáltatás elindításához.
15. Ha csak a hozzárendelt felhasználókat és csoportokat szinkronizálja (ajánlott), akkor ügyeljen arra, hogy a **felhasználók és csoportok** lapot jelölje ki, és rendelje hozzá a szinkronizálni kívánt felhasználókat vagy csoportokat.

A kezdeti ciklus elindítása után a bal oldali panelen kiválaszthatja a **kiépítési naplókat** a folyamat figyeléséhez, amely megjeleníti az alkalmazáson belüli kiépítési szolgáltatás által végrehajtott összes műveletet. Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](check-status-user-account-provisioning.md).

> [!NOTE]
> A kezdeti ciklus hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, ami körülbelül 40 percenként történik, amíg a szolgáltatás fut.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5\. lépés: az alkalmazás közzététele az Azure AD Application Galleryben

Ha egynél több bérlő által használt alkalmazást készít, azt az Azure AD Application Galleryben teheti elérhetővé. Ez megkönnyíti a szervezetek számára az alkalmazás felderítését és a kiépítés konfigurálását. Az alkalmazás közzététele az Azure AD-katalógusban és a mások számára elérhető kiépítés egyszerűvé tétele. Tekintse meg a lépéseket [itt](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing). A Microsoft együttműködik Önnel, hogy integrálja az alkalmazást a katalógusba, tesztelje a végpontot, és bocsásson ki a használati [dokumentációt](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) az ügyfelek számára. 


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Engedélyezés a kiépítési összekötők számára az alkalmazás-katalógusban
A SCIM spec nem határoz meg SCIM-specifikus sémát a hitelesítéshez és az engedélyezéshez. A meglévő iparági szabványok használatára támaszkodik. Az Azure AD-kiépítési ügyfél két engedélyezési módszert támogat a katalógusban lévő alkalmazásokhoz. 

**OAuth engedélyezési kód engedélyezése:** A kiépítési szolgáltatás támogatja az [engedélyezési kód engedélyezését](https://tools.ietf.org/html/rfc6749#page-24). Miután elküldte az alkalmazást a katalógusban való közzétételre vonatkozó kérelmét, a csapat együttműködik Önnel a következő információk összegyűjtéséhez:
*  Engedélyezési URL-cím: az ügyfél URL-címe, amely a felhasználói ügynök átirányításával szerzi be az erőforrás-tulajdonostól az engedélyt. A rendszer átirányítja a felhasználót erre az URL-címre, hogy engedélyezze a hozzáférést. 
*  Jogkivonat-Exchange URL-cím: az ügyfél URL-címe, amely egy hozzáférési jogkivonat engedélyezési engedélyének kiváltására használható, jellemzően az ügyfél-hitelesítéssel.
*  Ügyfél-azonosító: az engedélyezési kiszolgáló kiállítja a regisztrált ügyfelet az ügyfél-azonosítóval, amely egy egyedi karakterlánc, amely az ügyfél által megadott regisztrációs adatokat jelképezi.  Az ügyfél-azonosító nem titok; az erőforrás tulajdonosának van kitéve, és **nem** használható egyedül az ügyfél-hitelesítéshez.  
*  Ügyfél titka: az ügyfél titkos kulcsa az engedélyezési kiszolgáló által generált titok. Egyedi értéknek kell lennie, amely csak az engedélyezési kiszolgáló számára ismert. 

Ajánlott eljárások (javasolt, de nem kötelező):
* Több átirányítási URL-cím támogatása. A rendszergazdák a "portal.azure.com" és a "aad.portal.azure.com" típusból is konfigurálhatják az üzembe helyezést. Több átirányítási URL-cím támogatása biztosítja, hogy a felhasználók bármelyik portálról engedélyezzék a hozzáférést.
* Több titkot is támogat, hogy az állásidő nélkül zökkenőmentes legyen a titkos kulcs megújítása. 

**Hosszú életű OAuth-tulajdonosi jogkivonatok:** Ha az alkalmazás nem támogatja a OAuth-engedélyezési kód engedélyezésének folyamatát, hosszú élettartamú OAuth tulajdonosi jogkivonatot is létrehozhat, mint amennyit a rendszergazda a kiépítési integráció beállításához használhat. A tokennek véglegesnek kell lennie, különben a kiépítési feladatot a jogkivonat lejárta után [karanténba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) helyezi a rendszer. Ennek a tokennek a méretnél kisebbnek kell lennie a 1KB.  

További hitelesítési és engedélyezési módszerek esetén tudassa velünk a [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Az Azure AD-kiépítési szolgáltatás által az SCIM-kérések elvégzéséhez használt IP-címek engedélyezése

Bizonyos alkalmazások engedélyezik a bejövő adatforgalmat az alkalmazáshoz. Ahhoz, hogy az Azure AD kiépítési szolgáltatás a várt módon működjön, engedélyezni kell a használt IP-címeket. Az egyes szolgáltatási címkék/régiók IP-címeinek listáját lásd: JSON-fájl – [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519). Ezeket az IP-címeket igény szerint letöltheti és lefuttathatja a tűzfalon. Az Azure AD kiépítés számára fenntartott IP-címtartományok a "AzureActiveDirectoryDomainServices" alatt találhatók.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Felhasználói kiépítés és megszüntetés SaaS-alkalmazások automatizálása](user-provisioning.md)
* [A felhasználók átadásának attribútumleképezések testreszabása](customize-application-attributes.md)
* [Attribútum-leképezéshez kifejezések írása](functions-for-customizing-application-data.md)
* [A felhasználók átadásának Hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)
* [Alkalmazáskiépítési értesítések](user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
