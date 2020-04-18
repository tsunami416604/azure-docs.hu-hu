---
title: Az Azure AD attribútumleképezések testreszabása | Microsoft dokumentumok
description: Megtudhatja, hogy az Azure Active Directoryban milyen attribútumleképezések tartoznak az SaaS-alkalmazásokhoz, hogyan módosíthatja őket az üzleti igényeknek megfelelően.
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
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639746"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>SaaS-alkalmazások felhasználói kiépítési attribútum-hozzárendelések testreszabása az Azure Active Directoryban

A Microsoft Azure AD támogatást nyújt a külső SaaS-alkalmazásokba, például a Salesforce- hoz, a G Suite-hoz és másokhoz történő felhasználói kiépítéshez. Ha engedélyezi a felhasználó kiépítése egy külső SaaS-alkalmazás, az Azure Portal szabályozza az attribútum értékeit attribútum-leképezések.

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között egy előre konfigurált attribútum- és attribútum-leképezési készlet található. Egyes alkalmazások más típusú objektumokat is kezelnek a Felhasználókkal együtt, például a Csoportokkal együtt.

Az alapértelmezett attribútum-hozzárendeléseket az üzleti igényeknek megfelelően szabhatja testre. Így módosíthatja vagy törölheti a meglévő attribútum-leképezéseket, vagy új attribútum-leképezéseket hozhat létre.

## <a name="editing-user-attribute-mappings"></a>Felhasználói attribútum-hozzárendelések szerkesztése

Az alábbi lépések végrehajtásához hajtsa végre a **leképezések** szolgáltatás a felhasználói kiépítés:

1. Jelentkezzen be az [Azure Active Directory portálra.](https://aad.portal.azure.com)
1. Válassza a **vállalati alkalmazások lehetőséget** a bal oldali ablaktáblából. Megjelenik az összes konfigurált alkalmazás listája, beleértve a katalógusból hozzáadott alkalmazásokat is.
1. Jelölje ki bármelyik alkalmazást az alkalmazáskezelő ablaktáblán, ahol megtekintheti a jelentéseket és kezelheti az alkalmazásbeállításokat.
1. Válassza **a Kiépítés** lehetőséget a kijelölt alkalmazás felhasználói fiók kiépítési beállításainak kezeléséhez.
1. **Bontsa ki a leképezések** megtekintéséhez és szerkesztéséhez a felhasználói attribútumok közötti folyatása Azure AD és a célalkalmazás között. Ha a célalkalmazás támogatja, ez a szakasz lehetővé teszi a csoportok és felhasználói fiókok kiépítésének konfigurálását.

   ![Felhasználói attribútumok megtekintése és szerkesztése a Leképezések használatával](./media/customize-application-attributes/21.png)

1. Válasszon **egy Leképezési konfigurációt** a kapcsolódó **Attribútumleképezés** képernyő megnyitásához. Az SaaS-alkalmazások megfelelő működéséhez bizonyos attribútum-leképezések szükségesek. A szükséges attribútumok esetében a **Törlés** szolgáltatás nem érhető el.

   ![Az attribútumleképezés használata az alkalmazások attribútumleképezésének konfigurálásához](./media/customize-application-attributes/22.png)

   Ezen a képernyőképen láthatja, hogy a Salesforce-ban egy felügyelt objektum **Felhasználónév** attribútuma fel van töltve a csatolt Azure Active Directory-objektum **userPrincipalName** értékével.

1. Az **Attribútum szerkesztése** képernyő megnyitásához jelöljön ki egy meglévő **attribútumleképezést.** Itt szerkesztheti az Azure AD és a célalkalmazás között áramló felhasználói attribútumokat.

   ![Felhasználói attribútumok szerkesztése attribútummal](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Az attribútumleképezési típusok ismertetése

Az attribútum-leképezések segítségével szabályozhatja, hogy az attribútumok hogyan töltődjenek fel egy külső SaaS-alkalmazásban.
Négy különböző leképezési típus támogatott:

- **Közvetlen** – a célattribútum az Azure AD-ben a csatolt objektum attribútumának értékével van feltöltve.
- **Állandó** – a célattribútum egy megadott karakterláncmal van feltöltve.
- **Kifejezés** – a célattribútum egy parancsfájlszerű kifejezés eredménye alapján van feltöltve.
  További információt az [Attribútum-leképezések kifejezések írása az Azure Active Directoryban című témakörben talál.](../app-provisioning/functions-for-customizing-application-data.md)
- **Nincs** – a célattribútum változatlan marad. Ha azonban a célattribútum valaha is üres, a megadott alapértelmezett értékkel van feltöltve.

A négy alapvető típussal együtt az egyéni attribútumleképezések támogatják a választható **alapértelmezett** érték-hozzárendelés fogalmát. Az alapértelmezett érték-hozzárendelés biztosítja, hogy a célattribútum fel van töltve egy értékkel, ha nincs érték az Azure AD-ben vagy a célobjektumban. A leggyakoribb konfiguráció ezt üresen hagyja.

### <a name="understanding-attribute-mapping-properties"></a>Az attribútumleképezési tulajdonságok ismertetése

Az előző szakaszban már be vezették az attribútumleképezés típustulajdonságát.
Ezzel a tulajdonsággal együtt az attribútumleképezések a következő attribútumokat is támogatják:

- **Forrás attribútum** – A felhasználói attribútum a forrásrendszerből (például: Azure Active Directory).
- **Célattribútum** – A felhasználói attribútum a célrendszerben (például: ServiceNow).
- **Alapértelmezett érték, ha null (nem kötelező)** – Az az érték, amelyet a rendszer átad a célrendszernek, ha a forrásattribútum null értékű. Ez az érték csak akkor lesz kiépítve, ha a felhasználó jön létre. Az "alapértelmezett érték, ha null" nem lesz kiépítve egy meglévő felhasználó frissítésekor. Ha például a célrendszerben lévő összes meglévő felhasználót egy adott beosztással szeretné kiépíteni (ha az null a forrásrendszerben), akkor a következő [kifejezést](../app-provisioning/functions-for-customizing-application-data.md)használhatja: Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Győződjön meg arról, hogy cserélje le az "Alapértelmezett érték" a mit szeretne kiépíteni, ha null a forrásrendszerben. 
- **Objektumok egyeztetése ezzel az attribútummal** – Azt jelzi, hogy ezt a leképezést kell-e használni a felhasználók egyedi azonosítására a forrás- és a célrendszerek között. Általában a userPrincipalName vagy mail attribútum az Azure AD-ben, amely általában egy célalkalmazás felhasználónév mezőjébe van rendelve.
- **Egyező sorrend** – Több egyező attribútum is beállítható. Ha több van, akkor a program az ebben a mezőben meghatározott sorrendben értékeli ki őket. Amint egyezést talál, a rendszer nem értékeli ki a további egyező attribútumokat.
- **A leképezés alkalmazása**
  - **Mindig** – Alkalmazza ezt a leképezést a felhasználó létrehozási és frissítési műveletekre is.
  - **Csak a létrehozás során** – Ezt a leképezést csak a felhasználó létrehozási műveletekre alkalmazza.

## <a name="matching-users-in-the-source-and-target--systems"></a>A forrás- és célrendszerek felhasználóinak egyeztetése
Az Azure AD-kiépítési szolgáltatás telepíthető mind a "zöldmezős" forgatókönyvek (ahol a felhasználók nem lépnek ki a célrendszerben) és a "brownfield" forgatókönyvek (ahol a felhasználók már léteznek a célrendszerben). Mindkét forgatókönyv támogatása érdekében a létesítési szolgáltatás az egyező attribútumok fogalmát használja. Az egyező attribútumok lehetővé teszik annak meghatározását, hogy miként azonosíthatja a felhasználót a forrásban, és hogyan egyezhet meg a felhasználóval a célban. A központi telepítés megtervezése részeként azonosítsa azt az attribútumot, amely a forrás- és célrendszerekben lévő felhasználó egyedi azonosítására használható. Megkell jegyezni:

- **Az egyező attribútumoknak egyedinek kell lenniük:** Az ügyfelek gyakran használnak olyan attribútumokat, mint a userPrincipalName, a mail vagy az objektumazonosító egyező attribútumként.
- **Több attribútum is használható egyező attribútumként:** Több attribútumot is megadhat, amelyeket ki kell értékelni a felhasználók egyeztetése során, és a kiértékelésük sorrendjét (a felhasználói felületen egyező prioritásként definiálva). Ha például három attribútumot definiál egyező attribútumként, és a felhasználó egyedileg egyezteti az első két attribútum kiértékelése után, a szolgáltatás nem értékeli ki a harmadik attribútumot. A szolgáltatás kiértékeli az egyező attribútumokat a megadott sorrendben, és leállítja a kiértékelést, ha egyezést talál.  
- **A forrás és a cél értékének nem kell pontosan egyeznie:** A cél érték lehet néhány egyszerű függvénye az érték a forrásban. Így lehet egy emailAddress attribútum a forrásban és a userPrincipalName a célban, és egyezik az emailAddress attribútum egy függvényével, amely néhány karaktert állandó értékkel helyettesít.  
- **Az attribútumok kombinációján alapuló egyeztetés nem támogatott:** A legtöbb alkalmazás nem támogatja a két tulajdonságon alapuló lekérdezést. Ezért az attribútumok kombinációja alapján nem lehet egyeztetni. Lehetőség van az egyes tulajdonságok kiértékelésére a másik után.
- **Minden felhasználónak rendelkeznie kell legalább egy egyező attribútum értékkel:** Ha egy egyező attribútumot ad meg, minden felhasználónak rendelkeznie kell egy értékkel az adott attribútumhoz a forrásrendszerben. Ha például a userPrincipalName-t adja meg egyező attribútumként, akkor minden felhasználónak rendelkeznie kell userPrincipalName attribútummal. Ha több egyező attribútumot (pl. extensionAttribute1 és mail) ad meg, nem minden felhasználónak kell ugyanazt az egyező attribútumot megadnia. Egy felhasználó lehet egy extensionAttribute1, de nem mail, míg egy másik felhasználó volna e-mail, de nem extensionAttribute1. 
- **A célalkalmazásnak támogatnia kell a szűrést az egyező attribútumon:** Az alkalmazásfejlesztők engedélyezik a szűrést a felhasználói vagy csoport API-n lévő attribútumok egy részhalmaza számára. A katalógusban lévő alkalmazások esetében biztosítjuk, hogy az alapértelmezett attribútumleképezés egy olyan attribútumhoz szolgál, amelyen a célalkalmazás API-ja támogatja a szűrést. A célalkalmazás alapértelmezett egyező attribútumának módosításakor ellenőrizze a harmadik fél API dokumentációját, hogy az attribútum szűrhető legyen.  

## <a name="editing-group-attribute-mappings"></a>Csoport attribútum-hozzárendelések szerkesztése

A kiválasztott számú alkalmazás, például a ServiceNow, a Box és a G Suite támogatja a csoportobjektumok és a felhasználói objektumok kiépítését. A csoportobjektumok tartalmazhatnak csoporttulajdonságokat, például megjelenítendő neveket és e-mail aliasokat, valamint csoporttagokat.

![Példa: ServiceNow kiépített csoport és felhasználói objektumokkal](./media/customize-application-attributes/24.png)

A csoportkiépítés tetszés szerint engedélyezhető vagy letiltható, ha kiválasztja a csoportleképezéseket a **Leképezések**csoportban, és az **Attribútumleképezés** képernyőn **az Engedélyezve van** a kívánt beállításhoz.

A csoportobjektumok részeként kiosztott attribútumok ugyanúgy testreszabhatók, mint a korábban ismertetett Felhasználói objektumok. 

> [!TIP]
> A csoportobjektumok (tulajdonságok és tagok) kiépítése a csoportok alkalmazáshoz [való hozzárendelésének](../manage-apps/assign-user-or-group-access-portal.md) eltérő fogalma. Lehetőség van egy csoport hozzárendelésére egy alkalmazáshoz, de csak a csoportban lévő felhasználói objektumok kiépítése. A teljes csoportobjektumok kiépítése nem szükséges csoportokat használni a hozzárendelésekben.

## <a name="editing-the-list-of-supported-attributes"></a>A támogatott attribútumok listájának szerkesztése

Egy adott alkalmazás támogatott felhasználói attribútumok előre konfigurálva vannak. A legtöbb alkalmazás felhasználói felügyeleti API-k nem támogatják a séma felderítése. Így az Azure AD-kiépítési szolgáltatás nem képes dinamikusan létrehozni a támogatott attribútumok listáját az alkalmazás hívásai.

Egyes alkalmazások azonban egyéni attribútumokat támogatnak, és az Azure AD-kiépítési szolgáltatás képes olvasni és írni az egyéni attribútumok. Ha meg szeretné adni a definícióikat az Azure Portalon, jelölje be a **Speciális beállítások megjelenítése** jelölőnégyzetet az **Attribútumleképezés** képernyő alján, majd válassza az **attribútumlista szerkesztése lehetőséget.**

Az attribútumlista testreszabását támogató alkalmazások és rendszerek a következők:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory[(a Microsoft Graph REST API 1.0-s verzióbeli hivatkozási](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) és egyéni címtárkiterjesztések támogatottak)
- Az [SCIM 2.0-t](https://tools.ietf.org/html/rfc7643)támogató alkalmazásokat, ahol az [alapvető sémában](https://tools.ietf.org/html/rfc7643) definiált attribútumokat hozzá kell adni

> [!NOTE]
> A támogatott attribútumok listájának szerkesztése csak azoknak a rendszergazdáknak ajánlott, akik testre szabták az alkalmazásaik és rendszereik sémáját, és első kézből ismerik az egyéni attribútumok definiálásának módját. Ez néha megköveteli az alkalmazás vagy rendszer által biztosított API-k és fejlesztői eszközök ismeretének megismerését.

A támogatott attribútumok listájának szerkesztésekor a következő tulajdonságok állnak rendelkezésre:

- **Név** – az attribútum rendszerneve a célobjektum sémájában meghatározottak szerint.
- **Típus** – Az attribútum által tárolt adatok típusa a célobjektum sémájában meghatározottak szerint, amely a következő típusok egyike lehet:
  - *Bináris* - Az attribútum bináris adatokat tartalmaz.
  - *Logikai –* Az attribútum igaz vagy hamis értéket tartalmaz.
  - *DateTime* - Az attribútum dátumkarakterláncot tartalmaz.
  - *Egész* - Attribútum egész számmal rendelkezik.
  - *Hivatkozás* – Az attribútum olyan azonosítót tartalmaz, amely a célalkalmazás egy másik táblájában tárolt értékre hivatkozik.
  - *String* - Az attribútum szöveges karakterláncot tartalmaz.
- **Elsődleges kulcs?** - Azt jelzi, hogy az attribútum elsődleges kulcsmezőként van-e definiálva a célobjektum sémájában.
- **Szükséges?** - Azt jelzi, hogy az attribútumot fel kell-e tölteni a célalkalmazásban vagy a rendszerben.
- **Többértékű?** - Azt jelzi, hogy az attribútum több értéket is támogat-e.
- **Pontos ügy?** - Azt jelzi, hogy az attribútumértékek kiértékelése kis- és nagybetűk et is figyelembe vesz-e.
- **API-kifejezés** – ne használja, kivéve, ha erre egy adott kiépítési összekötő (például A Workday) dokumentációja erre utasítja.
- **Hivatkozott objektumattribútum** – Ha referenciatípusú attribútumról van szó, akkor ez a menü lehetővé teszi, hogy kiválassza a táblát és az attribútumot tartalmazó célalkalmazásban az attribútumhoz tartozó értéket tartalmazó attribútumot. Ha például van egy "Részleg" nevű attribútuma, amelynek tárolt értéke egy különálló "Részlegek" táblában lévő objektumra hivatkozik, válassza a "Departments.Name" lehetőséget. Egy adott alkalmazás hoz támogatott referenciatáblák és elsődleges azonosítómezők előre konfigurálva vannak, és jelenleg nem szerkeszthetők az Azure Portalhasználatával, de szerkeszthetők a [Microsoft Graph API használatával.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Egyéni bővítményattribútum kiépítése egy SCIM-kompatibilis alkalmazáshoz
Az SCIM RFC egy alapvető felhasználói és csoportsémát határoz meg, miközben lehetővé teszi a séma bővítményeit is, hogy megfeleljenek az alkalmazás igényeinek. Egyéni attribútum hozzáadása SCIM-alkalmazáshoz:
   1. Jelentkezzen be az [Azure Active Directory portálra,](https://aad.portal.azure.com)válassza a **Vállalati alkalmazások**lehetőséget, válassza ki az alkalmazást, majd a **Kiépítés lehetőséget.**
   2. A **Leképezések**csoportban jelölje ki azt az objektumot (felhasználót vagy csoportot), amelyhez egyéni attribútumot szeretne hozzáadni.
   3. A lap alján válassza a **Speciális beállítások megjelenítése**lehetőséget.
   4. Válassza **az AppName attribútumlistájának szerkesztése lehetőséget.**
   5. Az attribútumlista alján adja meg az egyéni attribútumadatait a megadott mezőkben. Ezután válassza **az Attribútum hozzáadása lehetőséget.**

Az SCIM-alkalmazások esetében az attribútum nevének az alábbi példában látható mintát kell követnie. A "CustomExtensionName" és a "CustomAttribute" testreszabható az alkalmazás követelményei szerint, például:  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:Felhasználó:CustomAttribute 
 * urn:ietf:params:scim:séma:kiterjesztés:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:érték

Ezek az utasítások csak az SCIM-kompatibilis alkalmazásokra vonatkoznak. Az olyan alkalmazások, mint a ServiceNow és a Salesforce nincsenek integrálva az Azure AD-vel az SCIM használatával, ezért nem igényelnek erre a konkrét névtérre egy egyéni attribútum hozzáadásakor.

Az egyéni attribútumok nem lehetnek hivatkozási vagy többértékű attribútumok. Az egyéni többértékű bővítményattribútumok jelenleg csak a katalógusban lévő alkalmazások esetében támogatottak.  
 
**Példa egy bővítményattribútummal rendelkező felhasználó ábrázolására:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Szerepkör kiépítése egy SCIM-alkalmazásba
Az alábbi lépésekkel szerepköröket helyezhet el egy felhasználó számára az alkalmazásba. Vegye figyelembe, hogy az alábbi leírás az egyéni SCIM-alkalmazásokra vonatkozik. A katalógus alkalmazások, például a Salesforce és serviceNow, használja az előre definiált szerepkör-leképezések. Az alábbi listajelek ismertetik, hogyan lehet átalakítani az AppRoleAssignments attribútumot az alkalmazás által elvárt formátumhoz.

- Egy appRoleAssignment az Azure AD egy szerepkör az alkalmazásban megköveteli, hogy az attribútum átalakítása egy [kifejezés](../app-provisioning/functions-for-customizing-application-data.md)használatával. Az appRoleAssignment attribútum **nem képezhető le közvetlenül** egy szerepkör attribútumhoz anélkül, hogy kifejezés használatával elemezze a szerepkör részleteit. 

- **SingleAppRoleAssignment** 
  - **Mikor kell használni:** A SingleAppRoleAssignment kifejezés sel egyetlen szerepkört létesíteni egy felhasználó számára, és adja meg az elsődleges szerepkört. 
  - **Konfigurálás:** A fenti lépések segítségével keresse meg az attribútumleképezések lapot, és a SingleAppRoleAssignment kifejezés használatával rendelje hozzá a roles attribútumot. Három szerepkör-attribútum közül választhat: (szerepkörök[elsődleges eq "Igaz"].megjelenítés, szerepkörök[elsődleges eq "Igaz].típus és szerepkörök[elsődleges eq "True"].érték). Dönthet úgy, hogy a leképezések bármelyikét vagy mindegyikét felszeretné vonni. Ha egynél több leképezést szeretne felvenni, csak adjon hozzá egy új leképezést, és vegye fel célattribútumként.  
  
  ![SingleAppRoleAssignment hozzáadása](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Megfontolandó dolgok**
    - Győződjön meg arról, hogy több szerepkör nincs hozzárendelve egy felhasználóhoz. Nem tudjuk garantálni, hogy melyik szerepet kell kiépíteni.
    
  - **Példa kimenetre** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsKomplex** 
  - **Mikor kell használni:** Az AppRoleAssignmentsComplex kifejezés sel több szerepkört létesítsen egy felhasználó számára. 
  - **Konfigurálás:** A támogatott attribútumok listájának a fent leírt módon való szerkesztése új attribútummal a szerepkörökhöz: 
  
    ![Szerepkörök hozzáadása](./media/customize-application-attributes/add-roles.png)<br>

    Ezután az AppRoleAssignmentsComplex kifejezéssel leképezze az egyéni szerepkör attribútumot az alábbi képen látható módon:

    ![AppRoleAssignmentsKomplex hozzáadása](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Megfontolandó dolgok**
    - Minden szerepkör elsődleges = hamis ként lesz kiépítve.
    - A POST a szerepkör típusát tartalmazza. A PATCH kérelem nem tartalmaz típust. Dolgozunk a postés patch kérelmekben való elküldésen.
    
  - **Példa kimenetre** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Többértékű attribútum kiépítése
Bizonyos attribútumok, például a telefonszámok és az e-mailek többértékű attribútumok, ahol különböző típusú telefonszámokat vagy e-maileket kell megadnia. Használja az alábbi kifejezést a többértékű attribútumokhoz. Lehetővé teszi, hogy adja meg az attribútum típusát, és leképezze, amely a megfelelő Azure AD felhasználói attribútum az értékhez. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].érték

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Az alapértelmezett attribútumok és attribútum-hozzárendelések visszaállítása

Ha újra kell kezdenie, és vissza kell állítania a meglévő hozzárendeléseket az alapértelmezett állapotukra, jelölje be az **Alapértelmezett hozzárendelések visszaállítása jelölőnégyzetet,** és mentse a konfigurációt. Ezzel úgy állítja be az összes leképezési és hatókör-szűrőket, mintha az alkalmazás csak az alkalmazáskatalógusból lett volna hozzáadva az Azure AD-bérlőhöz.

Ha ezt a beállítást választja, a kiépítési szolgáltatás futása közben az összes felhasználó újraszinkronizálása hatékonyan kikényszeríti.

> [!IMPORTANT]
> Azt javasoljuk, hogy **a kiépítés állapota** legyen **beállítva ki,** mielőtt ezt a beállítást.

## <a name="what-you-should-know"></a>Alapismeretek

- A Microsoft Azure AD a szinkronizálási folyamat hatékony megvalósítását biztosítja. Inicializált környezetben csak a frissítést igénylő objektumok kerülnek feldolgozásra a szinkronizálási ciklus során.
- Az attribútumleképezések frissítése hatással van a szinkronizálási ciklus teljesítményére. Az attribútumleképezési konfiguráció frissítéséhez az összes felügyelt objektumot újra kell értékelni.
- Ajánlott eljárás: az attribútum-hozzárendelések egymást követő módosításainak száma minimális szinten tartása.
- Az alkalmazáshoz kiépítendő fényképattribútum hozzáadása ma nem támogatott, mivel nem adhatja meg a fénykép szinkronizálásának formátumát. A funkciót a [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- Az IsSoftDeleted attribútum gyakran része egy alkalmazás alapértelmezett hozzárendeléseinek. IsSoftdeleted lehet igaz a négy forgatókönyv (a felhasználó hatókörön kívül miatt nincs hozzárendelve az alkalmazásból, a felhasználó hatókörön kívül van, mert nem felel meg a hatókörszűrő, a felhasználó már helyreállíthatóan törölt az Azure AD, vagy a tulajdonság AccountEnabled van beállítva, hogy hamis a felhasználó). 
- Az Azure AD-kiépítési szolgáltatás nem támogatja a null értékek kiépítését

## <a name="next-steps"></a>További lépések

- [Felhasználói kiépítés/sas-alkalmazásokba való kiépítés automatizálása](user-provisioning.md)
- [Kifejezések írása attribútumleképezéshez](../app-provisioning/functions-for-customizing-application-data.md)
- [Hatókörszűrők a felhasználók kiépítéshez](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
- [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
