---
title: Automatizált SaaS-alkalmazások felhasználó általi üzembe helyezése az Azure AD-ben | Microsoft Docs
description: Bevezetés az Azure AD használatával a felhasználói fiókok automatikus kiosztására, kiépítésére és folyamatos frissítésére több külső SaaS-alkalmazás között.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144541"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>A felhasználók kiépítésének automatizálása és az alkalmazásokkal való kiépítés Azure Active Directory

Azure Active Directory (Azure AD) esetében az **alkalmazás üzembe** helyezésének kifejezése arra a felhasználói identitások és szerepkörök automatikus létrehozására utal, amelyekhez[](https://azure.microsoft.com/overview/what-is-saas/)a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés a felhasználói identitások karbantartását és eltávolítását is magában foglalja az állapot vagy a szerepkörök módosításakor. Gyakori forgatókönyvek például az Azure AD-felhasználók üzembe helyezése olyan alkalmazásokban, mint a [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), a [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), a [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)és még sok más.

![Kiépítés – áttekintés diagram](media/user-provisioning/provisioning-overview.png)

Ez a funkció lehetővé teszi a következőket:

- A **kiépítés automatizálása**: automatikusan hozzon létre új fiókokat a megfelelő rendszerekben az új személyekhez, amikor csatlakoznak a csapathoz vagy szervezethez.
- **Kiépítés automatizálása:** Automatikusan inaktiválja a fiókokat a megfelelő rendszerekben, amikor a felhasználók elhagyják a csapatot vagy a szervezetet.
- **Az adatszinkronizálás a rendszerek között:** Győződjön meg arról, hogy az alkalmazásokban és rendszerekben lévő identitások naprakészek maradnak a címtár vagy az emberi erőforrások rendszerének változásai alapján.
- **Csoportok kiépítése:** Csoportok kiépítése az azokat támogató alkalmazásokba.
- **Hozzáférés szabályozása:** Az alkalmazásokban kiépített figyelés és naplózás.
- **Zökkenőmentes üzembe helyezés a barna mezőkben:** A meglévő identitások egyeztetése a rendszerek között, és az egyszerű integráció lehetővé tétele, még akkor is, ha a felhasználók már léteznek a megcélzott rendszerben.
- **Gazdag Testreszabás használata:** Kihasználhatja a testreszabható attribútum-hozzárendeléseket, amelyek meghatározzák, hogy a felhasználói adatok milyen mértékben áramlanak a forrásoldali rendszerből a célként megadott rendszerbe.
- **Riasztások beolvasása kritikus eseményekre:** A kiépítési szolgáltatás riasztásokat biztosít a kritikus fontosságú eseményekhez, és lehetővé teszi Log Analytics integrációt, ahol egyéni riasztásokat adhat meg az üzleti igényeknek megfelelően.

## <a name="benefits-of-automatic-provisioning"></a>Az automatikus kiépítés előnyei

Mivel a modern szervezeteknél használt alkalmazások száma folyamatosan nő, a rendszergazdák a nagy léptékű hozzáférés-kezeléssel rendelkeznek. Az olyan szabványok, mint például a biztonsági kikötések Markup Language (SAML) vagy a Open ID kapcsolódás (OIDC) lehetővé teszik a rendszergazdáknak, hogy gyorsan állítsanak be egyszeri bejelentkezést (SSO), de a hozzáféréshez az is szükséges, hogy a felhasználók beépítsék az alkalmazást. Számos rendszergazda számára a kiépítés azt jelenti, hogy manuálisan hozza létre az összes felhasználói fiókot, vagy a CSV-fájlokat minden héten feltölti, de ezek a folyamatok időigényesek, költségesek és hibákra hajlamosak. Az SAML-alapú (például JIT) megoldásokat a kiépítés automatizálására is elfogadták, de a vállalatoknak olyan megoldásra is szükségük van, amely a felhasználók kiépítését igényli, amikor a szervezet elhagyja a munkahelyet, vagy már nem igényelnek hozzáférést bizonyos alkalmazásokhoz a szerepkör módosítása alapján.

Az automatikus kiépítés használatának gyakori indítékai a következők:

- A kiépítési folyamatok hatékonyságának és pontosságának maximalizálása.
- Az egyedi fejlesztésű üzembe helyezési megoldások és szkriptek üzemeltetésével és karbantartásával kapcsolatos költségek mentése.
- A szervezet védelme azáltal, hogy azonnal eltávolítja a felhasználók identitását a Key SaaS-alkalmazásokból, amikor elhagyja a szervezetet.
- Számos felhasználót egyszerűen importálhat egy adott SaaS-alkalmazásba vagy-rendszerbe.
- Egyetlen szabályzattal meghatározhatja, hogy ki kiépített és ki tud jelentkezni egy alkalmazásba.

Az Azure AD-felhasználók üzembe helyezése segíthet a problémák megoldásában. Ha többet szeretne megtudni arról, hogy az ügyfelek hogyan használják az Azure AD-t, akkor olvassa el a [Asos-esettanulmányt](https://aka.ms/asoscasestudy). Az alábbi videó áttekintést nyújt a felhasználók üzembe helyezéséről az Azure AD-ben:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Milyen alkalmazásokat és rendszereket használhatok az Azure AD automatikus felhasználói üzembe helyezéséhez?

Az Azure AD számos népszerű SaaS-alkalmazás és emberi erőforrásrendszer előre integrált támogatását, valamint általános támogatást nyújt a [SCIM 2,0 standard](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)részét képező alkalmazások számára.

* **Előre integrált alkalmazások (Gallery SaaS-alkalmazások)** . Megtalálhatja az összes olyan alkalmazást, amelyhez az Azure AD támogatja a [felhasználó által kiépített alkalmazás-oktatóanyagokat](../saas-apps/tutorial-list.md)tartalmazó, előre integrált létesítési összekötőt. A katalógusban felsorolt előre integrált alkalmazások általában a SCIM 2,0-alapú felhasználói felügyeleti API-kat használják a kiépítés számára. 

   ![Salesforce embléma](media/user-provisioning/gallery-app-logos.png)

   Ha új alkalmazást szeretne igényelni az üzembe helyezéshez, [kérheti, hogy az alkalmazás integrálva legyen](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)az alkalmazás-galériával. A felhasználó kiépítési kérelméhez az alkalmazásnak SCIM-kompatibilis végponttal kell rendelkeznie. Kérje meg, hogy az alkalmazás gyártója kövesse a SCIM standardot, így gyorsan üzembe helyezhetjük az alkalmazást a platformon.

* **Az SCIM 2,0-et támogató alkalmazások**. Az SCIM 2,0-alapú felhasználói felügyeleti API-kat megvalósító alkalmazások általános összekapcsolásával kapcsolatos információkért lásd: a [scim használata a felhasználók és csoportok automatikus kiépítéséhez Azure Active Directoryról alkalmazásokba](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>Mi az a SCIM?

A kiépítés és a megszüntetés automatizálása érdekében az alkalmazások tulajdonosi felhasználói és csoportos API-kat tesznek elérhetővé. Azonban bárki, aki több alkalmazásban próbálta felügyelni a felhasználókat, azt fogja tudni, hogy minden alkalmazás ugyanazt az egyszerű műveletet próbálja végrehajtani, például a felhasználók létrehozását és frissítését, a felhasználók csoportokhoz való hozzáadását vagy a felhasználók megszüntetését. Az ilyen egyszerű műveletek azonban a különböző végponti elérési utakkal, a felhasználói adatok megadására szolgáló különböző módszerekkel, valamint egy másik, az egyes elemek ábrázolására szolgáló sémával eltérő módon valósíthatók meg.

A problémák megoldása érdekében a SCIM-specifikáció egy általános felhasználói sémát biztosít, amellyel a felhasználók beléphetnek az alkalmazásba, és onnan is megtalálhatják őket. A SCIM a kiépítés de facto szabványa, és az összevonási szabványok, például az SAML vagy az OpenID Connect együttes használata esetén a rendszergazdák teljes körű, a hozzáférés-vezérlésre vonatkozó szabványokon alapuló megoldást biztosítanak.

A felhasználók és csoportok alkalmazásba való kiépítésének és megszüntetésének automatizálására vonatkozó részletes útmutatásért lásd: a scim-SCIM való [kiépítés a Azure Active Directory](use-scim-to-provision-users-and-groups.md)használatával.

## <a name="how-does-automatic-provisioning-work"></a>Hogyan működik az automatikus kiépítés?

Az **Azure ad-kiépítési szolgáltatás** a felhasználókat az SaaS-alkalmazásokhoz és más rendszerekhez is kiépíti az egyes alkalmazások gyártói által biztosított felhasználói felügyeleti API-végpontokhoz való csatlakozással. Ezek a felhasználói felügyeleti API-végpontok lehetővé teszik az Azure AD számára a felhasználók programozott módon történő létrehozását, frissítését és eltávolítását. A kiválasztott alkalmazások esetében a kiépítési szolgáltatás további, identitással kapcsolatos objektumokat, például csoportokat és szerepköröket is létrehozhat, frissíthet és eltávolíthat.

![Azure AD-létesítési szolgáltatás](./media/user-provisioning/provisioning0.PNG)
*1. ábra: az Azure ad-kiépítési szolgáltatás*

![kimenő felhasználó kiépítési munkafolyamata](./media/user-provisioning/provisioning1.PNG)
*2. ábra: "kimenő" felhasználó kiépítési munkafolyamata az Azure ad-ből a népszerű SaaS-alkalmazásokhoz*

![bejövő felhasználó kiépítési munkafolyamata](./media/user-provisioning/provisioning2.PNG)
*3. ábra: "bejövő" felhasználó kiépítési munkafolyamata a népszerű humántőke-felügyeleti (HCM) alkalmazásokból a Azure Active Directory és a Windows serverre Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hogyan automatikus kiépítés beállítása egy alkalmazáshoz?

A katalógusban felsorolt előre integrált alkalmazások esetében az automatikus kiépítés beállításához lépésenkénti útmutató áll rendelkezésre. Tekintse [meg az integrált katalógus-alkalmazásokhoz tartozó oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/saas-apps/). A következő videó bemutatja, hogyan állíthatja be az automatikus felhasználó-kiépítés SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Az SCIM 2,0-et támogató egyéb alkalmazások esetében kövesse a [scim felhasználó kiépítés a Azure Active Directorytel](use-scim-to-provision-users-and-groups.md)című cikk lépéseit.

## <a name="what-happens-during-provisioning"></a>Mi történik a kiépítés során?

Ha az Azure AD a forrásoldali rendszer, a kiépítési szolgáltatás az [Azure ad Graph API különbözeti lekérdezési funkciójával](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) figyeli a felhasználókat és a csoportokat. A kiépítési szolgáltatás kezdeti ciklust futtat a forrásrendszer és a célként megadott rendszeren, majd az időszakos növekményes ciklusok után.

### <a name="initial-cycle"></a>Kezdeti ciklus

A kiépítési szolgáltatás indításakor a rendszer az első szinkronizálást futtatja:

1. Az összes felhasználó és csoport lekérdezése a forrásoldali rendszerből, az [attribútum-hozzárendelésekben](customize-application-attributes.md)definiált összes attribútum beolvasása.
1. A visszaadott felhasználók és csoportok szűrése bármely konfigurált [hozzárendelés](assign-user-or-group-access-portal.md) vagy [attribútum-alapú hatókör-szűrő](define-conditional-rules-for-provisioning-user-accounts.md)használatával.
1. Ha egy felhasználó hozzá van rendelve vagy hatókörben van az üzembe helyezéshez, a szolgáltatás lekérdezi a megfelelő felhasználót a megadott [egyező attribútumokkal](customize-application-attributes.md#understanding-attribute-mapping-properties). Példa: Ha a userPrincipal neve megegyezik a megfelelő attribútummal, és leképezi a felhasználónevet a célként megadott rendszeren, a kiépítési szolgáltatás lekérdezi a célként megadott rendszerrendszert a forrásrendszer userPrincipal-értékeinek megfelelő felhasználónevek számára.
1. Ha nem található egyező felhasználó a célszámítógépen, a rendszer a forrásrendszer által visszaadott attribútumok használatával hozza létre. A felhasználói fiók létrehozása után a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját, amely az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.
1. Ha a rendszer megfelelő felhasználót talál, az a forrásrendszer által megadott attribútumok használatával frissül. A felhasználói fiók egyeztetése után a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját, amely az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.
1. Ha az attribútum-hozzárendelések "Reference" attribútumokat tartalmaznak, a szolgáltatás további frissítéseket hoz létre a célszámítógépen a hivatkozott objektumok létrehozásához és összekapcsolásához. Előfordulhat például, hogy egy felhasználó "Manager" attribútummal rendelkezik a célszámítógépen, amely egy másik, a célszámítógépen létrehozott felhasználóhoz van társítva.
1. A kezdeti ciklus végén maradjon egy vízjelet, amely a későbbi növekményes ciklusok kiindulási pontját adja meg.

Egyes alkalmazások, például a ServiceNow, a G Suite és a Box támogatása nem csupán a felhasználókat, hanem a csoportok és a tagjaik létesítését is támogatják. Ezekben az esetekben, ha a csoportok kiosztása engedélyezve van a [leképezésekben](customize-application-attributes.md), a kiépítési szolgáltatás szinkronizálja a felhasználókat és a csoportokat, majd később szinkronizálja a csoporttagságok körét.

### <a name="incremental-cycles"></a>Növekményes ciklusok

A kezdeti ciklus után az összes többi ciklus a következő lesz:

1. A forrásrendszer lekérdezése minden olyan felhasználó és csoport esetében, amelyet az utolsó vízjel tárolása óta frissítettek.
1. A visszaadott felhasználók és csoportok szűrése bármely konfigurált [hozzárendelés](assign-user-or-group-access-portal.md) vagy [attribútum-alapú hatókör-szűrő](define-conditional-rules-for-provisioning-user-accounts.md)használatával.
1. Ha egy felhasználó hozzá van rendelve vagy hatókörben van az üzembe helyezéshez, a szolgáltatás lekérdezi a megfelelő felhasználót a megadott [egyező attribútumokkal](customize-application-attributes.md#understanding-attribute-mapping-properties).
1. Ha nem található egyező felhasználó a célszámítógépen, a rendszer a forrásrendszer által visszaadott attribútumok használatával hozza létre. A felhasználói fiók létrehozása után a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját, amely az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.
1. Ha a rendszer megfelelő felhasználót talál, az a forrásrendszer által megadott attribútumok használatával frissül. Ha ez egy újonnan hozzárendelt fiók, amely megfelel, a kiépítési szolgáltatás észleli és gyorsítótárazza az új felhasználóhoz tartozó rendszer AZONOSÍTÓját, amely az adott felhasználóra vonatkozó összes jövőbeli művelet futtatására szolgál.
1. Ha az attribútum-hozzárendelések "Reference" attribútumokat tartalmaznak, a szolgáltatás további frissítéseket hoz létre a célszámítógépen a hivatkozott objektumok létrehozásához és összekapcsolásához. Előfordulhat például, hogy egy felhasználó "Manager" attribútummal rendelkezik a célszámítógépen, amely egy másik, a célszámítógépen létrehozott felhasználóhoz van társítva.
1. Ha egy korábban a kiépítés hatókörében lévő felhasználó el lett távolítva a hatókörből (beleértve a hozzá nem rendeltt is), a szolgáltatás egy frissítésen keresztül letiltja a felhasználót a célszámítógépen.
1. Ha egy korábban a kiépítés hatókörében lévő felhasználó le van tiltva vagy nem törlődik a forrásrendszer, a szolgáltatás egy frissítésen keresztül letiltja a felhasználót a célszámítógépen.
1. Ha egy korábban a kiépítés hatókörében lévő felhasználó nem törlődik a forrásoldali rendszeren, a szolgáltatás törli a felhasználót a célszámítógépen. Az Azure AD-ben a felhasználók a törlés után 30 nappal törlődnek.
1. A növekményes ciklus végén egy új vízjel marad, amely a későbbi növekményes ciklusok kiindulási pontját adja meg.

> [!NOTE]
> A **létrehozási**, **frissítési**vagy **törlési** műveletet letilthatja a [leképezések](customize-application-attributes.md) szakasz **cél objektum műveletei** jelölőnégyzetének használatával. A felhasználók egy frissítés során való letiltásának logikáját a (z) "accountEnabled" mezőből származó attribútum-hozzárendeléssel is ellenőrzik.

A kiépítési szolgáltatás továbbra is határozatlan időre futtatja az [egyes alkalmazásokra vonatkozó oktatóanyagban](../saas-apps/tutorial-list.md)meghatározott időközöket, amíg az alábbi események egyike nem következik be:

- A szolgáltatás kézi leállítása a Azure Portal használatával vagy a megfelelő Graph API parancs használatával történik. 
- A rendszer az új kezdeti ciklust a **Törlés állapota és az újraindítás** lehetőség használatával indítja el a Azure Portalban, vagy a megfelelő Graph API parancs használatával. Ez a művelet törli a tárolt vízjeleket, és az összes forrásobjektum kiértékelését okozza.
- Az attribútum-hozzárendelések és a hatóköri szűrők változása miatt új kezdeti ciklust indít a rendszer. Ezzel a művelettel az összes tárolt vízjel törlődik, és az összes forrásoldali objektum újbóli kiértékelését okozza.
- A kiépítési folyamat Karanténba kerül (lásd alább), magas hiba miatt, és több mint négy hétig karanténba marad. Ebben az esetben a szolgáltatás automatikusan le lesz tiltva.

### <a name="errors-and-retries"></a>Hibák és újrapróbálkozások

Ha egy adott felhasználó nem adható hozzá, nem frissíthető vagy nem törölhető a megcélzott rendszeren, mert hiba történt a célszámítógépen, akkor a rendszer a következő szinkronizálási ciklusban újrapróbálkozik a művelettel. Ha a felhasználó továbbra is sikertelen, akkor az újrapróbálkozások kevesebb gyakorisággal fognak megtörténni, és a napi egyszeri próbálkozásra fokozatosan visszakerülnek. A hiba elhárításához a rendszergazdáknak ellenőriznie kell a [kiépítési naplókat](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) a kiváltó ok okának megállapításához és a megfelelő művelet meghozatalához. Gyakori hibák a következők lehetnek:

- Azok a felhasználók, akik nem rendelkeznek olyan attribútummal, amely a célként megadott rendszerhez szükséges a forrásoldali rendszeren
- Azok a felhasználók, akiknek attribútum értéke van abban a forrásrendszer esetében, amelyhez egyedi korlátozás van megadva a célszámítógépen, és ugyanaz az érték egy másik felhasználói rekordban van

Ezek a hibák a forrásrendszer érintett felhasználójának attribútum-értékeinek módosításával vagy az attribútum-hozzárendelések nem okozta ütközések miatti módosításával oldhatók fel.

### <a name="quarantine"></a>Karantén

Ha a megcélzott rendszerre irányuló hívások többsége vagy mindegyike egy hiba miatt nem sikerül (például érvénytelen rendszergazdai hitelesítő adatok esetén), akkor a kiépítési feladat "karantén" állapotba kerül. Ez az állapot a [kiépítési összefoglaló jelentésben](check-status-user-account-provisioning.md) és e-mailben, ha az e-mailes értesítések konfigurálva vannak a Azure Portalban.

Karantén esetén a növekményes ciklusok gyakorisága naponta egyszer csökken.

A kiépítési feladatot a rendszer eltávolítja a karanténba, miután az összes jogsértő hibát kijavította, és a következő szinkronizálási ciklus elindul. Ha a kiépítési feladatok több mint négy hétig maradnak karanténban, a kiépítési feladatok le vannak tiltva. További [információ itt található a karantén állapotáról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="how-long-will-it-take-to-provision-users"></a>Mennyi időt vesz igénybe a felhasználók kiépítése?

A teljesítmény attól függ, hogy a kiépítési feladatok kezdeti kiépítési ciklust vagy növekményes ciklust futtatnak-e. A kiépítés időtartamáról és a kiépítési szolgáltatás állapotának figyeléséről a következő témakörben tájékozódhat: [a felhasználó kiépítési állapotának ellenőrzésével](application-provisioning-when-will-provisioning-finish-specific-user.md)kapcsolatos információk.

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Honnan tudhatom meg, hogy a felhasználók megfelelően lettek-e kiépítve?

A felhasználói kiépítési szolgáltatás által futtatott összes művelet rögzítve van az Azure AD- [létesítési naplókban (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Ebbe beletartozik a forrás-és a megcélzott rendszerek összes írási és olvasási művelete, valamint az egyes műveletek során olvasott vagy írt felhasználói adatok.

További információ a kiépítési naplók beolvasásáról a Azure Portalban: a [kiépítési jelentési útmutató](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Hogyan a felhasználók kiépítési problémáinak elhárítását?

Az automatikus felhasználó-kiépítés hibaelhárításával kapcsolatos forgatókönyv-alapú útmutatásért tekintse meg a [felhasználók egy alkalmazáshoz való konfigurálásával és](application-provisioning-config-problem.md)kikapcsolásával kapcsolatos problémákat.

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Melyek az automatikus felhasználó-kiépítés előkészítésének ajánlott eljárásai?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

A kimenő felhasználók egy alkalmazáshoz való kiépítésének lépésenkénti üzembe helyezési tervét a következő témakörben találja: a [felhasználók kiépítésének identitás-telepítési útmutatója](https://aka.ms/deploymentplans/userprovisioning).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Az automatikus felhasználói kiépítés az SaaS-alkalmazásokba az Azure AD-ben B2B-felhasználókkal működik?

Igen, az Azure AD-beli felhasználói üzembe helyezési szolgáltatás használatával az Azure AD-ben elérhetővé teheti a VÁLLALATKÖZI (vagy vendég) felhasználókat az SaaS-alkalmazásokhoz.

Ahhoz azonban, hogy a B2B-felhasználók az Azure AD-vel jelentkezzenek be az SaaS-alkalmazásba, az SaaS-alkalmazásnak adott módon konfigurált SAML-alapú egyszeri bejelentkezési képességgel kell rendelkeznie. További információ az SaaS-alkalmazások a B2B-felhasználóktól való támogatásához való konfigurálásáról: [SaaS-alkalmazások konfigurálása B2B-együttműködéshez]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Működik az automatikus felhasználó-kiépítés az SaaS-alkalmazásokban dinamikus csoportok használatával az Azure AD-ben?

Igen. Ha a "csak a hozzárendelt felhasználók és csoportok szinkronizálása" beállításra van konfigurálva, az Azure AD-beli felhasználói kiépítési szolgáltatás a felhasználók számára is kiépítheti vagy kiépítheti az SaaS-alkalmazásokban lévő felhasználókat attól függően, hogy azok tagjai egy [dinamikus csoportnak](../users-groups-roles/groups-create-rule.md). A dinamikus csoportok a "minden felhasználó és csoport szinkronizálása" beállítással is működnek.

A dinamikus csoportok használata azonban hatással lehet az Azure AD-ből az SaaS-alkalmazásokba való teljes körű felhasználói kiépítés általános teljesítményére. Dinamikus csoportok használata esetén tartsa szem előtt ezeket a figyelmeztetéseket és javaslatokat:

- A dinamikus csoportokban lévő felhasználók egy SaaS-alkalmazásban való üzembe helyezésének és megszüntetésének gyorsasága attól függ, hogy a dinamikus csoport milyen gyorsan értékelheti a tagsági változásokat. További információ a dinamikus csoportok feldolgozási állapotának vizsgálatáról: [tagsági szabály feldolgozási állapotának](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)megtekintése.

- Dinamikus csoportok használatakor a szabályoknak körültekintően kell megfontolniuk a felhasználók kiépítését és megszüntetését, mivel a tagság elvesztése a megszüntetési eseményt eredményezi.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Az automatikus felhasználói kiépítés az SaaS-alkalmazásokban az Azure AD-ben beágyazott csoportokkal működik?

Nem. Ha a "csak a hozzárendelt felhasználók és csoportok szinkronizálása" beállítás van konfigurálva, az Azure AD-felhasználó kiépítési szolgáltatása nem tudja beolvasni vagy kiépíteni a beágyazott csoportokban lévő felhasználókat. Csak olyan felhasználókat tud beolvasni és kiépíteni, akik közvetlenül tagjai az explicit módon hozzárendelt csoportnak.

Ez a "csoportos hozzárendelések az alkalmazásokhoz" korlátozás, amely az egyszeri bejelentkezést is befolyásolja, és az [SaaS-alkalmazásokhoz való hozzáférés kezelésére szolgáló csoport használatával](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )van leírva.

Megkerülő megoldásként explicit módon rendeljen hozzá (vagy más [hatókört](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) azokhoz a csoportokhoz, amelyek tartalmazzák a kiépíteni kívánt felhasználókat.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>A kiépítés az Azure AD és egy célalkalmazás között titkosított csatornát használ?

Igen. A kiszolgáló céljához HTTPS SSL-titkosítást használunk.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
- [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](customize-application-attributes.md)
- [Kifejezések írása attribútum-hozzárendelésekhez](functions-for-customizing-application-data.md)
- [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
- [Felhasználók és csoportok automatikus kiépítés engedélyezése az SCIM használatával az Azure AD-ből alkalmazásokba](use-scim-to-provision-users-and-groups.md)
- [Az Azure AD szinkronizációs API áttekintése](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
