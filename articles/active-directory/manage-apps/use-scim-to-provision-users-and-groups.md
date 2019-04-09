---
title: Automatikus üzembe helyezés SCIM használata az Azure Active Directory-alkalmazások |} A Microsoft Docs
description: Az Azure Active Directory-felhasználók és csoportok bármilyen alkalmazás vagy az identitás tároló, amely egy webszolgáltatás által meghatározott a SCIM-protokoll specifikációinak felhasználói felülettel fronted van automatikusan létesítsen
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/03/2019
ms.author: celested
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: a404b5e6769c7bb91b4f7b5830cea18372ec456d
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007152"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>A rendszer a tartományok közötti Identity Management (SCIM) használatával automatikus kiépítésére a felhasználók és csoportok alkalmazásokhoz az Azure Active Directoryból

## <a name="overview"></a>Áttekintés

SCIM szabványos protokollt és a sémát, amely a célja, hogy a meghajtó nagyobb konzisztencia az rendszerekből identitások kezeléséről. Ha egy alkalmazás támogatja egy SCIM-végpontot a felhasználókezelést, a kiszolgáló üzembe helyezése az Azure AD felhasználói küldhet kérelmek létrehozása, módosítása vagy törlése a hozzárendelt felhasználók és csoportok ennek a végpontnak. 

Az Azure ad-ben támogatja az alkalmazások számos [előre integrált a felhasználók automatikus átadása](../saas-apps/tutorial-list.md) SCIM megvalósításához, mint az azt jelenti, hogy a felhasználó kap a módosítási értesítésekre.  Ezek mellett ügyfelek csatlakozhatnak egy adott profiljának támogató alkalmazások a [SCIM 2.0 protokoll-meghatározása](https://tools.ietf.org/html/rfc7644) az általános "katalógusban nem szereplő" integrációs lehetőséggel az Azure Portalon. 

A fő Ez a cikk célja az SCIM 2.0-s, amely az Azure AD a katalógusban nem szereplő alkalmazások általános SCIM-összekötő részeként megvalósítja a profilban. Azonban az alkalmazás tesztelése a sikeres, amely támogatja az SCIM az általános Azure AD-összekötő egy lépést, amely egy alkalmazást az Azure AD katalógusából származó igazoló felhasználókiépítés szereplő első. Hogy alkalmazása megjelenjen az Azure AD alkalmazáskatalógusában beszerzésével kapcsolatban további információkért lásd: a [Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx).
 

>[!IMPORTANT]
>2018. December 18. utolsó frissítés az Azure AD SCIM megvalósítási viselkedését. Mi változott a további információkért lásd: [SCIM 2.0 protokoll megfelelőség az Azure AD-felhasználó kiépítési szolgáltatás](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*1. ábra: Egy alkalmazás vagy identitás tároló, amely megvalósítja az SCIM való üzembe helyezést, az Azure Active Directoryból*

Ez a cikk van felosztva, amelyek négy részből áll:

* **[Felhasználók és csoportok külső alkalmazások, amelyek támogatják az SCIM 2.0 kiépítési](#provisioning-users-and-groups-to-applications-that-support-scim)**  – Ha a szervezet használja egy külső alkalmazás, hogy valósítja meg a profil az SCIM 2.0, Azure ad-ben támogatja, elkezdheti mindkét automatizálása üzembe helyezést és megszüntetést a felhasználók és csoportok még ma.

* **[Az Azure AD SCIM végrehajtásának megértése](#understanding-the-azure-ad-scim-implementation)**  -SCIM 2.0-s felhasználói felügyeleti API-t támogató alkalmazást fejleszt, ha ez a szakasz részletesen ismerteti, hogyan valósul meg az Azure AD SCIM-ügyfél, és hogyan kell modellezniük az SCIM-protokollt kérelem kezelésére és válaszokat.
  
* **[A Microsoft CLI kódtárak használatával SCIM-végpont létrehozása](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  – közös nyelvi infrastruktúra (CLI) kódtárak és Kódminták bemutatják, hogyan fejleszthet a SCIM-végpont, és lefordítja az SCIM-üzeneteket.  

* **[Felhasználók és csoportok sémaleírása](#user-and-group-schema-reference)**  -katalógusban nem szereplő alkalmazásokat az Azure AD SCIM végrehajtása támogatja a felhasználó és csoport séma ismerteti. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Felhasználók és csoportok alkalmazásokhoz, amelyek támogatják az SCIM-kiépítés
Az Azure AD beállítható úgy, hogy az automatikusan hozzárendelt felhasználók és csoportok olyan alkalmazások, amelyek egy adott profiljának megvalósítása a [SCIM 2.0 protokoll](https://tools.ietf.org/html/rfc7644). A profil tulajdonságairól vannak dokumentálva [ismertetése az Azure AD SCIM megvalósítási](#understanding-the-azure-ad-scim-implementation).

Ellenőrizze az alkalmazás szolgáltatója vagy utasítások, ezek a követelmények való kompatibilitás érdekében az alkalmazás szolgáltatója dokumentációját.

>[!IMPORTANT]
>Az Azure AD SCIM megvalósítása az Azure AD-felhasználót létesítési szolgáltatás, amely folyamatosan szinkronizálva legyenek a felhasználók Azure AD között épül és a cél alkalmazás és a egy standard szintű műveletek nagyon meghatározott készletének valósítja meg. Fontos tudni, hogy az Azure AD SCIM-ügyfél viselkedésének megértése viselkedést. További információkért lásd: [mi történik a felhasználó kiépítése során?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Első lépések
Ebben a cikkben leírt az SCIM-profil támogató alkalmazások csatlakoztathatók az Azure Active Directoryhoz a az Azure AD alkalmazáskatalógusában "katalógusban nem szereplő alkalmazás" funkciójával. A csatlakozás után a Azure ad-ben minden 40 perces, ahol azt lekérdezi az alkalmazás SCIM végpont hozzárendelt felhasználók és csoportok, és létrehozza vagy módosítja őket a hozzárendelés részletei alapján futtatja a szinkronizálási folyamat.

**SCIM használatát támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com). 

1. Válassza ki **vállalati alkalmazások** a bal oldali ablaktáblán. Az összes konfigurált alkalmazások listája látható, beleértve az alkalmazásokat, amelyek a katalógusból lettek hozzáadva.

1. Válassza ki **+ új alkalmazás** > **összes** > **katalógusban nem szereplő alkalmazás**.

1. Adja meg az alkalmazás nevét, és válassza ki **Hozzáadás** alkalmazás objektum létrehozásához. Az új alkalmazás adnak hozzá a vállalati alkalmazások listájában, és megnyílik az alkalmazás felügyeleti képernyőre.
    
   ![][1]
   *2. ábra: Az Azure AD alkalmazáskatalógusában*
    
1. Az alkalmazás felügyeleti képernyőn válassza ki a **kiépítési** a bal oldali panelen.
1. Az a **Kiépítési mód** menüjében válassza **automatikus**.
    
   ![][2]
   *3. ábra: Létrehozás az Azure Portalon konfigurálása*
    
1. Az a **bérlői URL-cím** mezőben adja meg az alkalmazás SCIM-végpont URL-CÍMÉT. Például: https://api.contoso.com/scim/v2/
1. Ha az SCIM-végpont egy OAuth tulajdonosi jogkivonat egy Azure AD-től eltérő kiállítótól van szüksége, majd másolja a szükséges OAuth tulajdonosi jogkivonat a választható **titkos jogkivonat** mező. Ha ezt a mezőt üresen hagyja, az Azure AD az OAuth tulajdonosi jogkivonat kiállított Azure ad-ben minden egyes kérelemmel magában foglalja. Az Azure AD Identitásszolgáltatóként használó alkalmazások az Azure AD által kiállított token ellenőrizheti.
1. Válassza ki **kapcsolat tesztelése** szeretné, hogy az Azure Active Directory megpróbál csatlakozni az SCIM-végpont. Ha a kísérlet sikertelen, hiba információk jelennek meg.  

    >[!NOTE]
    >**Kapcsolat tesztelése** lekérdezi egy felhasználó, amely nem létezik, véletlenszerű GUID használja, mint a kiválasztott Azure AD-konfigurációjának megfelelő tulajdonság az SCIM végpontját. A várt helyes válasz HTTP 200 OK egy üres SCIM ListResponse üzenettel. 

1. Ha a kísérel meg csatlakozni az alkalmazás Succeed, majd válassza ki **mentése** a rendszergazdai hitelesítő adatok mentéséhez.
1. Az a **leképezések** szakaszban, a két választható eljáráscsoport attribútumleképezések: egy a felhasználói objektumok és a egy csoport objektumainak. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználókat és csoportokat a frissítési műveleteket az alkalmazásban. Válassza ki **mentése** véglegesíti a módosításokat.

    >[!NOTE]
    >Igény szerint letilthatja a csoportobjektumokhoz, tiltsa le a "csoport" leképezési szinkronizálása. 

1. A **beállítások**, a **hatókör** mező határozza meg, hogy mely felhasználók és csoportok szinkronizálása. Válassza ki **szinkronizálás csak hozzárendelt felhasználók és csoportok** (ajánlott) csak a a hozzárendelt felhasználók és csoportok szinkronizálása a **felhasználók és csoportok** fülre.
1. A konfigurálás elvégzését követően állítsa be a **üzembe helyezési állapotra** való **a**.
1. Válassza ki **mentése** az Azure AD létesítési szolgáltatás elindításához. 
1. Ha szinkronizálása csak a hozzárendelt felhasználók és csoportok (ajánlott), mindenképpen jelölje ki a **felhasználók és csoportok** lapra, és rendelje hozzá a felhasználókat vagy csoportokat szeretne szinkronizálni.

A kezdeti szinkronizálás megkezdése után kiválaszthatja **Auditnaplók** folyamat állapotának monitorozásához a bal oldali panelen, amely mutatja a kiépítési szolgáltatást a az alkalmazás által végzett valamennyi művelet. Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](check-status-user-account-provisioning.md).

> [!NOTE]
> A kezdeti szinkronizálás végrehajtásához újabb szinkronizál, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut, mint hosszabb időt vesz igénybe. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Az Azure AD SCIM végrehajtásának megértése

SCIM 2.0-s felhasználói felügyeleti API-t támogató alkalmazást fejleszt, ha ez a szakasz részletesen ismerteti, hogyan valósul meg az Azure AD SCIM-ügyfél, és hogyan az SCIM-protokollt kell modellezniük kérés kezelésére és válaszokat. Miután az SCIM-végpont megvalósítása az előző szakaszban ismertetett eljárást követve tesztelheti.

Belül a [SCIM 2.0 protokoll-meghatározása](http://www.simplecloud.info/#Specification), az alkalmazás az alábbi követelményeknek kell megfelelnie:

* Támogatja a felhasználók, és igény szerint is csoportok szakasz alapján létrehozása [3.3-as verzióját az SCIM-protokoll](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Támogatja a PATCH-kérések rendelkező felhasználók vagy csoportok módosítása megfelelően [szakasz az SCIM-protokoll 3.5.2-es](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Támogatja a felhasználó vagy csoport a korábban létrehozott ismert erőforrás beolvasása megfelelően [szakasz az SCIM-protokoll 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Támogatja a felhasználók vagy csoportok szakasz alapján lekérdezése [az SCIM-protokoll 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Alapértelmezés szerint a felhasználók által blobnevet azok `id` és kellettek azok `username` és `externalid`, és a csoportok szerint a rendszer megkérdezi `displayName`.  
* Felhasználói azonosító és a kezelő megfelelően az SCIM-protokoll 3.4.2 szakaszában lekérdezése támogatja.  
* Lekérdezi csoportok azonosítója és a tag az SCIM-protokoll 3.4.2 szakaszában megfelelően támogatja.  
* Fogadja el a hitelesítés és engedélyezés az Azure AD az alkalmazás egyetlen tulajdonosi jogkivonattal.

Egy SCIM-végpontot, az Azure AD-kompatibilitás érdekében megvalósításához, kövesse az alábbi általános irányelveket:

* `id` van egy kötelező tulajdonság az összes erőforrást. Minden válasz, amely visszaadja az erőforrás biztosítania kell az egyes erőforrások van ez a tulajdonság az alábbiakat kivéve `ListResponse` nulla tagjaival.
* A lekérdezés vagy szűrő irányuló kérelemre adott válasz mindig kell egy `ListResponse`.
* Csoportok használata nem kötelező, de csak akkor támogatott, ha az SCIM végrehajtása támogatja a PATCH kéréseknek.
* Nem szükséges a teljes erőforrást a PATCH-válaszban.
* A Microsoft Azure AD csak használja az alábbi műveleteket:  
     - `eq`
     - `and`
* Nincs szükségük az SCIM adott Patch szerkezeti elemeket a nagybetűk megkülönböztetésével `op` művelet értékek, ahogyan az az https://tools.ietf.org/html/rfc7644#section-3.5.2. Az Azure AD másként "művelet" értékét bocsát ki `Add`, `Replace`, és `Remove`.
* A Microsoft Azure AD lehetővé teszi a kérelmek beolvasni egy véletlenszerű felhasználó és csoport, győződjön meg arról, hogy a végpont és a hitelesítő adatok érvényesek. Részeként is befejeződéséről **kapcsolat tesztelése** a flow a [az Azure portal](https://portal.azure.com). 
* Az attribútumot, amely az erőforrásokat a kérhetők le kell beállítani: egy egyező attribútum a az alkalmazás a [az Azure portal](https://portal.azure.com). További információkért lásd: [felhasználó kiépítési attribútumleképezések testreszabása](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Felhasználói üzembe helyezést és megszüntetést
A következő ábra azt mutatja, hogy az Azure Active Directory által SCIM szolgáltatásba küldött üzeneteket egy felhasználó a ügyfélidentitás-tárolóval, az alkalmazás életciklusának kezeléséhez.  

![][4]
*4. ábra: Felhasználói üzembe helyezést és megszüntetést a feladatütemezés*

### <a name="group-provisioning-and-de-provisioning"></a>Csoport üzembe helyezést és megszüntetést
Csoport üzembe helyezést és megszüntetést kitöltése nem kötelező. Ha engedélyezve van megvalósítva, a következő ábra üzeneteket jeleníti meg, hogy az Azure AD elküldi egy SCIM szolgáltatásnak egy csoporthoz az ügyfélidentitás-tárolóval, az alkalmazás életciklusának kezelésére.  Ezeket az üzeneteket az üzeneteket kétféle módon felhasználókra vonatkozó különböznek: 

* Csoportok beolvasására irányuló kérelmek adja meg, hogy a tag attribútum ki lesznek zárva a válasz a kérésre megadott bármely erőforrás.  
* Határozza meg, hogy rendelkezik-e a hivatkozási attribútum egy adott értéket a kérelmek azok a tag attribútum kapcsolatban.  

![][5]
*5. ábra: Csoport üzembe helyezést és megszüntetést a feladatütemezés*

### <a name="scim-protocol-requests-and-responses"></a>SCIM protokoll kérelmek és válaszok
Ez a szakasz tartalmazza az Azure AD SCIM-ügyfél és a példa által kibocsátott példa SCIM kérelmek várt válaszokat. A legjobb eredmények érdekében érdemes kódot az alkalmazás a következő formátumban kérelmek kezelése és a várt választ küldik.

>[!IMPORTANT]
>Hogyan és mikor a kiszolgáló üzembe helyezése az Azure AD felhasználói bocsát ki az alábbiakban leírt műveletek ismertetése: [mi történik a felhasználó kiépítése során?](user-provisioning.md#what-happens-during-provisioning).

- [Felhasználói műveletek](#user-operations)
  - [Felhasználó létrehozása](#create-user)
    - [Kérés](#request)
    - [Válasz](#response)
  - [Felhasználó beolvasása](#get-user)
    - [Kérés](#request-1)
    - [Válasz](#response-1)
  - [A lekérdezés által felhasználó beolvasása](#get-user-by-query)
    - [Kérés](#request-2)
    - [Válasz](#response-2)
  - [Felhasználó által a lekérdezés - eredmény beolvasása](#get-user-by-query---zero-results)
    - [Kérés](#request-3)
    - [Válasz](#response-3)
  - [Felhasználó frissítése [többértékű tulajdonságai]](#update-user-multi-valued-properties)
    - [Kérés](#request-4)
    - [Válasz](#response-4)
  - [Felhasználó frissítése [egyértékű tulajdonságai]](#update-user-single-valued-properties)
    - [Kérés](#request-5)
    - [Válasz](#response-5)
  - [Felhasználó törlése](#delete-user)
    - [Kérés](#request-6)
    - [Válasz](#response-6)
- [Csoport-műveletek](#group-operations)
  - [Csoport létrehozása](#create-group)
    - [Kérés](#request-7)
    - [Válasz](#response-7)
  - [Csoport beolvasása](#get-group)
    - [Kérés](#request-8)
    - [Válasz](#response-8)
  - [DisplayName Get-csoportosítás](#get-group-by-displayname)
    - [Kérés](#request-9)
    - [Válasz](#response-9)
  - [Frissítési csoport [harmadik attribútumok]](#update-group-non-member-attributes)
    - [Kérés](#request-10)
    - [Válasz](#response-10)
  - [Frissítési csoport [tagok hozzáadása]](#update-group-add-members)
    - [Kérés](#request-11)
    - [Válasz](#response-11)
  - [Frissítési csoport [Remove tagok]](#update-group-remove-members)
    - [Kérés](#request-12)
    - [Válasz](#response-12)
  - [Csoport törlése](#delete-group)
    - [Kérés](#request-13)
    - [Válasz](#response-13)

### <a name="user-operations"></a>Felhasználói műveletek

* Felhasználók által kérhető le `userName` vagy `email[type eq "work"]` attribútumok.  

#### <a name="create-user"></a>Felhasználó létrehozása

###### <a name="request"></a>Kérés
*POST/felhasználók*
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
*HTTP/1.1 201-es létrehozása*
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

###### <a name="request"></a>Kérés
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>Válasz
*HTTP/1.1-ES 200 OK*
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
#### <a name="get-user-by-query"></a>A lekérdezés által felhasználó beolvasása

##### <a name="request"></a>Kérés
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>Válasz
*HTTP/1.1-ES 200 OK*
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

#### <a name="get-user-by-query---zero-results"></a>Felhasználó által a lekérdezés - eredmény beolvasása

##### <a name="request"></a>Kérés
*GET/felhasználók? filter = felhasználónév-eq "felhasználó nem létező"*

##### <a name="response"></a>Válasz
*HTTP/1.1-ES 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Felhasználó frissítése [többértékű tulajdonságai]

##### <a name="request"></a>Kérés
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

##### <a name="response"></a>Válasz
*HTTP/1.1-ES 200 OK*
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

#### <a name="update-user-single-valued-properties"></a>Felhasználó frissítése [egyértékű tulajdonságai]

##### <a name="request"></a>Kérés
*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a>Válasz
*HTTP/1.1-ES 200 OK*
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

##### <a name="request"></a>Kérés
*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>Válasz
*HTTP/1.1 204 Nincs tartalom*

### <a name="group-operations"></a>Csoport-műveletek

* Csoportok kell mindig hozható létre egy üres tagok listája.
* Csoportok kérdezhetik a `displayName` attribútum.
* A PATCH-kérés frissítést kell felfüggesztési egy *HTTP 204 Nincs tartalom* a válaszban. A lista összes tagjából álló törzséhez adatszolgáltató nem ajánlott.
* Nem a csoport összes tagját visszaadó támogatásához szükséges.

#### <a name="create-group"></a>Csoport létrehozása

##### <a name="request"></a>Kérés
*POST/groups HTTP/1.1-es*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "id": "c4d56c3c-bf3b-4e96-9b64-837018d6060e",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a>Válasz
*HTTP/1.1 201-es létrehozása*
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

#### <a name="get-group"></a>Csoport beolvasása

##### <a name="request"></a>Kérés
*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a>Válasz
*HTTP/1.1-ES 200 OK*
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

#### <a name="get-group-by-displayname"></a>DisplayName Get-csoportosítás

##### <a name="request"></a>Kérés
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a>Válasz
*HTTP/1.1-ES 200 OK*
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
#### <a name="update-group-non-member-attributes"></a>Frissítési csoport [harmadik attribútumok]

##### <a name="request"></a>Kérés
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

##### <a name="response"></a>Válasz
*HTTP/1.1 204 Nincs tartalom*

### <a name="update-group-add-members"></a>Frissítési csoport [tagok hozzáadása]

##### <a name="request"></a>Kérés
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

##### <a name="response"></a>Válasz
*HTTP/1.1 204 Nincs tartalom*

#### <a name="update-group-remove-members"></a>Frissítési csoport [Remove tagok]

##### <a name="request"></a>Kérés
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

##### <a name="response"></a>Válasz
*HTTP/1.1 204 Nincs tartalom*

#### <a name="delete-group"></a>Csoport törlése

##### <a name="request"></a>Kérés
*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>Válasz
*HTTP/1.1 204 Nincs tartalom*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>A Microsoft CLI kódtárak használatával SCIM-végpont létrehozása
Hozzon létre egy SCIM webszolgáltatás is eltárolni, amely az Azure Active Directory, engedélyezheti a felhasználók automatikus átadása gyakorlatilag bármilyen alkalmazást vagy az identitás áruházhoz.

Itt látható, hogyan működik:

1. Az Azure AD egy közös nyelvi infrastructure (CLI) kódtár nevű Microsoft.SystemForCrossDomainIdentityManagement, a minták ismertetik az alábbi kódot is biztosít. Rendszer integrátorok és fejlesztők használható hozhat létre és telepíthet egy SCIM-alapú webszolgáltatási végpontot, amely az Azure AD csatlakozhat bármely alkalmazás ügyfélidentitás-tárolóval, ebben a könyvtárban.
2. A szabványos felhasználói sémájából leképezhető a felhasználó séma- és az alkalmazás által megkövetelt protokollt a web service-ben megvalósított leképezések. 
3. A végponti URL-cím regisztrálva van egy egyéni alkalmazást az alkalmazás katalógusában részeként az Azure AD-ben.
4. Ez az alkalmazás az Azure AD-felhasználók és csoportok vannak hozzárendelve. Hozzárendelés, hogy azok még put szinkronizálni kell a célalkalmazás egy üzenetsorba. A szinkronizálási folyamat kezelésére a várólista 40 percenként fut.

### <a name="code-samples"></a>Kódminták
A folyamat megkönnyítése, [Kódminták](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) állnak rendelkezésre, amelyek hozzon létre egy SCIM web service-végpont, és mutassa be, az Automatikus kiépítés. A minta egy szolgáltató, amely egy fájl fenntartja a felhasználók és csoportok vesszővel elválasztott értékeket tartalmazó van.    

**Előfeltételek**

* A Visual Studio 2013-as vagy újabb
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* Windows gép, amely támogatja az ASP.NET-keretrendszer 4.5-ös verzióját az SCIM-végpontként kell használni. Ez a gép a felhőben elérhetőknek kell lenniük.
* [Azure AD Premium érvényes próbaverziójával vagy licencével verziójával egy Azure-előfizetés](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Első lépések
A legegyszerűbb módja egy SCIM-végpontot, amely az Azure ad-ből kiépítési kérelmeket fogadhat megvalósításához készíthet és helyezhet üzembe a kódminta, amely a kiépített felhasználók egy vesszővel tagolt (CSV) fájl.

#### <a name="to-create-a-sample-scim-endpoint"></a>A minta SCIM-végpont létrehozása

1. Töltse le a kódcsomag minta: [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Tömörítse ki a csomagot, és helyezze el a Windows-gépen C:\AzureAD-BYOA-Provisioning-Samples\ például egy helyen.
1. Ebben a mappában nyissa meg a FileProvisioning\Host\FileProvisioningService.csproj projektre a Visual Studióban.
1. Válassza ki **eszközök** > **NuGet-Csomagkezelő** > **Package Manager Console**, és hajtsa végre a következő parancsokat a A megoldás hivatkozások feloldására projekt FileProvisioningService:

   ```
    Update-Package -Reinstall
   ```

1. A FileProvisioningService projekt buildjének elkészítéséhez.
1. Indítsa el a parancssort alkalmazást a Windows (rendszergazdaként), és használja a **cd** paranccsal lépjen be a **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**mappát.
1. Futtassa a következő parancsot, és cserélje le `<ip-address>` a Windows-gép IP-cím vagy tartomány nevére:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. A Windows alatt **Windows beállítások** > **hálózat és Internet beállítások**, jelölje be a **Windows tűzfal**  >   **Speciális beállítások**, és hozzon létre egy **bejövő szabály** , amely lehetővé teszi, hogy a port 9000 a bejövő hozzáférést.
1. Ha a Windows-gép útválasztó mögött található, a útválasztót kell állítani a hálózati hozzáférés fordítási futtassa a port 9000, amely közvetlenül csatlakozik az internethez, és a port 9000 között a Windows-gépen. Ez a konfiguráció szükség az Azure ad-ben, a végpontot a felhőben.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>A minta SCIM végpont regisztrálása az Azure ad-ben

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com). 

1. Válassza ki **vállalati alkalmazások** a bal oldali ablaktáblán. Az összes konfigurált alkalmazások listája látható, beleértve az alkalmazásokat, amelyek a katalógusból lettek hozzáadva.

1. Válassza ki **+ új alkalmazás** > **összes** > **katalógusban nem szereplő alkalmazás**.

1. Adja meg az alkalmazás nevét, és válassza ki **Hozzáadás** alkalmazás objektum létrehozásához. A létrehozott alkalmazás objektum célja a célalkalmazással kívánja kell való üzembe helyezést, majd egyszeri bejelentkezést a, és nem csak az SCIM-végpont jelölésére.

1. Az alkalmazás felügyeleti képernyőn válassza ki a **kiépítési** a bal oldali panelen.

1. Az a **Kiépítési mód** menüjében válassza **automatikus**.
    
   ![][2]
   *6. ábra: Létrehozás az Azure Portalon konfigurálása*
    
1. Az a **bérlői URL-cím** mezőben adja meg az interneten közzétett URL-cím és port a SCIM-végpont. A bejegyzés a következőképpen fog kinézni http://testmachine.contoso.com:9000 vagy a http://\<ip-cím >: 9000 /, ahol \<ip-cím > az internetről elérhető IP cím. 

1. Ha az SCIM-végpont egy OAuth tulajdonosi jogkivonat egy Azure AD-től eltérő kiállítótól van szüksége, majd másolja a szükséges OAuth tulajdonosi jogkivonat a választható **titkos jogkivonat** mező. Ha ezt a mezőt üresen hagyja, az Azure AD az OAuth tulajdonosi jogkivonat kiállított Azure ad-ben minden egyes kérelemmel tartalmazza. Alkalmazások, amelyek használhatja az Azure AD Identitásszolgáltatóként ellenőrizheti ezt az Azure AD-jogkivonatot állít ki.

1. Válassza ki **kapcsolat tesztelése** szeretné, hogy az Azure Active Directory megpróbál csatlakozni az SCIM-végpont. Ha a kísérlet sikertelen, hiba információk jelennek meg.  

    >[!NOTE]
    >**Kapcsolat tesztelése** lekérdezi egy felhasználó, amely nem létezik, véletlenszerű GUID használja, mint a kiválasztott Azure AD-konfigurációjának megfelelő tulajdonság az SCIM végpontját. A várt helyes válasz üres SCIM ListResponse üzenetet, ha HTTP 200 OK

1. Ha a kísérel meg csatlakozni az alkalmazás Succeed, majd válassza ki **mentése** a rendszergazdai hitelesítő adatok mentéséhez.

1. Az a **leképezések** szakaszban, a két választható eljáráscsoport attribútumleképezések: egy a felhasználói objektumok és a egy csoport objektumainak. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználókat és csoportokat a frissítési műveleteket az alkalmazásban. Válassza ki **mentése** véglegesíti a módosításokat.

1. A **beállítások**, a **hatókör** mező határozza meg, hogy mely felhasználók és az or csoportok vannak szinkronizálva. Válassza ki **"szinkronizálás csak hozzárendelt felhasználók és csoportok** (ajánlott) csak a a hozzárendelt felhasználók és csoportok szinkronizálása a **felhasználók és csoportok** fülre.

1. A konfigurálás elvégzését követően állítsa be a **üzembe helyezési állapotra** való **a**.

1. Válassza ki **mentése** az Azure AD létesítési szolgáltatás elindításához. 

1. Ha szinkronizálása csak a hozzárendelt felhasználók és csoportok (ajánlott), mindenképpen jelölje ki a **felhasználók és csoportok** lapra, és rendelje hozzá a felhasználókat vagy csoportokat szeretne szinkronizálni.

A kezdeti szinkronizálás megkezdése után kiválaszthatja **Auditnaplók** folyamat állapotának monitorozásához a bal oldali panelen, amely mutatja a kiépítési szolgáltatást a az alkalmazás által végzett valamennyi művelet. Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](check-status-user-account-provisioning.md).

Az utolsó lépés a minta ellenőrzése, hogy nyissa meg a TargetFile.csv fájlt a Windows-gépen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug mappában. A kiépítési folyamat futtatása után a fájl részleteit az összes hozzárendelt, és üzembe helyezett felhasználók és csoportok jeleníti meg.

### <a name="development-libraries"></a>Fejlesztési kódtárak
Segítségével hozhatja létre saját webes szolgáltatás, amely megfelel az SCIM-specifikációnak, először ismerkedjen meg az alábbi kódtárak segítségével felgyorsíthatja a fejlesztési folyamatot a Microsoft által biztosított: 

- Kódtárak nyelvi infrastruktúra (CLI) alapján az infrastruktúrát, például a C# nyelv használatra érhetők el. Ezeket a kódtárakat, Microsoft.SystemForCrossDomainIdentityManagement.Service, egyik kijelenti, hogy a megfelelő interfészre, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, az alábbi ábrán látható. A könyvtárak segítségével a fejlesztők, amelyeket egy olyan osztállyal, hogy lássa, mint szolgáltatónak említett, előfordulhat, hogy kapcsolat lenne megvalósítása. A könyvtárak a fejlesztő webszolgáltatás üzembe helyezése, amely megfelel az SCIM-specifikáció használatával. A webszolgáltatás Internet Information Services vagy bármilyen végrehajtható CLI szerelvény vagy lehet üzemeltetni. Kérelem alakítja át a rendszer a szolgáltató metódusokkal, amelyeket a fejlesztő néhány ügyfélidentitás-tárolóval, a művelethez használandó lenne programozott hívásokat.
  
   ![][3]
  
- [Express route-kezelők](https://expressjs.com/guide/routing.html) elemzéséhez node.js kérelem objektumok-hívás (megfelelően az SCIM-specifikáció szerint), amely elérhető végrehajtott egy node.js webes szolgáltatás.   

### <a name="building-a-custom-scim-endpoint"></a>Egy egyéni SCIM-végpont létrehozása
A CLI-kódtárakat használó fejlesztők is szolgáltatásaikat bármilyen végrehajtható CLI szerelvény, vagy az Internet Information Services belül. Itt látható mintakód a címen egy végrehajtható v sestavení szolgáltatás üzemeltetéséhez http://localhost:9000: 

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

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Itt a certhash argumentumhoz megadott érték a tanúsítvány ujjlenyomatát a megadott alkalmazásazonosító értéke egy tetszőleges globálisan egyedi azonosítóját.  

Az Internet Information Services szolgáltatás üzemeltetéséhez, a fejlesztők a parancssori felület kód könyvtár szerelvény lenne készíthet indítási sestavení alapértelmezett névtér osztály.  Íme egy példa az ilyen osztály: 

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

### <a name="handling-endpoint-authentication"></a>Kezelési végpont hitelesítés
Az Azure Active Directoryból kérések az OAuth 2.0 tulajdonosi jogkivonat tartalmazzák.   Minden olyan szolgáltatás, a kérelem fogadása a kibocsátó, hogy az Azure Active Directory a várt Azure Active Directory-bérlő az Azure Active Directory Graph web Service Access kell hitelesíteni.  A jogkivonat a kibocsátó iss jogcím, például a "iss" által azonosított: "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Ebben a példában a jogcímérték základní adresa https://sts.windows.net, azonosítja az Azure Active Directory, a kibocsátó relatív cím szegmens, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, az Azure Active Directory-bérlő egyedi azonosítója amely a token adtak ki.  A jogkivonatot az Azure Active Directory Graph webes szolgáltatás van kiadva, akkor a szolgáltatáshoz, 00000002-0000-0000-c000-000000000000 azonosítóját kell legyen a token aud jogcím értékét.  Egyetlen új bérlő regisztrált alkalmazások jelenhet meg ugyanaz `iss` jogcím SCIM-kérelmeket.

SCIM szolgáltatás létrehozásához a Microsoft által biztosított a CLI-kódtárakat használó fejlesztők hitelesíteni tudja a kéréseket az Azure Active Directoryból a Microsoft.Owin.Security.ActiveDirectory csomag segítségével az alábbi lépéseket: 

1. Egy szolgáltató meg Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior tulajdonság visszaadása egy metódust meghív, ha a szolgáltatás elindult, hogy: 

   ```
     public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
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

2. Ez a módszer bármely kérelem hitelesíti a rendszer egy adott bérlő, az Azure AD Graph web Service Access Azure Active Directory által kiállított jogkivonatok szem előtt a szolgáltatás-végpontok bármelyikét, hogy a következő kód hozzáadása: 

   ```
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
           ValidAudience = "00000002-0000-0000-c000-000000000000"
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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Kezelési kiépítésének és megszüntetésének biztosítása a felhasználók

1. Az Azure Active Directory az Azure ad-ben a felhasználó mailNickname attribútum értéke egyező externalId attribútumértékkel rendelkező felhasználó számára a szolgáltatás lekérdezi. Hypertext Transfer Protocol (HTTP) kérelem például viselkedésmintáit jyoung-e egy felhasználó Azure Active Directory-mailNickname mintát ebben a példában a lekérdezés fejezzük ki.

    >[!NOTE]
    > Ez a példa csak. Nem minden felhasználó egy mailNickname attribútum fog rendelkezik, és a felhasználó rendelkezik értéke nem lehet egyedi a címtárban. Egyezteti az attribútum (amely ebben az esetben externalId) is lehet beállítani a [az Azure AD-attribútum-leképezéshez](customize-application-attributes.md).

   ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ````
   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának CLI kódtárak használatával lett létrehozva, a kérelem fordítja van a szolgáltató lekérdezési metódus hívása.  Itt van ez a módszer aláírása: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ````
   Íme a Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters felület definíciója: 
   ````
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
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   If the service was built using the Common Language Infrastructure libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider.  Here is the signature of that method: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Here is the definition of the Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 

   ```
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

   In the following sample of a query for a user with a given value for the externalId attribute, values of the arguments passed to the Query method are: 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. If the response to a query to the web service for a user with an externalId attribute value that matches the mailNickname attribute value of a user doesn't return any users, then Azure Active Directory requests that the service provision a user corresponding to the one in Azure Active Directory.  Here is an example of such a request: 

   ````
    POST https://.../scim/Users HTTP/1.1 engedélyezése: Tulajdonosi...  A Content-type: application/scim + json {"sémák": ["urn: ietf:params:scim:schemas:core:2.0:User", "urn: ietf:params:scim:schemas:extension:enterprise:2.0User"], "externalId": "jyoung", "felhasználónév": "jyoung", "aktív": true, "cím": null    "displayName": "Tudatjuk Young", "e-mailek": [{"type": "munkaelemet", "value": "jyoung@Contoso.com", "elsődleges": true}], "metaadatok": {"resourceType": "User"} "nevű": {"familyName": "Fiatal", "givenName": "Tudatjuk"}, "phoneNumbers": null, "preferredLa nguage": null,"title": null,"részleg": null, a"manager": NULL értékű}
   ````
   The CLI libraries provided by Microsoft for implementing SCIM services would translate that request into a call to the Create method of the service’s provider.  The Create method has this signature: 
   ````
    System.Threading.Tasks.Tasks mscorlib.dll van meghatározva.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    A System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > Létrehozás (Microsoft.SystemForCrossDomainIdentityManagement.Resource erőforrás, karakterlánc correlationIdentifier);
   ````
   In a request to provision a user, the value of the resource argument is an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, defined in the Microsoft.SystemForCrossDomainIdentityManagement.Schemas library.  If the request to provision the user succeeds, then the implementation of the method is expected to return an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, with the value of the Identifier property set to the unique identifier of the newly provisioned user.  

3. To update a user known to exist in an identity store fronted by an SCIM, Azure Active Directory proceeds by requesting the current state of that user from the service with a request such as: 
   ````
    Kérje le a ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1 engedélyezési: Tulajdonosi...
   ````
   In a service built using the CLI libraries provided by Microsoft for implementing SCIM services, the request is translated into a call to the Retrieve method of the service’s provider.  Here is the signature of the Retrieve method: 
   ````
    System.Threading.Tasks.Tasks mscorlib.dll van meghatározva.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource és / vagy Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters / / Microsoft.SystemForCrossDomainIdentityManagement.Schemas vannak meghatározva.  
    A System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > beolvasása (Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters paraméterek, karakterlánc correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        {{Get;} Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier} IRetrievalParameters nyilvános felülete Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier {karakterlánc azonosítója {get; set;} karakterlánc Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier {get; set;}}
   ````
   In the example of a request to retrieve the current state of a user, the values of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. If a reference attribute is to be updated, then Azure Active Directory queries the service to determine whether the current value of the reference attribute in the identity store fronted by the service already matches the value of that attribute in Azure Active Directory. For users, the only attribute of which the current value is queried in this way is the manager attribute. Here is an example of a request to determine whether the manager attribute of a particular user object currently has a certain value: 

   If the service was built using the CLI libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider. The value of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Here, the value of the index x can be 0 and the value of the index y can be 1, or the value of x can be 1 and the value of y can be 0, depending on the order of the expressions of the filter query parameter.   

5. Here is an example of a request from Azure Active Directory to an SCIM service to update a user: 
   ````
    JAVÍTÁS ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1 engedélyezése: Tulajdonosi...  A Content-type: application/scim + json {"sémák": ["urn: ietf:params:scim:api:messages:2.0:PatchOp"], "Műveletek": [{"művelet": "Add", "elérési út": "manager", "value": [{"$ref": "http://.../scim/Users/2819c223-7f76-453a-919d-413861904646", "value": "2819c223-7f76-453a-919d-413861904646"}]}]}
   ````
   The Microsoft CLI libraries for implementing SCIM services would translate the request into a call to the Update method of the service’s provider. Here is the signature of the Update method: 
   ````
    System.Threading.Tasks.Tasks és / vagy System.Collections.Generic.IReadOnlyCollection<T> / / mscorlib.dll vannak meghatározva.  
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, / / Microsoft.SystemForCrossDomainIdentityManagement.OperationName, / / Microsoft.SystemForCrossDomainIdentityManagement.IPath és / vagy Microsoft.SystemForCrossDomainIdentityManagement.OperationValue / / az összes meghatározott Microsoft.SystemForCrossDomainIdentityManagement.Protocol vannak. 

    System.Threading.Tasks.Task frissítés (Microsoft.SystemForCrossDomainIdentityManagement.IPatch javítás, karakterlánc correlationIdentifier);

    nyilvános felülete Microsoft.SystemForCrossDomainIdentityManagement.IPatch {Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase PatchRequest {get; set;} {Get; set;} Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier        
    }

    nyilvános osztály PatchRequest2:    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase {nyilvános System.Collections.Generic.IReadOnlyCollection < Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation > Operations {get;}


   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a szolgáltató lekérdezési metódus hívása. A paraméterek argumentum értékeként megadott objektum tulajdonságainak értéke a következők: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: „AZONOSÍTÓ”
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* a paraméterek. AlternateFilters.ElementAt(y). AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): „AZONOSÍTÓ”
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Itt az index x értéke lehet 0 és az index y értéke 1, lehet vagy x értéke lehet 1 és y értékének lehet 0, attól függően, a szűrő lekérdezési paraméter a kifejezések sorrendjét.   

1. Íme egy példa egy kérést az Azure Active Directoryból frissíteni egy felhasználó egy SCIM-szolgáltatáshoz: 

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

   ```
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

1. Megszüntetése a felhasználói identitás adattárba fronted SCIM szolgáltatás által, az Azure AD egy kérést küld például: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a Delete metódust a szolgáltató hívása.   Ez a módszer a aláírással rendelkezik: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Az objektum az resourceIdentifier argumentum értékeként megadott a tulajdonságok értékeit a példában egy kérelem megszüntetése a felhasználó rendelkezik: 

1. Megszüntetése a felhasználói identitás adattárba fronted SCIM szolgáltatás által, az Azure AD egy kérést küld például: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának CLI kódtárak használatával lett létrehozva, a kérelem fordítja van a Delete metódust a szolgáltató hívása.   Ez a módszer a aláírással rendelkezik: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   Az objektum az resourceIdentifier argumentum értékeként megadott a tulajdonságok értékeit a példában egy kérelem megszüntetése a felhasználó rendelkezik: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Felhasználók és csoportok adatbázisséma hivatkozása
Az Azure Active Directoryban két típusú erőforrásokat az SCIM-webszolgáltatások helyezhet üzembe.  Ilyen típusú erőforrások a felhasználók és csoportok.  

Felhasználói erőforrásokat azonosítja a séma azonosító `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, amely tartalmazza a protokoll-meghatározása: https://tools.ietf.org/html/rfc7643.  Az alapértelmezett leképezést a felhasználó attribútumait az Azure Active Directory felhasználói attribútumok biztosítunk az 1.  

Felhasználóicsoport-erőforrások azonosítja a séma azonosító `urn:ietf:params:scim:schemas:core:2.0:Group`. 2. táblázat az alapértelmezett leképezést az attribútumok a csoportok az Azure Active Directory csoport erőforrások attribútumait mutatja.  

### <a name="table-1-default-user-attribute-mapping"></a>1. táblázat: Alapértelmezett felhasználói attribútumleképezés

| Az Azure Active Directory-felhasználó | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktív |
| displayName |displayName |
| Telefax-TelephoneNumber |phoneNumbers [típus eq "fax"] .value |
| givenName |name.givenName |
| Beosztás |cím |
| levelezés |e-mailek [típus eq "work"] .value |
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
| felhasználó-PrincipalName |Felhasználónév |

### <a name="table-2-default-group-attribute-mapping"></a>2. táblázat: Alapértelmezett csoport attribútumleképezés

| Azure Active Directory-csoport | urn: ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| levelezés |e-mailek [típus eq "work"] .value |
| mailNickname |displayName |
| tag |tag |
| objectId |ID (Azonosító) |
| proxyAddresses |e-mailek [Írja be a eq "egyéb"]. Érték |


## <a name="related-articles"></a>Kapcsolódó cikkek
* [Felhasználói kiépítés és megszüntetés SaaS-alkalmazások automatizálása](user-provisioning.md)
* [A felhasználók átadásának attribútumleképezések testreszabása](customize-application-attributes.md)
* [Kifejezések írása attribútum-leképezéshez](functions-for-customizing-application-data.md)
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
