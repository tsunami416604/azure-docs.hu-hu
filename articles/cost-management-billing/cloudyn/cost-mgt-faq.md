---
title: Gyakori kérdések az Azure-beli Cloudynről
description: A cikk a Cloudynnel kapcsolatos gyakori kérdésekre ad választ.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: b1ec81ea135079defb390becc025f51cde2dad7f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411237"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>A Cloudynre vonatkozó gyakori kérdések

A cikk a Cloudynnel kapcsolatos néhány gyakori kérdésre ad választ. Ha kérdései vannak a Cloudynnel kapcsolatban, a [Cloudyn GYIK-oldalán](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn) teheti fel őket.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hogyan oldhatok meg gyakori közvetlen vállalati beállítási problémákat?

Amikor először használja a Cloudyn portálját, a következő üzenetek jelenhetnek meg, ha Ön Nagyvállalati szerződést kötött, vagy egy felhőszolgáltató felhasználója:

- „The specified API key is not a top level enrollment key” (A megadott API-kulcs nem felső szintű regisztrációs kulcs) üzenet jelenik meg a **Set Up Cloudyn** (A Cloudyn beállítása) varázslóban.
- „Közvetlen regisztráció – Nem” üzenet jelenik meg a Nagyvállalati Szerződés portálján.
- "No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account” (Nem találhatók az elmúlt 30 napra vonatkozó használati adatok. Lépjen kapcsolatba a terjesztőjével, és ellenőrizze, hogy engedélyezve van-e a korrektúra az Ön Azure-fiókjában”) üzenet jelenik meg a Cloudyn portálon.

Az előző üzenetek arra utalnak, hogy egy viszonteladón vagy felhőszolgáltatón keresztül vásárolt Azure Nagyvállalati szerződést. A viszonteladónak vagy a felhőszolgáltatónak kell engedélyeznie a _korrektúrát_ az Azure-fiókjában, mielőtt Ön megtekinthetné az adatait a Cloudyn szolgáltatásban.

A problémák megoldása:

1. A viszonteladónak engedélyeznie kell a _korrektúrát_ a fiókjában. Útmutatás: [Közvetett ügyfeleknek szóló előkészítési útmutató](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Létre kell hoznia egy kulcsot az Azure Nagyvállalati Szerződésben, amelyet a Cloudynnel használhat. Útmutatásért tekintse meg az [Azure EA hozzáadását](quick-register-ea.md#register-with-cloudyn) ismertető szakaszt vagy az [EA regisztrációs azonosító és API-kulcs megkeresését](https://youtu.be/u_phLs_udig) bemutató videót.

A Cloudyn szolgáltatást csak egy Azure-szolgáltatásadminisztrátor engedélyezheti. A társadminisztrátori jogosultság ehhez nem elegendő.

A Cloudyn beállításához az Azure Nagyvállalati Szerződés API-kulcsának létrehozása előtt engedélyeznie kell az Azure számlázási API-t a következő útmutató követésével:

- [Jelentéskészítő API-k Enterprise-ügyfeleknek – áttekintés](../manage/enterprise-api.md)
- [Microsoft Azure Enterprise Portal jelentéskészítő API](https://ea.azure.com/helpdocs/reportingAPI)**Az adatok az API-hoz való hozzáférésének engedélyezése** területen


Előfordulhat, hogy a részlegek rendszergazdáinak, a fióktulajdonosoknak és a nagyvállalati rendszergazdáknak is engedélyt kell adnia a _díjak megtekintéséhez_ a számlázási API segítségével.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Miért nem látom az Optimizer (Optimalizáló) javaslatait?

A javaslatok csak aktivált fiókok számára érhetők el. A *nem aktivált* fiókok esetén nem jelennek meg a javaslatok az **Optimizer** (Optimalizáló) jelentéskategóriáiban, beleértve az alábbiakat:

- Optimization Manager (Optimalizáláskezelő)
- Sizing Optimization (Méretezés optimalizálása)
- Inefficiencies (Hatékonyságbeli hiányosságok)

Ha nem tudja megtekinteni az Optimizer (Optimalizáló) javaslatait, akkor valószínűleg nem rendelkezik aktivált fiókkal. A fiók aktiváláshoz regisztrálja a fiókját az Azure-beli hitelesítő adataival.

Fiók aktiválása:

1.    A Cloudyn portálon kattintson a **Settings** (Beállítások) gombra a jobb felső sarokban, és válassza a **Cloud Accounts** (Felhőbeli fiókok) lehetőséget.
2.    A Microsoft Azure Accounts (Microsoft Azure-fiókok) lapon keresse meg azokat a fiókokat, amelyek **nem aktivált** előfizetéssel rendelkeznek.
3.    Kattintson a nem aktivált fiók jobb oldalán található, ceruzára hasonlító **szerkesztés** szimbólumra.
4.    A bérlő- és díjazonosítót a rendszer automatikusan észleli. Kattintson a **Tovább** gombra.
5.    A program átirányítja az Azure Portalra. Jelentkezzen be a portálra, és engedélyezze a Cloudyn Collectornak az Azure adataihoz való hozzáférést.
6.    A rendszer ezt követően átirányítja a Cloudyn fiókkezelési oldalára, az előfizetését pedig **aktív** fiókállapotra frissíti. A fiók állapotánál egy zöld pipa látható.
7.    Ha nem lát zöld pipát egy vagy több előfizetés esetében, akkor nincs engedélye létrehozni az olvasó alkalmazást (a CloudynCollectort) az előfizetésben. Ehhez egy magasabb jogosultsággal rendelkező felhasználónak kell megismételnie a folyamat 3. és 4. lépését.  

Miután elvégezte az előző lépéseket, egy-két napon belül megtekintheti az Optimizer (Optimalizáló) javaslatait. Azonban akár öt napot is igénybe vehet, mire az optimalizálási adatok mindegyike elérhetővé válik.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hogyan engedélyezhetem a felfüggesztett vagy zárolt felhasználókat?

Először tekintsük át azt a leggyakoribb forgatókönyvet, amely miatt a felhasználói fiókok *kezdetben felfüggesztett* állapotba kerülhetnek.

> Tegyük fel, hogy Rendszergazda1 Microsoft-felhőszolgáltatói vagy Nagyvállalati Szerződéssel rendelkező felhasználó. A vállalata készen áll a Cloudyn használatára.  Az Azure Portalon keresztül regisztrál, és bejelentkezik a Cloudyn portálra. A Cloudyn szolgáltatást regisztráló, és a Cloudyn portálra bejelentkező személyként Rendszergazda1 lesz az *elsődleges rendszergazda*. Rendszergazda1 nem hoz létre felhasználói fiókokat. A Cloudyn portál használatával azonban létrehoz Azure-fiókokat, és beállít egy entitáshierarchiát. Rendszergazda1 arról tájékoztatja Rendszergazda2-t, a bérlői rendszergazdát, hogy regisztrálnia kell a Cloudynen, és be kell jelentkeznie a Cloudyn portálra.
>
> Rendszergazda2 az Azure Portalon keresztül regisztrál. A Cloudyn portálra való bejelentkezés során azonban hibaüzenetet kap, amely szerint a fiókja **fel van függesztve**. Az elsődleges rendszergazda, Rendszergazda1 értesítést kap a fiók felfüggesztéséről. Rendszergazda1-nek aktiválnia kell Rendszergazda2 fiókját, *rendszergazdai entitás-hozzáférést* kell biztosítania a megfelelő entitásokhoz, engedélyeznie kell a hozzáférést a felhasználókezeléshez, és aktiválnia kell a felhasználói fiókot.


Ha riasztást kap, amely egy felhasználó hozzáférésének engedélyezésére irányuló kérést tartalmaz, aktiválnia kell a felhasználói fiókot.

A felhasználói fiók aktiválása:

1. Jelentkezzen be a Cloudynre azzal az Azure rendszergazdai fiókkal, amelyet a Cloudyn beállításához használt. Másik lehetőségként jelentkezzen be egy rendszergazda hozzáféréssel rendelkező felhasználói fiókkal.
2. Kattintson a fogaskerék szimbólumra a jobb felső sarokban, és válassza a **User Management** (Felhasználókezelés) lehetőséget.
3. Keresse meg a felhasználót, kattintson a ceruza szimbólumra, majd szerkessze a felhasználót.
4. A **User status** (Felhasználó állapota) területen módosítsa az állapotot **Suspended** (Felfüggesztve) állapotról **Active** (Aktív) állapotra.

A Cloudyn felhasználói fiókjai az Azure-ból történő egyszeri bejelentkezéssel csatlakoznak. Ha a felhasználó elírja a jelszavát, előfordulhat, hogy zárolják a Cloudynben, még akkor is, ha továbbra is hozzáfér az Azure-hoz.

Ha a Cloudynben olyan e-mail-címre módosítja a címét, amely eltér az Azure-beli alapértelmezett címről, akkor előfordulhat, hogy a fiókja zárolva lesz. A „status initiallySuspended” (kezdetben Felfüggesztve állapot) állapotot jelenítheti meg. Ha felhasználói fiókja zárolva lett, a fiók alaphelyzetbe állításához vegye fel a kapcsolatot egy másik rendszergazdával.

Javasoljuk, hogy legalább két rendszergazdai Cloudyn-fiókot hozzon létre arra az esetre, ha az egyiket valamilyen okból zárolnák.

Ha nem tud bejelentkezni a Cloudyn portálra, ellenőrizze, hogy a helyes URL-címet használja a Cloudynbe történő bejelentkezéshez. A [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade) címet használja.

Ne használja a Cloudyn közvetlen `https://app.cloudyn.com` URL-címét.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hogyan aktiválhatom a nem aktivált fiókokat Azure hitelesítő adatokkal?

Amint a Cloudyn észleli az Azure-fiókokat, azonnal biztosítja a költségadatokat a költségalapú jelentésekben. Azonban ahhoz, hogy a Cloudyn használati és teljesítményadatokat is biztosítson, regisztrálnia kell a fiókok Azure-beli hitelesítő adatait. Útmutatásért tekintse meg a [Fiók hozzáadása vagy előfizetés frissítése](activate-subs-accounts.md#add-an-account-or-update-a-subscription) című szakaszt.

A fiókok Azure-beli hitelesítő adatainak hozzáadásához a Cloudyn portálon válassza a fiók neve (nem az előfizetés) mellett jobbra található szerkesztés szimbólumot.

Amíg hozzá nem adta az Azure-beli hitelesítő adatokat, a fiók _nem aktivált_ állapotúként jelenik meg.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hogyan adható hozzá több fiók és entitás egy meglévő előfizetéshez?

A további entitásokkal további Nagyvállalati Szerződések adhatók hozzá egy Cloudyn-előfizetéshez. További információ: [Entitások létrehozása és felügyelete](tutorial-user-access.md#create-and-manage-entities).

CSP-k esetén:

Ha további CSP-fiókokat szeretne hozzáadni egy entitáshoz, az új entitás létrehozásakor az **Enterprise** (Vállalat) helyett az **MSP Access** (MSP-hozzáférés) lehetőséget válassza. Ha a fiókja Nagyvállalati Szerződésként lett regisztrálva, és CSP hitelesítő adatokat kíván hozzáadni, előfordulhat, hogy a Cloudyn ügyfélszolgálati munkatársának módosítania kell a fiók beállításait. Ha Ön fizetős Azure-előfizetést használ, új támogatási kérést az Azure Portalon is létrehozhat. Válassza a **Súgó + támogatás**, majd az **Új támogatási kérés** elemet.

## <a name="currency-symbols-in-cloudyn-reports"></a>Pénznemszimbólumok a Cloudyn jelentéseiben

Lehet, hogy több különböző pénznemet használó Azure-fiókkal rendelkezik. A Cloudyn költségjelentései azonban egyszerre csak egy pénznemet jelenítenek meg jelentésenként.

Ha több, különböző pénznemet használó előfizetéssel rendelkezik, egy szülő- és annak gyermekentitásainak pénzneme **$** USD-ben jelenik meg. Az ajánlott eljárás az, hogy ne használjon különböző pénznemeket ugyanabban az entitáshierarchiában. Más szóval az entitásstruktúrába rendezett előfizetések mindegyikének ugyanazt a pénznemet kell használnia.

A Cloudyn automatikusan észleli a Nagyvállalati Szerződéshez tartozó előfizetés pénznemét, és helyesen jeleníti meg a jelentésekben.  A Cloudyn azonban csak **$** USD-t jelenít meg a CSP- és Web Direct Azure-fiókok esetén.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Melyek a Cloudyn adatfrissítési ütemezései?

A Cloudyn az alábbi adatfrissítési ütemezésekkel rendelkezik:

- **Kezdeti**: A beállítás után akár 24 órát is igénybe vehet, amíg a költségadatok megtekinthetők lesznek a Cloudynben. Akár 10 napot is igénybe vehet, mire a Cloudyn elegendő adatot gyűjt a méretezési javaslatok megjelenítéséhez.
- **Napi**: Minden hónap tizedik napjától kezdve a hónap végéig a Cloudyn naprakész adatokat jelenít meg az előző napról körülbelül UTC+3 után a következő napon.
- **Havi**: Előfordulhat, hogy minden hónap első napjától a tizedik napjáig a Cloudyn csak az előző hónap végéig tartó adatokat jelenít meg.

A Cloudyn akkor dolgozza fel az előző nap adatait, amikor az előző nap összes adata elérhetővé válik. Az előző nap adatai általában körülbelül UTC+3-ra válnak elérhetővé mindennap. Bizonyos adatok (például címkék) feldolgozása további 24 órát is igénybe vehet.

Az aktuális hónap adatai általában még nem gyűjthetők minden hónap elején. Ebben az időszakban véglegesítik a szolgáltatók az előző hónapra vonatkozó számlákat. Az előző hónap adatai az egyes hónapok kezdete után 5–10 nappal jelennek meg a Cloudynben. Ebben az időszakban előfordulhat, hogy az előző hónapból csak az amortizált költségek láthatók. Előfordulhat, hogy nem látja a napi számlázási vagy használati adatokat. Amikor az adatok elérhetővé válnak, a Cloudyn visszamenőleg feldolgozza őket. Feldolgozás után a havi adatok mindegyike megjelenik az egyes hónapok ötödik és tizedik napja között.

Az Azure akkor is rögzíti az adatokat, ha Azure és a Cloudyn között késedelmet szenved az adatok elküldése. A kapcsolat helyreállítása után a rendszer átviszi az adatokat a Cloudynbe.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Költségek ingadozása a Cloudyn költségjelentéseiben

A költségjelentésekben költségingadozások láthatók, amikor a felhőszolgáltatók elküldik a frissített számlázási fájlokat. Az ingadozó költségek akkor fordulnak elő, ha új fájlok érkeznek egy felhőszolgáltatótól a szokásos napi vagy havi jelentési ütemezésen kívül. A költségek változását nem a Cloudyn általi újraszámítás okozza.

A hóna során a felhőszolgáltató által elküldött számlázási fájlok mindegyike a napi költségek becslésére szolgál. Az adatok néha gyakran frissülnek, alkalmanként naponta többször is. A frissítések gyakoribbak az AWS, mint az Azure használata esetén. A költségek teljes összegének stabilnak kell maradnia, ha az előző hónap számlázási számítása befejeződött, és megérkezik a végleges számla. Ez általában a hónap tizedik napjáig történik meg.

Változásokat okozhat az is, ha költségkiigazítások érkeznek a felhőszolgáltatótól. Ilyenek lehetnek például a jóváírások. A módosítások a vonatkozó hónap lezárása után hónapokkal is előfordulhatnak. A módosítások mindig megjelennek, amikor a felhőszolgáltató újraszámítást végez. A Cloudyn frissíti a korábbi adatait, gondoskodva arról, hogy minden kiigazítás újraszámítása megtörténjen. Azt is ellenőrzi, hogy a költségek pontosan jelennek-e meg a jelentésekben.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hogyan konfigurálhat egy közvetlen CSP Cloudyn-hozzáférést konfigurálni közvetett CSP-ügyfeleinek vagy -partnereinek?

Útmutatásért tekintse meg a [Közvetett CSP-hozzáférés konfigurálása a Cloudynben](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn) című szakaszt.

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Mi okozza az Optimizer (Optimalizáló) menüelem megjelenését?

Az Azure Resource Manager-hozzáférés hozzáadása és az adatok összegyűjtése után megjelenik az **Optimizer** (Optimalizáló) lehetőség. Az Azure Resource Manager-hozzáférés aktiválásához tekintse meg a [Hogyan aktiválhatom a nem aktivált fiókokat Azure hitelesítő adatokkal?](#how-do-i-activate-unactivated-accounts-with-azure-credentials) kérdést.

## <a name="is-cloudyn-agent-based"></a>A Cloudyn ügynökalapú?

Nem. Nem használ ügynököket. A virtuális gépek Azure-beli virtuálisgép-metrikaadatainak gyűjtése a Microsoft Insights API-ból történik. Ha metrikaadatokat szeretne gyűjteni az Azure-beli virtuális gépekről, engedélyeznie kell rajtuk a diagnosztikai beállításokat.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>A Cloudyn-jelentések megjelenítenek több AD-bérlőt jelentésenként?

Igen. Minden AD-bérlő számára [létrehozhat egy megfelelő felhőfiók-entitást](tutorial-user-access.md#create-and-manage-entities). Ezután megtekintheti az összes Azure AD-bérlője adatait és más felhőszolgáltatókat is, például az Amazon Web Servicest és a Google Cloud Platformot.
