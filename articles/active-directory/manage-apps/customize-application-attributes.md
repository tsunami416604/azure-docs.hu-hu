---
title: Az Azure AD-attribútumok megfeleltetésének testreszabása | Microsoft Docs
description: Ismerje meg, hogy az SaaS-alkalmazásokhoz milyen attribútum-hozzárendelések Azure Active Directory az üzleti igények kielégítése érdekében.
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
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240992"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>A felhasználó kiépítési attribútumának testreszabása – SaaS-alkalmazások leképezése Azure Active Directory

A Microsoft Azure AD támogatja a külső SaaS-alkalmazások, például a Salesforce, a G Suite és más felhasználók számára történő üzembe helyezést. Ha engedélyezi a felhasználók kiosztását egy külső SaaS-alkalmazás számára, a Azure Portal attribútum-hozzárendelések segítségével vezérli az attribútum értékeit.

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között van egy előre konfigurált attribútum és attribútum-hozzárendelés. Egyes alkalmazások más típusú objektumokat is kezelhetnek a felhasználók, például a csoportok mellett.

Az alapértelmezett attribútum-hozzárendelések testreszabhatók az üzleti igényeknek megfelelően. Így módosíthatja vagy törölheti a meglévő attribútum-hozzárendeléseket, illetve létrehozhat új attribútum-hozzárendeléseket is.

## <a name="editing-user-attribute-mappings"></a>Felhasználói attribútum szerkesztése – leképezések

Kövesse az alábbi lépéseket a felhasználók kiosztásának **leképezési** funkciójának eléréséhez:

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com).
1. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
1. Válassza ki bármelyik alkalmazást az alkalmazás-felügyeleti panel betöltéséhez, ahol megtekintheti a jelentéseket, és kezelheti az alkalmazás beállításait.
1. A **kiépítés** lehetőség kiválasztásával kezelheti a kiválasztott alkalmazás felhasználói fiókjának kiépítési beállításait.
1. A **leképezések** kibontásával megtekintheti és szerkesztheti a felhasználói attribútumokat, amelyek az Azure ad és a célalkalmazás között áramlanak. Ha a célalkalmazás támogatja azt, ez a szakasz lehetővé teszi a csoportok és felhasználói fiókok üzembe helyezésének igény szerinti konfigurálását.

   ![Leképezések használata felhasználói attribútumok megtekintéséhez és szerkesztéséhez](./media/customize-application-attributes/21.png)

1. Válassza ki a **leképezések** konfigurációját a kapcsolódó **attribútum-leképezési** képernyő megnyitásához. Az SaaS-alkalmazások megfelelő működéséhez bizonyos attribútum-hozzárendelések szükségesek. A kötelező attribútumok esetében a **törlési** funkció nem érhető el.

   ![Attribútum-hozzárendelés használata az alkalmazások attribútumainak konfigurálásához](./media/customize-application-attributes/22.png)

   Ebben a képernyőfelvételben láthatja, hogy a Salesforce található felügyelt objektum **username** attribútuma a csatolt Azure Active Directory objektum **userPrincipalName** értékével van feltöltve.

1. Válasszon ki egy meglévő **attribútum-hozzárendelést** az **attribútum szerkesztése** képernyő megnyitásához. Itt szerkesztheti az Azure AD és a célalkalmazás közötti adatforgalom felhasználói attribútumait.

   ![Felhasználói attribútumok szerkesztése a szerkesztési attribútum használatával](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Az attribútum-leképezési típusok ismertetése

Az attribútum-hozzárendelésekkel szabályozhatja, hogy az attribútumok hogyan legyenek feltöltve egy külső gyártótól származó SaaS-alkalmazásban.
Négy különböző leképezési típus támogatott:

- **Direct (közvetlen** ) – a TARGET attribútum az Azure ad-ben csatolt objektum attribútumának értékével van feltöltve.
- **Konstans** – a TARGET attribútum egy megadott karakterlánccal van feltöltve.
- **Kifejezés** – a cél attribútum a parancsfájl-szerű kifejezés eredménye alapján van feltöltve.
  További információ: [kifejezések írása az attribútumhoz – leképezések Azure Active Directory](functions-for-customizing-application-data.md).
- **Nincs** – a cél attribútum változatlan marad. Ha azonban a cél attribútum még mindig üres, akkor a rendszer a megadott alapértelmezett értékkel tölti fel.

A négy alaptípussal együtt az egyéni attribútum-hozzárendelések az opcionális **alapértelmezett** érték-hozzárendelés fogalmát támogatják. Az alapértelmezett érték-hozzárendelés biztosítja, hogy a TARGET attribútum értéke akkor legyen feltöltve, ha nincs érték az Azure AD-ben vagy a TARGET objektumon. A leggyakoribb beállítás az, ha üresen hagyja ezt a beállítást.

### <a name="understanding-attribute-mapping-properties"></a>Attribútumok – megfeleltetési tulajdonságok ismertetése

Az előző szakaszban már be lett vezetve az attribútum-leképezési típus tulajdonságra.
A tulajdonsággal együtt az attribútum-hozzárendelések a következő attribútumokat is támogatják:

- **Forrásoldali attribútum** – a felhasználói attribútum a forrásoldali rendszerből (példa: Azure Active Directory).
- **Target attribútum** – a felhasználói attribútum a célként megadott rendszeren (például: ServiceNow).
- **Objektumok egyeztetése ezzel az attribútummal** – azt határozza meg, hogy ez a leképezés használható-e a felhasználók egyedi azonosítására a forrás-és a célként megadott rendszerek között. Általában az Azure AD userPrincipalName vagy mail attribútumára van beállítva, amely általában egy célalkalmazás username mezőjére van leképezve.
- **Megfeleltetési prioritás** – a rendszer több egyező attribútumot is beállíthat. Ha több is van, azok kiértékelése a mező által meghatározott sorrendben történik. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.
- **A leképezés alkalmazása**
  - **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is.
  - **Csak a létrehozás során** – alkalmazza ezt a leképezést csak a felhasználói létrehozási műveletekre.

## <a name="editing-group-attribute-mappings"></a>Csoport attribútumának szerkesztése – leképezések

A kiválasztott számú alkalmazás, például a ServiceNow, a Box és a G Suite lehetővé teszi a csoport objektumainak és felhasználói objektumainak kiépítését. A csoport objektumai tartalmazhatnak olyan csoport-tulajdonságokat, mint a megjelenítendő nevek és az e-mail-aliasok, a csoporttagokkal együtt.

![Példa a kiépített csoporttal és felhasználói objektumokkal rendelkező ServiceNow](./media/customize-application-attributes/24.png)

A csoportok kiosztása opcionálisan engedélyezhető vagy letiltható úgy, hogy kijelöli a **hozzárendelések**csoport leképezése elemét, és **engedélyezi** az **attribútum-leképezési** képernyőn a kívánt beállítást.

A csoport objektumainak részeként kiépített attribútumok ugyanúgy testreszabhatók, mint a korábban leírt felhasználói objektumok. 

> [!TIP]
> A csoport objektumainak (tulajdonságai és tagjai) [kiosztása a csoportok](assign-user-or-group-access-portal.md) alkalmazáshoz való hozzárendelésének különböző fogalma. Egy csoportot hozzárendelhet egy alkalmazáshoz, de csak a csoportban lévő felhasználói objektumokat kell kiépíteni. A teljes csoport objektumainak kiosztása nem szükséges a hozzárendelésekhez tartozó csoportok használatához.

## <a name="editing-the-list-of-supported-attributes"></a>A támogatott attribútumok listájának szerkesztése

Az adott alkalmazás által támogatott felhasználói attribútumok előre konfigurálva vannak. A legtöbb alkalmazás felhasználói felügyeleti API-jai nem támogatják a séma-felderítést. Így az Azure AD-kiépítési szolgáltatás nem képes dinamikusan létrehozni a támogatott attribútumok listáját az alkalmazás hívásával.

Egyes alkalmazások azonban támogatják az egyéni attribútumokat, az Azure AD-kiépítési szolgáltatás pedig képes az egyéni attribútumok olvasására és írására. Ha meg szeretné adni a definíciókat a Azure Portalban, jelölje be a **Speciális beállítások megjelenítése** jelölőnégyzetet az **attribútum-leképezési** képernyő alján, majd válassza az **attribútumok szerkesztése** elemet az alkalmazáshoz.

Az attribútumok listájának testreszabását támogató alkalmazások és rendszerek a következők:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (az[Azure AD Graph API alapértelmezett attribútumai](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) és az egyéni címtárszolgáltatás-bővítmények támogatottak)
- Az [SCIM 2,0](https://tools.ietf.org/html/rfc7643)-et támogató alkalmazások, ahol az [alapsémában](https://tools.ietf.org/html/rfc7643) definiált attribútumokat fel kell venni

> [!NOTE]
> A támogatott attribútumok listájának szerkesztése csak olyan rendszergazdák számára ajánlott, akik testre szabták az alkalmazásaikat és rendszereit, és az egyéni attribútumok definiálásának első kézből való ismerete. Ez esetenként az alkalmazás vagy a rendszer által biztosított API-k és fejlesztői eszközök ismeretét igényli.

A támogatott attribútumok listájának szerkesztésekor a következő tulajdonságokat kell megadnia:

- **Név** – a cél objektum sémájában definiált attribútum rendszerneve.
- **Type (típus** ) – az attribútum által a célobjektum sémájában definiált adattípus, amely a következő típusok egyike lehet:
  - *Bináris* – az attribútum bináris adatértékeket tartalmaz.
  - *Logikai* – az attribútum igaz vagy hamis értéket tartalmaz.
  - *Dátum és idő* – az attribútum egy Date karakterláncot tartalmaz.
  - Az *Integer* -attribútum egész számot tartalmaz.
  - A *Reference* -ATTRIBÚTUM olyan azonosítót tartalmaz, amely a célalkalmazás egy másik táblájában tárolt értékre hivatkozik.
  - *Karakterlánc* – az attribútum szöveges karakterláncot tartalmaz.
- **Elsődleges kulcs?** – Azt határozza meg, hogy az attribútum a célobjektum sémájában elsődleges kulcs mezőként van-e definiálva.
- **Szükséges?** – Azt határozza meg, hogy az attribútumot kötelező-e kitölteni a célalkalmazás vagy a rendszer számára.
- **Több érték?** – Azt határozza meg, hogy az attribútum több értéket is támogat-e.
- **Pontos eset?** – Azt határozza meg, hogy az attribútumok értékei kis-és nagybetűk megkülönböztetésével legyenek kiértékelve.
- **API-kifejezés** – ne használja, kivéve, ha erre a célra egy adott kiépítési összekötő (például a munkanap) dokumentációja kéri.
- **Hivatkozott Object attribútum** – ha ez egy hivatkozástípus attribútum, akkor ezzel a menüvel kiválaszthatja a célalkalmazás azon tábláját és attribútumát, amely az attribútumhoz társított értéket tartalmazza. Ha például egy "részleg" nevű attribútummal rendelkezik, amelynek tárolt értéke egy különálló "részleg" táblában található objektumra hivatkozik, akkor válassza a "Departments.Name" elemet. Az adott alkalmazáshoz támogatott hivatkozási táblák és elsődleges azonosító mezők előre vannak konfigurálva, és jelenleg nem szerkeszthetők a Azure Portal használatával, de a [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)használatával szerkeszthetők.

Új attribútum hozzáadásához görgessen a támogatott attribútumok listájának végére, és töltse ki a fenti mezőket a megadott bemenetek használatával, majd válassza az **attribútum hozzáadása**lehetőséget. Attribútumok hozzáadásának befejeződése után válassza a **Mentés** lehetőséget. Ezután újra kell töltenie a **kiépítés** lapot, hogy az új attribútumok elérhetővé váljanak az attribútum-leképezési szerkesztőben.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Az alapértelmezett attribútumok és attribútumok leképezésének visszaállítása

Ha újra kell indítania a meglévő leképezéseket, és vissza kell állítania az alapértelmezett állapotukat, akkor jelölje be az **alapértelmezett leképezések visszaállítása** jelölőnégyzetet, és mentse a konfigurációt. Ez úgy állítja be az összes leképezést, mintha az alkalmazás már hozzá lett adva az Azure AD-bérlőhöz az alkalmazás-katalógusból.

Ha ezt a beállítást választja, a kiépítési szolgáltatás futása közben minden felhasználó újraszinkronizálását fogja kényszeríteni.

> [!IMPORTANT]
> Javasoljuk, hogy a beállítás meghívása előtt állítsa **ki** a **kiépítési állapotot** .

## <a name="what-you-should-know"></a>Alapismeretek

- Microsoft Azure AD a szinkronizálási folyamat hatékony megvalósítását teszi lehetővé. Egy inicializált környezetben csak a frissítéseket igénylő objektumok lesznek feldolgozva szinkronizálási ciklusban.
- Az attribútumok frissítése-a leképezések hatással vannak a szinkronizálási ciklus teljesítményére. Az attribútum-hozzárendelési konfiguráció frissítéséhez az összes felügyelt objektum újraértékelése szükséges.
- Az ajánlott eljárás az, ha a minimálisan megőrzi az attribútum-hozzárendelések egymást követő módosításait.
- Ha olyan fénykép-attribútumot ad hozzá, amelyet egy alkalmazáshoz szeretne kiépíteni, ma nem lehet megadnia a fénykép szinkronizálásának formátumát. A funkciót [felhasználói hangon](https://feedback.azure.com/forums/169401-azure-active-directory) kérheti le
- A IsSoftDeleted attribútum gyakran része az alkalmazás alapértelmezett leképezésének. A IsSoftdeleted a négy forgatókönyv egyikében igaz lehet (a felhasználó hatókörén kívül esik az alkalmazásból való kivonás miatt, a felhasználó hatókörén kívül esik, mert nem felel meg egy hatóköri szűrőnek, a felhasználót nem sikerült törölni az Azure AD-ben, vagy a AccountEnabled tulajdonság hamis értékre van állítva.  a felhasználón). 
- Az Azure ad-kiépítési szolgáltatás nem támogatja a null értékek kiépítés

## <a name="next-steps"></a>Következő lépések

- [A felhasználók üzembe helyezésének és megszüntetésének automatizálása az SaaS-alkalmazásokban](user-provisioning.md)
- [Kifejezések írása attribútum-leképezésekhez](functions-for-customizing-application-data.md)
- [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
- [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
