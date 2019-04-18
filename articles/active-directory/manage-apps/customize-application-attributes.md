---
title: Azure AD-attribútum-leképezések testreszabása |} A Microsoft Docs
description: Ismerje meg, milyen attribútumleképezések SaaS-alkalmazásokhoz az Azure Active Directoryban, hogyan módosíthatja azokat az üzleti igények kielégítésében.
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
ms.date: 04/03/2019
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a2965fecd3aca17d6c4df7e49ad466377de9762
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267208"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Az SaaS-alkalmazásokhoz az Azure Active Directory-attribútumleképezések testreszabása Felhasználókiépítés
A Microsoft Azure AD külső SaaS-alkalmazások például a Salesforce, a Google Apps és a másokkal való támogatást nyújt. Ha engedélyezi a felhasználók átadásának egy külső SaaS-alkalmazáshoz, az Azure Portalon szabályozza az attribútumértékek attribútum-leképezések használatával.

Nincs attribútumokat és az Azure AD felhasználói és minden egyes SaaS-alkalmazás felhasználói objektumok között attribútumleképezések előre konfigurált készletével. Néhány alkalmazás más típusú objektumok és a felhasználók, például a csoportok kezelése.

Az alapértelmezett attribútumleképezések igényeinek megfelelően testre szabhatja. Tehát módosítsa vagy törölje a meglévő attribútumleképezések, vagy hozzon létre új attribútum-leképezések.
 
## <a name="editing-user-attribute-mappings"></a>Felhasználói attribútum-leképezések szerkesztése

Kövesse az alábbi lépéseket eléréséhez a **leképezések** felhasználókiépítés funkcióját:

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com).

1. Válassza ki **vállalati alkalmazások** a bal oldali ablaktáblán. Az összes konfigurált alkalmazások listája látható, beleértve az alkalmazásokat, amelyek a katalógusból lettek hozzáadva.

1. Válassza ki az alkalmazás felügyeleti ablaktábla, ahol jelentések megtekintése és kezelése az alkalmazásbeállítások betölteni bármely alkalmazást.

1. Válassza ki **kiépítési** kezelheti a felhasználói fiók kiépítése a kiválasztott alkalmazás beállításait.

1. Bontsa ki a **leképezések** megtekintéséhez és szerkesztéséhez a felhasználói attribútumok, amelyek az Azure AD között és a cél alkalmazás. Ha a cél alkalmazás ezt támogatja, ez a szakasz igény szerint a csoportok és felhasználói fiókok kiépítése konfigurálását teszi lehetővé.

   ![Salesforce](./media/customize-application-attributes/21.png) 

1. Válassza ki a **leképezések** konfigurációját, és nyissa meg a kapcsolódó **attribútumleképzés** képernyő. Néhány attribútumleképezések egy SaaS-alkalmazáshoz megfelelő működéséhez szükségesek. A szükséges attribútumok a **törlése** funkció nem érhető el.

   ![Salesforce](./media/customize-application-attributes/22.png)

   Ezen a képernyőfelvételen láthatja, hogy a **felhasználónév** attribútum a Salesforce-ban felügyelt objektum megjelenik a **userPrincipalName** a társított Azure Active Directory objektum értékét.

1. Válasszon egy meglévő **attribútumleképzés** megnyitásához a **attribútum szerkesztése** képernyő. Itt szerkesztheti a felhasználói attribútumok, amelyek az Azure AD között és a cél alkalmazás.

   ![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Attribútum-hozzárendelési típusainak ismertetése
Az attribútum-leképezések meghatározhatja, hogyan fel van töltve attribútumai külső SaaS-alkalmazásban. Támogatott négy különböző leképezés típusa van:

* **Közvetlen** – a target attribútum a csatolt objektum egy attribútum értéke megjelenik az Azure ad-ben.
* **Állandó** – a target attribútum feltöltése egy meghatározott karakterlánccal megadott.
* **Kifejezés** -target attribútum egy parancsfájl-szerű kifejezés eredménye alapján van feltöltve. 
  További információkért lásd: [írása a kifejezéseket az Azure Active Directoryban attribútumleképezések](functions-for-customizing-application-data.md).
* **Nincs** -marad a célattribútum változatlan. Azonban ha target attribútum minden eddiginél üres, akkor megjelenik az Ön által megadott alapértelmezett értéket.

Egyéni attribútum-leképezések ezek négy alapvető típusok, valamint támogatja a fogalmat egy nem kötelező **alapértelmezett** érték-hozzárendelés. Az alapértelmezett érték-hozzárendelés biztosítja, hogy target attribútuma megadni az értéket, ha nem áll egy értéket az Azure ad-ben vagy a célobjektum. A leggyakrabban használt beállítások mellett akkor hagyja üresen a mezőt.


### <a name="understanding-attribute-mapping-properties"></a>Attribútumleképzés tulajdonságok ismertetése

Az előző szakaszban már mutatta be attribútumleképezés tulajdonság.
Ezt a tulajdonságot, valamint attribútum-leképezések is támogatja a következő attribútumokat:

- **Adatforrás-attribútum** – a felhasználói attribútum a forrásrendszerből (Példa: Azure Active Directory).
- **Célattribútum** – a felhasználói attribútum a célrendszeren (Példa: A ServiceNow).
- **Ezzel az attribútummal objektumok** – Ez a leképezés használandó egyedileg azonosíthatja azokat a felhasználókat, a forrás és cél közötti-e. Ez általában be van állítva a userPrincipalName vagy mail attribútum az Azure ad-ben, amely általában egy felhasználónév mezője célalkalmazás van hozzárendelve.
- **Megfeleltetési prioritás** – több egyező attribútumok beállítása. Ha több, hogy értékeli ki őket ez a mező által meghatározott sorrendben. Amint egyezést talál, további megfelelő attribútumok értékeli ki.
- **Leképezés alkalmazása**
    - **Mindig** – ezt a hozzárendelést alkalmazni, mind a felhasználó létrehozása és a frissítési műveletek.
    - **Csak a létrehozásakor** -leképezés alkalmazása csak a felhasználó-létrehozási műveletek.


## <a name="editing-group-attribute-mappings"></a>Csoport attribútum-leképezések szerkesztése

Alkalmazások, például a ServiceNow, a Box és a Google Apps, a kiválasztott számú támogatja az objektumok és a felhasználói objektumok létrehozására. Objektumok is tartalmaz a csoport tulajdonságai, például a megjelenített nevek és e-mail-aliasokat, és a csoport tagjai.

![ServiceNow](./media/customize-application-attributes/24.png)

Csoportos kiépítését lehet szükség esetén engedélyezhető vagy letiltható az eszközcsoport-leképezés szerinti kiválasztásával **leképezések**, és a beállítás **engedélyezve** a kívánt beállítást, a **attribútumleképezés** képernyő.

Az attribútumok csoportobjektumokhoz részeként üzembe helyezett testre szabható az azonos felhasználói objektumok, korábban leírt módon. 

>[!TIP]
>Egy különálló fogalom a csoportobjektumokhoz (a tulajdonságok és a tagok) üzembe helyezése [csoportok hozzárendelése](assign-user-or-group-access-portal.md) alkalmazáshoz. Egy csoport hozzárendelése egy alkalmazáshoz, de csak kiépítése a felhasználói objektumok, a csoportban lévő lehetőség. Csoportok használata a hozzárendelések nem szükséges a teljes csoportobjektumokhoz kiépítését.


## <a name="editing-the-list-of-supported-attributes"></a>Támogatott attribútumok listáját szerkesztése

A támogatott az adott alkalmazásra vonatkozó felhasználói attribútumokat előre konfigurálva. A legtöbb alkalmazás felhasználói felügyeleti API-k nem támogatják a séma felderítés. Tehát az Azure AD létesítési szolgáltatás nem tud, amellyel dinamikusan generálható meghívni az alkalmazás által támogatott attribútumok listáját. 

Azonban egyes alkalmazások támogatják az egyéni attribútumokat, és az Azure AD létesítési szolgáltatás olvashat és írhat az egyéni attribútumokat. Meg kell adnia a definíciójukat az Azure Portalon, válassza ki a **speciális beállítások megjelenítése** alján, a jelölőnégyzet jelölését a **attribútumleképzés** képernyőn, és válassza ki **attribútumlistájánakszerkesztése** alkalmazását.

Alkalmazások és rendszerek, amelyek támogatják az attribútumlista testre szabhatja a következők:

* Salesforce
* ServiceNow
* Munkanap
* Az Azure Active Directory ([Azure AD Graph API alapértelmezett attribútumok](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) és egyéni címtárbővítmények támogatottak)
* Támogató alkalmazások [SCIM 2.0](https://tools.ietf.org/html/rfc7643), ahol az attribútumok meghatározott a [core séma](https://tools.ietf.org/html/rfc7643) hozzá kell adni

>[!NOTE]
>Támogatott attribútumok listáját szerkesztési csak rendszergazdák számára, akik testreszabták a saját alkalmazások és rendszerek sémája és első kézből tudomásuk egyéni attribútumaik definiálva hogyan ajánlott. Ez néha az egy alkalmazás vagy rendszer által biztosított API-k és a fejlesztői eszközök ismeretét igényli. 

Támogatott attribútumok listáját szerkesztésekor a következő tulajdonságok áll rendelkezésre:

* **Név** – az attribútum a célobjektum sémában meghatározott rendszer nevét. 
* **Típus** -adatok az attribútum tárolja, ahogyan az a célobjektum séma, amely a következők egyike lehet:
   * *Bináris* -attribútum bináris adatokat tartalmaz.
   * *Logikai* -attribútum igaz vagy hamis értéket tartalmaz.
   * *Dátum és idő* -attribútum tartalmazza a dátum karakterláncot.
   * *Egész szám* -attribútumot tartalmaz egy egész számot.
   * *Hivatkozás* -attribútum által hivatkozott egy értéket egy másik táblájában a célalkalmazás Azonosítót tartalmaz.
   * *Karakterlánc* -szöveges karakterláncot attribútumot tartalmaz. 
* **Elsődleges kulcs?** -Az attribútum e a célobjektum sémában található elsődleges kulcs mezőként van definiálva.
* **Kötelező?** -Az attribútum e a cél alkalmazás- vagy kitöltve szükség.
* **Többértékű?** -Az attribútum e több értékeket támogatja.
* **Megkülönböztetése?** -Az attribútumok értékek e a kis-és nagybetűket módon értékeli ki.
* **API-kifejezés** – ne használja, kivéve, ha kifejezetten a dokumentáció (például a Workday) egy adott kiépítési összekötőre kéri.
* **Hivatkozott objektumattribútum** – Ha a hivatkozási attribútum, amelyek ezután ebből a menüből válassza ki a táblát és az attribútum a célalkalmazásban, amely tartalmazza az attribútum társított értéket. Például ha egy attribútumot, amelynek tárolt érték egy külön táblázatban "Részleg" objektumra hivatkozik, az "részleg" nevű, kiválasztott "Departments.Name". A referencia-táblák és a egy adott alkalmazás támogatott elsődleges azonosító mezők előre konfigurált, és jelenleg nem szerkeszthető, az Azure portal használatával, de használatával szerkesztheti a [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Egy új attribútum hozzáadásához, görgessen a támogatott attribútumok listáját a végén, adja meg a mezőkben a megadott bemeneti felett, és válassza **attribútum hozzáadása**. Válassza ki **mentése** Ha hozzáadta az attribútumokat. Szeretne töltse be újra a **kiépítési** válnak elérhetővé az attribútum-hozzárendelési szerkesztőt, az új attribútumok fülre.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Visszaállítás az alapértelmezett attribútumok és attribútum-leképezések

Meg kell újrakezdés és a meglévő hozzárendelések biztonsági másolatot az alapértelmezett állapotba alaphelyzetbe állítása, kiválaszthatja a **visszaállítja az alapértelmezett hozzárendeléseket** jelölje be a jelölőnégyzetet, és a konfiguráció mentéséhez. Minden leképezések így állítja be, mint ha az alkalmazás előbbiekben felvett, az Azure AD-bérlő az alkalmazáskatalógusból. 

Ez a beállítás hatékonyan kényszeríti az összes olyan felhasználó újraszinkronizálását a kiépítési szolgáltatás futása közben. 

>[!IMPORTANT]
>Erősen ajánlott, amelyek **előkészítési állapotát** állítható **ki** Ez a beállítás meghívása előtt.


## <a name="what-you-should-know"></a>Alapismeretek

* A Microsoft Azure AD hatékony megvalósítását a szinkronizálási folyamat nyújt. Inicializált környezetben csak a frissítést igénylő objektumok feldolgozása során egy szinkronizálási ciklust. 

* Attribútum-leképezések frissítése egy szinkronizálási ciklust teljesítményére hatással van. Az attribútum-hozzárendelési konfigurációra egy frissítéshez kell újraértékeli az összes felügyelt objektumok. 

* Ajánlott ahhoz, hogy az attribútum-leképezések legalább egymást követő módosítások számát.


## <a name="next-steps"></a>További lépések

* [Felhasználói kiépítés és megszüntetés SaaS-alkalmazások automatizálása](user-provisioning.md)
* [Kifejezések írása attribútum-leképezések](functions-for-customizing-application-data.md)
* [A felhasználók átadásának Hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)


