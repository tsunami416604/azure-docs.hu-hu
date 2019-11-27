---
title: Gyakori kérdések az Azure a Cloudyn |} A Microsoft Docs
description: Ez a cikk ismerteti a Cloudyn kapcsolatos gyakori kérdésekre adott válaszokat.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: troubleshooting
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: faa7181ebc7b886fbe06e4ac01c704016d4b0c51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230100"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>A Cloudyn kapcsolatos gyakori kérdések

Ez a cikk foglalkozik a Cloudyn kapcsolatos gyakori kérdésekre. Ha kérdése van a Cloudyn kapcsolatban, megkérheti őket a [Cloudyn kapcsolatos gyakori kérdések](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn)között.

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hogyan oldhatom fel a közvetett nagyvállalati telepítő kapcsolatos gyakori problémák?

Amikor először használja a Cloudyn portálját, a következő üzenetek jelenhetnek meg, ha Ön Nagyvállalati szerződést kötött, vagy egy felhőszolgáltató felhasználója:

- "A megadott API-kulcs nem a legfelső szintű beléptetési kulcs" üzenet jelenik meg a **Cloudyn beállítása** varázslóban.
- "A közvetlen regisztráció – nem" a nagyvállalati szerződés portálon jelenik meg.
- "Nem használati adatok az elmúlt 30 napban nem található. A Cloudyn portálon lépjen kapcsolatba a terjesztőt, hogy ellenőrizze, hogy jelölőnyelvi engedélyezve lett, az Azure-fiók"jelenik meg.

Az előző üzenetek arra utalnak, hogy egy viszonteladón vagy felhőszolgáltatón keresztül vásárolt Azure Nagyvállalati szerződést. A viszonteladónak vagy a felhőszolgáltatónak kell engedélyeznie a _korrektúrát_ az Azure-fiókjában, mielőtt Ön megtekinthetné az adatait a Cloudyn szolgáltatásban.

A problémák megoldása:

1. A viszonteladónak engedélyeznie kell a _korrektúrát_ a fiókjában. Útmutatás: [Közvetett ügyfeleknek szóló előkészítési útmutató](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Létre kell hoznia egy kulcsot az Azure Nagyvállalati Szerződésben, amelyet a Cloudynnel használhat. Útmutatásért lásd: [Az Azure EA hozzáadása](quick-register-ea.md#register-with-cloudyn) vagy [az EA regisztrációs AZONOSÍTÓjának és API-kulcsának megkeresése](https://youtu.be/u_phLs_udig).

A Cloudyn szolgáltatást csak egy Azure-szolgáltatásadminisztrátor engedélyezheti. A társadminisztrátori jogosultság ehhez nem elegendő.

A Cloudyn beállításához az Azure Nagyvállalati Szerződés API-kulcsának létrehozása előtt engedélyeznie kell az Azure számlázási API-t a következő útmutató követésével:

- [Jelentéskészítő API-k Enterprise-ügyfeleknek – áttekintés](../billing/billing-enterprise-api.md)
- [Microsoft Azure Enterprise Portal jelentéskészítő API](https://ea.azure.com/helpdocs/reportingAPI) **Az adatok az API-hoz való hozzáférésének engedélyezése** területen


Előfordulhat, hogy a részlegek rendszergazdáinak, a fióktulajdonosoknak és a nagyvállalati rendszergazdáknak is engedélyt kell adnia a _díjak megtekintéséhez_ a számlázási API segítségével.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Miért nem látom, hogy optimalizáló javaslatok?

Az ajánlás információk csak aktív fiókok érhetők el. A nem *aktivált*fiókok esetében nem jelenik meg javaslati információ a **optimalizáló** jelentési kategóriákban, beleértve a következőket:

- Optimalizálás Manager
- Méretezés optimalizálása
- Hatékonysági

Ha bármely optimalizáló javaslat adatai nem tekinthetők meg, akkor nagy valószínűséggel rendelkezik fiókok, amelyek időközét adja meg. Egy fiók aktiválásához szüksége regisztrálja az Azure hitelesítő adatait.

Egy fiók aktiválása:

1.  A Cloudyn portálon kattintson a **Settings** (Beállítások) gombra a jobb felső sarokban, és válassza a **Cloud Accounts** (Felhőbeli fiókok) lehetőséget.
2.  A Microsoft Azure fiókok lapon keressen olyan fiókokat, amelyek nem **aktivált** előfizetéssel rendelkeznek.
3.  A nem aktivált fióktól jobbra kattintson a ceruzára hasonlító **Szerkesztés** szimbólumra.
4.  A bérlő azonosítója és sebessége azonosítója automatikusan észleli. Kattintson a **Tovább** gombra.
5.  A program átirányítja az Azure Portalon. Jelentkezzen be a portálra, és engedélyezze a Cloudyn gyűjtő el az Azure-beli adatait.
6.  Ezután a rendszer átirányítja a Cloudyn accounts Management lapra, és az előfizetését **aktív** fiók állapottal frissíti. Egy zöld pipa szimbólumot jeleníti meg.
7.  Ha egy zöld pipa szimbólumot egy vagy több előfizetés nem jelenik meg, az azt jelenti, hogy egy levélolvasó alkalmazás (CloudynCollector) az előfizetés létrehozásához szükséges engedélyek nem rendelkeznek. Egy magasabb szintű jogosultságokkal rendelkező felhasználónak kell ismételje meg a 3. és 4.  

Miután elvégezte az előző lépéseket, megtekintheti a optimalizáló javaslatok egy-két napon belül. Azonban mielőtt érhető el teljes optimalizálási adatok legfeljebb öt nappal is igénybe vehet.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hogyan engedélyezhetem a felfüggesztett vagy zárolt felhasználók?

Először nézzük meg a leggyakoribb forgatókönyvet, amely a felhasználói fiókok *initiallySuspended*beolvasását okozza.

> A Rendszergazda1 lehet Microsoft Cloud-megoldás szolgáltatója vagy Nagyvállalati Szerződés felhasználó. A szervezet készen áll a Cloudyn használatának megkezdésére.  Ő regisztrálja az Azure Portalon keresztül, és bejelentkezik a Cloudyn portálon. Ahogy a Cloudyn szolgáltatást regisztrálja, és bejelentkezik a Cloudyn-portálra, a Rendszergazda1 lesz az *elsődleges rendszergazda*. Rendszergazda1 nem hoz létre az összes felhasználói fiókot. A Cloudyn-portál használatával azonban Azure-fiókokat hozhat létre, és beállíthat egy entitás-hierarchiát. A Rendszergazda1 tájékoztatja a admin2, a bérlői rendszergazdától, hogy regisztrálniuk kell a Cloudyn, és be kell jelentkezniük a Cloudyn portálra.
>
> Admin2 regisztrálja az Azure Portalon keresztül. Azonban amikor megpróbálnak bejelentkezni a Cloudyn-portálra, hibaüzenetet kapnak, amely azt jelzi, hogy a fiókja fel van **függesztve**. Az elsődleges rendszergazdai rendszergazda1, értesítést kap, a fiók felfüggesztését. A Rendszergazda1 aktiválni kell a Admin2's-fiókot, és biztosítania kell a *rendszergazdai entitások hozzáférését* a megfelelő entitásokhoz, és lehetővé teszi a felhasználói fiókok hozzáférését és az aktív felhasználói fiókot.


Riasztás, hozzáférés engedélyezése egy felhasználó kérést kap, ha a felhasználói fiók aktiválásához szüksége.

A felhasználói fiók aktiválása:

1. Jelentkezzen be a Cloudyn, amellyel a Cloudyn beállítása az Azure rendszergazdai felhasználói fiók használatával. Vagy jelentkezzen be egy felhasználói fiókot, amely rendszergazdai hozzáférést.
2. Válassza ki a fogaskerék szimbólumot a jobb felső sarokban, és válassza a **felhasználói kezelés**lehetőséget.
3. Keresse meg a felhasználót, és válassza ki a ceruzaikonra szerkesztheti a felhasználó.
4. A **felhasználó állapota**területen módosítsa az állapotot **felfüggesztve** értékről **aktívra**.

A Cloudyn felhasználói fiókokat létesíthetnek egyszeri bejelentkezés az Azure-ból. Ha egy felhasználó mistypes a jelszavát, akkor előfordulhat, hogy zárja ki magát a Cloudyn, annak ellenére, hogy továbbra is hozzáférhet az Azure.

Ha a Cloudyn az Azure-beli alapértelmezett címről módosítja az e-mail-címét, akkor a fiókja zárolva lesz. Ez a "Status initiallySuspended" jelenhet meg. Ha a felhasználói fiók zárolva van, forduljon a alaphelyzetbe állítja a fiók egy másik rendszergazda.

Azt javasoljuk, hogy legalább két Cloudyn rendszergazdai fiókokat hozhat létre abban az esetben az egyik fiók lekérdezi zárolva.

Ha nem tud bejelentkezni a Cloudyn portált, győződjön meg arról, hogy a helyes URL-címet használ a Cloudyn bejelentkezni. [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade)használata.

Ne használja a Cloudyn Direct URL-címet https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hogyan lehet aktiválni az Azure-beli hitelesítő időközét adja meg fiókokat?

Amint az Azure-fiókok a Cloudyn által felfedezett költségadatok azonnal nyújtott költség-alapú jelentésekhez. Azonban a Cloudyn használati és teljesítményadatokat biztosít, akkor regisztrálnia kell a fiókok Azure hitelesítő adatait. Útmutatásért lásd: [fiók hozzáadása vagy előfizetés frissítése](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Az Azure a hitelesítő adatait, a Cloudyn portálon hozzáadásához válassza a Szerkesztés szimbólum jobb oldalán a fiók neve, nem az előfizetés.

Amíg az Azure-beli hitelesítő adatait nem adja hozzá a Cloudyn-hez, a fiók nem _aktívként_jelenik meg.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hogyan adhatok hozzá több fiókhoz és entitások egy meglévő előfizetéshez?

További entitásokat segítségével további nagyvállalati szerződések hozzáadása a Cloudyn-előfizetéshez. További információ: [entitások létrehozása és kezelése](tutorial-user-access.md#create-and-manage-entities).

A CSP-k:

Ha további CSP-fiókokat szeretne hozzáadni egy entitáshoz, válassza az új entitás létrehozásakor az **MSP-hozzáférés** lehetőséget a **vállalat** helyett. Ha a fiók regisztrálva van, a nagyvállalati szerződés, és szeretne hozzáadni a CSP hitelesítő adatokat, a Cloudyn támogatási csoporthoz előfordulhat, hogy módosítania kell a fiók beállításait. Ha Ön egy fizetős Azure-előfizető, létrehozhat egy új támogatási kérelmet az Azure Portalon. Válassza a **Súgó + támogatás**lehetőséget, majd kattintson az **új támogatási kérelem**elemre.

## <a name="currency-symbols-in-cloudyn-reports"></a>A Cloudyn jelentésekben pénznemszimbólum

Előfordulhat, hogy különböző pénznemek használatával több Azure-fiókokon. A Cloudyn cost jelentések azonban ne jelenjen meg egynél több pénznem típusa jelentésenként.

Ha több előfizetéssel rendelkezik különböző pénznemek használatával, a szülő entitás és az alárendelt entitás pénzneme USD **$ban** jelenik meg. A javasolt ajánlott eljárás, hogy kerülje a különböző pénznemek entitás ugyanabban a hierarchiában. Más szóval egy entitás struktúrájából összes előfizetés az adott pénznemben kell használnia.

A Cloudyn automatikusan észleli a nagyvállalati szerződés előfizetés pénznem és mutat be megfelelően a jelentésekben.  A Cloudyn azonban csak az USD **$t** jeleníti meg a CSP és a web-Direct Azure-fiókok esetében.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Mik a Cloudyn adatok frissítése az ütemtervek?

A Cloudyn rendelkezik a következő adatok frissítési ütemtervet:

- **Kezdeti**: a beállítás után akár 24 óráig is eltarthat a költségadatok megtekintése a Cloudyn. A Cloudyn elegendő adatgyűjtéshez méretezési javaslatok megjelenítése akár 10 nap is is igénybe vehet.
- **Napi**: a tizedik naptól kezdve minden hónap végéig a Cloudyn az előző naptól számítva az UTC + 3 nap után a következő napon kell megjeleníteni az adatait.
- **Havonta**: az első naptól az egyes hónapok tizedik napjáig a Cloudyn csak az előző hónap végéig jelenítheti meg az adatokat.

A Cloudyn feldolgozza az adatokat az előző napi, amikor az előző nap adatainak teljes elérhetőnek kell lennie. Az előző nap adatainak a Cloudyn által kapcsolatos UTC + 3 általában érhető el minden nap. Egyes adatok, például címkék, feldolgozni további 24 órát is igénybe vehet.

Az aktuális hónaphoz adatok nem érhető el minden hónap elején a gyűjteményhez. Az időszakban a szolgáltatók azok az előző havi használati díjának felszámolása véglegesítése. Az előző hónap adatai jelennek meg a Cloudyn 5-10 nappal minden hónap kezdete után. Ebben az időszakban az előző hónap csak amortizált költségek merülhetnek fel. Ön nem láthatja a napi számlázási és használati adatokat. Amikor elérhetővé válik az adatok a Cloudyn, a feldolgozásuk visszamenőlegesen. A feldolgozás után a havi adatok között az ötödik és a tizedik minden hónap napja jelenik meg.

Ha az Azure-ból történő adatküldés a Cloudyn késés, adatok továbbra is keletkezik az Azure-ban. Az adatok átkerülnek a Cloudyn, ha a kapcsolat helyreáll.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Cloudyn-Költségjelentésekben ingadozása költség

Költségjelentésben költség ingadozások által megkövetelt jeleníti meg, amikor a felhőszolgáltatók számlázási frissített fájlok küldése. Változó költségű fordulhat elő, ha új fájlokat a felhőszolgáltató a szokásos módon kívül a napi és havi ütemezés reporting érkezik. A Cost módosítások Cloudyn újraszámítás nem eredménye.

A hónap során a felhőszolgáltató által küldött minden elszámolási fájlok a napi költségek becslése. Néha adatok gyakran frissülő – alkalmanként többször naponta. Frissítések állnak az AWS üzemezésnél gyakoribb Azure. Költség tekintetében stabil maradjon, ha az előző havi számlázási kiszámítása befejeződött, és a végső számlázási fájl érkezik. Általában a hónap 10.

Változások történnek, amikor költség korrekciók kapott a felhőszolgáltató. Egy példa a kreditek fogadására. Módosítások hónapok fordulhat elő a releváns hónap volt bezárása után. Módosítások jelennek meg, ha az újraszámítást végezni a felhőszolgáltató által. A Cloudyn frissíti az előzményadatok, győződjön meg arról, hogy újraszámít minden módosítását. Azt is ellenőrzi, hogy a költségeket pontosan a jelentést készít.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hogyan egy közvetlen CSP konfigurálja a Cloudyn hozzáférést közvetett CSP-ügyfelek és partnerek számára?

Útmutatásért lásd: [a közvetett CSP-hozzáférés konfigurálása a Cloudyn-ben](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Az optimalizáló menüpont jelennek meg, mi okozza?

A Azure Resource Manager-hozzáférés és az adatok összegyűjtése után meg kell jelennie az **optimalizálási** lehetőségnek. Azure Resource Manager hozzáférésének aktiválásához tekintse meg [a nem aktivált fiókok Azure-beli hitelesítő adatokkal](#how-do-i-activate-unactivated-accounts-with-azure-credentials) való aktiválásának Hogyanát ismertető témakört.

## <a name="is-cloudyn-agent-based"></a>A Cloudyn ügynök alapján?

Nem. Ügynökök nem használ. A Microsoft insights-beli API-beli virtuális gépek az Azure virtuális gép metrikaadatok összegyűjtött. Ha azt szeretné, az Azure virtuális gépek metrikai adatok gyűjtését, szükségük a diagnosztikai beállítások engedélyezve van.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Hajtsa végre a Cloudyn jelentésekben meg a jelentés egynél több AD-bérlő?

Igen. [Létrehozhat egy megfelelő felhőalapú fiók entitást](tutorial-user-access.md#create-and-manage-entities) minden olyan ad-bérlőhöz, amelyhez rendelkezik. Majd megtekintheti az összes Azure AD-bérlő adatait és más platform felhőszolgáltatók, például az Amazon Web Services és a Google Cloud Platform.
