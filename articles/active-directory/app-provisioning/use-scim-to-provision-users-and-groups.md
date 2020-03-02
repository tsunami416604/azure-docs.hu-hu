---
title: SCIM-végpont fejlesztése az Azure AD-ből származó alkalmazások felhasználó általi üzembe helyezéséhez
description: A rendszer a tartományok közötti Identitáskezelés (SCIM) esetében szabványosítja a felhasználók automatikus kiépítési folyamatát. Ismerje meg, hogyan fejleszthet SCIM-végpontokat, hogyan integrálhatja a SCIM API-t a Azure Active Directoryval, és megkezdheti a felhasználók és csoportok kiépítésének automatizálását a felhőalapú alkalmazásokba.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2fda5d1bdd00a601df363bd930e5f2f6d610c7f
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208712"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>SCIM-végpont létrehozása és a felhasználók üzembe helyezésének konfigurálása Azure Active Directory (Azure AD) segítségével

Alkalmazás-fejlesztőként használhatja a tartományok közötti Identitáskezelés (SCIM) felhasználói felügyeleti API-t, hogy lehetővé váljon a felhasználók és csoportok automatikus kiépítés az alkalmazás és az Azure AD között. Ez a cikk bemutatja, hogyan hozhat létre egy SCIM-végpontot, és hogyan integrálható az Azure AD kiépítési szolgáltatásával. A SCIM-specifikáció általános felhasználói sémát biztosít az üzembe helyezéshez. Az olyan összevonási szabványokkal együtt, mint az SAML vagy az OpenID Connect, a SCIM teljes körű, szabványokon alapuló megoldást biztosít a rendszergazdáknak a hozzáférés-kezeléshez.

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

Minden alkalmazáshoz különböző attribútumok szükségesek egy felhasználó vagy csoport létrehozásához. Az integráció megkezdéséhez azonosítsa az alkalmazás által igényelt objektumokat (felhasználókat, csoportokat) és attribútumokat (név, felettes, beosztás stb.). A SCIM standard definiál egy sémát a felhasználók és csoportok kezeléséhez. Az alapvető felhasználói sémához csak három attribútum szükséges: **azonosító** (szolgáltató által definiált azonosító), **externalId** (ügyfél által definiált azonosító) és **meta** (csak olvasható metaadatok a szolgáltató által karbantartva). Az összes többi attribútum nem kötelező. Az alapszintű felhasználói sémán kívül a SCIM standard definiál egy vállalati felhasználói bővítményt és egy modellt a felhasználói séma kibővítéséhez, hogy megfeleljen az alkalmazás igényeinek. Ha például az alkalmazás egy felhasználó felettesét igényli, a vállalati felhasználói sémával összegyűjtheti a felhasználó felettesét és a központi sémát a felhasználó e-mail-címének összegyűjtéséhez. A séma kialakításához kövesse az alábbi lépéseket:
  1. Az alkalmazás által igényelt attribútumok listázása. Hasznos lehet a követelményeknek a hitelesítéshez (pl. loginName és e-mailek) való bontása, a felhasználó életciklusának kezeléséhez szükséges attribútumok (pl. állapot/aktív) és az adott alkalmazás működéséhez szükséges egyéb attribútumok (például a kezelő, a címke).
  2. Győződjön meg arról, hogy ezek az attribútumok már definiálva vannak-e az alapszintű felhasználói sémában vagy a vállalati felhasználói sémában. Ha az alapszintű vagy a vállalati felhasználói sémák nem fedik le az összes szükséges attribútumot, akkor meg kell adnia egy bővítményt a felhasználói sémához, amely tartalmazza a szükséges attribútumokat. Az alábbi példában egy bővítményt adtunk hozzá a felhasználóhoz, amely lehetővé teszi a felhasználó számára a "címke" kiépítési lehetőséget. A legjobb, ha csak az alapszintű és a vállalati felhasználói sémákkal indul, és később további egyéni sémákat szeretne kibővíteni.  
  3. Képezze le a SCIM attribútumokat az Azure AD felhasználói attribútumaira. Ha a SCIM-végpontban definiált attribútumok egyike nem rendelkezik egyértelmű jogosultsággal az Azure AD felhasználói sémájában, akkor az adatai nem a legtöbb bérlőn található felhasználói objektumon vannak tárolva. Vegye figyelembe, hogy ez az attribútum nem kötelező-e a felhasználók létrehozásához. Ha az attribútum kritikus fontosságú az alkalmazás működéséhez, a bérlői rendszergazdát a séma kiterjesztéséhez, vagy a "címkék" tulajdonsághoz az alábbi ábrán látható módon használja.

### <a name="table-1-outline-the-attributes-that-you-need"></a>1\. táblázat: a szükséges attribútumok körvonalazása 
| 1\. lépés: az alkalmazás által igényelt attribútumok meghatározása| 2\. lépés: az alkalmazásra vonatkozó követelmények leképezése a SCIM standard értékre| 3\. lépés: SCIM-attribútumok leképezése az Azure AD-attribútumokra|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|név. lastName|lastName|
|workMail|E-mailek [type EQ "work"]. Value|Mail|
|kezelő|kezelő|kezelő|
|tag|urn: IETF: params: scim: sémák: bővítmény: 2.0: CustomExtension: címke|extensionAttribute1|
|status|aktív|isSoftDeleted (nem a felhasználó által tárolt számított érték)|

A fent definiált séma az alábbi JSON-adattartalommal lesz ábrázolva. Vegye figyelembe, hogy az alkalmazáshoz szükséges attribútumok mellett a JSON-ábrázolás a szükséges "id", "externalId" és "meta" attribútumokat is tartalmazza.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>2\. táblázat: alapértelmezett felhasználói attribútum leképezése
Ezután az alábbi táblázat segítségével megismerheti, hogy az alkalmazás által igényelt attribútumok hogyan képezhetők le az Azure AD-ben és a SCIM RFC-ben található attribútumokra. [Testreszabhatja](customize-application-attributes.md) az attribútumok leképezését az Azure ad és a scim végpontja között. Vegye figyelembe, hogy nem kell támogatnia a felhasználókat és a csoportokat, sem az összes alább látható attribútumot. Ezek arra utalnak, hogy az Azure AD-beli attribútumok gyakran a SCIM protokoll tulajdonságaira vannak leképezve. 

| Az Azure Active Directory-felhasználó | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktív |
|Szervezeti egység|urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|
| displayName |displayName |
|employeeId|urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|
| Telefax-TelephoneNumber |phoneNumbers [típus eq "fax"] .value |
| givenName |name.givenName |
| Beosztás |Cím |
| mail |e-mailek [típus eq "work"] .value |
| mailNickname |externalId |
| kezelő |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager |
| mobil |phoneNumbers [típus eq "mobileszköz"] .value |
| Irányítószám |.postalCode címek [típus eq "work"] |
| proxy-Addresses |e-mailek [Írja be a eq "egyéb"]. Érték |
| fizikai-kézbesítési-OfficeName |a címek [Írja be a eq "egyéb"]. Formázott |
| streetAddress |.streetAddress címek [típus eq "work"] |
| Vezetéknév |name.familyName |
| Telefonszám – |phoneNumbers [típus eq "work"] .value |
| felhasználó-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>3\. táblázat: az alapértelmezett Group attribútum leképezése

| Azure Active Directory-csoport | urn: IETF: params: scim: sémák: Core: 2.0: Group |
| --- | --- |
| displayName |displayName |
| mail |e-mailek [típus eq "work"] .value |
| mailNickname |displayName |
| tag |tag |
| objectId |externalId |
| proxyAddresses |e-mailek [Írja be a eq "egyéb"]. Érték |

A SCIM RFC-ben több végpont is definiálva van. Megkezdheti a/User-végpont megkezdését, majd onnan kiterjesztheti. Az/schemas-végpont hasznos lehet egyéni attribútumok használatakor, vagy ha a séma gyakran változik. Lehetővé teszi, hogy az ügyfél automatikusan lekérje a legfrissebb sémát. A/bulk végpont különösen hasznos a csoportok támogatásakor. Az alábbi táblázat a SCIM standardban definiált különböző végpontokat ismerteti. Az/schemas-végpont hasznos lehet egyéni attribútumok használatakor, vagy ha a séma gyakran változik. Lehetővé teszi, hogy az ügyfél automatikusan beolvassa a legnaprakészebb sémát. A/bulk végpont különösen hasznos a csoportok támogatásakor. Az alábbi táblázat a SCIM standardban definiált különböző végpontokat ismerteti. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>4\. táblázat: a fejleszteni kívánt végpontok meghatározása
|VÉGPONT|LEÍRÁS|
|--|--|
|/User|SZIFILISZ-műveletek végrehajtása felhasználói objektumon.|
|/Group|SZIFILISZ-műveletek végrehajtása egy csoport objektumon.|
|/ServiceProviderConfig|A SCIM szabvány által támogatott funkciók részleteit tartalmazza, például a támogatott erőforrásokat és a hitelesítési módszert.|
|/ResourceTypes|Az egyes erőforrásokra vonatkozó metaadatok megadása|
|/Schemas|Az egyes ügyfelek és szolgáltatók által támogatott attribútumok különbözőek lehetnek. Míg az egyik szolgáltató tartalmazhatja a "Name", a "title" és az "e-maileket", míg egy másik szolgáltató a "Name", a "title" és a "phoneNumbers" nevet használja. A sémák végpont lehetővé teszi a támogatott attribútumok felderítését.|
|/Bulk|A tömeges műveletek lehetővé teszik, hogy az erőforrás-objektumok nagy gyűjteményében műveleteket hajtson végre egyetlen műveletben (például egy nagy csoport frissítési tagsága).|


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
* Azt az attribútumot, amely alapján az erőforrásokat le lehet kérdezni, megfelelő attribútumként kell beállítani az alkalmazásban a [Azure Portalban](https://portal.azure.com). További információ: a [felhasználói kiépítési attribútumok társításának testreszabása](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Felhasználók kiépítése és megszüntetése

A következő ábra azokat az üzeneteket mutatja be, amelyeket Azure Active Directory küld egy SCIM szolgáltatásnak az alkalmazás identitás-tárolójában lévő felhasználó életciklusának kezeléséhez.  

![megjeleníti a felhasználó kiépítésének és megszüntetésének folyamatát](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*A felhasználó kiépítése és megszüntetése*

### <a name="group-provisioning-and-deprovisioning"></a>Csoport kiépítése és megszüntetése

A csoport kiépítése és megszüntetése nem kötelező. A implementált és a engedélyezést követően az alábbi ábra azokat az üzeneteket mutatja be, amelyeket az Azure AD a SCIM szolgáltatásnak küld egy csoport életciklusának kezeléséhez az alkalmazás identitás-tárolójában.  Ezek az üzenetek két módon különböznek a felhasználók üzeneteitől:

* A csoportok beolvasására irányuló kérések azt határozzák meg, hogy a tagok attribútumot ki kell zárni a kérelemre válaszként megadott összes erőforrásból.  
* Határozza meg, hogy rendelkezik-e a hivatkozási attribútum egy adott értéket a kérelmek azok a tag attribútum kapcsolatban.  

![megjeleníti a csoport kiépítésének és megszüntetésének menetét](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Csoport kiépítése és megszüntetési folyamata*

### <a name="scim-protocol-requests-and-responses"></a>SCIM és válaszok
Ez a szakasz példákat tartalmaz az Azure AD SCIM-ügyfél által kibocsátott SCIM-kérelmekre, és példa erre a várt válaszokra. A legjobb eredmény érdekében az alkalmazásnak az ilyen formátumú kérelmek kezelésére és a várt válaszokat kell kibocsátania.

> [!IMPORTANT]
> Ha meg szeretné tudni, hogyan és mikor bocsátja ki az Azure AD-beli felhasználó-kiépítési szolgáltatást az alább ismertetett műveleteket, tekintse meg a [kiépítési](how-provisioning-works.md) [ciklusok: kezdeti és növekményes](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) kiépítés című szakaszt.

[Felhasználói műveletek](#user-operations)
  - [Felhasználó létrehozása](#create-user) ( / [Válasz](#response)[kérése](#request) )
  - [Felhasználó beolvasása](#get-user) ( / [Válasz](#response-1)[kérése](#request-1) )
  - [Felhasználó lekérdezése lekérdezéssel](#get-user-by-query) ( / [Válasz](#response-2)[kérése](#request-2) )
  - Felhasználó lekérése [lekérdezéssel – nulla eredmények](#get-user-by-query---zero-results) (
/ [Válasz](#response-3)[kérése](#request-3) )
  - [Felhasználó frissítése [többértékű tulajdonságok]](#update-user-multi-valued-properties) ( /  [Válasz](#response-4)[kérése](#request-4) )
  - [Felhasználó frissítése [egyértékű tulajdonságok]](#update-user-single-valued-properties) (
/ [Válasz](#response-5)[kérése](#request-5) ) 
  - [Felhasználó letiltása](#disable-user) ( / 
[Válasz](#response-14)[kérése](#request-14) )
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
[Válasz](#response-12) [kérése](#request-12) )
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
*/Users/5d48a0a8e9f04aa38008 beolvasása* 

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

*/Users beolvasása? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

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

*PATCH/Users/6764549bef60420686bc HTTP/1.1*
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

### <a name="disable-user"></a>Felhasználó letiltása

##### <a name="request-14"></a>Kérelem

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response-14"></a>Válasz

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Felhasználó törlése

##### <a name="request-6"></a>Kérelem

*/Users/5171a35d82074e068ce2 törlése HTTP/1.1*

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

*/Groups/40734ae655284ad3abcc beolvasása? excludedAttributes = tagok HTTP/1.1*

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
*/Groups beolvasása? excludedAttributes = tagok & Filter = displayName EQ "displayName" HTTP/1.1*

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

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
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

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*/Groups/cdb1ce18f65944079d37 törlése HTTP/1.1*

##### <a name="response-13"></a>Válasz

*HTTP/1.1 204 nincs tartalom*

### <a name="security-requirements"></a>Biztonsági követelmények
**TLS protokoll verziói**

Az egyetlen elfogadható TLS protokoll-verzió a TLS 1,2 és a TLS 1,3. A TLS más verziói nem engedélyezettek. Nem engedélyezett az SSL verziója. 
- Az RSA-kulcsoknak legalább 2 048 bitenek kell lenniük.
- Az ECC-kulcsoknak legalább 256 bitenek kell lenniük, jóváhagyott elliptikus görbe használatával létrehozva


**Kulcs hossza**

Az összes szolgáltatásnak a megfelelő hosszúságú titkosítási kulcsok használatával generált X. 509 tanúsítványokat kell használnia, ami azt jelenti, hogy:

**Titkosítási csomagok**

Minden szolgáltatást úgy kell konfigurálni, hogy a következő titkosítási csomagokat használja az alább megadott sorrendben. Vegye figyelembe, hogy ha csak RSA-tanúsítvánnyal rendelkezik, akkor a ECDSA titkosítási csomagok nem lépnek érvénybe. </br>

TLS 1,2 titkosítási csomagok minimális sávja:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>3\. lépés: SCIM-végpont létrehozása

Most, hogy desidned a sémát, és megértette az Azure AD SCIM megvalósítását, megkezdheti az SCIM-végpont fejlesztését. Ahelyett, hogy teljesen elkezdené, és a megvalósítást teljes mértékben saját maga építheti fel, a SCIM-commuinty által közzétett nyílt forráskódú SCIM-könyvtárakra támaszkodhat.  
Az Azure AD-kiépítési csapat által közzétett nyílt forráskódú .NET Core- [hivatkozási kód](https://aka.ms/SCIMReferenceCode) egy ilyen erőforrás, amely elkezdheti a fejlesztést. Miután létrehozta a SCIM-végpontot, tesztelni szeretné. Használhatja a hivatkozási kód részeként megadott [Poster-tesztek](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) gyűjteményét, vagy futtathatja a [fentebb](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)ismertetett minta kérelmeket/válaszokat.  

Megjegyzés: a hivatkozási kód célja, hogy segítséget nyújtson a SCIM-végpont létrehozásában, és "ahogy van". A közösségi hozzájárulások szívesen segítenek felépíteni és karbantartani a kódot. 

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4\. lépés: a SCIM-végpont integrálása az Azure AD SCIM-ügyféllel

Az Azure AD beállítható úgy, hogy automatikusan kiépítse a hozzárendelt felhasználókat és csoportokat olyan alkalmazásokhoz, amelyek a [SCIM 2,0 protokoll](https://tools.ietf.org/html/rfc7644)egy adott profilját implementálják. A profil sajátosságait a [2. lépés: az Azure ad scim implementációjának ismertetése című témakör](#step-2-understand-the-azure-ad-scim-implementation)ismerteti.

Ellenőrizze az alkalmazás szolgáltatója vagy utasítások, ezek a követelmények való kompatibilitás érdekében az alkalmazás szolgáltatója dokumentációját.

> [!IMPORTANT]
> Az Azure AD SCIM implementációja az Azure AD-beli felhasználói kiépítési szolgáltatásra épül, amelynek célja, hogy folyamatosan szinkronizálja a felhasználókat az Azure AD és a célalkalmazás között, és a szabványos műveletek nagyon specifikus készletét implementálja. Fontos megérteni ezeket a viselkedéseket az Azure AD SCIM-ügyfél működésének megismerése érdekében. További információ: a [kiépítés működésének](how-provisioning-works.md) [kezdeti és növekményes](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) szakasza.

### <a name="getting-started"></a>Első lépések

Ebben a cikkben leírt az SCIM-profil támogató alkalmazások csatlakoztathatók az Azure Active Directoryhoz a az Azure AD alkalmazáskatalógusában "katalógusban nem szereplő alkalmazás" funkciójával. A csatlakozás után a Azure ad-ben minden 40 perces, ahol azt lekérdezi az alkalmazás SCIM végpont hozzárendelt felhasználók és csoportok, és létrehozza vagy módosítja őket a hozzárendelés részletei alapján futtatja a szinkronizálási folyamat.

**SCIM támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com). Vegye figyelembe, hogy a [fejlesztői programra](https://developer.microsoft.com/office/dev-program) való feliratkozással a P2-licenccel rendelkező Azure Active Directory ingyenes próbaverzióját érheti el
2. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
3. Válassza az **+ új alkalmazás** > **az összes** > **nem Gallery-alkalmazás**lehetőséget.
4. Adja meg az alkalmazás nevét, majd kattintson a **Hozzáadás** elemre az alkalmazás-objektum létrehozásához. A rendszer hozzáadja az új alkalmazást a vállalati alkalmazások listájához, és megnyílik az alkalmazás-felügyeleti képernyőjén.

   ![képernyőképen az Azure AD-alkalmazás katalógusa látható](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-alkalmazás katalógusa*

5. Az App Management képernyőn válassza a **kiépítés** lehetőséget a bal oldali panelen.
6. A **létesítési mód** menüben válassza az **automatikus**lehetőséget.

   ![példa: az alkalmazás kiépítési lapja a Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *A kiépítés konfigurálása a Azure Portalban*

7. A **bérlői URL-cím** mezőben adja meg az alkalmazás scim-végpontjának URL-címét. Például: https://api.contoso.com/scim/
8. Ha az SCIM-végpont OAuth-tulajdonosi jogkivonatot igényel az Azure AD-től eltérő kibocsátótól, akkor másolja a szükséges OAuth-tulajdonosi tokent a nem kötelező **titkos jogkivonat** mezőbe. Ha ez a mező üresen marad, az Azure AD az Azure AD-ből kiállított OAuth-tulajdonosi jogkivonatot tartalmaz minden kéréssel. Az Azure AD-t identitás-szolgáltatóként használó alkalmazások ellenőrizhetik ezt az Azure AD-kiállított jogkivonatot. 
   > [!NOTE]
   > Ezt a mezőt ***nem*** ajánlott üresen hagyni, és az Azure ad által generált jogkivonatra támaszkodni. Ez a lehetőség elsősorban tesztelési célokra használható.
9. Válassza a **kapcsolat tesztelése** lehetőséget, hogy Azure Active Directory próbáljon csatlakozni az scim-végponthoz. Ha a kísérlet sikertelen, a rendszer hibaüzenetet jelenít meg.  

    > [!NOTE]
    > A SCIM a nem létező felhasználóhoz tartozó végpontot **kérdezi** le, véletlenszerű GUID azonosítóval, amely az Azure ad-konfigurációban kiválasztott egyező tulajdonság. A várt helyes válasz a HTTP 200 OK, amely üres SCIM ListResponse-üzenettel rendelkezik.

10. Ha az alkalmazáshoz való kapcsolódási kísérlet sikeres, kattintson a **Save (Mentés** ) gombra a rendszergazdai hitelesítő adatok mentéséhez.
11. A **leképezések** szakaszban két választható [attribútum-hozzárendelés](customize-application-attributes.md)közül választhat: egyet a felhasználói objektumokhoz és egyet a csoport objektumaihoz. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az alkalmazás felhasználóinak és csoportjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    > [!NOTE]
    > Igény szerint letilthatja a csoportobjektumokhoz, tiltsa le a "csoport" leképezési szinkronizálása.

12. A **Beállítások**területen a **hatókör** mező határozza meg, hogy mely felhasználók és csoportok legyenek szinkronizálva. Jelölje be a **csak a hozzárendelt felhasználók** és csoportok szinkronizálása (javasolt) lehetőséget, hogy csak a **felhasználók és csoportok** lapon hozzárendelt felhasználókat és csoportokat szinkronizálja.
13. A konfiguráció befejezése után állítsa be a **kiépítési állapotot** **a**következőre:.
14. Válassza a **Mentés** lehetőséget az Azure ad-kiépítési szolgáltatás elindításához.
15. Ha csak a hozzárendelt felhasználókat és csoportokat szinkronizálja (ajánlott), akkor ügyeljen arra, hogy a **felhasználók és csoportok** lapot jelölje ki, és rendelje hozzá a szinkronizálni kívánt felhasználókat vagy csoportokat.

A kezdeti ciklus elindítása után a bal oldali panelen kiválaszthatja a **kiépítési naplókat** a folyamat figyeléséhez, amely megjeleníti az alkalmazáson belüli kiépítési szolgáltatás által végrehajtott összes műveletet. Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](check-status-user-account-provisioning.md)helyezéséhez.

> [!NOTE]
> A kezdeti ciklus hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, ami körülbelül 40 percenként történik, amíg a szolgáltatás fut.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5\. lépés: az alkalmazás közzététele az Azure AD Application Galleryben

Ha egynél több bérlő által használt alkalmazást készít, azt az Azure AD Application Galleryben teheti elérhetővé. Ez megkönnyíti a szervezetek számára az alkalmazás felderítését és a kiépítés konfigurálását. Az alkalmazás közzététele az Azure AD-katalógusban és a mások számára elérhető kiépítés egyszerűvé tétele. Tekintse meg a lépéseket [itt](../develop/howto-app-gallery-listing.md). A Microsoft együttműködik Önnel, hogy integrálja az alkalmazást a katalógusba, tesztelje a végpontot, és bocsásson ki a használati [dokumentációt](../saas-apps/tutorial-list.md) az ügyfelek számára. 

### <a name="gallery-onboarding-checklist"></a>Katalógus-előkészítési ellenőrzőlista
Kövesse az alábbi feladatlistát, és győződjön meg arról, hogy az alkalmazás készen áll, és az ügyfelek zökkenőmentes üzembe helyezési tapasztalattal rendelkeznek. A katalógusba való bevezetéskor a rendszer összegyűjti az adatokat. 
> [!div class="checklist"]
> * [SCIM 2,0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) felhasználói és csoportos végpont támogatása (csak egy szükséges, de mindkettő ajánlott)
> * Legalább 25 kérelem támogatása a bérlők számára másodpercenként (kötelező)
> * Mérnöki és támogatási kapcsolattartók létrehozása útmutató az ügyfeleknek a katalógus bevezetéséhez (kötelező)
> * 3 nem lejáró teszt hitelesítő adatai az alkalmazáshoz (kötelező)
> * Támogassa a OAuth engedélyezési kód támogatását vagy az alább leírtak szerint a hosszú élettartamú tokent (kötelező)
> * Hozzon létre egy mérnöki és támogatási pontot az ügyfelek támogatásához a katalógus bevezetése után (kötelező)
> * Több csoporttagság frissítésének támogatása egyetlen JAVÍTÁSsal (ajánlott) 
> * A SCIM-végpont nyilvánosan történő dokumentálása (ajánlott) 
> * [Támogatási séma felderítése](https://tools.ietf.org/html/rfc7643#section-6) (ajánlott)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Engedélyezés a kiépítési összekötők számára az alkalmazás-katalógusban
A SCIM spec nem határoz meg SCIM-specifikus sémát a hitelesítéshez és az engedélyezéshez. A meglévő iparági szabványok használatára támaszkodik. Az Azure AD-kiépítési ügyfél két engedélyezési módszert támogat a katalógusban lévő alkalmazásokhoz. 

|Engedélyezési módszer|Szakemberek számára|Hátrányok|Támogatás|
|--|--|--|--|
|Felhasználónév és jelszó (az Azure AD nem javasolja vagy támogatja)|Könnyen megvalósítható|Nem biztonságos – [a PA $ $Word nem számít](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|A Gallery-alkalmazások eseti alapon támogatottak. Nem Gallery-alkalmazások esetében nem támogatott.|
|Hosszú élettartamú tulajdonosi jogkivonat|A hosszú élettartamú tokenek nem igénylik a felhasználó jelenlétét. A rendszergazdák egyszerűen használhatók a kiépítés beállításakor.|A hosszú élettartamú tokenek nehezen oszthatók meg rendszergazdaként anélkül, hogy nem biztonságos módszereket, például e-maileket kellene használnia. |A katalógus és a nem Gallery-alkalmazások esetében támogatott. |
|OAuth engedélyezési kód engedélyezése|A hozzáférési tokenek sokkal rövidebbek, mint a jelszavak, és olyan automatikus frissítési mechanizmussal rendelkeznek, amelyet a hosszú élettartamú tulajdonosi jogkivonatok nem rendelkeznek.  Egy valós felhasználónak jelen kell lennie a kezdeti engedélyezés során, és hozzá kell adnia egy szintű elszámoltathatóságot. |A felhasználónak jelen kell lennie. Ha a felhasználó elhagyja a szervezetet, a jogkivonat érvénytelen, és az engedélyezést újra el kell végezni.|Gallery-alkalmazások esetén támogatott. Nem Gallery-alkalmazások támogatása folyamatban van.|
|OAuth-ügyfél hitelesítő adatainak megadása|A hozzáférési tokenek sokkal rövidebbek, mint a jelszavak, és olyan automatikus frissítési mechanizmussal rendelkeznek, amelyet a hosszú élettartamú tulajdonosi jogkivonatok nem rendelkeznek. Az engedélyezési kód és az ügyfél hitelesítő adatai is azonos típusú hozzáférési tokent hoznak létre, így a módszerek közötti váltás az API-ra is átlátható.  A kiépítés teljesen automatizált lehet, és az új tokenek felhasználói beavatkozás nélkül is csendesen kérhetők. ||Katalógus-és nem katalógus-alkalmazások esetén nem támogatott. A támogatás a várakozó fájlok között található.|

[!NOTE] Nem ajánlott üresen hagyni a jogkivonat mezőt az Azure AD kiépítési konfiguráció egyéni alkalmazás felhasználói felületén. A generált jogkivonat elsődlegesen tesztelési célokra használható.

**OAuth engedélyezési kód engedélyezése:** A kiépítési szolgáltatás támogatja az [engedélyezési kód engedélyezését](https://tools.ietf.org/html/rfc6749#page-24). Miután elküldte az alkalmazást a katalógusban való közzétételre vonatkozó kérelmét, a csapat együttműködik Önnel a következő információk összegyűjtéséhez:
*  Engedélyezési URL-cím: az ügyfél URL-címe, amely a felhasználói ügynök átirányításával szerzi be az erőforrás-tulajdonostól az engedélyt. A rendszer átirányítja a felhasználót erre az URL-címre, hogy engedélyezze a hozzáférést. 
*  Jogkivonat-Exchange URL-cím: az ügyfél URL-címe, amely egy hozzáférési jogkivonat engedélyezési engedélyének kiváltására használható, jellemzően az ügyfél-hitelesítéssel.
*  Ügyfél-azonosító: az engedélyezési kiszolgáló kiállítja a regisztrált ügyfelet az ügyfél-azonosítóval, amely egy egyedi karakterlánc, amely az ügyfél által megadott regisztrációs adatokat jelképezi.  Az ügyfél-azonosító nem titok; az erőforrás tulajdonosának van kitéve, és **nem** használható egyedül az ügyfél-hitelesítéshez.  
*  Ügyfél titka: az ügyfél titkos kulcsa az engedélyezési kiszolgáló által generált titok. Egyedi értéknek kell lennie, amely csak az engedélyezési kiszolgáló számára ismert. 

Vegye figyelembe, hogy az OAuth v1 nem támogatott az ügyfél titkos kulcsa miatt. A OAuth v2 támogatott.  

Ajánlott eljárások (javasolt, de nem kötelező):
* Több átirányítási URL-cím támogatása. A rendszergazdák a "portal.azure.com" és a "aad.portal.azure.com" típusból is konfigurálhatják az üzembe helyezést. Több átirányítási URL-cím támogatása biztosítja, hogy a felhasználók bármelyik portálról engedélyezzék a hozzáférést.
* Több titkot is támogat, hogy az állásidő nélkül zökkenőmentes legyen a titkos kulcs megújítása. 

**Hosszú életű OAuth-tulajdonosi jogkivonatok:** Ha az alkalmazás nem támogatja a OAuth-engedélyezési kód engedélyezésének folyamatát, hosszú élettartamú OAuth tulajdonosi jogkivonatot is létrehozhat, mint amennyit a rendszergazda a kiépítési integráció beállításához használhat. A tokennek véglegesnek kell lennie, különben a kiépítési feladatot a jogkivonat lejárta után [karanténba](application-provisioning-quarantine-status.md) helyezi a rendszer. Ennek a tokennek a méretnél kisebbnek kell lennie a 1KB.  

További hitelesítési és engedélyezési módszerek esetén tudassa velünk a [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>A katalógus piacról indított indítási ellenőrzési listája
Ha segítségre van szüksége a közös integrációval kapcsolatos ismeretek és igények kielégítéséhez, javasoljuk, hogy frissítse a meglévő dokumentációját, és bővítse az integrációt a marketing-csatornákon.  Az alábbi ellenőrzőlista-tevékenységeket ajánljuk az indítás támogatásához.

* **Értékesítési és ügyféltámogatási készültség.** Gondoskodjon arról, hogy az értékesítési és támogatási csapatok tisztában legyenek, és beszéljenek az integrációs képességekről. Küldje el az értékesítési és támogatási csapatát, és adja meg a gyakori kérdéseket, és foglalja bele az értékesítési anyagokba való integrációt. 
* **Blogbejegyzés és/vagy sajtóközlemény.** Készítsen egy blogbejegyzést, vagy nyomja meg a kiadást, amely leírja a közös integrációt, az előnyöket és az első lépéseket. [Példa: inprivata és Azure Active Directory Press sajtóközlemény](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Közösségi média.** A közösségi média, például a Twitter, a Facebook vagy a LinkedIn segítségével népszerűsítheti ügyfelei integrációját. Ügyeljen arra, hogy @AzureAD, hogy a postát is meg lehessen retweet. [Példa: inprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketing webhely.** Hozza létre vagy frissítse a marketing-oldalakat (például integrációs oldal, partner oldal, díjszabási oldal stb.), hogy tartalmazza a közös integráció rendelkezésre állását. [Példa: Pingboard Integration Page](https://pingboard.com/org-chart-for), [Smartsheet Integration Page](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com díjszabása oldal](https://monday.com/pricing/) 
* **Technikai dokumentáció.** Hozzon létre egy Help Center-cikket vagy technikai dokumentációt arról, hogy az ügyfelek hogyan kezdhetik meg az első lépéseket. [Példa: megbízottat + Microsoft Azure Active Directory Integration.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Ügyfél-kommunikáció.** Az ügyfél-kommunikáció (havi hírlevél, e-mail-kampányok, termék-kibocsátási megjegyzések) révén riasztást készíthet az ügyfelektől az új integrációról. 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Az Azure AD-kiépítési szolgáltatás által az SCIM-kérések elvégzéséhez használt IP-címek engedélyezése

Bizonyos alkalmazások engedélyezik a bejövő adatforgalmat az alkalmazáshoz. Ahhoz, hogy az Azure AD kiépítési szolgáltatás a várt módon működjön, engedélyezni kell a használt IP-címeket. Az egyes szolgáltatási címkék/régiók IP-címeinek listáját lásd: JSON-fájl – [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519). Ezeket az IP-címeket igény szerint letöltheti és lefuttathatja a tűzfalon. Az Azure AD kiépítés számára fenntartott IP-címtartományok a "AzureActiveDirectoryDomainServices" alatt találhatók.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [A felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások megszüntetése](user-provisioning.md)
* [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](customize-application-attributes.md)
* [Kifejezések írása attribútum-hozzárendelésekhez](functions-for-customizing-application-data.md)
* [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
* [Fiók kiépítési értesítései](user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
