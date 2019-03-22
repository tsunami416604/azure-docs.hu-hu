---
title: Megismerheti, és az Azure Cost Management hatókörök kezelése |} A Microsoft Docs
description: Ez a cikk segít megérteni a számlázás és a resource management hatókörök elérhető az Azure és a hatóköröket a Cost Management és az API-k használata.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 4e7956e8873b552fcd73c51a51f51d99f21af324
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002933"
---
# <a name="understand-and-work-with-scopes"></a>A hatókörök ismertetése és használata

Ez a cikk segít megérteni a számlázás és a resource management hatókörök elérhető az Azure és a hatóköröket a Cost Management és az API-k használata.

## <a name="scopes"></a>Hatókörök

A _hatókör_ a csomópont, az Azure-erőforrás hierarchiában, ahol az Azure AD-felhasználók elérésére és a szolgáltatások kezeléséhez. A legtöbb Azure-erőforrások létrehozása és üzembe helyezve az erőforráscsoportok, előfizetések részét alkotják. Microsoft Azure-előfizetések számlázási adatok felügyelendő szerepköröket speciális rendelkezik felett két hierarchia által:
- Számlázási adatok, például a kifizetések és számlák
- Felhőszolgáltatások, például a költségek és a házirend irányítási

Hatókörök: ahol, számlázási adatok kezelésére, szerepkörök egyedi kifizetéseket, számlák megtekintése és viselkedési szabályzattal az általános felhasználóifiók-kezelés. Számlázási és fiókkezelési szerepkörök az erőforrás-kezelést, amelyek külön kezelt [Azure RBAC](../role-based-access-control/overview.md). Egyértelműen különbséget tenni a szándéka a különálló hatóköröket, access control különbségek, többek között ezeket nevezzük _hatókörök számlázási_ és _RBAC hatókörök_, illetve.

## <a name="how-cost-management-uses-scopes"></a>Hogyan használja a Cost Management a hatókörök

A Cost Management működik minden hatóköröket, hogy a szervezetek számára, hogy a szintjén, amelyen ők is hozzáférhetnek, költségek kezelésére, akár a teljes számlázási fiókra vagy egyetlen erőforrás felett. Számlázási hatókör mennyiségén alapulnak a Microsoft-szerződés (előfizetés-típus), az RBAC-hatókörök viszont nem.

## <a name="azure-rbac-scopes"></a>Az Azure RBAC-hatókörök

Az Azure erőforrás-kezelés három hatókört támogatja. Minden hatókör kezelése hozzáférési és cégirányítási, beleértve a támogatja, de nem Költségkezelés.

- [**Felügyeleti csoportok** ](../governance/management-groups/index.md) -hierarchikus tárolók, legfeljebb nyolc szint, az Azure-előfizetések rendezéséhez.

    Erőforrás típusa: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Előfizetések** -elsődleges tárolók az Azure-erőforrásokhoz.

    Erőforrás típusa: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Erőforráscsoportok** ](../azure-resource-manager/resource-group-overview.md#resource-groups) – logikai csoportosításán, amelyek azonos életciklussal Azure-megoldásokhoz kapcsolódó erőforrásokat. Például erőforrások üzembe helyezett és törölt együtt.

    Erőforrás típusa: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Felügyeleti csoportok lehetővé teszik előfizetéseinek hierarchiába. Például előfordulhat, hogy hozzon létre egy logikai szervezeti hierarchia felügyeleti csoportok használatával. Ezt követően adjon csapatok előfizetések az éles és a fejlesztési/tesztelési feladatokhoz. És erőforráscsoportok hozhatók létre az előfizetések kezeléséhez minden alrendszer vagy összetevő.

Létrehozásához, egy szervezeti hierarchia lehetővé teszi a költségek és a szabályzatoknak való megfelelés összesítő a szervezet által. Ezután minden egyes vezető megtekintheti és elemezheti az aktuális költségekkel. És ezután akadályozhatják hibás költségkeret mintákat, és optimalizálhatja a költségeket az Advisor-javaslatok a legalacsonyabb szintű költségvetése hozhatnak létre.

Költségek megtekintése, és igény szerint kezelheti a költségek konfigurációját, például a költségvetés és a kivitel, hozzáférés biztosítása a cégirányítási hatókörök Azure RBAC használatával történik. Azure RBAC használatával adja meg az Azure AD-felhasználók és csoportok érhetik el előre meghatározott szerepkör alatt és a egy adott hatókörhöz definiált műveletek végrehajtásához. Például egy felügyeleticsoport-hatókör rendelt is szerepkörök ugyanazokkal az engedélyekkel beágyazott előfizetésekhez és erőforráscsoportokhoz.

A Cost Management a következő beépített szerepkörök támogatja a következő hatókörök mindegyike esetében:

- [**Tulajdonos** ](../role-based-access-control/built-in-roles.md#owner) – megtekintheti a költségek és mindent felügyelhetnek, beleértve költség.
- [**Közreműködői** ](../role-based-access-control/built-in-roles.md#contributor) – megtekintheti a költségek és mindent felügyelhetnek, beleértve költség, de a hozzáférés-vezérlés kivételével.
- [**Olvasó** ](../role-based-access-control/built-in-roles.md#reader) –, aki mindent megtekinthet, többek között a költségadatok és a konfigurációt, de nem módosíthatja.
- [**Cost Management közreműködői** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) – költségek megtekintheti és kezelheti a költségek konfigurálása.
- [**Cost Management olvasó** ](../role-based-access-control/built-in-roles.md#cost-management-reader) – költségadatok és konfigurációs megtekintését teszi lehetővé.

A Cost Management közreműködői olyan ajánlott minimális jogosultságon alapuló szerepkör. Személyek hozhat létre és kezelhet költségvetése hozzáférést tesz lehetővé, és exportálja az további hatékony figyelése és jelentések, a költségek. Cost Management közreműködők is szükség lehet további szerepkörök teljes körű cost management helyzetek feltételeinek megteremtésére. Vegye figyelembe a következő esetekben:

- **Intézkedjen költségvetése túllépése esetén** – felügyeleti közreműködőkkel is hozzáférhetnek a létrehozása és/vagy Műveletcsoportok túlhasználattal automatikusan reagálni kezelése. Fontolja meg a jegykiadó [közreműködő figyelése](../role-based-access-control/built-in-roles.md#monitoring-contributor) egy erőforráscsoportot, amely tartalmazza a műveletcsoport szeretne használni, amikor a költségvetés küszöbérték túllépése. Bizonyos műveletek automatizálásához szükséges további szerepkörök használja, mint például az Automation és az Azure Functions megadott konkrét szolgáltatások esetében.
- **Adatok exportálása költség ütemezés** – felügyeleti közreműködőkkel is kell ütemezni másolhat adatokat egy tárfiókba történő exportálását a storage-fiókok kezelése a hozzáférést. Fontolja meg a jegykiadó [Tárfiók-közreműködő](../role-based-access-control/built-in-roles.md#storage-account-contributor) egy erőforráscsoportba, amely tartalmazza a tárolási fiók, ahol a költségadatok van exportálva.
- **Megtekintés, költségtakarékos javaslatok** – Cost Management olvasó és közreműködő nincs hozzáférése javaslatok alapértelmezés szerint. Javaslatok a hozzáférést az egyes erőforrások olvasási hozzáférésre van szüksége. Fontolja meg a jegykiadó [olvasó](../role-based-access-control/built-in-roles.md#reader) vagy egy [szolgáltatásspecifikus szerepkör](../role-based-access-control/built-in-roles.md#built-in-role-descriptions).

## <a name="enterprise-agreement-scopes"></a>Nagyvállalati Szerződés hatókörök

Nagyvállalati Szerződés (EA) számlázási fiókok regisztrációk, más néven a következő hatókörök rendelkezik:

- [**Számlázási fiók** ](../billing/billing-view-all-accounts.md) – EA-regisztrációhoz jelöli. Ebben a hatókörben számlákat jönnek létre. Beszerzés, amelyek nem használatalapú, például a Marketplace-en és a fenntartás, csak érhetők el ebben a hatókörben. Azok a részlegek vagy regisztrációs fiókok nem jelöli.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Részleg** – nem kötelező regisztrációs fiókok csoportosítása.

    Erőforrás típusa: `Billing/billingAccounts/departments`

- **Eszközregisztráció-fiók** – egyetlen fióktulajdonos jelöli. Nem támogatja a több személynek való hozzáférés biztosítása.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Bár a cégirányítási hatókörök egyetlen címtárban vannak kötve, nagyvállalati szerződés számlázási hatókörök nem. Egy nagyvállalati szerződés számlázási fiók tetszőleges mennyiségű Azure AD-címtár előfizetéssel is rendelkezik.

Nagyvállalati szerződés számlázási hatókörök a következő szerepkörök támogatják:

- **Vállalati rendszergazda** – felügyelheti számlázási fiók beállításait, és hozzáférést, megtekintheti az összes költséget, és kezelheti a költségek konfigurálása. Ha például a környezetet, és exportálja. A függvényben, a nagyvállalati szerződés számlázási hatókör megegyezik a [Cost Management közreműködői Azure RBAC szerepkör](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Vállalati olvasási jogosultsággal rendelkező felhasználó** – számlázási fiók beállításait, a költségadatok és a költségek konfigurációs megtekintheti. Ha például a környezetet, és exportálja. A függvényben, a nagyvállalati szerződés számlázási hatókör pedig ugyanaz, mint a [Cost Management olvasó Azure RBAC szerepkör](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Részleg rendszergazdai** – is részleg beállítások, például a költséghely, kezelése és eléréséhez, megtekintheti összes költséget, és költség konfigurációjának kezelése. Ha például a környezetet, és exportálja.  A **DA költségek megtekintése** számlázási fiók beállítását engedélyezni kell a részleg rendszergazdái és az írásvédett felhasználó megtekintheti a költségek. Ha **DA költségek megtekintése** van le van tiltva, szervezeti egység felhasználók nem látják költségek bármilyen szinten, akkor is, ha egy fiók vagy előfizetés tulajdonosa.
- **Részleg olvasási jogosultsággal rendelkező felhasználó** – részleg beállításait, a költségadatok és a költségek konfigurációs megtekintheti. Ha például a környezetet, és exportálja. Ha **DA költségek megtekintése** van le van tiltva, szervezeti egység felhasználók nem látják költségek bármilyen szinten, akkor is, ha egy fiók vagy előfizetés tulajdonosa.
- **Fióktulajdonos** – is regisztrációs fiók beállításait (például költségközpont), megtekintheti az összes költséget, valamint kezelheti és költség konfigurációjáról (például költségvetés és a kivitel) a regisztrációs fiókhoz. A **AO költségek megtekintése** számlázási fiók beállítását engedélyezni kell a fióktulajdonosok és RBAC felhasználók költségek megtekintéséhez.

Nagyvállalati szerződés számlázási fiók felhasználók nincsenek számlák közvetlen hozzáférést. A rendszer egy külső mennyiségi számlákat érhetők el.

Az Azure-előfizetések a regisztrációs fiókok beágyazott. Számlázási felhasználók hozzáférhetnek a költségadatok az előfizetések és erőforráscsoportok, amely alatt a megfelelő hatókörök. Tekintse meg vagy kezelheti az erőforrásokat az Azure Portalon való hozzáférés nem rendelkeznek. A számlázás felhasználók megtekinthetik a költségek az **Költségkezelés + számlázás** szolgáltatások az Azure portal listájában. Ezt követően azok szűrheti a meghatározott előfizetések és -erőforráscsoportok kell jelentse a költségek.

Számlázási felhasználóknak nem kell hozzáférést a felügyeleti csoportokhoz, mert ezek nem explicit módon tartoznak egy adott számlázási fiók. Hozzáférés rendelkeznie kell a felügyeleti csoportok explicit módon. Felügyeleti csoportok összesítő költségeket az összes beágyazott előfizetésből. Azonban használatalapú vásárlások csak tartalmaznak. Például a foglalásokat és a külső Piactéri ajánlatok elérhetőségének vásárlások azok nem tartalmaznak. Ezeket a díjakat megtekintéséhez használja a nagyvállalati szerződés számlázási fiók.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Az egyes megállapodás (használatalapú fizetés) hatókörök

(PAYG) használatalapú fizetéses előfizetéseket, beleértve az kapcsolódó típusok, például az ingyenes vagy próbaverziója, és fejlesztési/tesztelési ajánlatok, nem kell explicit számlázási fiók hatókör. Ehelyett minden előfizetési csomaghoz tartozik egy fiók tulajdonosa vagy a fiókadminisztrátor, például a nagyvállalati szerződéssel rendelkező fiók tulajdonosától.

- [**Számlázási fiók** ](../billing/billing-view-all-accounts.md) – egy vagy több Azure-előfizetésekre vonatkozó egyetlen fióktulajdonos jelöli. Ez jelenleg nem támogatja a több személynek és összesített költsége nézetekhez való hozzáférés való hozzáférés biztosítása.

    Erőforrás típusa: Nem alkalmazható

Használatalapú előfizetés fiók-Adminisztrátorok megtekintheti és kezelheti a számlázási adatokat, például a számlákat és kifizetéseket, az a [Azure Account Center](https://account.azure.com/subscriptions). Nem lehet azonban, költségadatok megtekintése, vagy kezelheti az erőforrásokat az Azure Portalon. A fiókadminisztrátor hozzáférést, a Cost Management szerepkörök azt korábban említettük, használhatók.

Használatalapú előfizetés fiók-Adminisztrátorok nagyvállalati szerződéssel rendelkező, ellentétben a számláikat, az Azure Portalon látható. Ne feledje, hogy a Cost Management olvasó és a Cost Management közreműködői szerepkör nem ad meg a számlákat való hozzáférést. További információkért lásd: [PAYG számla hozzáférés biztosításának módja](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Ügyfél szerződés hatókörök

A Microsoft Ügyfélszerződéséhez számlázási fiókok rendelkeznek a következő hatókörök:

- **Számlázási fiók** – több Microsoft-termékek és szolgáltatások egy ügyfélszerződéséhez jelöli. Ügyfél szerződés számlázási fiókok nem funkcionálisan ugyanaz, mint a nagyvállalati szerződéses regisztrációval. Nagyvállalati szerződéses regisztrációval számlázási profilok jobban igazodik.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **A számlázás profil** – határozza meg az előfizetést, egy számla szerepelnek. Számlázási profilok felelnek meg működési, egy regisztrált Nagyvállalati szerződéshez, mert ez a hatókör, amely a számlákat, jönnek. Ehhez hasonlóan, amelyek nem a használat alapú (például a Marketplace-en és foglalások) vásárlások csak érhetők el ebben a hatókörben. Számla szakaszok azokat nem tartalmazza.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts/billingProfiles`

- **A szakasz a számlához** -előfizetések, a számla vagy számlázási profilja csoportja. Számla szakaszok hasonlóak a részlegek – több ember férhet hozzá egy számla szakaszt.

    Erőforrás típusa: `Microsoft.Billing/billingAccounts/invoiceSections`

Ellentétben a nagyvállalati szerződés számlázási hatókörök, számlázási fiókok Ügyfélszerződéséhez _vannak_ egyetlen címtárban kötve, és nem lehet előfizetések több Azure AD-címtár között.

Ügyfél szerződés számlázási hatókörök a következő szerepkörök támogatják:

- **Tulajdonos** – számlázási beállítások és hozzáférés kezelése, megtekintheti az összes költségeit, és költség konfigurációjának kezelése. Ha például a környezetet, és exportálja. A függvényben, a hatókör számlázási Ügyfélszerződéséhez pedig ugyanaz, mint a [Cost Management közreműködői Azure RBAC szerepkör](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Közreműködői** – hozzáférés kivételével számlázási beállítások kezelése, megtekintheti az összes költségek, és költség konfigurációjának kezelése. Ha például a környezetet, és exportálja. A függvényben, a hatókör számlázási Ügyfélszerződéséhez pedig ugyanaz, mint a [Cost Management közreműködői Azure RBAC szerepkör](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Olvasó** – számlázási beállításait, a költségadatok és a költségek konfigurációs megtekintheti. Ha például a környezetet, és exportálja. A függvényben, a hatókör számlázási Ügyfélszerződéséhez pedig ugyanaz, mint a [Cost Management olvasó Azure RBAC szerepkör](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Számla manager** – megtekintheti és fizet a számlákat és is adatot és konfigurációs megjelenítéséhez. Ha például a környezetet, és exportálja. A függvényben, a hatókör számlázási Ügyfélszerződéséhez pedig ugyanaz, mint a [Cost Management olvasó Azure RBAC szerepkör](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-előfizetés létrehozója** – az Azure-előfizetések létrehozása, megtekintheti költségeit, és költség konfigurációjának kezelése. Ha például a környezetet, és exportálja. A függvényben a Ügyfélszerződéséhez számlázási hatóköre ugyanaz, mint az EA regisztrációs fiók tulajdonosi szerepkör.

Az Azure-előfizetések beágyazott alatt számla szakaszok, például hogyan EA regisztrációs fiókok alatt van. Számlázási felhasználók hozzáférhetnek a költségadatok az előfizetések és erőforráscsoportok, amelyek alapján a megfelelő hatókörök. Tekintse meg vagy kezelheti az erőforrásokat az Azure Portalon való hozzáférés nem rendelkeznek. A számlázás felhasználók megtekinthetik a költségek az **Költségkezelés + számlázás** szolgáltatások az Azure portal listájában. Szűrjön a meghatározott előfizetések és -erőforráscsoportok kell jelentse a költségek.

Számlázási felhasználóknak nem kell hozzáférést a felügyeleti csoportokhoz, mivel ezek az explicit módon nem tartoznak a számlázási fiók alatt. Azonban ha a szervezet számára engedélyezve vannak a felügyeleti csoportokhoz, minden előfizetési díjat vannak összegzett számlázási fiókjához, és a legfelső szintű felügyeleti csoporthoz, mivel azok is tanúsította, egyetlen címtárban. Felügyeleti csoportok csak a vásárlásokat, amelyek a használat alapú tartalmazzák. Például a foglalásokat és a külső Piactéri ajánlatok elérhetőségének vásárlások felügyeleti csoportok nem szerepelnek. Így a számlázási fiók és a legfelső szintű felügyeleti csoport különböző összegek feltétlenül jelentik. Ezeket a díjakat megtekintéséhez használja a számlázási fiókot vagy megfelelő számlázási profilja.

## <a name="cloud-solution-provider-csp-scopes"></a>Cloud Solution Provider (CSP) hatókörök

Cloud Solution Provider (CSP) partner nem támogatottak a Cost Management még ma. Ehelyett használhat [Partnerközpont](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Váltás a Cost Management hatókörök

Az Azure Portalon a Cost Management minden nézetek az alábbiak egy **hatókör** megszámlálásához, a bal felső a nézetet. Segítségével gyorsan a hatókör módosítása. Kattintson a **hatókör** megszámlálásához a Hatókörválasztó megnyitásához. Azt jeleníti meg a számlázási fiókok, a legfelső szintű felügyeleti csoport és a gyökérszintű felügyeleti csoport nem beágyazva foglalt olyan előfizetéseket. A hatókör kiválasztásához kattintson a kiemelés, és kattintson a háttér **kiválasztása** alján. Részletezés a beágyazott hatókörökre, például az erőforráscsoportok található egy előfizetésben, kattintson a hatókör neve hivatkozásra. A szülő hatókörnek bármely beágyazott szinten kiválasztásához kattintson **válassza ezt &lt;hatókör&gt;**  a Hatókörválasztó tetején.

## <a name="identify-the-resource-id-for-a-scope"></a>Az erőforrás-azonosítója egy hatókör azonosítása

A Cost Management API-k használatakor, hogy a hatókör fontos. Az alábbi információk segítségével hozhat létre a megfelelő hatókörben URI-t a Cost Management API-k.

### <a name="billing-accounts"></a>Számlázási fiókok

1. Nyissa meg az Azure Portalt, és navigáljon arra **Költségkezelés + számlázás** a szolgáltatások listájában.
2. Válassza ki **tulajdonságok** számlázási fiók menüjében.
3. Másolja ki a számlázási fiók azonosítóját.
4. A hatókör van: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Számlázási profilok

1. Nyissa meg az Azure Portalt, és navigáljon arra **Költségkezelés + számlázás** a szolgáltatások listájában.
2. Válassza ki **számlázási profilok** számlázási fiók menüjében.
3. Kattintson a kívánt számlázási profil nevét.
4. Válassza ki **tulajdonságok** a számlázási profil menüben.
5. Másolja át a számlázási fiók és számlázás profil azonosítók.
6. A hatókör van: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Számlaszakaszok

1. Nyissa meg az Azure Portalt, és navigáljon arra **Költségkezelés + számlázás** a szolgáltatások listájában.
2. Válassza ki **szakaszok számlázása** számlázási fiók menüjében.
3. Kattintson a kívánt számlázási szakasz nevét.
4. Válassza ki **tulajdonságok** a számla szakasz menüben.
5. Készítsen másolatot a számlázási fiók, és a számla szakasz azonosítók.
6. A hatókör van: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Nagyvállalati szerződéssel rendelkező szervezeti egységek

1. Nyissa meg az Azure Portalt, és navigáljon arra **Költségkezelés + számlázás** a szolgáltatások listájában.
2. Válassza ki **részlegek** számlázási fiók menüjében.
3. Kattintson a kívánt szervezeti egység nevére.
4. Válassza ki **tulajdonságok** részleg menüjében.
5. Másolja át a számlázási fiók és a részleg azonosítókat.
6. A hatókör van: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA regisztrációs fiók

1. Nyissa meg az Azure Portalon, és navigáljon a **Költségkezelés + számlázás** a szolgáltatások listájában.
2. Válassza ki **regisztrációs fiókok** számlázási fiók menüjében.
3. Kattintson a kívánt eszközregisztráció-fiók nevére.
4. Válassza ki **tulajdonságok** a regisztrációs fiók menüjében.
5. Másolja a számlázási fiók és a regisztrációs fiók azonosítói.
6. A hatókör van: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Felügyeleti csoport

1. Nyissa meg az Azure Portalon, és navigáljon a **felügyeleti csoportok** a szolgáltatások listájában.
2. Keresse meg a kívánt felügyeleti csoport.
3. Másolja a felügyeleti csoport azonosítója a táblából.
4. A hatókör van: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Előfizetés

1. Nyissa meg az Azure Portalon, és navigáljon a **előfizetések** a szolgáltatások listájában.
2. Másolja az előfizetés-Azonosítót a táblából.
3. A hatókör van: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Erőforráscsoportok

1. Nyissa meg az Azure Portalon, és navigáljon a **erőforráscsoportok** a szolgáltatások listájában.
2. Kattintson a kívánt erőforráscsoport nevét.
3. Válassza ki **tulajdonságok** csoport erőforrás menüben.
4. Másolja ki az erőforrást azonosító mező értékét.
5. A hatókör van: `"/subscriptions/{id}/resourceGroups/{name}"`

A Cost Management jelenleg támogatott [Azure globális](https://management.azure.com) és [Azure Government](https://management.usgovcloudapi.net). Az Azure Government kapcsolatos további információkért lásd: [Azure globális és a kormányzati API végpontok](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_._

## <a name="next-steps"></a>További lépések

- Ha még nem elvégezte már az első rövid Költségkezelés, olvassa el a [elemezheti a költségek](quick-acm-cost-analysis.md).
