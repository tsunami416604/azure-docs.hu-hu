---
title: Gyakori kérdések az Azure költség Management szolgáltatásról |} Microsoft Docs
description: A témakör válaszokat ad Azure költség-kezeléssel kapcsolatos gyakori kérdésekre.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0742e1e96e03840f138dde2bca7b2bcda1e49dfe
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298409"
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Gyakori kérdések az Azure költség Management

Ez a cikk foglalkozik Azure költség Management (más néven Cloudyn) kapcsolatos gyakori kérdésekre. Ha költség-kezeléssel kapcsolatos kérdése van, kérje meg őket, [gyakori kérdések az Azure költség felügyeleti](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Hogyan oldhatja meg a közvetett vállalati beállításával kapcsolatos gyakori problémák?

Ha először a Cloudyn portál, az alábbi üzenetek láthatja, ha egy vállalati szerződésben, vagy a Cloud Solution Provider (CSP) felhasználó:

- "A megadott API-kulcs nem a legfelső szintű regisztrációs kulcs" jelenik meg a **beállítva fel Azure költség felügyeleti** varázsló.
- "Közvetlen regisztráció – nem" jelenik meg a nagyvállalati szerződés portálon.
- "A nem használati adatok az elmúlt 30 napban található. Lépjen kapcsolatba a terjesztőt, hogy ellenőrizze, hogy engedélyezték a jelölés során az Azure-fiókjával"jelenik meg a Cloudyn portálon.

Az előző üzenetek arra utalnak, hogy egy viszonteladón vagy felhőszolgáltatón keresztül vásárolt Azure Nagyvállalati szerződést. Engedélyeznie kell a viszonteladóhoz vagy a CSP _markup_ az az Azure fiók, hogy az adatok megtekintheti a Cloudyn.

A problémák megoldása:

1. A viszonteladónak engedélyeznie kell a _korrektúrát_ a fiókjában. Útmutatás: [Közvetett ügyfeleknek szóló előkészítési útmutató](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Létrehozhat az Azure nagyvállalati szerződéssel kulcs Cloudyn való használatra. Útmutatásért lásd: [hozzáadása az Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) vagy [található a EA regisztrációs azonosítója és API-kulcs](https://youtu.be/u_phLs_udig).

A Cost Management szolgáltatást csak egy Azure-szolgáltatásadminisztrátor engedélyezheti. A társadminisztrátori jogosultság ehhez nem elegendő.

Mielőtt Cloudyn beállítása Azure nagyvállalati szerződés API-kulcsot is létrehozhat, engedélyeznie kell az Azure számlázási API által megadott utasítások:

- [Jelentéskészítő API-k Enterprise-ügyfeleknek – áttekintés](../billing/billing-enterprise-api.md)
- [Microsoft Azure Enterprise Portal jelentéskészítő API](https://ea.azure.com/helpdocs/reportingAPI) **Az adatok az API-hoz való hozzáférésének engedélyezése** területen


Előfordulhat, hogy a részlegek rendszergazdáinak, a fióktulajdonosoknak és a nagyvállalati rendszergazdáknak is engedélyt kell adnia a _díjak megtekintéséhez_ a számlázási API segítségével.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Miért nem látom az optimalizáló javaslatok?

Javaslat információk csak aktív fiókokhoz érhető el. Nem jelenik meg minden javaslat információ **optimalizáló** kategóriák fiókok, amelyek jelentést *aktivált*, többek között a következőket:

- Optimalizálás Manager
- Méretezési optimalizálása
- Hatékonyság hiánya

Ha bármely optimalizáló javaslat adatai nem tekinthetők meg, a legvalószínűbb ok az, akkor rendelkezik aktivált fiókok. Egy fiók aktiválásához szüksége regisztrálja őket az Azure hitelesítő adatait.

Aktivált fiókra:

1.  A Cloudyn portálon kattintson a **Settings** (Beállítások) gombra a jobb felső sarokban, és válassza a **Cloud Accounts** (Felhőbeli fiókok) lehetőséget.
2.  A Microsoft Azure-fiók lapon keresse meg a fiókokat, amelyek rendelkeznek egy **aktivált** előfizetés.
3.  Kattintson a jobb oldalán egy aktivált fiókhoz, a **szerkesztése** egy ceruza levő szimbólum.
4.  A bérlői és sebessége azonosító automatikusan észleli. Kattintson a **Tovább** gombra.
5.  A program átirányítja az Azure-portálon. Jelentkezzen be a portálra, és az Azure adatok eléréséhez Cloudyn adatgyűjtő engedélyezéséhez.
6.  A következő program átirányítja a Cloudyn fiókok kezelése lapon, és az előfizetésben **aktív** Fiókállapot. Azt illusztrálja, egy zöld pipa szimbólumot.
7.  Ha egy vagy több előfizetés nem egy zöld pipa szimbólumot lát, az azt jelenti, Ön nem jogosult az előfizetés olvasó alkalmazás (CloudynCollector) létrehozása. Az előfizetés magasabb szintű engedélyekkel rendelkező felhasználó kell ismételje meg a 3. és 4.  

A fenti lépések végrehajtása után megtekintheti a optimalizáló ajánlások egy-két napon belül. Azonban legfeljebb öt napig érhető el teljes optimalizálási adatokat is igénybe vehet.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Hogyan engedélyezhető felfüggesztett vagy zárolt felhasználók?

Első lépésként nézzük legfeljebb gyakori forgatókönyv, amelyek hatására az beszerzése a felhasználói fiókok *initiallySuspended*.

> Rendszergazda1 előfordulhat, hogy a Microsoft Cloud Solution Provider vagy a nagyvállalati szerződés felhasználó. A szervezet készen áll a költség felügyeleti használatának megkezdéséhez.  Ezután regisztrálja az Azure portálon keresztül, és aláírja a Cloudyn portált. Regisztrálja a költség felügyeleti szolgáltatás és bejelentkezhet a Cloudyn portált személynek, ő lesz a *elsődleges rendszergazda*. Rendszergazda1 nem hoz létre egyetlen felhasználói fiókot. Azonban a Cloudyn portál használatával, ezután létrehozása az Azure-fiókra, és állít be egy entitás hierarchiához. Rendszergazda1 tájékoztatja Admin2, a bérlői rendszergazda, amelyet ezután regisztrálja a felügyeleti költségek, és jelentkezzen be a Cloudyn portálra.

> Admin2 regisztrálja az Azure portálon keresztül. Azonban ha úgy próbál jelentkezzen be a Cloudyn portálra, akkor hibaüzenetet kap arról, hogy a fiók **felfüggesztve**. A fiók felfüggesztését a fő rendszergazdai rendszergazda1, értesítést kap. Rendszergazda1 kell aktiválniuk mobilalkalmazásukat Admin2 meg, és biztosítson számára *rendszergazdai entitás hozzáférés* a megfelelő entitások, és lehetővé teszi a felhasználói hozzáférés és aktív felhasználói fiók.


Ha engedélyezi a hozzáférést egy felhasználó kéri figyelmeztetést kap, a felhasználói fiók aktiválása szeretné.

A felhasználói fiók aktiválása:

1. Jelentkezzen be az Azure rendszergazdai felhasználói fiókkal, amellyel Cloudyn beállítása Cloudyn. Vagy be kell jelentkezniük egy felhasználói fiókot, amely rendszergazdai hozzáféréssel kapott.
2. A fogaskerék szimbólum a jobb felső részén válassza ki és **felhasználókezelés**.
3. Keresse meg azt a felhasználót, jelölje ki a Ceruza szimbólumot, és módosítsa a felhasználó.
4. A **felhasználói állapot**, módosítani az állapotát **felfüggesztett** való **aktív**.

Felhasználói fiókok Cloudyn csatlakozhat az egyszeri bejelentkezés az Azure-ból. Ha egy felhasználó mistypes a jelszavát, akkor előfordulhat, hogy ártatlan tévedéssel Cloudyn, annak ellenére, hogy továbbra is hozzáférjenek Azure.

Cloudyn e-mail-címét az Azure-ban az alapértelmezett cím vált, ha a fiók képes lekérni zárolva. Csak akkor lehet, hogy megjelenni "állapot initiallySuspended." Ha a felhasználói fiók zárolva van, lépjen kapcsolatba a visszaállítása fiókjába egy másik rendszergazda.

Azt javasoljuk, hogy legalább két Cloudyn rendszergazdai fiókokat hoz létre, abban az esetben, ha az egyik fiók lekérdezi zárolása.

Ha Ön nem jelentkezhet be a Cloudyn portálra, győződjön meg arról, hogy bejelentkezni Cloudyn a helyes Azure költség felügyeleti URL-címet használja. Használjon [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Kerülje a Cloudyn közvetlen URL-cím https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Hogyan aktiválja a aktivált fiókok Azure hitelesítő adataival?

Amint az Azure-fiókra Cloudyn által felderített, költség adatok azonnal találhatók költség-alapú jelentésekhez. Azonban a használati és teljesítményadatokat biztosításához Cloudyn kell regisztrálni a fiókok az Azure hitelesítő adataival. Útmutatásért lásd: [hozzáadása Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

A Cloudyn portálon Azure hitelesítő adatokat egy olyan fiók hozzáadásához válassza a Szerkesztés szimbólum jobb oldalán a fiók nevét, nem az előfizetést.

Azure hitelesítő adatait Cloudyn kerülnek, amíg a fiók akkor jelenik meg, mint _nem aktivált_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Hogyan több fiók és entitások hozzá egy meglévő előfizetéshez?

További entitások további nagyvállalati szerződések hozzáadása Cloudyn előfizetés szolgálnak. Az alábbi hivatkozások mutatják be, hogy további entitásokat hozzáadása:

- [Egy entitás hozzáadása](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) cikk
- [A hierarchia költség entitások meghatározása](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) videó

Azon felhőbeli szolgáltatók esetében:

Egy entitás további CSP fiókok hozzáadásához válassza **MSP hozzáférés** ahelyett, hogy **vállalati** az új entitás létrehozásakor. Ha a fiókjához regisztrált nagyvállalati szerződéssel, és hozzá szeretné adni a kriptográfiai Szolgáltató hitelesítő adatai, Cloudyn technikai támogatási csoporthoz módosítania a fiók beállításait. Ha egy fizetős Azure-előfizető, létrehozhat egy új támogatási kérelem az Azure portálon. Válassza ki **súgó + támogatás**, majd válassza ki **új támogatja a kérelem**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Pénznemszimbólumot Cloudyn jelentések

Lehetséges, hogy a különböző pénznemeket használó több Azure-fiókra. Költség jelentéseket Cloudyn azonban ne jelenjen meg egynél több pénznem típusa jelentésenként.

Ha különböző pénznemek használatával több előfizetéssel rendelkezik, egy szülőentitás és annak gyermek entitás pénznemek jelennek USD **$**. A javasolt ajánlott kerülheti el a különböző pénznemek entitás ugyanabba a hierarchiába. Más szóval egy entitás struktúrájából összes előfizetés megegyező pénznemet kell használni.

Cloudyn automatikusan észleli a nagyvállalati szerződés előfizetés pénznem és mutatja be megfelelően a jelentésekben.  Azonban Cloudyn csak jeleníti USD **$** CSP és a web-közvetlen Azure-fiókra.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Mik azok a Cloudyn adatok frissítése ütemtervek?

Cloudyn a következő adatok frissítési ütemtervek rendelkezik:

- **Kezdeti**: telepítése után, Cloudyn költség adatainak megtekintéséhez akár 24 óráig is eltarthat. A méretezési javaslatok megjelenítése elég adatok gyűjtéséért felelős ügyfélfeladatot Cloudyn legfeljebb 10 nap is eltarthat.
- **Napi**: az minden hónap végéig 10 nap, Cloudyn meg kell jelennie az adatok naprakészek legyenek az előző nap után UTC + 3 kapcsolatban a következő naptól érvényesek.
- **Havi**: 10 napja minden hónap első napján, a Cloudyn megjelenítése előfordulhat, hogy az adatok csak az előző hónap végéig.

Cloudyn adatokat az előző napi mikor érhető el az előző nap teljes adatokat dolgozza fel. Az előző napi érhetők el az adatok általában Cloudyn által UTC + 3 kapcsolatos minden nap. Egyes adatok, például a címkék, feldolgozni a további 24 órát is igénybe vehet.

Az aktuális hónapra vonatkozó adatok nem érhető el minden hónap elején gyűjtemény. Az időszak alatt a szolgáltatók a számlázás az előző hónap véglegesítése. Az előző hónap adat Cloudyn 5 10 nap után minden hónap kezdetét jelenik meg. Ebben az időszakban jelenhet meg az előző hónap csak amortized költségeket. Napi számlázással vagy a használati adatok nem jelenhet meg. Amikor az adatok elérhetővé válik, Cloudyn folyamatokat engedélyez visszamenőleges. A feldolgozás után a havi adatok között a ötödik és minden hónap 10 napja jelenik meg.

Ha az Azure-ból történő adatküldés Cloudyn késleltetés, adatok Azure továbbra is rögzíti. Az adatok átkerül Cloudyn, amikor a kapcsolat helyreáll.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Hogyan lehet egy közvetlen CSP Cloudyn hozzáférésének beállítása közvetett CSP-ügyfelekre és partnerekre?

Útmutatásért lásd: [közvetett CSP hozzáférés konfigurálása Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Mi okozza a optimalizáló menüpont jelenik meg?

Miután hozzáadta az Azure Resource Manager hozzáférési és adatokat kell felvenni, kell megjelennie a **optimalizáló** lehetőséget. Aktiválja az Azure Resource Manager hozzáférés, lásd: [hogyan aktiválása aktivált fiókok Azure hitelesítő adataival?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>Költség felügyeleti/Cloudyn ügynök alapja

Nem. Ügynökök nem használhatók. Azure virtuális gép metrikus adatokat virtuális gépek gyűjtött a Microsoft Hirdetéselemző API-t. Ha azt szeretné, az Azure virtuális gépek metrika adatok gyűjtését, szükségük van engedélyezve diagnosztikai beállítások.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Tegye Cloudyn szóló jelentésekben megtekintheti a jelentés egynél több AD-bérlő?

Igen. Is [hozzon létre egy megfelelő cloud Partner entitás](tutorial-user-access.md#create-and-manage-entities) az egyes AD bérlők, hogy rendelkezik. Majd megtekintheti az összes Azure AD-bérlő adatait és más felhőalapú platform szolgáltatók, beleértve az Amazon Web Services és a Google Cloud Platform.
