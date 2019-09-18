---
title: Automatikus üzembe helyezés SCIM használata az Azure Active Directory-alkalmazások |} A Microsoft Docs
description: Az Azure Active Directory-felhasználók és csoportok bármilyen alkalmazás vagy az identitás tároló, amely egy webszolgáltatás által meghatározott a SCIM-protokoll specifikációinak felhasználói felülettel fronted van automatikusan létesítsen
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
ms.date: 07/31/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: b135838558a493cff0e28a8429d31f5a03a69857
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033460"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>A rendszer a tartományok közötti Identity Management (SCIM) használatával automatikus kiépítésére a felhasználók és csoportok alkalmazásokhoz az Azure Active Directoryból

A SCIM szabványosított protokoll és séma, amelynek célja, hogy nagyobb konzisztencia legyen az identitások rendszerek közötti felügyeletében. Ha egy alkalmazás támogatja a SCIM-végpontot a felhasználói felügyelethez, az Azure AD-felhasználó kiépítési szolgáltatása kéréseket küldhet a hozzárendelt felhasználók és csoportok létrehozásához, módosításához vagy törléséhez ehhez a végponthoz.

Számos alkalmazás, amely esetében az Azure AD támogatja az [előzetesen integrált automatikus felhasználó-kiépítés](../saas-apps/tutorial-list.md) megvalósítását, scim, mint a felhasználói módosítási értesítések fogadásának módja.  Ezen kívül az ügyfelek olyan alkalmazásokat is összekapcsolhatnak, amelyek támogatják a [SCIM 2,0 protokoll specifikációjának](https://tools.ietf.org/html/rfc7644) egy adott profilját, az általános "nem katalógus" integrációs lehetőség használatával a Azure Portalban.

Ennek a cikknek a fő témája a SCIM 2,0 profilja, amelyet az Azure AD az általános SCIM-összekötő részeként implementál a nem Gallery-alkalmazásokhoz. Egy olyan alkalmazás sikeres tesztelése azonban, amely támogatja a SCIM-t az általános Azure AD-összekötővel, egy lépés az Azure AD-katalógusban szereplő alkalmazás beszerzésére, amely támogatja a felhasználók üzembe helyezését. Az alkalmazás Azure ad-alkalmazás-katalógusban való beszerzésével kapcsolatos további információkért [lásd: How to: Az alkalmazás listázása az Azure AD Application Galleryben](../develop/howto-app-gallery-listing.md).

> [!IMPORTANT]
> Az Azure AD-SCIM implementációjának viselkedését legutóbb 2018. december 18-án frissítették. További információ a változásokról: [SCIM 2,0 protokoll megfelelősége az Azure ad felhasználói kiépítési szolgáltatáshoz](application-provisioning-config-problem-scim-compatibility.md).

![Kiépítés megjelenítése az Azure AD-ből egy alkalmazásba vagy egy identitás-tárolóba][0]<br/>
*1. ábra: Kiépítés Azure Active Directoryból egy olyan alkalmazás-vagy SCIM, amely megvalósítja a következőt:*

Ez a cikk négy szakaszra oszlik:

* **[Felhasználók és csoportok kiépítése harmadik féltől származó, a 2,0 scim-t támogató alkalmazások](#provisioning-users-and-groups-to-applications-that-support-scim)** számára – ha a szervezet olyan külső alkalmazást használ, amely az Azure ad által támogatott scim 2,0 profilt valósítja meg, elkezdheti a kiépítés automatizálását is a felhasználók és csoportok kiépítés ma.
* **[Az Azure ad-scim implementációjának ismertetése](#understanding-the-azure-ad-scim-implementation)** – ha olyan alkalmazást készít, amely támogatja a scim 2,0 felhasználó-felügyeleti API-t, ez a szakasz részletesen ismerteti az Azure ad scim-ügyfél megvalósításának módját, valamint azt, hogy miként modellezheti a scim-protokollt kérelmek kezelését és válaszait.
* **[Scim-végpont létrehozása a Microsoft CLI-kódtárak használatával](#building-a-scim-endpoint-using-microsoft-cli-libraries)** – a Common Language Infrastructure (CLI) kódtárai és a scim-végpontok bemutatják, hogyan fejleszthető ki egy-végpont, és hogyan fordíthatók le scim  
* **[Felhasználói és csoport sémájának leírása](#user-and-group-schema-reference)** – az Azure ad scim implementációja által támogatott felhasználói és csoportosítási sémát ismerteti a nem katalógusbeli alkalmazásokhoz.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Felhasználók és csoportok alkalmazásokhoz, amelyek támogatják az SCIM-kiépítés

Az Azure AD beállítható úgy, hogy automatikusan kiépítse a hozzárendelt felhasználókat és csoportokat olyan alkalmazásokhoz, amelyek a [SCIM 2,0 protokoll](https://tools.ietf.org/html/rfc7644)egy adott profilját implementálják. A profil sajátosságait az [Azure ad scim implementációjának megismerése](#understanding-the-azure-ad-scim-implementation)című dokumentumban ismertetjük.

Ellenőrizze az alkalmazás szolgáltatója vagy utasítások, ezek a követelmények való kompatibilitás érdekében az alkalmazás szolgáltatója dokumentációját.

> [!IMPORTANT]
> Az Azure AD SCIM implementációja az Azure AD-beli felhasználói kiépítési szolgáltatásra épül, amelynek célja, hogy folyamatosan szinkronizálja a felhasználókat az Azure AD és a célalkalmazás között, és a szabványos műveletek nagyon specifikus készletét implementálja. Fontos megérteni ezeket a viselkedéseket az Azure AD SCIM-ügyfél működésének megismerése érdekében. További információ: [Mi történik a felhasználó üzembe helyezése során?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Első lépések

Ebben a cikkben leírt az SCIM-profil támogató alkalmazások csatlakoztathatók az Azure Active Directoryhoz a az Azure AD alkalmazáskatalógusában "katalógusban nem szereplő alkalmazás" funkciójával. A csatlakozás után a Azure ad-ben minden 40 perces, ahol azt lekérdezi az alkalmazás SCIM végpont hozzárendelt felhasználók és csoportok, és létrehozza vagy módosítja őket a hozzárendelés részletei alapján futtatja a szinkronizálási folyamat.

**SCIM használatát támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com). 
1. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
1. Válassza az **+ új alkalmazás** > **minden** > nem katalógusbeli**alkalmazás**lehetőséget.
1. Adja meg az alkalmazás nevét, majd kattintson a **Hozzáadás** elemre az alkalmazás-objektum létrehozásához. A rendszer hozzáadja az új alkalmazást a vállalati alkalmazások listájához, és megnyílik az alkalmazás-felügyeleti képernyőjén.

   ![Képernyőfelvétel az Azure AD Application galleryről][1]<br/>
   *2. ábra: Azure AD-alkalmazás katalógusa*

1. Az App Management képernyőn válassza a **kiépítés** lehetőséget a bal oldali panelen.
1. Az a **Kiépítési mód** menüjében válassza **automatikus**.

   ![Példa: Egy alkalmazás kiépítési lapja a Azure Portal][2]<br/>
   *3. ábra: A kiépítés konfigurálása a Azure Portalban*

1. Az a **bérlői URL-cím** mezőben adja meg az alkalmazás SCIM-végpont URL-CÍMÉT. Például: https://api.contoso.com/scim/
1. Ha az SCIM-végpont egy OAuth tulajdonosi jogkivonat egy Azure AD-től eltérő kiállítótól van szüksége, majd másolja a szükséges OAuth tulajdonosi jogkivonat a választható **titkos jogkivonat** mező. Ha ez a mező üresen marad, az Azure AD az Azure AD-ből kiállított OAuth-tulajdonosi jogkivonatot tartalmaz minden kéréssel. Az Azure AD-t identitás-szolgáltatóként használó alkalmazások ellenőrizhetik ezt az Azure AD-kiállított jogkivonatot.
1. Válassza a **kapcsolat tesztelése** lehetőséget, hogy Azure Active Directory próbáljon csatlakozni az scim-végponthoz. Ha a kísérlet sikertelen, a rendszer hibaüzenetet jelenít meg.  

    > [!NOTE]
    > A SCIM a nem létező felhasználóhoz tartozó végpontot **kérdezi** le, véletlenszerű GUID azonosítóval, amely az Azure ad-konfigurációban kiválasztott egyező tulajdonság. A várt helyes válasz a HTTP 200 OK, amely üres SCIM ListResponse-üzenettel rendelkezik.

1. Ha az alkalmazáshoz való kapcsolódási kísérlet sikeres, kattintson a **Save (Mentés** ) gombra a rendszergazdai hitelesítő adatok mentéséhez.
1. Az a **leképezések** szakaszban, a két választható eljáráscsoport attribútumleképezések: egy a felhasználói objektumok és a egy csoport objektumainak. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználókat és csoportokat a frissítési műveleteket az alkalmazásban. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    > [!NOTE]
    > Igény szerint letilthatja a csoportobjektumokhoz, tiltsa le a "csoport" leképezési szinkronizálása.

1. A **Beállítások**területen a **hatókör** mező határozza meg, hogy mely felhasználók és csoportok legyenek szinkronizálva. Jelölje be a **csak a hozzárendelt felhasználók** és csoportok szinkronizálása (javasolt) lehetőséget, hogy csak a **felhasználók és csoportok** lapon hozzárendelt felhasználókat és csoportokat szinkronizálja.
1. A konfiguráció befejezése után állítsa be a **kiépítési állapotot** **a**következőre:.
1. Válassza a **Mentés** lehetőséget az Azure ad-kiépítési szolgáltatás elindításához.
1. Ha csak a hozzárendelt felhasználókat és csoportokat szinkronizálja (ajánlott), akkor ügyeljen arra, hogy a **felhasználók és csoportok** lapot jelölje ki, és rendelje hozzá a szinkronizálni kívánt felhasználókat vagy csoportokat.

A kezdeti ciklus megkezdése után a bal oldali panelen kiválaszthatja a **naplókat** a folyamat figyeléséhez, amely a kiépítési szolgáltatás által az alkalmazáson végzett összes műveletet megjeleníti. Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](check-status-user-account-provisioning.md).

> [!NOTE]
> A kezdeti ciklus hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, ami körülbelül 40 percenként történik, amíg a szolgáltatás fut.

## <a name="understanding-the-azure-ad-scim-implementation"></a>Az Azure AD SCIM implementációjának ismertetése

Ha olyan alkalmazást hoz létre, amely támogatja a SCIM 2,0 felhasználó-felügyeleti API-t, ez a szakasz részletesen ismerteti az Azure AD SCIM-ügyfél megvalósításának módját, valamint azt, hogy miként modellezheti a SCIM protokoll kérelmek kezelését és válaszait. A SCIM-végpont implementálása után tesztelheti azt az előző szakaszban leírt eljárással.

Az [SCIM 2,0 protokoll specifikációja](http://www.simplecloud.info/#Specification)keretében az alkalmazásnak meg kell felelnie a következő követelményeknek:

* Támogatja a felhasználók létrehozását és opcionálisan csoportokat is, [az scim protokoll 3,3](https://tools.ietf.org/html/rfc7644#section-3.3). szakaszának megfelelően.  
* Támogatja a javítási kérelmekkel rendelkező felhasználók vagy csoportok módosítását [a scim protokoll 3.5.2. szakasza](https://tools.ietf.org/html/rfc7644#section-3.5.2)szerint.  
* A támogatja a korábban létrehozott felhasználók vagy csoportok ismert erőforrásának beolvasását, [a scim protokoll 3.4.1-es szakaszának](https://tools.ietf.org/html/rfc7644#section-3.4.1)megfelelően.  
* Támogatja a felhasználók vagy csoportok lekérdezését [a scim protokoll 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2). szakaszának megfelelően.  Alapértelmezés szerint a felhasználók lekérik a és `id` a által lekérdezett `username` `externalid`felhasználókat, és a csoportokat a által kérdezik `displayName`le.  
* Támogatja a felhasználó azonosító és kezelő általi lekérdezését az SCIM protokoll 3.4.2. szakaszának megfelelően.  
* Támogatja a csoportok lekérdezését azonosító és tag szerint, a SCIM protokoll 3.4.2. szakaszának megfelelően.  
* Elfogad egyetlen tulajdonosi jogkivonatot az Azure AD hitelesítéséhez és engedélyezéséhez az alkalmazáshoz.

Az Azure AD-vel való kompatibilitás érdekében kövesse az alábbi általános irányelveket a SCIM-végpontok megvalósításához:

* `id`az összes erőforráshoz kötelező tulajdonság. Minden erőforrást visszaadó válasznak biztosítania kell, hogy minden erőforrás rendelkezik ezzel `ListResponse` a tulajdonsággal, kivéve a nulla taggal.
* A lekérdezési/szűrési kérelemre adott válasznak `ListResponse`mindig a következőnek kell lennie:.
* A csoportok nem kötelezőek, de csak akkor támogatottak, ha a SCIM implementációja támogatja a javítási kérelmeket.
* A javítás válaszában nem szükséges a teljes erőforrás belefoglalása.
* Microsoft Azure AD csak a következő operátorokat használja:  
     - `eq`
     - `and`
* Nincs szükség a kis-és nagybetűk megkülönböztetésére a scim szerkezeti elemein, `op` különösen a javítás műveleti értékein https://tools.ietf.org/html/rfc7644#section-3.5.2 , ahogy az a-ben van meghatározva. Az Azure ad az "op" `Add` `Replace`értékeket bocsátja ki, és `Remove`.
* Microsoft Azure AD egy véletlenszerűen kiválasztott felhasználó és csoport beolvasását kéri a végpont és a hitelesítő adatok érvényességének biztosításához. Emellett a [Azure Portal](https://portal.azure.com) **tesztelési kapcsolati** folyamatának részeként is végrehajtja. 
* Azt az attribútumot, amely alapján az erőforrásokat le lehet kérdezni, megfelelő attribútumként kell beállítani az alkalmazásban a [Azure Portalban](https://portal.azure.com). További információ: a [felhasználói kiépítési attribútumok társításának testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Felhasználói üzembe helyezést és megszüntetést

A következő ábra azokat az üzeneteket mutatja be, amelyeket Azure Active Directory küld egy SCIM szolgáltatásnak az alkalmazás identitás-tárolójában lévő felhasználó életciklusának kezeléséhez.  

![A felhasználó kiépítési és kiépítési sorrendjének megjelenítése][4]<br/>
*4. ábra: A felhasználók üzembe helyezése és a kiépítés kiépítési sorozata*

### <a name="group-provisioning-and-de-provisioning"></a>Csoport üzembe helyezést és megszüntetést

A csoportos kiépítés és a kiépítés nem kötelező. A implementált és a engedélyezést követően az alábbi ábra azokat az üzeneteket mutatja be, amelyeket az Azure AD a SCIM szolgáltatásnak küld egy csoport életciklusának kezeléséhez az alkalmazás identitás-tárolójában.  Ezek az üzenetek két módon különböznek a felhasználók üzeneteitől:

* A csoportok beolvasására irányuló kérések azt határozzák meg, hogy a tagok attribútumot ki kell zárni a kérelemre válaszként megadott összes erőforrásból.  
* Határozza meg, hogy rendelkezik-e a hivatkozási attribútum egy adott értéket a kérelmek azok a tag attribútum kapcsolatban.  

![A csoport kiépítés és kiépítési sorozatának megjelenítése][5]<br/>
*5. ábra: Csoportos kiépítés és kiépítési sorozat*

### <a name="scim-protocol-requests-and-responses"></a>SCIM és válaszok
Ez a szakasz példákat tartalmaz az Azure AD SCIM-ügyfél által kibocsátott SCIM-kérelmekre, és példa erre a várt válaszokra. A legjobb eredmény érdekében az alkalmazásnak az ilyen formátumú kérelmek kezelésére és a várt válaszokat kell kibocsátania.

> [!IMPORTANT]
> Ha szeretné megtudni, hogyan és mikor bocsátja ki az Azure AD-beli felhasználó-kiépítési szolgáltatást az alább ismertetett műveletek elvégzésére, tekintse meg a [Mi történik a felhasználó üzembe helyezése során?](user-provisioning.md#what-happens-during-provisioning)

- [Felhasználói műveletek](#user-operations)
  - [Felhasználó létrehozása](#create-user)
    - [Kérés](#request)
    - [Válasz](#response)
  - [Felhasználó beolvasása](#get-user)
    - [Kérés](#request-1)
    - [Válasz](#response-1)
  - [Felhasználó lekérése lekérdezés alapján](#get-user-by-query)
    - [Kérés](#request-2)
    - [Válasz](#response-2)
  - [Felhasználó lekérése lekérdezéssel – nulla eredmények](#get-user-by-query---zero-results)
    - [Kérés](#request-3)
    - [Válasz](#response-3)
  - [Felhasználó frissítése [többszörös értékű tulajdonságok]](#update-user-multi-valued-properties)
    - [Kérés](#request-4)
    - [Válasz](#response-4)
  - [Felhasználó frissítése [egyértékű tulajdonságok]](#update-user-single-valued-properties)
    - [Kérés](#request-5)
    - [Válasz](#response-5)
  - [Felhasználó törlése](#delete-user)
    - [Kérés](#request-6)
    - [Válasz](#response-6)
- [Csoportosítási műveletek](#group-operations)
  - [Csoport létrehozása](#create-group)
    - [Kérés](#request-7)
    - [Válasz](#response-7)
  - [Csoport beolvasása](#get-group)
    - [Kérés](#request-8)
    - [Válasz](#response-8)
  - [Csoport beolvasása displayName alapján](#get-group-by-displayname)
    - [Kérés](#request-9)
    - [Válasz](#response-9)
  - [Csoport frissítése [nem tag attribútumok]](#update-group-non-member-attributes)
    - [Kérés](#request-10)
    - [Válasz](#response-10)
  - [Csoport frissítése [Tagok hozzáadása]](#update-group-add-members)
    - [Kérés](#request-11)
    - [Válasz](#response-11)
  - [Csoport frissítése [tagok eltávolítása]](#update-group-remove-members)
    - [Kérés](#request-12)
    - [Válasz](#response-12)
  - [Csoport törlése](#delete-group)
    - [Kérés](#request-13)
    - [Válasz](#response-13)

### <a name="user-operations"></a>Felhasználói műveletek

* A `userName` felhasználókat lekérdezéssel vagy `email[type eq "work"]` attribútumokkal lehet lekérdezni.  

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

###### <a name="response-1"></a>Válasz
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
* A csoportokat az `displayName` attribútum kérdezheti le.
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
    "members": [],
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

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>SCIM-végpont létrehozása a Microsoft CLI-kódtárak használatával

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
1. Válassza az **eszközök** > **NuGet Package** > Manager-**konzol**lehetőséget, majd hajtsa végre a következő parancsokat a FileProvisioningService projekthez a megoldás hivatkozásainak feloldásához:

   ```powershell
    Update-Package -Reinstall
   ```

1. A FileProvisioningService projekt buildjének elkészítéséhez.
1. Indítsa el a parancssort alkalmazást a Windows (rendszergazdaként), és használja a **cd** paranccsal lépjen be a **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**mappát.
1. Futtassa a következő parancsot, és `<ip-address>` cserélje le a kifejezést a Windows-gép IP-címére vagy tartománynevére:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. A Windows alatt a **Windows beállításai** > **hálózati & Internetbeállítások**területen válassza ki **a Windows tűzfal** > **speciális beállításait**, és hozzon létre egy **bejövő szabályt** , amely engedélyezi a bejövő hozzáférést a porthoz. 9000.
1. Ha a Windows rendszerű gép egy útválasztó mögött található, az útválasztót úgy kell konfigurálni, hogy a hálózati hozzáférés fordítását a 9000-as portja és a 9000-es portja között futtassa a Windows-gépen. Ez a konfiguráció szükséges ahhoz, hogy az Azure AD hozzáférjen ehhez a végponthoz a felhőben.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>A minta SCIM-végpont regisztrálása az Azure AD-ben

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com). 
1. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
1. Válassza az **+ új alkalmazás** > **minden** > nem katalógusbeli**alkalmazás**lehetőséget.
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
  
   ![Bontás A szolgáltató metódusai felé irányuló kérések lefordítása][3]
  
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

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

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

Az Azure Active Directoryból kérések az OAuth 2.0 tulajdonosi jogkivonat tartalmazzák.   A kérést fogadó bármely szolgáltatásnak hitelesítenie kell a kibocsátót úgy, hogy Azure Active Directory a várt Azure Active Directory bérlő számára, hogy hozzáférjen a Azure Active Directory Graph webszolgáltatáshoz.  A jogkivonatban a kiállítót egy ISS jogcím azonosítja, például az "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ".  Ebben a példában a jogcím értékének https://sts.windows.net alapszintű címe, amely a kiállítóként azonosítja Azure Active Directory, míg a relatív cím szegmens, a cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 a Azure Active Directory bérlő egyedi azonosítója a következőhöz: a jogkivonat kiállítása. A jogkivonat célközönsége lesz az alkalmazás sablon-azonosítója a katalógusban. Az összes egyéni alkalmazáshoz tartozó 8adf8e6e-67b2-4cf2-a259-e3dc5476c621-azonosító. A katalógusban szereplő alkalmazások sablonjának azonosítója változó. Vegye fel ProvisioningFeedback@microsoft.com a kapcsolatot a Gallery-alkalmazáshoz tartozó alkalmazás sablonjának azonosítójával kapcsolatos kérdésekkel. Az egyes bérlők által regisztrált alkalmazások ugyanazt `iss` a jogcímet kapják meg a scim-kérelmekkel.

A SCIM szolgáltatás létrehozásához a Microsoft által biztosított CLI Azure Active Directory-kódtárakat használó fejlesztők a Microsoft. Owin. Security. ActiveDirectory csomag használatával hitelesíthetők a következő lépésekkel: 

1. Egy szolgáltató meg Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior tulajdonság visszaadása egy metódust meghív, ha a szolgáltatás elindult, hogy: 

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

1. Adja hozzá a következő kódot a metódushoz, hogy bármilyen kérést Kérjen a szolgáltatás egy adott bérlő számára Azure Active Directory által kiállított jogkivonattal, az Azure AD Graph webszolgáltatáshoz való hozzáféréshez: 

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

1. Az Azure Active Directory az Azure ad-ben a felhasználó mailNickname attribútum értéke egyező externalId attribútumértékkel rendelkező felhasználó számára a szolgáltatás lekérdezi. A lekérdezés Hypertext Transfer Protocol (HTTP) kérelemként van kifejezve, mint például az a példa, amelyben a jyoung egy, a Azure Active Directory felhasználójának egy mailNickname-mintája.

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
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ÖsszehasonlítóOperátor. Equals
   * a paraméterek. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
   * correlationIdentifier: System .net. http. HttpRequestMessage. GetOwinEnvironment ["owin. Kérelemazonosító "] 

1. Ha egy olyan felhasználó számára, aki egy externalId attribútum értékkel egyezik, amely megfelel a felhasználó mailNickname attribútumának, nem ad vissza felhasználót, majd Azure Active Directory kéri, hogy a szolgáltatás kiépítse a felhasználónak megfelelő felhasználót. Azure Active Directory.  Íme egy példa a kérés: 

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

1. Frissíteni egy felhasználó által az SCIM fronted identitás tárolóban található ismert, Azure Active Directory által a szolgáltatás egy kérelmet, mint például az, hogy a felhasználó aktuális állapotának kér folytatódik: 
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
  
   * Azonosító "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. Ha frissíteni kell egy hivatkozási attribútumot, akkor Azure Active Directory lekérdezi a szolgáltatást annak meghatározására, hogy a szolgáltatás által elindított, az identitás-tárolóban lévő Reference attribútum aktuális értéke már megegyezik-e az Azure Active-beli attribútum értékével Directory. A felhasználóknak, amelyek a jelenlegi érték lekérdezése követi, így egyetlen attribútuma a vezetői attribútumához. Íme egy példa kérést megállapítására, hogy a kezelő attribútum egy adott felhasználói objektum jelenleg jogosult-e a megadott érték: 

   Ha a szolgáltatás a Microsoft által a SCIM Services megvalósításához biztosított CLI-könyvtárakkal lett létrehozva, akkor a rendszer lefordítja a kérést a szolgáltatás szolgáltatójának lekérdezési módjára. A paraméterek argumentum értékeként megadott objektum tulajdonságainak értéke a következők: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: „AZONOSÍTÓ”
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ÖsszehasonlítóOperátor. Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * a paraméterek. AlternateFilters.ElementAt(y). AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ÖsszehasonlítóOperátor. Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): „AZONOSÍTÓ”
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Itt az x index értéke 0 is lehet, az y index értéke pedig 1, vagy az x értéke 1, az y értéke pedig 0, a Filter lekérdezési paraméter kifejezésének sorrendjétől függően.   

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
   A SCIM Services megvalósítására szolgáló Microsoft CLI-kódtárak lefordítják a kérést a szolgáltatás szolgáltatójának frissítési módjára. Itt látható a frissítés metódus aláírása: 
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
   ```

   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a szolgáltató lekérdezési metódus hívása. A paraméterek argumentum értékeként megadott objektum tulajdonságainak értéke a következők: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: „AZONOSÍTÓ”
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ÖsszehasonlítóOperátor. Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* a paraméterek. AlternateFilters.ElementAt(y). AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ÖsszehasonlítóOperátor. Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): „AZONOSÍTÓ”
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Itt az x index értéke 0 is lehet, az y index értéke pedig 1, vagy az x értéke 1, az y értéke pedig 0, a Filter lekérdezési paraméter kifejezésének sorrendjétől függően.   

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
  
   * ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2). Operations. Count: 1
   * (PatchRequest as PatchRequest2). Operations. ElementAt (0). OperationName OperationName.Add
   * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Path.AttributePath: "manager"
   * (PatchRequest as PatchRequest2). Operations. ElementAt (0). Érték. darabszám: 1
   * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.ElementAt(0). Referencia: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Érték 2819c223-7f76-453a-919d-413861904646

1. Megszüntetése a felhasználói identitás adattárba fronted SCIM szolgáltatás által, az Azure AD egy kérést küld például: 

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

   Az objektum az resourceIdentifier argumentum értékeként megadott a tulajdonságok értékeit a példában egy kérelem megszüntetése a felhasználó rendelkezik: 

1. Megszüntetése a felhasználói identitás adattárba fronted SCIM szolgáltatás által, az Azure AD egy kérést küld például: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Ha a szolgáltatás a Microsoft által a SCIM Services megvalósításához biztosított CLI-könyvtárakkal lett létrehozva, akkor a rendszer lefordítja a kérést a szolgáltatás szolgáltatójának delete metódusára.   Ez a módszer a aláírással rendelkezik: 
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
  
   * ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Felhasználói és csoport sémájának referenciája

Az Azure Active Directoryban két típusú erőforrásokat az SCIM-webszolgáltatások helyezhet üzembe.  Ilyen típusú erőforrások a felhasználók és csoportok.  

A felhasználói erőforrásokat a séma azonosítója `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`azonosítja, amely a protokoll specifikációjában szerepel:. https://tools.ietf.org/html/rfc7643  A felhasználói erőforrások attribútumaihoz Azure Active Directory felhasználók attribútumainak alapértelmezett leképezését az 1. táblázat tartalmazza.  

Felhasználóicsoport-erőforrások azonosítja a séma azonosító `urn:ietf:params:scim:schemas:core:2.0:Group`. A 2. táblázat a csoportok attribútumainak alapértelmezett leképezését mutatja Azure Active Directory a csoport erőforrásainak attribútumaihoz.  

### <a name="table-1-default-user-attribute-mapping"></a>1\. táblázat: Alapértelmezett felhasználói attribútum leképezése

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
| felhasználó-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>2\. táblázat: Alapértelmezett Group attribútum-hozzárendelés

| Azure Active Directory-csoport | urn: IETF: params: scim: sémák: Core: 2.0: Group |
| --- | --- |
| displayName |externalId |
| levelezés |e-mailek [típus eq "work"] .value |
| mailNickname |displayName |
| tag |tag |
| objectId |ID (Azonosító) |
| proxyAddresses |e-mailek [Írja be a eq "egyéb"]. Érték |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Az Azure AD-kiépítési szolgáltatás által az SCIM-kérések elvégzéséhez használt IP-címek engedélyezése

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
