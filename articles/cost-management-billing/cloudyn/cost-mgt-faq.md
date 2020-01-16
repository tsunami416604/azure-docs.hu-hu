---
title: Gyakori kérdések az Azure-beli Cloudyn-ről | Microsoft Docs
description: Ez a cikk a Cloudyn kapcsolatos gyakori kérdésekre ad választ.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: troubleshooting
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 70f5edfb186cb7a9c05fe8ee59533193cee4fcd4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990761"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Gyakori kérdések a Cloudyn

Ez a cikk a Cloudyn kapcsolatos gyakori kérdéseket tárgyalja. Ha kérdése van a Cloudyn kapcsolatban, megkérheti őket a [Cloudyn kapcsolatos gyakori kérdések](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn)között.

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hogyan oldhatók meg a nagyvállalati vállalatok általános telepítési problémái?

Amikor először használja a Cloudyn portálját, a következő üzenetek jelenhetnek meg, ha Ön Nagyvállalati szerződést kötött, vagy egy felhőszolgáltató felhasználója:

- "A megadott API-kulcs nem a legfelső szintű beléptetési kulcs" üzenet jelenik meg a **Cloudyn beállítása** varázslóban.
- "Közvetlen regisztráció – nem" jelenik meg a Nagyvállalati Szerződés-portálon.
- "Az elmúlt 30 napban nem találhatók használati adatok. Forduljon a forgalmazóhoz, és győződjön meg arról, hogy a Cloudyn-portálon megjelenő Azure-fiókhoz engedélyezve van-e a Markup.

Az előző üzenetek arra utalnak, hogy egy viszonteladón vagy felhőszolgáltatón keresztül vásárolt Azure Nagyvállalati szerződést. A viszonteladónak vagy a felhőszolgáltatónak kell engedélyeznie a _korrektúrát_ az Azure-fiókjában, mielőtt Ön megtekinthetné az adatait a Cloudyn szolgáltatásban.

A problémák megoldása:

1. A viszonteladónak engedélyeznie kell a _korrektúrát_ a fiókjában. Útmutatás: [Közvetett ügyfeleknek szóló előkészítési útmutató](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Létre kell hoznia egy kulcsot az Azure Nagyvállalati Szerződésben, amelyet a Cloudynnel használhat. Útmutatásért lásd: [Az Azure EA hozzáadása](quick-register-ea.md#register-with-cloudyn) vagy [az EA regisztrációs AZONOSÍTÓjának és API-kulcsának megkeresése](https://youtu.be/u_phLs_udig).

A Cloudyn szolgáltatást csak egy Azure-szolgáltatásadminisztrátor engedélyezheti. A társadminisztrátori jogosultság ehhez nem elegendő.

A Cloudyn beállításához az Azure Nagyvállalati Szerződés API-kulcsának létrehozása előtt engedélyeznie kell az Azure számlázási API-t a következő útmutató követésével:

- [Jelentéskészítő API-k Enterprise-ügyfeleknek – áttekintés](../manage/enterprise-api.md)
- [Microsoft Azure Enterprise Portal jelentéskészítő API](https://ea.azure.com/helpdocs/reportingAPI)**Az adatok az API-hoz való hozzáférésének engedélyezése** területen


Előfordulhat, hogy a részlegek rendszergazdáinak, a fióktulajdonosoknak és a nagyvállalati rendszergazdáknak is engedélyt kell adnia a _díjak megtekintéséhez_ a számlázási API segítségével.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Miért nem láthatók az optimalizáló javaslatai?

Az ajánlási információ csak az aktivált fiókok esetében érhető el. A nem *aktivált*fiókok esetében nem jelenik meg javaslati információ a **optimalizáló** jelentési kategóriákban, beleértve a következőket:

- Optimalizálási kezelő
- Méretezés optimalizálása
- Elégtelenségek

Ha nem tudja megtekinteni az egyes optimalizáló javaslatokat, akkor valószínűleg nincs aktiválva fiókja. Egy fiók aktiválásához regisztrálnia kell az Azure-beli hitelesítő adataival.

Fiók aktiválása:

1.  A Cloudyn portálon kattintson a **Settings** (Beállítások) gombra a jobb felső sarokban, és válassza a **Cloud Accounts** (Felhőbeli fiókok) lehetőséget.
2.  A Microsoft Azure fiókok lapon keressen olyan fiókokat, amelyek nem **aktivált** előfizetéssel rendelkeznek.
3.  A nem aktivált fióktól jobbra kattintson a ceruzára hasonlító **Szerkesztés** szimbólumra.
4.  A rendszer automatikusan észleli a bérlő AZONOSÍTÓját és a ráta AZONOSÍTÓját. Kattintson a **Tovább** gombra.
5.  A rendszer átirányítja a Azure Portalra. Jelentkezzen be a portálra, és engedélyezze a Cloudyn Collector számára az Azure-beli adatai elérését.
6.  Ezután a rendszer átirányítja a Cloudyn accounts Management lapra, és az előfizetését **aktív** fiók állapottal frissíti. Egy zöld pipa szimbólumot mutat be.
7.  Ha nem lát zöld pipa szimbólumot egy vagy több előfizetéshez, az azt jelenti, hogy nincs engedélye arra, hogy az előfizetéshez tartozó olvasó alkalmazást (CloudynCollector) hozzon létre. Az előfizetéshez magasabb engedélyekkel rendelkező felhasználónak kell megismételni a 3. és a 4. lépést.  

Az előző lépések elvégzése után egy-két napon belül megtekintheti az optimalizáló javaslatait. Azonban akár öt napig is eltarthat, amíg a teljes optimalizálási adat elérhetővé válik.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hogyan a felfüggesztett vagy kizárt felhasználók engedélyezése?

Először nézzük meg a leggyakoribb forgatókönyvet, amely a felhasználói fiókok *initiallySuspended*beolvasását okozza.

> A Rendszergazda1 lehet Microsoft Cloud-megoldás szolgáltatója vagy Nagyvállalati Szerződés felhasználó. A szervezet készen áll a Cloudyn használatának megkezdésére.  Regisztrálja a Azure Portal, és bejelentkezik a Cloudyn-portálra. Ahogy a Cloudyn szolgáltatást regisztrálja, és bejelentkezik a Cloudyn-portálra, a Rendszergazda1 lesz az *elsődleges rendszergazda*. A Rendszergazda1 nem hoz létre felhasználói fiókokat. A Cloudyn-portál használatával azonban Azure-fiókokat hozhat létre, és beállíthat egy entitás-hierarchiát. A Rendszergazda1 tájékoztatja a admin2, a bérlői rendszergazdától, hogy regisztrálniuk kell a Cloudyn, és be kell jelentkezniük a Cloudyn portálra.
>
> A admin2 regisztrálja a Azure Portal. Azonban amikor megpróbálnak bejelentkezni a Cloudyn-portálra, hibaüzenetet kapnak, amely azt jelzi, hogy a fiókja fel van **függesztve**. A Rendszergazda1 elsődleges rendszergazdája értesítést kap a fiók felfüggesztéséről. A Rendszergazda1 aktiválni kell a Admin2's-fiókot, és biztosítania kell a *rendszergazdai entitások hozzáférését* a megfelelő entitásokhoz, és lehetővé teszi a felhasználói fiókok hozzáférését és az aktív felhasználói fiókot.


Ha riasztást kap egy felhasználó hozzáférésének engedélyezésére vonatkozó kéréssel, aktiválnia kell a felhasználói fiókot.

A felhasználói fiók aktiválása:

1. Jelentkezzen be a Cloudyn-be a Cloudyn beállításához használt Azure rendszergazdai felhasználói fiók használatával. Vagy jelentkezzen be egy olyan felhasználói fiókkal, amely rendszergazdai hozzáférést kapott.
2. Válassza ki a fogaskerék szimbólumot a jobb felső sarokban, és válassza a **felhasználói kezelés**lehetőséget.
3. Keresse meg a felhasználót, válassza ki a ceruza szimbólumot, majd szerkessze a felhasználót.
4. A **felhasználó állapota**területen módosítsa az állapotot **felfüggesztve** értékről **aktívra**.

A Cloudyn felhasználói fiókjai az Azure-ból történő egyszeri bejelentkezéssel csatlakoznak. Ha a felhasználó elgépeli a jelszavát, előfordulhat, hogy kizárják a Cloudyn, még akkor is, ha azok továbbra is hozzáférhetnek az Azure-hoz.

Ha a Cloudyn az Azure-beli alapértelmezett címről módosítja az e-mail-címét, akkor a fiókja zárolva lesz. Ez a "Status initiallySuspended" jelenhet meg. Ha a felhasználói fiókja ki van zárva, forduljon egy másik rendszergazdához a fiók alaphelyzetbe állításához.

Javasoljuk, hogy hozzon létre legalább két Cloudyn rendszergazdai fiókot abban az esetben, ha az egyik fiók zárolva van.

Ha nem tud bejelentkezni a Cloudyn portálra, győződjön meg arról, hogy a megfelelő URL-címet használja a Cloudyn való bejelentkezéshez. [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade)használata.

Ne használja a Cloudyn Direct URL-címet https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hogyan aktiválja az Azure-beli hitelesítő adatokkal rendelkező nem aktivált fiókokat?

Amint az Azure-fiókokat a Cloudyn felderíti, a költségadatok azonnal bekerülnek a Cost-alapú jelentésekben. A használati és teljesítményadatokat biztosító Cloudyn azonban regisztrálnia kell a fiókokhoz tartozó Azure-beli hitelesítő adatait. Útmutatásért lásd: [fiók hozzáadása vagy előfizetés frissítése](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Ha Azure-beli hitelesítő adatokat szeretne hozzáadni egy fiókhoz, a Cloudyn-portálon válassza a fiók neve mellett jobbra található Szerkesztés szimbólumot, ne pedig az előfizetést.

Amíg az Azure-beli hitelesítő adatait nem adja hozzá a Cloudyn-hez, a fiók nem _aktívként_jelenik meg.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hogyan több fiókot és entitást is hozzáadhat egy meglévő előfizetéshez?

A további entitások további nagyvállalati szerződések Cloudyn-előfizetéshez való hozzáadására szolgálnak. További információ: [entitások létrehozása és kezelése](tutorial-user-access.md#create-and-manage-entities).

CSP-ket:

Ha további CSP-fiókokat szeretne hozzáadni egy entitáshoz, válassza az új entitás létrehozásakor az **MSP-hozzáférés** lehetőséget a **vállalat** helyett. Ha a fiókja Nagyvállalati Szerződés van regisztrálva, és hozzá kívánja adni a CSP hitelesítő adatait, előfordulhat, hogy a Cloudyn-támogatási személyzetnek módosítania kell a fiók beállításait. Ha fizetős Azure-előfizető, új támogatási kérést hozhat létre a Azure Portal. Válassza a **Súgó + támogatás**lehetőséget, majd kattintson az **új támogatási kérelem**elemre.

## <a name="currency-symbols-in-cloudyn-reports"></a>Pénznem szimbólumok a Cloudyn-jelentésekben

Több Azure-fiókkal is rendelkezhet különböző pénznemek használatával. A Cloudyn lévő Cost-jelentések azonban jelentésben nem jelenítik meg a pénznemek számát.

Ha több előfizetéssel rendelkezik különböző pénznemek használatával, a szülő entitás és az alárendelt entitás pénzneme USD **$ban** jelenik meg. Az ajánlott eljárás az, hogy ne használjon különböző pénznemeket ugyanabban az entitás-hierarchiában. Más szóval az entitások struktúrájába szervezett összes előfizetésnek ugyanazt a pénznemet kell használnia.

A Cloudyn automatikusan észleli a Nagyvállalati Szerződés előfizetés pénznemét, és helyesen mutatja be a jelentésekben.  A Cloudyn azonban csak az USD **$t** jeleníti meg a CSP és a web-Direct Azure-fiókok esetében.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Mik azok az Cloudyn-adatfrissítési ütemtervek?

A Cloudyn a következő adatfrissítési ütemtervekkel rendelkezik:

- **Kezdeti**: a beállítás után akár 24 óráig is eltarthat a költségadatok megtekintése a Cloudyn. Akár 10 napot is igénybe vehet, hogy a Cloudyn elegendő adatot gyűjtsön a méretezési javaslatok megjelenítéséhez.
- **Napi**: a tizedik naptól kezdve minden hónap végéig a Cloudyn az előző naptól számítva az UTC + 3 nap után a következő napon kell megjeleníteni az adatait.
- **Havonta**: az első naptól az egyes hónapok tizedik napjáig a Cloudyn csak az előző hónap végéig jelenítheti meg az adatokat.

Az Cloudyn feldolgozza az előző nap adatait, amikor az előző naptól származó teljes adat elérhető. Az előző napi adat általában a Cloudyn-ben érhető el az UTC + 3 naponta. Bizonyos adatműveletek (például címkék) további 24 órát is igénybe vehetnek.

Az aktuális hónapra vonatkozó adatmennyiség minden hónap elején nem érhető el gyűjteményhez. Az időszak alatt a szolgáltatók véglegesítik a számlázást az előző hónapban. Az előző havi adatmennyiség az Cloudyn 5 – 10 nappal az egyes hónapok kezdete után jelenik meg. Ebben az időszakban előfordulhat, hogy az előző hónapból csak az elszámolt költségek láthatók. Előfordulhat, hogy nem látja a napi számlázási vagy használati adatokat. Ha az adathozzáférés elérhetővé válik, a Cloudyn visszamenőlegesen dolgozza fel. A feldolgozás után az összes havi adat az ötödik nap és a hónap tizedik napja között jelenik meg.

Ha az Azure-ból a Cloudyn-be adatokat küld, az adatok továbbra is az Azure-ban lesznek rögzítve. A rendszer az adatátvitelt a Cloudyn továbbítja a rendszer a kapcsolatok visszaállításakor.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>A Cloudyn Cost-jelentéseinek költséghatékonysága

A Cost-jelentések megmutatják a költséghatékonyságot, amikor a Cloud Service-szolgáltatók frissített számlázási fájlokat küldenek. Az ingadozó költségek akkor fordulnak elő, ha új fájlok érkeznek egy felhőalapú szolgáltatótól a szokásos napi vagy havi jelentési ütemterven kívül. A költségcsökkentés nem a Cloudyn újraszámításának eredménye.

A teljes hónapban a felhőalapú szolgáltató által eljuttatott összes számlázási fájl a napi költségek becslése. Időnként gyakran frissülnek az adatmennyiségek – alkalmanként naponta többször is. A frissítések gyakoribbak az AWS-vel, mint az Azure. A Cost összegének stabilnak kell lennie, ha az előző hónap számlázási kiszámítása befejeződött, és a végső számlázási fájl fogadása megtörténik. Általában a hónap 10.

Változások történnek, ha a felhőalapú szolgáltatótól kapja a költséghatékonyságot. A kreditek fogadása egy példa. A módosítások az érintett hónap lezárulása után hónapokba kerülhetnek. A módosítások akkor jelennek meg, amikor a felhőalapú szolgáltató újraszámítja a számítást. A Cloudyn frissíti a korábbi adatbázisait, hogy minden módosítás újraszámítva legyen. Azt is ellenőrzi, hogy a költségek pontosan jelennek-e meg az informatikai jelentésekben.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hogyan konfigurálható a közvetlen CSP Cloudyn hozzáférés a közvetett CSP-ügyfelekhez vagy-partnerekhez?

Útmutatásért lásd: [a közvetett CSP-hozzáférés konfigurálása a Cloudyn-ben](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Mi okozza a optimalizáló menüelem megjelenítését?

A Azure Resource Manager-hozzáférés és az adatok összegyűjtése után meg kell jelennie az **optimalizálási** lehetőségnek. Azure Resource Manager hozzáférésének aktiválásához tekintse meg [a nem aktivált fiókok Azure-beli hitelesítő adatokkal](#how-do-i-activate-unactivated-accounts-with-azure-credentials) való aktiválásának Hogyanát ismertető témakört.

## <a name="is-cloudyn-agent-based"></a>Cloudyn-ügynök alapján?

Nem. Az ügynökök nincsenek használatban. A virtuális gépek Azure-beli virtuálisgép-metrikájának adatait a Microsoft elemzések API-ból gyűjtjük. Ha Azure-beli virtuális gépekről szeretne metrikus adatokat gyűjteni, engedélyezni kell a diagnosztikai beállításokat.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>A Cloudyn jelentések több AD-bérlőt jelenítenek meg jelentésekben?

Igen. [Létrehozhat egy megfelelő felhőalapú fiók entitást](tutorial-user-access.md#create-and-manage-entities) minden olyan ad-bérlőhöz, amelyhez rendelkezik. Ezután megtekintheti az összes Azure AD-bérlői és más felhőalapú platform-szolgáltatót, beleértve a Amazon Web Services és a Google Cloud Platform.
