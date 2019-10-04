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
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac78029ba2d1f45ef67ef0d858fdd2917bd4a97a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033338"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>A felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory

Azure Active Directory (Azure AD) segítségével automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását a Felhőbeli ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) alkalmazásokban, például a Dropbox, a Salesforce, a ServiceNow és más rendszerekben. Ez az úgynevezett SaaS-alkalmazások automatikus felhasználó-kiépítés.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

Ez a funkció lehetővé teszi a következőket:

- Új fiókok automatikus létrehozása a megfelelő rendszerekben új személyekhez, amikor csatlakoznak a csapathoz vagy szervezethez.
- Automatikusan inaktiválja a fiókokat a megfelelő rendszerekben, amikor a felhasználók elhagyják a csapatot vagy a szervezetet.
- Győződjön meg arról, hogy az alkalmazásokban és rendszerekben lévő identitások naprakészek maradnak a címtár változásai vagy az emberi erőforrások rendszere alapján.
- Nem felhasználói objektumok, például csoportok kiépítése az azokat támogató alkalmazásokba.

Az automatikus felhasználó-kiépítés magában foglalja a következő funkciókat is:

- A forrás-és a megcélzott rendszerek közötti meglévő identitások egyeztetésének lehetősége.
- Testreszabható attribútum-hozzárendelések, amelyek meghatározzák, hogy a rendszer milyen felhasználói adatokból álljon át a forrásrendszer és a cél rendszer között.
- Nem kötelező e-mail-riasztások kiépítési hibák esetén.
- Jelentéskészítési és tevékenységi naplók, amelyek segítenek a figyelésben és a hibaelhárításban.

## <a name="why-use-automated-provisioning"></a>Miért érdemes az automatikus kiépítés használatára?

A funkció használatának néhány gyakori indítéka a következők:

- A manuális kiépítési folyamatokkal kapcsolatos költségek, eredménytelenség és emberi hibák elkerülése.
- Az egyedi fejlesztésű üzembe helyezési megoldások és parancsfájlok üzemeltetésével és kezelésével kapcsolatos költségek elkerülése.
- A szervezet védelme azáltal, hogy azonnal eltávolítja a felhasználók identitását a Key SaaS-alkalmazásokból, amikor elhagyja a szervezetet.
- Számos felhasználót egyszerűen importálhat egy adott SaaS-alkalmazásba vagy-rendszerbe.
- Egyetlen szabályzattal meghatározhatja, hogy ki kiépített és ki tud jelentkezni egy alkalmazásba.

## <a name="how-does-automatic-provisioning-work"></a>Hogyan működik az automatikus kiépítés?

Az **Azure ad-kiépítési szolgáltatás** a felhasználókat az SaaS-alkalmazásokhoz és más rendszerekhez is kiépíti az egyes alkalmazások gyártói által biztosított felhasználói felügyeleti API-végpontokhoz való csatlakozással. Ezek a felhasználói felügyeleti API-végpontok lehetővé teszik az Azure AD számára a felhasználók programozott módon történő létrehozását, frissítését és eltávolítását. A kiválasztott alkalmazások esetében a kiépítési szolgáltatás további, identitással kapcsolatos objektumokat, például csoportokat és szerepköröket is létrehozhat, frissíthet és eltávolíthat.

![Az Azure ad-kiépítési](./media/user-provisioning/provisioning0.PNG)
szolgáltatás*1. ábrája: Az Azure AD-kiépítési szolgáltatás*

![Kimenő felhasználó kiépítési munkafolyamata](./media/user-provisioning/provisioning1.PNG)
*2. ábra: "Kimenő" felhasználó kiépítési munkafolyamata az Azure AD-ből népszerű SaaS-alkalmazásokba*

![Bejövő felhasználó kiépítési munkafolyamata](./media/user-provisioning/provisioning2.PNG)
*3. ábra: "Bejövő" felhasználó kiépítési munkafolyamata a népszerű humántőke-felügyeleti (HCM) alkalmazásokból a Azure Active Directory és a Windows Server Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Milyen alkalmazásokat és rendszereket használhatok az Azure AD automatikus felhasználói üzembe helyezéséhez?

Az Azure AD számos népszerű SaaS-alkalmazás és emberi erőforrásrendszer előre integrált támogatását, valamint általános támogatást nyújt a SCIM 2,0 standard részét képező alkalmazások számára.

### <a name="pre-integrated-applications"></a>Előre integrált alkalmazások

Azon alkalmazások listájáért, amelyekhez az Azure AD támogatja az előre integrált létesítési összekötőt, tekintse meg a felhasználók üzembe helyezéséhez kapcsolódó [alkalmazás-oktatóanyagok listáját](../saas-apps/tutorial-list.md).

Ha az Azure AD mérnöki csapatával szeretne további alkalmazásokat kiépíteni, küldjön egy üzenetet a [Azure Active Directory visszajelzési fórumán](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)keresztül.

> [!NOTE]
> Ahhoz, hogy egy alkalmazás támogassa az automatikus felhasználói üzembe helyezést, először meg kell adnia a szükséges felhasználói felügyeleti API-kat, amelyek lehetővé teszik, hogy a külső programok automatizálják a felhasználók létrehozását, karbantartását és eltávolítását. Ezért nem minden SaaS-alkalmazás kompatibilis ezzel a szolgáltatással. A felhasználói felügyeleti API-kat támogató alkalmazások esetében az Azure AD mérnöki csapata kiépítheti ezeket az alkalmazásokat, és ezt a munkát az aktuális és a leendő ügyfelek igényei szerint rangsorolhatja.

### <a name="connecting-applications-that-support-scim-20"></a>A SCIM 2,0-et támogató alkalmazások csatlakoztatása

Az SCIM 2,0-alapú felhasználói felügyeleti API-kat megvalósító alkalmazások általános összekapcsolásával kapcsolatos információkért lásd: a [scim használata a felhasználók és csoportok automatikus kiépítéséhez Azure Active Directoryról alkalmazásokba](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hogyan automatikus kiépítés beállítása egy alkalmazáshoz?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

A Azure Active Directory portál használatával konfigurálhatja az Azure AD kiépítési szolgáltatást egy kiválasztott alkalmazáshoz.

1. Nyissa meg a **[Azure Active Directory portált](https://aad.portal.azure.com)** .
1. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Az összes konfigurált alkalmazás listája látható.
1. Alkalmazás hozzáadásához válassza az **+ új alkalmazás** lehetőséget. Adja hozzá a következők egyikét a forgatókönyvtől függően:

   - A **saját alkalmazás hozzáadása** lehetőség az egyéni fejlesztésű scim-integrációkat támogatja.
   - Az > automatikus kiépítés az összes olyan alkalmazáshoz, **amelyet a katalógus** **Kiemelt alkalmazások** szakasza tartalmaz. Tekintse meg az [alkalmazás-oktatóanyagokat a felhasználók kiépítési](../saas-apps/tutorial-list.md) feladatait ismertető listában.

1. Adja meg a részleteket, és válassza a **Hozzáadás**lehetőséget. A rendszer hozzáadja az új alkalmazást a vállalati alkalmazások listájához, és megnyitja az alkalmazás-kezelés képernyőjét.
1. A **kiépítés** lehetőség kiválasztásával kezelheti az alkalmazás felhasználói fiókjának kiépítési beállításait.

   ![A kiépítési beállítások képernyő megjelenítése](./media/user-provisioning/provisioning_settings0.PNG)

1. Válassza a **létesítési mód** automatikus beállítását a rendszergazdai hitelesítő adatok, hozzárendelések, Indítás és Leállítás, valamint a szinkronizálás beállításainak megadásához.

   - A **rendszergazdai hitelesítő adatok** kibontásával adja meg azokat a hitelesítő adatokat, amelyek szükségesek ahhoz, hogy az Azure ad csatlakozhasson az alkalmazás felhasználói felügyeleti API-hoz. Ez a szakasz azt is lehetővé teszi, hogy engedélyezze az e-mailes értesítéseket, ha a hitelesítő adatok meghiúsulnak, vagy a kiépítési feladat [karanténba](#quarantine)kerül.
   - A **leképezések** kibontásával megtekintheti és szerkesztheti az Azure ad és a célalkalmazás közötti felhasználói attribútumokat, amikor a felhasználói fiókokat kiépítik vagy frissítik. Ha a célalkalmazás támogatja azt, ez a szakasz lehetővé teszi a csoportok és felhasználói fiókok üzembe helyezésének igény szerinti konfigurálását. Válasszon ki egy leképezést a táblázatban a leképezési szerkesztő jobbra való megnyitásához, ahol megtekintheti és testreszabhatja a felhasználói attribútumokat.

     A **hatóköri szűrők** közlik a kiépítési szolgáltatással, hogy a forrásrendszer mely felhasználói és csoportjai legyenek kiépítve vagy kiépítve a célként megadott rendszerbe. Az **attribútum-hozzárendelés** ablaktáblán válassza ki a **forrásoldali objektum hatókörét** az adott attribútumérték szűréséhez. Például megadhatja, hogy csak a „Department” attribútumhoz „Sales” értékkel rendelkező felhasználók tartozzanak az átadás hatókörébe. További információkért tekintse meg [a hatókörszűrők használatát ismertető cikket](define-conditional-rules-for-provisioning-user-accounts.md).

     További információ: az [attribútumok megfeleltetésének testreszabása](customize-application-attributes.md).

   - A **Beállítások** vezérlik egy alkalmazás kiépítési szolgáltatásának működését, beleértve azt is, hogy jelenleg fut-e. A **hatókör** menü segítségével megadhatja, hogy csak a hozzárendelt felhasználók és csoportok legyenek a kiépítés hatókörében, vagy ha az Azure ad-címtár minden felhasználóját ki kell építeni. A felhasználók és csoportok „hozzárendelésével” kapcsolatos információkért lásd [az Azure Active Directoryban a felhasználók és csoportok vállalati alkalmazásokhoz való hozzárendelését ismertető cikket](assign-user-or-group-access-portal.md).

Az App Management képernyőn válassza ki a **kiépítési naplók (előzetes verzió)** lehetőséget az Azure ad-kiépítési szolgáltatás által futtatott összes művelet rekordjainak megtekintéséhez. További információkért lásd a [kiépítési jelentéskészítési útmutatót](check-status-user-account-provisioning.md).

![Példa: kiépítési naplók képernyője egy alkalmazáshoz](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Az Azure AD-beli felhasználói kiépítési szolgáltatás a [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)használatával is konfigurálható és kezelhető.

## <a name="what-happens-during-provisioning"></a>Mi történik a kiépítés során?

Ha az Azure AD a forrásoldali rendszer, a kiépítési szolgáltatás az [Azure ad Graph API különbözeti lekérdezési funkciójával](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) figyeli a felhasználókat és a csoportokat. A kiépítési szolgáltatás kezdeti ciklust futtat a forrásrendszer és a célként megadott rendszeren, majd az időszakos növekményes ciklusok után.

### <a name="initial-cycle"></a>Kezdeti ciklus

A kiépítési szolgáltatás indításakor a rendszer az első szinkronizálást futtatja:

1. Az összes felhasználó és csoport lekérdezése a forrásoldali rendszerből, az [attribútum-hozzárendelésekben](customize-application-attributes.md)definiált összes attribútum beolvasása.
1. A visszaadott felhasználók és csoportok szűrése bármely konfigurált [hozzárendelés](assign-user-or-group-access-portal.md) vagy [attribútum-alapú hatókör-szűrő](define-conditional-rules-for-provisioning-user-accounts.md)használatával.
1. Ha egy felhasználó hozzá van rendelve vagy hatókörben van az üzembe helyezéshez, a szolgáltatás lekérdezi a megfelelő felhasználót a megadott [egyező attribútumokkal](customize-application-attributes.md#understanding-attribute-mapping-properties). Példa: Ha a userPrincipal neve megegyezik a megfelelő attribútummal, és leképezi a felhasználónevet a célhelyen, akkor a kiépítési szolgáltatás lekérdezi a célként megadott rendszerrendszert a forrásrendszer userPrincipal nevének megfelelő felhasználónevek számára.
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

A kiépítési feladatot a rendszer eltávolítja a karanténba, miután az összes jogsértő hibát kijavította, és a következő szinkronizálási ciklus elindul. Ha a kiépítési feladatok több mint négy hétig maradnak karanténban, a kiépítési feladatok le vannak tiltva.

## <a name="how-long-will-it-take-to-provision-users"></a>Mennyi időt vesz igénybe a felhasználók kiépítése?

A teljesítmény attól függ, hogy a kiépítési feladatok kezdeti kiépítési ciklust vagy növekményes ciklust futtatnak-e. A kiépítés időtartamáról és a kiépítési szolgáltatás állapotának figyeléséről a következő témakörben tájékozódhat: [a felhasználó kiépítési állapotának ellenőrzésével](application-provisioning-when-will-provisioning-finish-specific-user.md)kapcsolatos információk.

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Honnan tudhatom meg, hogy a felhasználók megfelelően lettek-e kiépítve?

A felhasználói kiépítési szolgáltatás által futtatott összes művelet rögzítve van az Azure AD- [létesítési naplókban (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Ebbe beletartozik a forrás-és a megcélzott rendszerek összes írási és olvasási művelete, valamint az egyes műveletek során olvasott vagy írt felhasználói adatok.

További információ a kiépítési naplók beolvasásáról a Azure Portalban: a [kiépítési jelentési útmutató](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Hogyan a felhasználók kiépítési problémáinak elhárítását?

Az automatikus felhasználó-kiépítés hibaelhárításával kapcsolatos forgatókönyv-alapú útmutatásért tekintse meg a [felhasználók egy alkalmazáshoz való konfigurálásával és](application-provisioning-config-problem.md)kikapcsolásával kapcsolatos problémákat.

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Melyek az automatikus felhasználó-kiépítés előkészítésének ajánlott eljárásai?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

A kimenő felhasználók egy alkalmazáshoz való kiépítésének lépésenkénti üzembe helyezési tervét a következő témakörben találja: a [felhasználók kiépítésének identitás-telepítési útmutatója](https://aka.ms/userprovisioningdeploymentplan).

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
