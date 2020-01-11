---
title: Azure Cost Management hatókörök megismerése és használata
description: Ez a cikk segít megérteni az Azure-ban elérhető számlázási és erőforrás-kezelési hatóköröket, valamint a hatókörök használatát Cost Management és API-kkal.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 653e57a69e1c7ff8dbeadb14cda6e415ff3a9ee9
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887563"
---
# <a name="understand-and-work-with-scopes"></a>A hatókörök ismertetése és használata

Ez a cikk segít megérteni az Azure-ban elérhető számlázási és erőforrás-kezelési hatóköröket, valamint a hatókörök használatát Cost Management és API-kkal.

## <a name="scopes"></a>Hatókörök

A _hatókör_ az Azure-erőforrás-hierarchia azon csomópontja, ahol az Azure ad-felhasználók hozzáférnek és kezelhetnek szolgáltatásokat. A legtöbb Azure-erőforrást az előfizetések részét képező erőforráscsoportok hozzák létre és telepítik. A Microsoft az Azure-előfizetések feletti két hierarchiát is kínál, amelyek speciális szerepkörökkel rendelkeznek a számlázási adat kezeléséhez:
- Számlázási információk, például kifizetések és számlák
- Cloud Services, például a Cost és a Policy irányítás

A hatókörök a számlázási információk kezelése, a kifizetésekhez tartozó szerepkörök, a számlák megtekintése és az általános fiókok kezelése. A számlázási és a fiók szerepkörei külön kezelhetők az erőforrás-kezeléshez használt adatoktól, amelyek az [Azure RBAC](../role-based-access-control/overview.md)használják. Ahhoz, hogy egyértelműen megkülönböztesse a különálló hatókörök célját, beleértve a hozzáférés-vezérlési eltéréseket is, ezeket _Számlázási hatóköröknek_ és _RBAC-hatóköröknek_nevezzük.

## <a name="how-cost-management-uses-scopes"></a>A Cost Management hatókörök használata

Cost Management az erőforrások feletti összes hatókörben működik, hogy a szervezetek a teljes számlázási fiók vagy egyetlen erőforráscsoport esetében is kezeljék a költségeket. Bár a számlázási hatókörök eltérnek a Microsoft-szerződés (előfizetés típusa) alapján, a RBAC hatókörök nem.

## <a name="azure-rbac-scopes"></a>Azure RBAC-hatókörök

Az Azure három hatókört támogat az erőforrás-kezeléshez. Minden hatókör támogatja a hozzáférés és irányítás kezelését, beleértve a Cost managementet, de nem kizárólagosan.

- [**Felügyeleti csoportok**](../governance/management-groups/overview.md) – az Azure-előfizetések rendszerezésére szolgáló hierarchikus tárolók, legfeljebb nyolc szint.

    Erőforrás típusa: [Microsoft. Management/managementGroups](/rest/api/resources/managementgroups)

- **Előfizetések** – elsődleges tárolók az Azure-erőforrásokhoz.

    Erőforrás típusa: [Microsoft. Resources/előfizetések](/rest/api/resources/subscriptions)

- [**Erőforráscsoportok**](../azure-resource-manager/management/overview.md#resource-groups) – a kapcsolódó erőforrások logikai csoportosítása egy olyan Azure-megoldáshoz, amely ugyanazt az életciklust használja. Ilyenek például az üzembe helyezett és a együtt törölt erőforrások.

    Erőforrás típusa: [Microsoft. Resources/Subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

A felügyeleti csoportok lehetővé teszik az előfizetések hierarchiába rendezését. Létrehozhat például egy logikai szervezeti hierarchiát a felügyeleti csoportok használatával. Ezután adja meg a csapatok előfizetéseit a termelési és fejlesztési/tesztelési feladatokhoz. Ezután hozzon létre erőforráscsoportokat az előfizetésekben az egyes alrendszerek vagy összetevők kezeléséhez.

A szervezeti hierarchia létrehozása lehetővé teszi a költséghatékony és a szabályzatoknak való megfelelőség felépítését. Ezután minden vezető megtekintheti és elemezheti a jelenlegi költségeit. Ezután létrehozhatnak költségvetéseket a rossz kiadási minták megfékezéséhez és a költségek optimalizálásához a legalacsonyabb szintű Advisor-javaslatokkal.

Hozzáférés biztosítása a költségek megtekintéséhez és opcionálisan kezelheti a költségek konfigurációját, például a költségvetést és az exportot, az Azure RBAC használatával végezhető irányítási hatókörökön. Az Azure RBAC használatával biztosíthatja, hogy az Azure AD-felhasználók és-csoportok hozzáférjenek az adott hatókörben és az alatta lévő szerepkörökben definiált előre meghatározott műveletek végrehajtásához. Egy felügyeleti csoport hatóköréhez hozzárendelt szerepkör például ugyanazokat az engedélyeket biztosítja a beágyazott előfizetések és erőforráscsoportok számára is.

Cost Management a következő beépített szerepköröket támogatja az egyes hatókörökhöz:

- [**Tulajdonos**](../role-based-access-control/built-in-roles.md#owner) – megtekintheti a költségeket és kezelhet mindent, beleértve a költségek konfigurálását.
- [**Közreműködő**](../role-based-access-control/built-in-roles.md#contributor) – megtekintheti a költségeket és kezelheti az összeset, beleértve a költségek konfigurálását, a hozzáférés-vezérlés kizárásával.
- [**Olvasó**](../role-based-access-control/built-in-roles.md#reader) – mindent megtekinthet, beleértve a költségadatok és a konfigurációt, de nem végezheti el a módosításokat.
- [**Cost Management közreműködő**](../role-based-access-control/built-in-roles.md#cost-management-contributor) – megtekintheti a költségeket, kezelheti a költségek konfigurációját, és megtekintheti a javaslatokat.
- [**Cost Management olvasó**](../role-based-access-control/built-in-roles.md#cost-management-reader) – megtekintheti a költségadatok és a költséghatékonyság beállításait, és megtekintheti az ajánlásokat.

Cost Management közreműködő az ajánlott legkevesebb jogosultsággal rendelkező szerepkör. Lehetővé teszi a felhasználók számára, hogy a költségvetések és exportálások létrehozása és kezelése hatékonyabban figyelje és jelentse a költségeket. Cost Management közreműködők további szerepköröket is igényelhetnek a teljes körű Cost Management-forgatókönyvek támogatásához. Vegyük példaként a következő eseteket:

- **Ha túllépi a költségvetést** , Cost Management közreműködők számára is hozzáférést kell biztosítani a műveleti csoportok létrehozásához és/vagy kezeléséhez, hogy a rendszer automatikusan reagáljon a túllépésekre. Vegye fontolóra, hogy a [figyelő közreműködőt](../role-based-access-control/built-in-roles.md#monitoring-contributor) olyan erőforráscsoporthoz adja meg, amely tartalmazza a költségvetési küszöbértékek túllépése esetén használandó műveleti csoportot. Az egyes műveletek automatizálásához további szerepkörökre van szükség a használt szolgáltatások, például az Automation és a Azure Functions számára.
- A **költségadatok exportálásának ütemezett időpontja** – Cost Management közreműködők is hozzáféréssel kell rendelkezniük a Storage-fiókok kezeléséhez, hogy a rendszer exportálja az Adatmásolást egy Storage-fiókba. Érdemes lehet a [Storage-fiók közreműködőjét](../role-based-access-control/built-in-roles.md#storage-account-contributor) olyan erőforráscsoporthoz adni, amely tartalmazza azt a Storage-fiókot, ahol a költségadatok exportálva vannak.
- A **költségmegtakarítással kapcsolatos javaslatok megtekintése** – Cost Management olvasók és Cost Management közreműködők alapértelmezés szerint *tekinthetik* meg a Cost-ajánlásokat. A Cost-javaslatokhoz való hozzáféréshez azonban hozzáférésre van szükség az egyes erőforrásokhoz. Ha Cost-alapú javaslatot szeretne felvenni, vegye fontolóra egy [szolgáltatás-specifikus szerepkör](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) megadását.

## <a name="enterprise-agreement-scopes"></a>Hatókörök Nagyvállalati Szerződés

A Nagyvállalati Szerződés (EA) számlázási fiókjai (más néven regisztrációk) a következő hatókörökkel rendelkeznek:

- [**Számlázási fiók**](../billing/billing-view-all-accounts.md) – EA-regisztrációt jelent. A számlák ezen a hatókörön jönnek létre. A nem használatos, például a piactéren és a foglalásokon alapuló vásárlások csak ezen a hatókörön érhetők el. Nem szerepelnek a részlegekben vagy a beléptetési fiókokban.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Részleg** – a beléptetési fiókok választható csoportosítása.

    Erőforrás típusa: `Billing/billingAccounts/departments`

- **Beléptetési fiók** – egyetlen fiók tulajdonosát jelöli. Nem támogatja több személy hozzáférésének megadását.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Bár az irányítási hatókörök egyetlen címtárhoz vannak kötve, az EA számlázási hatókörök nem. Az EA számlázási fiókok tetszőleges számú Azure AD-címtárban rendelkezhetnek előfizetéssel.

Az EA számlázási hatókörök a következő szerepköröket támogatják:

- **Vállalati rendszergazda** – kezelheti a számlázási fiókok beállításait és hozzáférését, megtekintheti az összes költséget, és kezelheti a költségek konfigurációját. Például: költségvetések és exportálások. A függvényben az EA számlázási hatókör megegyezik a [Cost Management közreműködő Azure RBAC szerepkörével](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Vállalati írásvédett felhasználó** – megtekintheti a számlázási fiókok beállításait, a költségadatok és a költségadatok beállításait. Például: költségvetések és exportálások. A függvényben az EA számlázási hatókör megegyezik a [Cost Management Reader Azure RBAC szerepkörével](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Részleg rendszergazdája** – kezelheti a részleg beállításait, például a Cost centert, elérheti, megtekintheti az összes költséget, és kezelheti a költségek konfigurációját. Például: költségvetések és exportálások.  A **da View díjak** számlázási fiók beállítását engedélyezni kell a részleg rendszergazdái és a csak olvasási jogosultsággal rendelkező felhasználók számára a költségek megtekintéséhez. Ha a **da View díjak** le vannak tiltva, a részleg felhasználói semmilyen szinten nem láthatják a költségeket, még akkor sem, ha ezek a fiókok vagy az előfizetés tulajdonosai.
- **Csak olvasási jogosultsággal rendelkező felhasználó** – megtekintheti az részleg beállításait, a költségadatok és a költségadatok beállításait. Például: költségvetések és exportálások. Ha a **da View díjak** le vannak tiltva, a részleg felhasználói semmilyen szinten nem láthatják a költségeket, még akkor sem, ha ezek a fiókok vagy az előfizetés tulajdonosai.
- **Fiók tulajdonosa** – kezelheti a beléptetési fiók beállításait (például a Cost centert), megtekintheti az összes költséget, és kezelheti a beléptetési fiókhoz tartozó költségek konfigurációját (például a költségvetést és az exportálást). A fiók tulajdonosai és a RBAC felhasználók számára engedélyezni kell az **Ao View díjak** számlázási fiók beállítását.

Az EA számlázási fiók felhasználói nem rendelkeznek közvetlen hozzáféréssel a számlákhoz. A számlák külső mennyiségi licencelési rendszerből érhetők el.

Az Azure-előfizetések beágyazva vannak a beléptetési fiókokba A számlázási felhasználók a megfelelő hatókörük alá tartozó előfizetések és erőforráscsoportok esetében férhetnek hozzá a költségadatokhoz. Nem rendelkeznek hozzáféréssel a Azure Portal erőforrásainak megtekintéséhez és kezeléséhez. A számlázási felhasználók megtekinthetik a költségeket, ha a szolgáltatások Azure Portal listájában a **Cost Management + számlázás** lehetőségre navigálnak. Ezután szűrhetik a költségeket azokra a konkrét előfizetésekre és erőforrás-csoportokra, amelyekre jelentést kell készíteniük.

A számlázási felhasználók nem férhetnek hozzá a felügyeleti csoportokhoz, mert nem tartoznak explicit módon egy adott számlázási fiókhoz. A hozzáférést explicit módon kell megadni a felügyeleti csoportoknak. A felügyeleti csoportok az összes beágyazott előfizetésből származó költségeket összesítik. Azonban csak a használaton alapuló vásárlásokat tartalmazzák. Nem tartoznak ide olyan vásárlások, mint a foglalások és a harmadik féltől származó piactér-ajánlatok. A költségek megtekintéséhez használja az EA számlázási fiókot.

## <a name="individual-agreement-scopes"></a>Egyes szerződések hatókörei

Az egyéni ajánlatokból (például utólagos elszámolású és kapcsolódó típusok, például az ingyenes próbaverziós és fejlesztési/tesztelési ajánlatokhoz) létrehozott Azure-előfizetések nem rendelkeznek explicit számlázási fiók hatókörrel. Ehelyett minden előfizetéshez tartozik egy fiók tulajdonosa vagy fiókjának rendszergazdája, például az EA-fiók tulajdonosa.

- [**Számlázási fiók**](../billing/billing-view-all-accounts.md) – egy vagy több Azure-előfizetéshez tartozó egyetlen fiók tulajdonosát jelöli. Jelenleg nem támogatja több személy hozzáférésének biztosítását, vagy az összesített Cost-nézetekhez való hozzáférést.

    Erőforrás típusa: nem alkalmazható

Az egyes Azure-előfizetési fiókok rendszergazdái megtekinthetik és kezelhetik a számlázási adatait, például a számlákat és a kifizetéseket a [Azure Fiókközpont](https://account.azure.com/subscriptions). Azonban nem tekinthetik meg a költségeket, és nem kezelhetik az erőforrásokat a Azure Portalban. Ha hozzáférést szeretne adni a fiók rendszergazdájához, használja a korábban említett Cost Management-szerepköröket.

Az EA-vel ellentétben az egyes Azure-előfizetési fiókok rendszergazdái megtekinthetik a számlákat a Azure Portalban. Ne feledje, hogy Cost Management olvasó-és Cost Management közreműködői szerepkörök nem biztosítanak hozzáférést a számlákhoz. További információ: [hozzáférés engedélyezése a számlákhoz](../billing/billing-manage-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft Customer Agreement-hatókörök

A Microsoft Customer Agreement számlázási fiókjai a következő hatókörökkel rendelkeznek:

- **Számlázási fiók** – több Microsoft-termékhez és-szolgáltatáshoz tartozó ügyfél-szerződést jelent. Az ügyfél-szerződés számlázási fiókjai nem ugyanúgy működnek, mint az EA-regisztrációk. Az EA-regisztrációk szorosabban illeszkednek a számlázási profilokhoz.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Számlázási profil** – a számlán szereplő előfizetéseket határozza meg. A számlázási profilok az EA-regisztráció funkcionális megfelelői, mivel ez a hatókör, amelyet a számlák generálnak. Hasonlóképpen, a nem használaton alapuló (például a piactér és a foglalások) vásárlások csak ezen a hatókörön érhetők el. Nem szerepelnek a számlázási szakaszban.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Számla szakasz** – előfizetések egy csoportját jelöli egy számlán vagy számlázási profilban. A számlázási szakaszok olyanok, mint a részlegek – több ember férhet hozzá egy számla szakaszhoz.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Ügyfél** – olyan előfizetések egy csoportját jelöli, amelyek egy adott ügyfélhez vannak társítva, amely egy partner által készített Microsoft-ügyfél-szerződésbe tartozik. Ez a hatókör a CSP-re vonatkozik.

Az EA számlázási hatóköröktől eltérően az ügyfél-szerződés számlázási fiókjai egyetlen címtárhoz _vannak_ kötve, és több Azure ad-címtárban nem lehetnek előfizetések.

Az ügyfél-szerződés számlázási hatóköre nem vonatkozik a partnerekre. A partneri szerepkörök és engedélyek dokumentálva vannak a [felhasználói szerepkörök és engedélyek hozzárendelésével](/partner-center/permissions-overview)kapcsolatban.

Az ügyfél-szerződés számlázási hatókörei a következő szerepköröket támogatják:

- **Tulajdonos** – kezelheti a számlázási beállításokat és a hozzáférést, megtekintheti az összes költséget, és kezelheti a költségek konfigurációját. Például: költségvetések és exportálások. A függvényben ez az ügyfél-szerződés számlázási hatóköre megegyezik a [Cost Management közreműködő Azure RBAC szerepkörével](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Közreműködő** – kezelheti a számlázási beállításokat, kivéve a hozzáférést, megtekintheti az összes költséget, és kezelheti a költségek konfigurációját. Például: költségvetések és exportálások. A függvényben ez az ügyfél-szerződés számlázási hatóköre megegyezik a [Cost Management közreműködő Azure RBAC szerepkörével](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Olvasó** – megtekintheti a számlázási beállításokat, a költségadatok és a költségadatok beállításait. Például: költségvetések és exportálások. A függvényben ez az ügyfél-szerződés számlázási hatóköre megegyezik a [Cost Management Reader Azure RBAC szerepkörével](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Számla-kezelő** – megtekintheti és kifizetheti a számlákat, és megtekintheti a költségadatok és a konfigurációt. Például: költségvetések és exportálások. A függvényben ez az ügyfél-szerződés számlázási hatóköre megegyezik a [Cost Management Reader Azure RBAC szerepkörével](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-előfizetés létrehozója** – Azure-előfizetéseket hozhat létre, megtekintheti a költségeket, és kezelheti a költségek konfigurációját. Például: költségvetések és exportálások. A függvényben ez az ügyfél-szerződés számlázási hatóköre megegyezik az EA regisztrációs fiók tulajdonosi szerepkörével.

Az Azure-előfizetések a számla szakaszban vannak beágyazva, például az EA regisztrációs fiókok alatt. A számlázási felhasználók a megfelelő hatókörök alá tartozó előfizetések és erőforráscsoportok esetében férhetnek hozzá a költségadatokhoz. Azonban nem rendelkeznek hozzáféréssel a Azure Portal erőforrásainak megtekintéséhez és kezeléséhez. A számlázási felhasználók megtekinthetik a költségeket, ha a szolgáltatások Azure Portal listájában a **Cost Management + számlázás** lehetőségre navigálnak. Ezután szűrheti a költségeket azokra az előfizetésekre és erőforrás-csoportokra, amelyekre jelentést kell készítenie.

A számlázási felhasználók nem férhetnek hozzá a felügyeleti csoportokhoz, mert nem tartoznak kifejezetten a számlázási fiókhoz. Ha azonban a felügyeleti csoportok engedélyezve vannak a szervezet számára, az összes előfizetés költsége a számlázási fiókra és a gyökérszintű felügyeleti csoportra is vonatkozik, mivel mindkettőt egyetlen címtárhoz korlátozzák. A felügyeleti csoportok csak a használaton alapuló vásárlásokat tartalmazzák. A felügyeleti csoportokban nem szerepelnek a vásárlások, például a foglalások és a harmadik féltől származó piactér-ajánlatok. Így a Számlázási fiók és a gyökérszintű felügyeleti csoport különböző összegeket jelenthet. A költségek megtekintéséhez használja a számlázási fiókot vagy a megfelelő számlázási profilt.

## <a name="aws-scopes"></a>AWS-hatókörök

Az AWS-integráció befejezése után tekintse meg az [AWS-integráció beállítása és konfigurálása](aws-integration-set-up-configure.md)című témakört. A következő hatókörök érhetők el:

- **Külső számlázási fiók** – a harmadik féltől származó gyártótól származó vásárlói szerződést jelöl. Ez hasonló az EA számlázási fiókhoz.

    Erőforrás típusa: `Microsoft.CostManagement/externalBillingAccounts`

- **Külső előfizetés** – egy külső gyártótól származó ügyfél-működési fiókot képvisel. Ez hasonló az Azure-előfizetésekhez.

    Erőforrás típusa: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>A Cloud Solution Provider (CSP) hatókörök

A következő hatókörök támogatottak a Microsoft ügyfél-szerződésben lévő ügyfelekkel rendelkező CSP-ket:

- **Számlázási fiók** – több Microsoft-termékhez és-szolgáltatáshoz tartozó ügyfél-szerződést jelent. Az ügyfél-szerződés számlázási fiókjai nem ugyanúgy működnek, mint az EA-regisztrációk. Az EA-regisztrációk szorosabban illeszkednek a számlázási profilokhoz.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Számlázási profil** – a számlán szereplő előfizetéseket határozza meg. A számlázási profilok az EA-regisztráció funkcionális megfelelői, mivel ez a hatókör, amelyet a számlák generálnak. Hasonlóképpen, a nem használaton alapuló (például a piactér és a foglalások) vásárlások csak ezen a hatókörön érhetők el.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Ügyfél** – olyan előfizetések egy csoportját jelöli, amelyek egy adott ügyfélhez tartoznak, amely egy partner által készített Microsoft-ügyfél-szerződéshez tartozik.

Csak a *globális rendszergazdai* és *rendszergazdai ügynök* szerepkörökkel rendelkező felhasználók kezelhetik és tekinthetik meg a számlázási fiókok, a számlázási profilok és az ügyfelek költségeit közvetlenül a partner Azure-bérlője számára. A partner Center szerepköreivel kapcsolatos további információkért lásd: [felhasználói szerepkörök és engedélyek kiosztása](/partner-center/permissions-overview).

A Azure Cost Management csak akkor támogatja a CSP-partneri ügyfeleket, ha az ügyfelek Microsoft-ügyfél szerződéssel rendelkeznek. A Microsoft ügyfél-szerződésben még nem szereplő, CSP által támogatott ügyfelekért lásd: [partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Váltás hatókörök között Cost Management

A Azure Portal összes Cost Management nézete tartalmaz egy **hatókör** -kiválasztási pirulát a nézet bal felső részén. Ezzel gyorsan módosíthatja a hatókört. Kattintson a **hatókör** pirulára a hatókör-választó megnyitásához. Megjeleníti a számlázási fiókokat, a legfelső szintű felügyeleti csoportot, valamint azokat az előfizetéseket, amelyek nincsenek beágyazva a gyökérszintű felügyeleti csoportba. Hatókör kiválasztásához kattintson a háttérre, és válassza ki a kívánt elemet, majd kattintson az alul található **kijelölés** elemre. A beágyazott hatókörök, például az előfizetéshez tartozó erőforráscsoportok részletezéséhez kattintson a hatókör neve hivatkozásra. Ha a szülő hatókört bármely beágyazott szinten szeretné kijelölni, kattintson a **&lt;hatókör kiválasztása&gt;** elemre a hatókör-választó tetején.

## <a name="identify-the-resource-id-for-a-scope"></a>Hatókör erőforrás-AZONOSÍTÓjának azonosítása

Cost Management API-k használatakor a hatókör ismerete kritikus fontosságú. A következő információk segítségével hozza létre a megfelelő hatókörű URI-t Cost Management API-khoz.

### <a name="billing-accounts"></a>Számlázási fiókok

1. Nyissa meg a Azure Portal, majd a szolgáltatások listájában keresse meg **Cost Management + számlázás** lehetőséget.
2. A Számlázási fiók menüben válassza a **Tulajdonságok** lehetőséget.
3. Másolja a Számlázási fiók AZONOSÍTÓját.
4. A hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Számlázási profilok

1. Nyissa meg a Azure Portal, majd a szolgáltatások listájában keresse meg **Cost Management + számlázás** lehetőséget.
2. Válassza a **Számlázási profilok** lehetőséget a Számlázási fiók menüben.
3. Kattintson a kívánt számlázási profil nevére.
4. A számlázási profil menüben válassza a **Tulajdonságok** lehetőséget.
5. Másolja a számlázási fiókot és a számlázási profil azonosítóit.
6. A hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Számlaszakaszok

1. Nyissa meg a Azure Portal, majd a szolgáltatások listájában keresse meg **Cost Management + számlázás** lehetőséget.
2. A Számlázási fiók menüben válassza a **számla csoportok** lehetőséget.
3. Kattintson a kívánt számla szakasz nevére.
4. A számla szakasz menüjében válassza a **Tulajdonságok** lehetőséget.
5. Másolja a Számlázási fiók és a számla szakasz azonosítóit.
6. A hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-részlegek

1. Nyissa meg a Azure Portal, majd a szolgáltatások listájában keresse meg **Cost Management + számlázás** lehetőséget.
2. A Számlázási fiók menüben válassza a **részlegek** lehetőséget.
3. Kattintson a kívánt részleg nevére.
4. A részleg menüben válassza a **Tulajdonságok** lehetőséget.
5. Másolja a számlázási fiókot és a részleg azonosítóit.
6. A hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA regisztrációs fiók

1. Nyissa meg a Azure Portal, és a szolgáltatások listájában navigáljon a **Cost Management + számlázás** elemre.
2. A Számlázási fiók menüben válassza a **beléptetési fiókok** elemet.
3. Kattintson a kívánt beléptetési fiók nevére.
4. A beléptetési fiók menüben válassza a **Tulajdonságok** lehetőséget.
5. Másolja a számlázási fiókot és a beléptetési fiók azonosítóit.
6. A hatóköre: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Felügyeleti csoport

1. Nyissa meg a Azure Portal, és a szolgáltatások listájában navigáljon a **felügyeleti csoportokhoz** .
2. Navigáljon a kívánt felügyeleti csoporthoz.
3. Másolja a felügyeleti csoport AZONOSÍTÓját a táblából.
4. A hatóköre: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Előfizetés

1. Nyissa meg a Azure Portal, és navigáljon a szolgáltatások listájában lévő **előfizetésekhez** .
2. Másolja az előfizetés-azonosítót a táblából.
3. A hatóköre: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Erőforráscsoportok

1. Nyissa meg a Azure Portal, és navigáljon a szolgáltatások listájában lévő **erőforráscsoportok** között.
2. Kattintson a kívánt erőforráscsoport nevére.
3. Az erőforráscsoport menüben válassza a **Tulajdonságok** lehetőséget.
4. Másolja ki az erőforrás-azonosító mező értékét.
5. A hatóköre: `"/subscriptions/{id}/resourceGroups/{name}"`

A Cost Management jelenleg az [Azure globális](https://management.azure.com) és [Azure Government](https://management.usgovcloudapi.net)támogatja. További információ a Azure Governmentről: [Azure globális és kormányzati API-végpontok](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Következő lépések

- Ha még nem végezte el a Cost Management első gyors útmutatóját, olvassa el a [költségeket](quick-acm-cost-analysis.md).
