---
title: SCIM-végpont kialakítása az Azure AD-ből származó alkalmazásokba való felhasználói kiépítéshez
description: A tartományok közötti identitáskezelési (SCIM) rendszer egységesíti az automatikus felhasználói kiépítést. Ismerje meg, hogyan fejleszthet SCIM-végpontot, integrálhatja az SCIM API-t az Azure Active Directoryval, és automatizálhatja a felhasználók és csoportok üzembe helyezhető üzembe helyezhető a felhőalkalmazásokban.
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
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297680"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>SCIM-végpont létrehozása és a felhasználók kiépítésének konfigurálása az Azure Active Directoryval (Azure AD)

Alkalmazásfejlesztőként használhatja a System for Cross-Domain Identity Management (SCIM) felhasználói felügyeleti API-t a felhasználók és csoportok automatikus kiépítésének engedélyezéséhez az alkalmazás és az Azure AD között. Ez a cikk ismerteti, hogyan hozhat létre egy SCIM-végpontot, és integrálható az Azure AD-kiépítési szolgáltatással. Az SCIM-specifikáció közös felhasználói sémát biztosít a kiépítéshez. Ha az összevonási szabványokkal, például az SAML-lel vagy az OpenID Connecttel együtt használja, az SCIM a rendszergazdák számára egy végpontok között, szabványokon alapuló megoldást biztosít a hozzáférés-kezeléshez.

Az SCIM két végpont szabványosított definíciója: a /Users végpont és /Csoportok végpont. Az objektumok létrehozásához, frissítéséhez és törléséhez általános REST-műveleteket használ, valamint egy előre definiált sémát a gyakori attribútumokhoz, például a csoport névhez, a felhasználónévhez, a vezetéknévhez, a vezetéknévhez és az e-mailhez. Az SCIM 2.0 REST API-t kínáló alkalmazások csökkenthetik vagy kiküszöbölhetik a saját felhasználói felügyeleti API-val való munka fájdalmát. Például minden megfelelő SCIM-ügyfél tudja, hogyan készíthet EGY JSON-objektum HTTP-POST-át a /Users végpontra egy új felhasználói bejegyzés létrehozásához. Ahelyett, hogy egy kicsit más API-t kellene használnia az alapvető műveletekhez, az SCIM szabványnak megfelelő alkalmazások azonnal kihasználhatják a már meglévő ügyfelek, eszközök és kódok előnyeit. 

![Kiépítés az Azure AD-ből egy alkalmazásba az SCIM-mel](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Az SCIM 2.0-ban [(RFC 7642,](https://tools.ietf.org/html/rfc7642) [7643, 7644)](https://tools.ietf.org/html/rfc7643)definiált általános felhasználói objektumséma és restaparha API-k lehetővé teszik az identitásszolgáltatók és alkalmazások könnyebb en-integrálását egymással. [7644](https://tools.ietf.org/html/rfc7644) Az SCIM-végpontot fejlesztő alkalmazásfejlesztők egyéni munka nélkül integrálhatók bármely SCIM-kompatibilis ügyféllel.

Egy alkalmazás üzembe építése szükséges, és integrálja az SCIM-végpont az Azure AD SCIM-ügyfél. Hajtsa végre az alábbi lépéseket a felhasználók és csoportok alkalmazásba való kiépítésének megkezdéséhez. 
    
  * **[1. lépés: Tervezze meg a felhasználó és a csoport sémáját.](#step-1-design-your-user-and-group-schema)** Azonosítsa az alkalmazás igényeinek objektumokat és attribútumokat, és határozza meg, hogyan felelnek meg az Azure AD SCIM-implementáció által támogatott felhasználói és csoportsémának.

  * **[2. lépés: Ismerje meg az Azure AD SCIM-implementáció.](#step-2-understand-the-azure-ad-scim-implementation)** Ismerje meg, hogyan valósítható meg az Azure AD SCIM-ügyfél, és modellezheti az SCIM protokollkérelmek kezelését és válaszait.

  * **[3. lépés: SciM-végpont létrehozása.](#step-3-build-a-scim-endpoint)** Az Azure AD-kiépítési szolgáltatással való integrációhoz a végpontnak SCIM 2.0-kompatibilisnek kell lennie. Lehetőségként a Microsoft Common Language Infrastructure (CLI) kódtárak és kódminták segítségével hozhat létre a végpontot. Ezek a minták csak referencia- és vizsgálatra szolgálnak; javasoljuk, hogy ne kódolja az éles alkalmazást, hogy függőséget vállaljon tőlük.

  * **[4. lépés: Integrálja az SCIM-végpontot az Azure AD SCIM-ügyféllel.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Ha a szervezet egy külső alkalmazás, amely megvalósítja az SCIM 2.0, amely az Azure AD támogatja, azonnal elkezdheti a felhasználók és csoportok üzembe szervezésének automatizálását és a felhasználók és csoportok leszerelése.

  * **[5. lépés: Tegye közzé az alkalmazást az Azure AD alkalmazásgalériában.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Az ügyfelek könnyen felderíthetik az alkalmazást, és egyszerűen konfigurálhatják a kiépítést. 

![Az SCIM-végpontok Azure AD-vel való integrálásának lépései](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>1. lépés: A felhasználó és a csoport sémájának megtervezése

Minden alkalmazásnak különböző attribútumokra van szüksége egy felhasználó vagy csoport létrehozásához. Indítsa el az integrációt az alkalmazás által igényelt objektumok (felhasználók, csoportok) és attribútumok (név, vezető, beosztás stb.) azonosításával. Az SCIM-szabvány egy sémát határoz meg a felhasználók és csoportok kezeléséhez. Az alapvető felhasználói sémához csak három attribútum szükséges: **id** (szolgáltató által definiált azonosító), **külső azonosító** (ügyfél által definiált azonosító) és **meta** (csak olvasható metaadatok, amelyeket a szolgáltató tart fenn). Az összes többi attribútum megadása nem kötelező. Az alapvető felhasználói séma mellett az SCIM-szabvány egy vállalati felhasználói bővítményt és egy modellt is meghatároz a felhasználói séma alkalmazás igényeinek megfelelő bővítéséhez. Ha például az alkalmazás megköveteli a felhasználó kezelője, használhatja a vállalati felhasználói séma gyűjteni a felhasználó felettesét, és az alapvető séma a felhasználó e-mail-adatok gyűjtésére. A séma megtervezéséhez kövesse az alábbi lépéseket:
  1. Sorolja fel az alkalmazás által igényelt attribútumokat. Hasznos lehet, ha a követelményeket a hitelesítéshez szükséges attribútumokra (pl. loginName és e-mail), a felhasználó életciklusának kezeléséhez szükséges attribútumokra (pl. állapot/ aktív) és az adott alkalmazás működéséhez szükséges egyéb attribútumokra (pl. kezelő, címke) bontja le.
  2. Ellenőrizze, hogy ezek az attribútumok már definiálva vannak-e az alapvető felhasználói sémában vagy a vállalati felhasználói sémában. Ha olyan attribútumok, amelyekre szüksége van, és nem szerepelnek a mag vagy a vállalati felhasználói sémák, meg kell határoznia egy bővítményt a felhasználói séma, amely lefedi a szükséges attribútumokat. Az alábbi példában egy bővítményt adtunk hozzá a felhasználóhoz, amely lehetővé teszi a "címke" kiépítését a felhasználón. A legjobb, ha csak a mag és a vállalati felhasználói sémák, és bontsa ki a további egyéni sémák később.  
  3. Az SCIM-attribútumok hozzárendelése a felhasználói attribútumokhoz az Azure AD-ben. Ha az SCIM-végpontban definiált attribútumok egyike nem rendelkezik egyértelmű megfelelővel az Azure AD felhasználói sémán, jó esély van arra, hogy az adatok egyáltalán nem tárolódnak a felhasználói objektumon a legtöbb bérlőn. Fontolja meg, hogy ez az attribútum választható lehet-e egy felhasználó létrehozásához. Ha az attribútum kritikus fontosságú az alkalmazás működéséhez, irányítsa a bérlői rendszergazdát, hogy bővítse ki a sémát, vagy használjon bővítményattribútumot a "címkék" tulajdonságalább látható módon.

### <a name="table-1-outline-the-attributes-that-you-need"></a>1. táblázat: A szükséges attribútumok körvonalazása 
| 1. lépés: Az alkalmazás által igényelt attribútumok meghatározása| 2. lépés: Alkalmazáskövetelmények leképezése az SCIM szabványhoz| 3. lépés: SCIM-attribútumok leképezése az Azure AD-attribútumokhoz|
|--|--|--|
|bejelentkezési név|userName (Felhasználónév)|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|munkaMail|E-mailek[típus eq "work"].value|Mail|
|manager|manager|manager|
|címke|urn:ietf:params:scim:sémák:kiterjesztés:2.0:CustomExtension:tag|extensionAttribute1|
|status|Aktív|isSoftDeleted (számított érték nem tárolja a felhasználó)|

A fent meghatározott séma az alábbi Json-hasznos adat használatával jelenik meg. Vegye figyelembe, hogy az alkalmazáshoz szükséges attribútumokon kívül a JSON-ábrázolás tartalmazza a szükséges "id", "externalId" és "meta" attribútumokat.

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

### <a name="table-2-default-user-attribute-mapping"></a>2. táblázat: Alapértelmezett felhasználói attribútumleképezés
Ezután az alábbi táblázat segítségével megtudhatja, hogy az alkalmazás által igényelt attribútumok hogyan képezhetik le egy attribútumot az Azure AD-ben és az SCIM RFC-ben. [Testreszabhatja,](customize-application-attributes.md) hogyan vannak leképezve attribútumok az Azure AD és az SCIM-végpont között. Ne feledje, hogy nem kell támogatnia a felhasználókat és csoportokat, illetve az alább látható összes attribútumot. Ezek egy hivatkozást, hogyan attribútumok az Azure AD gyakran le vannak képezve a tulajdonságok az SCIM protokoll. 

| Az Azure Active Directory felhasználói | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:Felhasználó" |
| --- | --- |
| IsSoftDeleted |Aktív |
|Részleg|urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:részleg|
| displayName |displayName |
|alkalmazottazonosító|urn:ietf:params:scim:sémák:kiterjesztés:vállalat:2.0:Felhasználó:alkalmazottSzám|
| Fax-telefonszám |phoneNumbers[type eq "fax"].érték |
| givenName |name.givenName |
| jobTitle |cím |
| Levelezés |e-mailek[típus eq "work"].value |
| mailBece |külső azonosító |
| manager |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:menedzser |
| mobil |phoneNumbers[type eq "mobile"].value |
| postai kód |addresses[type eq "work"].postalCode |
| proxy-címek |e-mailek[típus eq "egyéb"]. Érték |
| fizikai-Kézbesítés-OfficeName |addresses[type eq "other"]. Formázott |
| utcacím |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telefonszám |phoneNumbers[type eq "work"].value |
| felhasználó-PrincipalName |userName (Felhasználónév) |


### <a name="table-3-default-group-attribute-mapping"></a>3. táblázat: Alapértelmezett csoportattribútum-hozzárendelés

| Azure Active Directory-csoport | urn:ietf:params:scim:sémák:core:2.0:Csoport |
| --- | --- |
| displayName |displayName |
| Levelezés |e-mailek[típus eq "work"].value |
| mailBece |displayName |
| tagok |tagok |
| objectId |külső azonosító |
| proxyAddresses |e-mailek[típus eq "egyéb"]. Érték |

Az SCIM RFC-ben több végpont van definiálva. A /User végpont ismerkedésével, majd onnan bontsa ki a kibontást. A /Séma-végpont akkor hasznos, ha egyéni attribútumokat használ, vagy ha a séma gyakran változik. Lehetővé teszi, hogy az ügyfél automatikusan lekérje a legfrissebb sémát. A /Bulk végpont különösen hasznos csoportok támogatásakor. Az alábbi táblázat az SCIM-szabványban meghatározott különböző végpontokat ismerteti. A /Séma-végpont akkor hasznos, ha egyéni attribútumokat használ, vagy ha a séma gyakran változik. Lehetővé teszi, hogy az ügyfél automatikusan lekérje a legfrissebb sémát. A /Bulk végpont különösen hasznos csoportok támogatásakor. Az alábbi táblázat az SCIM-szabványban meghatározott különböző végpontokat ismerteti. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>4. táblázat: Határozza meg a fejleszteni kívánt végpontokat
|Végpont|LEÍRÁS|
|--|--|
|/Felhasználó|Crud műveletek végrehajtása felhasználói objektumon.|
|/Csoport|CRUD-műveletek végrehajtása csoportobjektumon.|
|/ServiceProviderConfig|Az SCIM-szabvány támogatott szolgáltatásainak részleteit tartalmazza, például a támogatott erőforrásokat és a hitelesítési módszert.|
|/Erőforrástípusok|Az egyes erőforrások metaadatait adja meg|
|/Sémák|Az egyes ügyfelek és szolgáltatók által támogatott attribútumkészlet eltérő lehet. Míg az egyik szolgáltató tartalmazhatja a "név", a "cím" és az "e-mail", míg egy másik szolgáltató a "name", a "title" és a "phoneNumbers" nevet használja. A séma végpont lehetővé teszi a támogatott attribútumok felderítését.|
|/Tömeges|A tömeges műveletek lehetővé teszik, hogy egyetlen műveletben hajtson végre műveleteket az erőforrás-objektumok nagy gyűjteményén (például egy nagy csoport tagságának frissítésén).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>2. lépés: Az Azure AD SCIM implementációjának megismerése
> [!IMPORTANT]
> Az Azure AD SCIM-implementáció viselkedése utoljára 2018. A rról, hogy mi változott, lásd: [SCIM 2.0 protokoll megfelelősége az Azure AD felhasználói kiépítési szolgáltatás.](application-provisioning-config-problem-scim-compatibility.md)

Ha egy SCIM 2.0 felhasználói felügyeleti API-t támogató alkalmazást hoz fel, ez a szakasz részletesen ismerteti, hogyan valósítható meg az Azure AD SCIM-ügyfél. Azt is bemutatja, hogyan modellezheti az SCIM protokollkérelmek kezelését és válaszait. Miután megvalósította az SCIM-végpontot, tesztelheti az előző szakaszban ismertetett eljárást követve.

Az [SCIM 2.0 protokoll specifikációján](http://www.simplecloud.info/#Specification)belül az alkalmazásnak meg kell felelnie az alábbi követelményeknek:

* Támogatja a felhasználók létrehozását, és szükség esetén csoportokat is, [az SCIM protokoll 3.3 szakasza szerint.](https://tools.ietf.org/html/rfc7644#section-3.3)  
* Támogatja a felhasználók vagy csoportok patch kérelmekkel történő módosítását [az SCIM protokoll 3.5.2 szakasza szerint.](https://tools.ietf.org/html/rfc7644#section-3.5.2)  
* Támogatja egy korábban létrehozott felhasználó vagy csoport ismert erőforrásának beolvasását [az SCIM protokoll 3.4.1 szakasza szerint.](https://tools.ietf.org/html/rfc7644#section-3.4.1)  
* Támogatja a felhasználók vagy csoportok lekérdezését [az SCIM protokoll 3.4.2 szakasza szerint.](https://tools.ietf.org/html/rfc7644#section-3.4.2)  Alapértelmezés szerint a felhasználókat `id` a saját és `username` a `externalid`és a lekérdezése, `displayName`a csoportlekérdezi a rendszer.  
* Támogatja a felhasználó azonosító és kezelő szerint történő lekérdezését az SCIM protokoll 3.4.2 szakasza szerint.  
* Támogatja a csoportok lekérdezését azonosító és tag szerint, az SCIM protokoll 3.4.2 szakasza szerint.  
* Egyetlen tulajdonosi jogkivonatot fogad el az Azure AD hitelesítéséhez és engedélyezéséhez az alkalmazáshoz.

Az Azure AD-vel való kompatibilitás biztosítása érdekében kövesse az alábbi általános irányelveket az SCIM-végpontok megvalósításakor:

* `id`az összes erőforrás kötelező tulajdonsága. Minden erőforrást visszaadó válasznak biztosítania kell, `ListResponse` hogy minden erőforrás rendelkezzen ezzel a tulajdonsággal, kivéve a nulla tagot.
* A lekérdezési/szűrési kérésre `ListResponse`mindig a választ kell adni.
* A csoportok nem kötelezőek, de csak akkor támogatottak, ha az SCIM-implementáció támogatja a PATCH-kérelmeket.
* Nem szükséges a teljes erőforrást felvenni a PATCH válaszba.
* A Microsoft Azure AD csak a következő operátorokat használja:  
    - `eq`
    - `and`
* Nincs szükség kis- és nagybetűk megkülönböztetésére az SCIM `op` szerkezeti elemeivel, https://tools.ietf.org/html/rfc7644#section-3.5.2különösen a PATCH műveleti elemekkel kapcsolatban a alkalmazásban meghatározottak szerint. Az Azure AD az "op" `Add` `Replace`értékét `Remove`bocsátja ki , és .
* A Microsoft Azure AD kéri, hogy egy véletlenszerű felhasználó és csoport lekérése annak érdekében, hogy a végpont és a hitelesítő adatok érvényesek. Ez az [Azure Portalon](https://portal.azure.com)a **Tesztkapcsolat** folyamatrészeként is történik. 
* Az attribútumot, amelyen az erőforrások lekérdezhetők, az [Azure Portalon](https://portal.azure.com)az alkalmazás megfelelő attribútumaként kell beállítani. További információ: [A felhasználói kiépítési attribútum-hozzárendelések testreszabása](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Felhasználói kiépítés és megszüntetés

A következő ábra azokat az üzeneteket mutatja be, amelyeket az Azure Active Directory küld egy SCIM-szolgáltatásnak az alkalmazás identitástárolójában lévő felhasználó életciklusának kezeléséhez.  

![A felhasználó kiépítési és megszüntetési sorozatának megjelenítése](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Felhasználó kiépítési és megszüntetési sorrendje*

### <a name="group-provisioning-and-deprovisioning"></a>Csoportkiépítés és -megszüntetés

A csoportkiépítés és a megszüntetés nem kötelező. Ha implementálva és engedélyezve van, a következő ábrán látható, hogy az Azure AD milyen üzeneteket küld egy SCIM-szolgáltatásnak az alkalmazás identitástárolójában lévő csoport életciklusának kezeléséhez.  Ezek az üzenetek kétféleképpen különböznek a felhasználókkal kapcsolatos üzenetektől:

* A csoportok lekérésére irányuló kérések azt határozzák meg, hogy a tagok attribútumát ki kell zárni a kérésre adott bármely erőforrásból.  
* A hivatkozási attribútum bizonyos értékkel rendelkező jának meghatározására irányuló kérések a tagok attribútumával kapcsolatos kérelmek.  

![A csoport kiépítési és megszüntetési sorrendjének megjelenítése](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Csoportkiépítési és -megszüntetési sorrend*

### <a name="scim-protocol-requests-and-responses"></a>SCIM protokoll kérések és válaszok
Ez a szakasz az Azure AD SCIM-ügyfél által kibocsátott scim-kérelmeket és a várt válaszok példáit ismerteti. A legjobb eredmény érdekében kódolja az alkalmazást, hogy kezelje ezeket a kéréseket ebben a formátumban, és adja ki a várt válaszokat.

> [!IMPORTANT]
> Ha meg szeretné tudni, hogy az Azure AD-felhasználó létesítési szolgáltatás a lent ismertetett műveleteket hogyan és mikor bocsátja ki, tekintse meg a [Kiépítési ciklusok: Kezdeti és növekményes](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [a kiépítés működésének](how-provisioning-works.md)című szakaszban.

[Felhasználói műveletek](#user-operations)
  - [Felhasználó létrehozása](#create-user) ([Kérésre](#request) / [adott válasz](#response))
  - [Felhasználó bekérése](#get-user) ([Kérésre](#request-1) / [adott válasz](#response-1))
  - [Felhasználó lekérdezés szerint beszerezni](#get-user-by-query) ([Válasz](#response-2)[kérése](#request-2) / )
  - [Felhasználó lekérdezéssel – Nulla eredmény](#get-user-by-query---zero-results) ([Válasz](#response-3)[kérése](#request-3)
/ )
  - [Felhasználó frissítése [Többértékű tulajdonságok]](#update-user-multi-valued-properties) ([Kérésválasz](#request-4) /  [Response](#response-4))
  - [Felhasználó frissítése [Egyértékű tulajdonságok]](#update-user-single-valued-properties) ([Kérésválasz](#request-5)
/ [Response](#response-5)) 
  - [Felhasználó letiltása](#disable-user) ( / 
[kérésválasz](#response-14))[Request](#request-14)
  - [Felhasználó törlése](#delete-user) ([Válasz kérése](#request-6) / 
[)](#response-6)


[Csoportos műveletek](#group-operations)
  - [Csoport létrehozása](#create-group) ( [Válasz kérése](#request-7) / [)](#response-7)
  - [Csoport bekérése](#get-group) ([Válasz](#response-8) [kérése](#request-8) / )
  - [Csoport bekérése displayName](#get-group-by-displayname) [(Kérésválasz) szerint](#request-9) / [Response](#response-9)
  - [Csoport frissítése [Tagsággal nem rendelkező attribútumok]](#update-group-non-member-attributes) ( [Válasz](#response-10)[kérése](#request-10) /
 )
  - [Csoport frissítése [Tagok hozzáadása]](#update-group-add-members) ( [Válasz kérése](#request-11) /
[)](#response-11)
  - [Csoport frissítése [Tagok eltávolítása]](#update-group-remove-members) ( [Válasz kérése](#request-12) /
[)](#response-12)
  - [Csoport törlése](#delete-group) ([Válasz](#response-13)[kérése](#request-13) /
)

### <a name="user-operations"></a>Felhasználói műveletek

* A felhasználók lekérdezhetők `email[type eq "work"]` vagy `userName` attribútumok.  

#### <a name="create-user"></a>Felhasználó létrehozása

###### <a name="request"></a>Kérés

*POST /Felhasználók*
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

###### <a name="request"></a><a name="request-1"></a>Kérés
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Válasz (a felhasználó talált)
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
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Válasz (A felhasználó nem található. Ne feledje, hogy a részletek nem szükségesek, csak állapot.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Felhasználó lekérdezés szerint beszerezni

##### <a name="request"></a><a name="request-2"></a>Kérés

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Válasz

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

#### <a name="get-user-by-query---zero-results"></a>Felhasználó lekérdezés szerint beszerezni - Nulla eredmény

##### <a name="request"></a><a name="request-3"></a>Kérés

*GET /Users?filter=userName eq "nem létező felhasználó"*

##### <a name="response"></a><a name="response-3"></a>Válasz

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

#### <a name="update-user-multi-valued-properties"></a>Felhasználó frissítése [Többértékű tulajdonságok]

##### <a name="request"></a><a name="request-4"></a>Kérés

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

##### <a name="response"></a><a name="response-4"></a>Válasz

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

#### <a name="update-user-single-valued-properties"></a>Felhasználó frissítése [Egyértékű tulajdonságok]

##### <a name="request"></a><a name="request-5"></a>Kérés

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

##### <a name="response"></a><a name="response-5"></a>Válasz

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

##### <a name="request"></a><a name="request-14"></a>Kérés

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-14"></a>Válasz

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

##### <a name="request"></a><a name="request-6"></a>Kérés

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Válasz

*HTTP/1.1 204 Nincs tartalom*

### <a name="group-operations"></a>Csoportos műveletek

* A csoportokat mindig üres taglistával kell létrehozni.
* A csoportokat az `displayName` attribútum lekérdezheti.
* A csoport PATCH-kérelemre való frissítésnek *http 204 nincs tartalom a* válaszban. Egy holttest visszaadása az összes tag listájával nem tanácsos.
* Nem szükséges támogatni a csoport összes tagjának visszaadását.

#### <a name="create-group"></a>Csoport létrehozása

##### <a name="request"></a><a name="request-7"></a>Kérés

*POST /Csoportok HTTP/1.1*
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

##### <a name="response"></a><a name="response-7"></a>Válasz

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

#### <a name="get-group"></a>Csoport beolvasása

##### <a name="request"></a><a name="request-8"></a>Kérés

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Válasz
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

#### <a name="get-group-by-displayname"></a>Csoport beszerezni a displayName-t

##### <a name="request"></a><a name="request-9"></a>Kérés
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Válasz

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

#### <a name="update-group-non-member-attributes"></a>Csoport frissítése [Tagsággal nem rendelkező attribútumok]

##### <a name="request"></a><a name="request-10"></a>Kérés

*PATCH /Csoportok/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Válasz

*HTTP/1.1 204 Nincs tartalom*

### <a name="update-group-add-members"></a>Csoport frissítése [Tagok hozzáadása]

##### <a name="request"></a><a name="request-11"></a>Kérés

*PATCH /Csoportok/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Válasz

*HTTP/1.1 204 Nincs tartalom*

#### <a name="update-group-remove-members"></a>Csoport frissítése [Tagok eltávolítása]

##### <a name="request"></a><a name="request-12"></a>Kérés

*PATCH /Csoportok/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Válasz

*HTTP/1.1 204 Nincs tartalom*

#### <a name="delete-group"></a>Csoport törlése

##### <a name="request"></a><a name="request-13"></a>Kérés

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Válasz

*HTTP/1.1 204 Nincs tartalom*

### <a name="security-requirements"></a>Biztonsági követelmények
**TLS protokollverziók**

Az egyetlen elfogadható TLS protokollverzió a TLS 1.2 és a TLS 1.3. A TLS más verziói nem engedélyezettek. Az SSL-nek nem engedélyezett. 
- Az RSA-kulcsoknak legalább 2048 bitesnek kell lenniük.
- Az ECC-kulcsoknak legalább 256 bitesnek kell lenniük, és jóváhagyott elliptikus görbével kell létrejönniük


**Kulcshosszok**

Minden szolgáltatásnak megfelelő hosszúságú kriptográfiai kulcsokkal létrehozott X.509 tanúsítványokat kell használnia, ami a következőket jelenti:

**Rejtjelező lakosztályok**

Minden szolgáltatást úgy kell konfigurálni, hogy a következő titkosítási csomagokat használja az alábbiakszerint meghatározott sorrendben. Ne feledje, hogy ha csak RSA-tanúsítvánnyal rendelkezik, az ECDSA titkosítási csomagok telepítése nincs hatással. </br>

TLS 1.2 Cipher Suites minimális sáv:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>3. lépés: SciM-végpont létrehozása

Most, hogy megtervezte a sémát, és megértette az Azure AD SCIM-implementációt, elkezdheti az SCIM-végpont fejlesztését. Ahelyett, hogy a semmiből, és az épület a végrehajtás teljesen a saját, akkor számíthat számos nyílt forráskódú SCIM könyvtárak által közzétett SCIM közösség.

A nyílt forráskódú .NET Core [referenciakód](https://aka.ms/SCIMReferenceCode) az Azure AD kiépítési csapat által közzétett egy ilyen erőforrás, amely ugrik a fejlesztés elindításához. Miután elkészítette a SCIM-végpontot, ki kell próbálnia. Használhatja a gyűjtemény [postás tesztek](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) részeként megadott referencia-kódot, vagy végigfut a minta kérelmek / válaszok [a fent](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)megadott .  

   > [!Note]
   > A referenciakód célja, hogy segítsen az SCIM-végpont létrehozásának megkezdésében, és "AS IS" (AS IS) A közösség hozzájárulásait szívesen látjuk a kód felépítésében és fenntartásában.

A megoldás két projektből áll: a _Microsoft.SCIM_ és a _Microsoft.SCIM.WebHostSample_.

A _Microsoft.SCIM_ projekt az a könyvtár, amely meghatározza a webszolgáltatás nak az SCIM specifikációnak megfelelő összetevőit. Deklarálja a _Microsoft.SCIM.IProvider_felületet, a kéréseket a szolgáltató metódusaira irányuló hívásokra fordítják le, amelyeket úgy programoznának, hogy egy identitástárolóban működjenek.

![Bontás: A szolgáltató metódusaira irányuló hívásokra fordított kérelem](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

A _Microsoft.SCIM.WebHostSample_ projekt egy Visual Studio ASP.NET Core webalkalmazás, amely az _Üres_ sablonon alapul. Ez lehetővé teszi, hogy a mintakód önállóként legyen telepítve, tárolókban vagy az Internet Information Services szolgáltatáson belül tárolva. Azt is megvalósítja a _Microsoft.SCIM.IProvider_ felület vezetése osztályok a memóriában, mint a minta identitás tároló.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Egyéni SCIM-végpont létrehozása

Az SCIM-szolgáltatásnak HTTP-címmel és kiszolgálóhitelesítési tanúsítvánnyal kell rendelkeznie, amelynek a legfelső szintű hitelesítésszolgáltatója az alábbi nevek egyike:

* Cnnic
* Comodo
* CyberTrust (Kibertröszt)
* DigiCert
* GeoTrust között
* Globalsign
* Menj Apu
* Verisign
* WoSign (WoSign)

A . Attól függően, hogy hogyan futtatja a ASP.NET Core webalkalmazás, akkor hallgatni egy másik port:

* Microsoft.SCIM.WebHostMinta:https://localhost:5001
* IIS Express:https://localhost:44359/

A HTTPS-ről ASP.NET Core-ban további információkért használja az alábbi hivatkozást: [HTTPS kényszerítése ASP.NET Core-ban](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Végpont-hitelesítés kezelése

Az Azure Active Directoryból érkező kérelmek tartalmaznak egy OAuth 2.0-s tulajdonosi jogkivonatot. A kérést fogadó bármely szolgáltatásnak hitelesítenie kell a kibocsátót, mint azure Active Directoryt a várt Azure Active Directory-bérlőszámára.

A tokenben a kibocsátót egy iss jogcím azonosítja, például `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`. Ebben a példában a jogcímérték `https://sts.windows.net`alapcíme, a , az Azure Active Directoryt nevezi meg kibocsátóként, míg a relatív címszegmens, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422,_ amelynek a jogkivonatot kiadták.

A jogkivonat közönsége lesz az alkalmazássablon-azonosító az alkalmazás a katalógusban, az egyes regisztrált `iss` alkalmazások egyetlen bérlőben is megkapja ugyanazt a jogcímet az SCIM-kérelmek. Az alkalmazás sablon azonosítója minden alkalmazás a galériában változik, kérjük, forduljon [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) a kérdések körül az alkalmazás sablon azonosítója egy galéria alkalmazás. Az alkalmazássablon azonosítója az összes egyéni alkalmazáshoz _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

A mintakódban a kérelmek hitelesítése a Microsoft.AspNetCore.Authentication.JwtBearer csomag használatával lesz hitelesítve. A következő kód kényszeríti, hogy a szolgáltatás bármely végpontjára irányuló kérelmek hitelesítése az Azure Active Directory által egy adott bérlőhöz kiadott tulajdonosi jogkivonat használatával történik:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

A megadott [postástesztek](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) használatához és a localhost használatával helyi hibakeresés hez is szükség van egy tulajdonosi jogkivonat. A mintakód ASP.NET core környezetek használatával módosítja a hitelesítési beállításokat a fejlesztési szakaszban, és engedélyezi az önaláírt jogkivonat használatát.

A Core több környezet ASP.NETével kapcsolatos további információkért használja az alábbi hivatkozást: [Több környezet használata ASP.NET Core-ban](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

A következő kód kikényszeríti, hogy a szolgáltatás bármely végpontjára irányuló kérelmek hitelesítése egy egyéni kulccsal aláírt tulajdonosi jogkivonat használatával:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

GET-kérés küldése a tokenvezérlőnek egy érvényes tulajdonosi jogkivonat lekéréséhez, a _GenerateJSONWebToken_ metódus felelős a fejlesztéshez konfigurált paramétereknek megfelelő token létrehozásáért:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>A felhasználók kiépítésének és megszüntetésének kezelése

***1. példa. A szolgáltatás lekérdezése egy megfelelő felhasználó számára***

Az Azure Active Directory lekérdezi a szolgáltatást egy olyan felhasználó számára, akinek egy externalId attribútumértéke megfelel az Azure AD-ben lévő felhasználó mailNickname attribútumértékének. A lekérdezés egy HTTP-kérelemként jelenik meg, például ebben a példában, amelyben jyoung egy minta egy felhasználó egy felhasználó az Azure Active Directoryban.

>[!NOTE]
> Ez csak egy példa. Nem minden felhasználó rendelkezik mailNickname attribútummal, és a felhasználó értéke nem feltétlenül egyedi a címtárban. Emellett az egyeztetéshez használt attribútum (amely ebben az esetben külsőazonosító) konfigurálható az [Azure AD attribútum-leképezésekben.](customize-application-attributes.md)

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

A mintakódban a rendszer lefordítja a rendszer a szolgáltatás szolgáltatójának QueryAsync metódusára irányuló hívássá. Itt van az aláírása, hogy a módszer: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

A mintalekérdezésben a externalId attribútumhoz megadott értéket használó felhasználó esetében a QueryAsync metódusnak átadott argumentumok értékei a következők:

* Paraméterek. AlternateFilters.Count: 1
* Paraméterek. AlternateFilters.ElementAt(0). AttribútumPath: "externalId"
* Paraméterek. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* Paraméterek. AlternateFilter.elementat(0). ComparisonValue: "jyoung"

***2. példa. Felhasználó kiépítése***

Ha a felhasználó mailNickname attribútumértékének megfelelő externalId attribútumértékű felhasználó webszolgáltatásra irányuló lekérdezésére adott válasz nem ad vissza egyetlen felhasználót sem, majd az Azure Active Directory azt kéri, hogy a szolgáltatás egy olyan felhasználót létesítsen, amely megfelel az egyiknek. az Azure Active Directoryban.  Íme egy példa egy ilyen kérésre: 

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

A mintakódban a kérelem a szolgáltatás szolgáltatójának CreateAsync metódusának hívására lesz lefordítva. Itt van az aláírása, hogy a módszer: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

A felhasználó létesítésére vonatkozó kérelemben az erőforrásargumentum értéke a Microsoft.SCIM.Core2EnterpriseUser osztály nak a Microsoft.SCIM.Schemas könyvtárban definiált példánya.  Ha a felhasználó létesítési kérelme sikeres, akkor a metódus megvalósításának vissza kell adnia a Microsoft.SCIM.Core2EnterpriseUser osztály egy példányát, az Azonosító tulajdonság értéke pedig az újonnan kiépített egyedi azonosítójára van állítva. Felhasználó.  

***3. példa. Felhasználó aktuális állapotának lekérdezése*** 

Egy SCIM által előrebejáratolt identitástárolóban ismert felhasználó frissítéséhez az Azure Active Directory úgy folytatja, hogy a felhasználó aktuális állapotát kéri a szolgáltatástól egy olyan kéréssel, mint például: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

A mintakódban a rendszer a kérést a szolgáltatás szolgáltatójának RetrieveAsync metódusára irányuló hívássá fordítja le. Itt van az aláírása, hogy a módszer: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

A felhasználó aktuális állapotának lekérésére irányuló kérelem példájában a paraméterek argumentum értékeként megadott objektum tulajdonságainak értékei a következők: 
  
* Azonosító: "54D382a4-2050-4C03-94D1-E769F1D15682"
* Sémaazonosító: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:Felhasználó"

***4. példa. Frissítendő referenciaattribútum értékének lekérdezése*** 

Ha egy hivatkozási attribútumot frissíteni kell, majd az Azure Active Directory lekérdezi a szolgáltatást annak megállapítására, hogy a szolgáltatás által előrebejáratolt identitástárolóban lévő referenciaattribútum aktuális értéke már megegyezik-e az adott attribútum értékével az Azure Active ban Taglista. A felhasználók számára az egyetlen attribútum, amelynek az aktuális érték lekérdezése ily módon a kezelő attribútum. Íme egy példa annak meghatározására, hogy egy felhasználói objektum kezelőattribútuma jelenleg rendelkezik-e egy bizonyos értékkel: A mintakódban a kérelem a szolgáltatás szolgáltatójának QueryAsync metódusába történő hívássá lesz lefordítva. A paraméterek argumentum értékeként megadott objektum tulajdonságainak értéke a következő: 
  
* Paraméterek. AlternateFilters.Count: 2
* Paraméterek. AlternateFilters.ElementAt(x). Attribútumpath: "Azonosító"
* Paraméterek. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* Paraméterek. AlternateFilter.ElementAt(x). Összehasonlításérték: "54D382a4-2050-4C03-94D1-E769F1D15682"
* Paraméterek. AlternateFilters.ElementAt(y). AttribútumPath: "menedzser"
* Paraméterek. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* Paraméterek. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* Paraméterek. RequestedAttributes.ElementAt(0): "ID"
* Paraméterek. Sémaazonosító: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:Felhasználó"

Itt az index x értéke 0 lehet, az y index értéke pedig 1, az x értéke pedig 1, az y értéke pedig 0, a szűrőlekérdezési paraméter kifejezéseinek sorrendjétől függően.   

***5. példa. Kérelem az Azure AD-től egy SCIM-szolgáltatáshoz egy felhasználó frissítéséhez*** 

Íme egy példa az Azure Active Directorytól egy SCIM-szolgáltatáshoz egy INC-szolgáltatásnak a felhasználó frissítésére irányuló kérelemre: 

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

A mintakódban a rendszer lefordítja a kérést a szolgáltató UpdateAsync metódusának hívására. Itt van az aláírása, hogy a módszer: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

A felhasználó frissítésére vonatkozó kérelem példájában a javításargumentum értékeként megadott objektum a következő tulajdonságértékekkel rendelkezik: 
  
* Erőforrásazonosító.Azonosító: "54D382a4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:Felhasználó"
* (PatchRequest mint PatchRequest2). Operations.Count: 1
* (PatchRequest mint PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest mint PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "menedzser"
* (PatchRequest mint PatchRequest2). Operations.ElementAt(0). Érték.Gróf: 1
* (PatchRequest mint PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Hivatkozás: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest mint PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Érték: 2819c223-7f76-453a-919d-413861904646

***6. példa. Felhasználó kiirtásának kiirtása***

Ha egy iniciációs szolgáltatás által előrebejáratolt identitástárolóból szeretne kiépíteni egy felhasználót, az Azure AD például a következő kérést küldi:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

A mintakódban a kérelem a szolgáltatás szolgáltatójának DeleteAsync metódusára irányuló hívássá lesz lefordítva. Itt van az aláírása, hogy a módszer: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

A resourceIdentifier argumentum értékeként megadott objektum a felhasználó megszüntetésére irányuló kérelem példájában a következő tulajdonságértékeket tartalmazza: 

* Erőforrásazonosító.Azonosító: "54D382a4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:Felhasználó"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4. lépés: Az SCIM-végpont integrálása az Azure AD SCIM-ügyféllel

Az Azure AD beállítható úgy, hogy automatikusan hozzárendelt felhasználókat és csoportokat létesítsen az [SCIM 2.0 protokoll](https://tools.ietf.org/html/rfc7644)egy adott profilját megvalósító alkalmazásokhoz. A profil sajátosságait a [2.](#step-2-understand-the-azure-ad-scim-implementation)

Érdeklődjön az alkalmazás szolgáltatójánál vagy az alkalmazásszolgáltató dokumentációjában az ezen követelményekkel való kompatibilitási nyilatkozatokról.

> [!IMPORTANT]
> Az Azure AD SCIM-implementáció az Azure AD felhasználói kiépítési szolgáltatásra épül, amely folyamatosan szinkronizálja a felhasználókat az Azure AD és a célalkalmazás között, és egy nagyon speciális szabványos műveletkészletet valósít meg. Fontos, hogy ezeket a viselkedéseket az Azure AD SCIM-ügyfél viselkedésének megértéséhez. További információt a [Kiépítési ciklusok: Kezdeti és növekményes](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [a kiépítés működésének](how-provisioning-works.md)című szakaszban talál.

### <a name="getting-started"></a>Első lépések

A cikkben ismertetett SCIM-profilt támogató alkalmazások az Azure AD-alkalmazásgyűjtemény "nem katalógusalkalmazás" szolgáltatásával kapcsolódhatnak az Azure Active Directoryhoz. A csatlakozás után az Azure AD 40 percenként futtat egy szinkronizálási folyamatot, amelyben lekérdezi az alkalmazás SCIM-végpontját a hozzárendelt felhasználók és csoportok számára, és létrehozza vagy módosítja azokat a hozzárendelés részleteinek megfelelően.

**SciM-et támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be az [Azure Active Directory portálra.](https://aad.portal.azure.com) Vegye figyelembe, hogy a [fejlesztői programra](https://developer.microsoft.com/office/dev-program) való feliratkozással ingyenes próbaverziót kaphat az Azure Active Directoryhoz P2 licencekkel
2. Válassza a **vállalati alkalmazások lehetőséget** a bal oldali ablaktáblából. Megjelenik az összes konfigurált alkalmazás listája, beleértve a katalógusból hozzáadott alkalmazásokat is.
3. Válassza a **+ Új alkalmazás** > **Minden** > **nem galéria alkalmazás**lehetőséget.
4. Adja meg az alkalmazás nevét, és válassza a **Hozzáadás** lehetőséget egy alkalmazásobjektum létrehozásához. Az új alkalmazás hozzáadódik a vállalati alkalmazások listájához, és megnyílik az alkalmazásfelügyeleti képernyőn.

   ![Képernyőkép az Azure AD alkalmazásgyűjteményét jeleníti meg](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Az Azure AD alkalmazásgyűjteménye*

5. Az alkalmazáskezelés képernyőn válassza **a Kiépítés** a bal oldali panelen.
6. A **Kiépítési mód** menüben válassza az **Automatikus parancsot.**

   ![Példa: Egy alkalmazás kiépítési lapja az Azure Portalon](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Kiépítés konfigurálása az Azure Portalon*

7. A **Bérlő URL-címe** mezőbe írja be az alkalmazás SCIM-végpontjának URL-címét. Például: `https://api.contoso.com/scim/`
8. Ha az SCIM-végpont hoz egy OAuth-tulajdonosi jogkivonatot egy másik kiállítótól, mint az Azure AD, majd másolja a szükséges OAuth-tulajdonosi jogkivonatot a választható **titkos jogkivonat** mezőbe. Ha ez a mező üresen marad, az Azure AD tartalmaz egy OAuth-tulajdonosi jogkivonatot az Azure AD-ből minden egyes kérelemhez. Az Azure AD-t identitásszolgáltatóként használó alkalmazások érvényesíthetik ezt az Azure AD által kibocsátott jogkivonatot. 
   > [!NOTE]
   > ***Nem*** ajánlott üresen hagyni ezt a mezőt, és az Azure AD által létrehozott jogkivonattámaszkodás. Ez a beállítás elsősorban tesztelési célokra érhető el.
9. Válassza ki **a Kapcsolat tesztelése** lehetőséget, ha azt szeretné, hogy az Azure Active Directory próbáljon meg csatlakozni az SCIM-végponthoz. Ha a kísérlet sikertelen, hibainformáció jelenik meg.  

    > [!NOTE]
    > **A tesztkapcsolat** lekérdezi az SCIM-végpontot egy nem létező felhasználó számára, egy véletlenszerű GUID-ot használva az Azure AD-konfigurációban kiválasztott megfelelő tulajdonságként. A várt helyes válasz HTTP 200 OK üres SCIM ListResponse üzenettel.

10. Ha az alkalmazáshoz való kapcsolódási kísérletek sikeresek, válassza a **Mentés** lehetőséget a rendszergazdai hitelesítő adatok mentéséhez.
11. A **Leképezések** szakaszban két választható [attribútumleképezési](customize-application-attributes.md)csoport van: az egyik a felhasználói objektumok, a másik a csoportobjektumok esetében. Jelölje ki mindegyiket az Azure Active Directoryból az alkalmazással szinkronizált attribútumok áttekintéséhez. Az **Egyező** tulajdonságokként kijelölt attribútumok az alkalmazás felhasználóinak és csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a **Mentés** gombot.

    > [!NOTE]
    > A "csoportok" leképezésének letiltásával letilthatja a csoportobjektumok szinkronizálását.

12. A **Beállítások csoportban**a **Hatókör** mező határozza meg, hogy mely felhasználók és csoportok lesznek szinkronizálva. Jelölje **be a Csak hozzárendelt felhasználók és csoportok szinkronizálása** jelölőnégyzetet (ajánlott) ha csak a Felhasználók és csoportok lapon hozzárendelt felhasználókat **és** csoportokat szeretné szinkronizálni.
13. A konfiguráció befejezése után állítsa a **Kiépítési állapot** ot **Bekapcsolva**.
14. Válassza a **Mentés** lehetőséget az Azure AD-kiépítési szolgáltatás elindításához.
15. Ha csak a hozzárendelt felhasználókés csoportok szinkronizálása (ajánlott), jelölje ki a **Felhasználók és csoportok** lapot, és rendelje hozzá a szinkronizálni kívánt felhasználókat vagy csoportokat.

Miután a kezdeti ciklus elindult, **kiválaszthatja a kiépítési naplók** a bal oldali panelen a folyamat figyeléséhez, amely megjeleníti az összes műveletet a kiépítési szolgáltatás az alkalmazáson. Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](check-status-user-account-provisioning.md)olvashat bővebben.

> [!NOTE]
> A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5. lépés: Az alkalmazás közzététele az Azure AD alkalmazásgalériában

Ha egy olyan alkalmazást hoz össze, amelyet egynél több bérlő fog használni, elérhetővé teheti az Azure AD alkalmazásgyűjteményében. Ez megkönnyíti a szervezetek számára az alkalmazás felderítését és a kiépítés konfigurálását. Az alkalmazás közzététele az Azure AD-galériában, és a kiépítés mások számára is elérhetővé tétele egyszerű. Nézze meg a lépéseket [itt](../develop/howto-app-gallery-listing.md). A Microsoft önnel együttműködve integrálja alkalmazását a galériánkba, teszteli a végpontot, és kiadja a bevezetési [dokumentációt](../saas-apps/tutorial-list.md) az ügyfelek számára. 

### <a name="gallery-onboarding-checklist"></a>Galéria bevezetési ellenőrzőlistája
Kövesse az alábbi ellenőrzőlistát annak érdekében, hogy az alkalmazás gyorsan be legyen táblázva, és az ügyfelek zökkenőmentes üzembe helyezési élményben legyenek. Az információkat öntől gyűjtik, amikor a galériába kerül. 
> [!div class="checklist"]
> * [SciM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) felhasználói és csoportvégpont támogatása (csak egy szükséges, de mindkettő ajánlott)
> * Legalább 25 kérelem támogatása másodpercenként bérlőnként (kötelező)
> * Mérnöki és támogatási kapcsolattartók létrehozása a galéria bevezetést közzétenni kívánó ügyfelek számára (kötelező)
> * 3 Nem lejáró teszt hitelesítő adatok az alkalmazás (kötelező)
> * Az OAuth engedélyezési kód megadásának vagy az alábbiakban ismertetett hosszú élettartamú jogkivonat nak a támogatása (kötelező)
> * Mérnöki és támogatási kapcsolattartó létrehozása a galéria közzétételt közzétenni kívánó ügyfelek támogatásához (kötelező)
> * Több csoporttagság frissítésének támogatása egyetlen PATCH-tel (ajánlott) 
> * Az SCIM-végpont nyilvános dokumentálása (ajánlott) 
> * [Támogatás sémafelderítés (ajánlott)](https://tools.ietf.org/html/rfc7643#section-6)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Összekötők létesítésének engedélyezése az alkalmazásgyűjteményben
Az SCIM-specifikáció nem határoz meg SCIM-specifikus hitelesítési és engedélyezési sémát. A meglévő ipari szabványok alkalmazására támaszkodik. Az Azure AD-kiépítési ügyfél két engedélyezési módszert támogat a katalógusban lévő alkalmazásokhoz. 

|Engedélyezési módszer|Előnyök|Hátrányok|Támogatás|
|--|--|--|--|
|Felhasználónév és jelszó (az Azure AD nem ajánlott vagy nem támogatja)|Könnyen megvalósítható|Bizonytalan - [A Pa $ $word nem számít](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|A galériaalkalmazások eseti alapon támogatottak. Nem támogatott a nem galéria alkalmazások.|
|Hosszú élettartamú bemutatóra szóló token|A hosszú élettartamú jogkivonatoknem igénylik a felhasználó jelen lévő. Ezek a rendszergazdák könnyen használható a kiépítés beállításakor.|A hosszú élettartamú jogkivonatokat nehéz megosztani egy rendszergazdával nem biztonságos módszerek, például e-mail használata nélkül. |Galéria és nem galéria alkalmazások támogatottak. |
|OAuth engedélyezési kód megadása|A hozzáférési jogkivonatok sokkal rövidebb élettartamúak, mint a jelszavak, és olyan automatikus frissítési mechanizmussal rendelkeznek, amely a hosszú élettartamú tulajdonosi jogkivonatokkal nem rendelkezik.  A kezdeti engedélyezés során egy valódi felhasználónak jelen kell lennie, hozzátéve az elszámoltathatóság szintjét. |A felhasználónak jelen kell lennie. Ha a felhasználó elhagyja a szervezetet, a jogkivonat érvénytelen, és az engedélyezést újra be kell fejezni.|Galériaalkalmazások támogatottak. A nem galéria alkalmazások támogatása folyamatban van.|
|OAuth ügyfél hitelesítő adatok megadása|A hozzáférési jogkivonatok sokkal rövidebb élettartamúak, mint a jelszavak, és olyan automatikus frissítési mechanizmussal rendelkeznek, amely a hosszú élettartamú tulajdonosi jogkivonatokkal nem rendelkezik. Mind az engedélyezési kód megadása, mind az ügyfél hitelesítő adatok megadása azonos típusú hozzáférési jogkivonatot hoz létre, így a metódusok közötti váltás az API számára transzparens.  Kiépítése lehet teljesen automatizált, és az új jogkivonatok csendesen kérhető felhasználói beavatkozás nélkül. ||Nem támogatott a galéria és a nem galéria alkalmazások. A támogatás a hátralékunkban van.|

[!NOTE] Nem ajánlott üresen hagyni a jogkivonat-mezőt az Azure AD-kiépítési konfigurációs egyéni alkalmazás felhasználói felületén. A létrehozott jogkivonat elsősorban tesztelési célokra érhető el.

**OAuth engedélyezési kód engedélyezési folyamat:** A létesítési szolgáltatás támogatja az [engedélyezési kód megadását.](https://tools.ietf.org/html/rfc6749#page-24) Miután elküldte az alkalmazás közzétételére vonatkozó kérését a galériában, csapatunk együttműködik Önnel a következő információk összegyűjtésén:
*  Engedélyezési URL: Az ügyfél URL-címe, amely az erőforrás tulajdonosától a felhasználói ügynök átirányításán keresztül kap engedélyt. A felhasználó át lesz irányítva erre az URL-címre a hozzáférés engedélyezéséhez. 
*  Jogkivonat-csere URL-címe: Az ügyfél URL-címe egy hozzáférési jogkivonat engedélyezési megadásának cseréjéhez, általában az ügyfél hitelesítésével.
*  Ügyfélazonosító: Az engedélyezési kiszolgáló ügyfélazonosítót ad ki a regisztrált ügyfélnek, amely az ügyfél által megadott regisztrációs adatokat képviselő egyedi karakterlánc.  Az ügyfélazonosító nem titkos; az erőforrás tulajdonosa nem **használható** egyedül az ügyfél hitelesítéséhez.  
*  Ügyféltitok: Az ügyféltitok az engedélyezési kiszolgáló által létrehozott titok. Olyan egyedi értéknek kell lennie, amelyet csak az engedélyezési kiszolgáló ismer. 

Vegye figyelembe, hogy az OAuth v1 nem támogatott az ügyféltitkos lista felvannak téve. OAuth v2 támogatott.  

Bevált gyakorlatok (ajánlott, de nem kötelező):
* Több átirányítási URL támogatása. A rendszergazdák konfigurálhatják a kiépítést a "portal.azure.com" és a "aad.portal.azure.com" között is. Több átirányítási URL-ek támogatása biztosítja, hogy a felhasználók bármelyik portálról engedélyezhetik a hozzáférést.
* Több titkos titok támogatása a titkos zökkenőmentes megújítás érdekében, állásidő nélkül. 

**Hosszú életű OAuth hordozó tokenek:** Ha az alkalmazás nem támogatja az OAuth engedélyezési kód engedélyezési folyamat, hozhat létre egy hosszú élettartamú OAuth-tulajdonosi jogkivonatot, mint amely a rendszergazda segítségével a létesítési integráció beállítása. A jogkivonatnak örökösnek kell lennie, különben a kiépítési feladat [karanténba](application-provisioning-quarantine-status.md) kerül, amikor a jogkivonat lejár. Ennek a jogkivonatnak 1 KB alatt kell lennie.  

További hitelesítési és engedélyezési módszerek, tudassa velünk a [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Galéria go-to-market dob ellenőrző lista
A közös integráció ismertségének és igényének növelése érdekében javasoljuk, hogy frissítse meglévő dokumentációját, és erősítse meg a marketingcsatornákba való integrációt.  Az alábbiakban egy sor ellenőrzőlista tevékenységek javasoljuk, hogy töltse ki, hogy támogassa a dob

* **Értékesítési és ügyfélszolgálati készenlét.** Győződjön meg arról, hogy értékesítési és támogatási csapatai tisztában vannak az integrációs képességekkel, és tudnak beszélni. Tájékoztassa értékesítési és támogatási csapatát, adjon meg nekik GYIK-et, és foglalja bele az értékesítési anyagokba való integrációt. 
* **Blogbejegyzés és/vagy sajtóközlemény.** Hozzon ki egy blogbejegyzést vagy sajtóközleményt, amely leírja a közös integrációt, az előnyöket és az indulás módját. [Példa: Imprivata és Az Azure Active Directory sajtóközleménye](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Közösségi média.** Használja ki a közösségi médiát, például a Twittert, a Facebookot vagy a LinkedInt, hogy előmozdítsa az integrációt ügyfelei számára. Ügyeljen arra, @AzureAD hogy tartalmazza, így tudjuk retweet a post. [Példa: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketing weboldal.** Hozza létre vagy frissítse marketingoldalait (pl. integrációs oldal, partneroldal, árképzési oldal stb.), hogy az tartalmazza a közös integráció elérhetőségét. [Példa: Pingboard integrációs oldal](https://pingboard.com/org-chart-for), [Smartsheet integrációs oldal](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com árképzési oldal](https://monday.com/pricing/) 
* **Műszaki dokumentáció.** Hozzon létre egy súgócikket vagy műszaki dokumentációt arról, hogyan kezdhetik el az ügyfelek. [Példa: Envoy + Microsoft Azure Active Directory-integráció.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Ügyfélkommunikáció.** Figyelmeztesse az ügyfeleket az új integrációra az ügyfélkommunikáción keresztül (havi hírlevelek, e-mail kampányok, termékkibocsátási megjegyzések). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Az Azure AD-kiépítési szolgáltatás által az SCIM-kérelmek hez használt IP-címek engedélyezése

Bizonyos alkalmazások lehetővé teszik a bejövő forgalmat az alkalmazásukba. Annak érdekében, hogy az Azure AD-létesítési szolgáltatás a várt módon működjön, a használt IP-címek engedélyezve kell. Az egyes szolgáltatáscímkékhez/-régiókhoz rendelt IP-címek listáját a JSON-fájl – [Azure IP-tartományok és szolgáltatáscímkék – nyilvános felhő tartalmazza.](https://www.microsoft.com/download/details.aspx?id=56519) Ezeket az IP-ket szükség szerint letöltheti és beprogramozhatja a tűzfalba. Az Azure AD-kiépítés fenntartott IP-tartományai az "AzureActiveDirectoryDomainServices" alatt találhatók.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [A felhasználók kiépítésének és a SaaS-alkalmazásokhoz való kiépítésautomatizálása](user-provisioning.md)
* [Attribútumleképezések testreszabása a felhasználók kiépítéséhez](customize-application-attributes.md)
* [Kifejezések írása attribútumleképezéshez](functions-for-customizing-application-data.md)
* [Hatókörszűrők a felhasználók kiépítéshez](define-conditional-rules-for-provisioning-user-accounts.md)
* [Fiókkiépítési értesítések](user-provisioning.md)
* [Az SaaS-alkalmazások integrálására vonatkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
