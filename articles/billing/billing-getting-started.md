---
title: Váratlan költségek megelőzése és a számlázás kezelése az Azure-ban
description: Ismerje meg, hogyan kerülheti el a váratlan költségeket az Azure-számlán. Azure-előfizetések esetén a Cost-Tracking és a Management funkcióinak használata.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612102"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Az Azure-számlázással és a költségek kezelésével kapcsolatos váratlan költségek megelőzése

Az Azure-ra való regisztráció során több dolog is elvégezhető a kiadások jobb megismerése érdekében:

- A [díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) az Azure-erőforrások létrehozása előtt is megbecsülheti a költségeket. 

- A [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) az előfizetésének aktuális díjszabását és előrejelzését biztosítja. 

- Ha szeretné csoportosítani és megérteni a különböző projektek vagy csapatok költségeit, tekintse meg az [erőforrás](../azure-resource-manager/resource-group-using-tags.md)-címkézést. Ha a szervezete rendelkezik a használni kívánt jelentési rendszerrel, tekintse meg a [Számlázási API-kat](billing-usage-rate-card-overview.md).

- Ha előfizetését egy Nagyvállalati Szerződés (EA) hozta létre, akkor a költségeket a Azure Portalban tekintheti meg. Ha az előfizetése egy felhőalapú megoldás-szolgáltatón (CSP) vagy Azure Sponsorshipon keresztül történik, előfordulhat, hogy az alábbi funkciók némelyike nem vonatkozik Önre. További információ: [az EA, a CSP és a szponzorálás további erőforrásai](#other-offers).

- Ha az előfizetés egy ingyenes próbaverziós ajánlat, a [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), a Azure in Open LICENCPROGRAM (AIO) vagy a BizSpark, az előfizetés automatikusan le lesz tiltva, ha az összes kredit használatban van. Ismerje meg a költségkereteket [, hogy](#spending-limit) ne kelljen váratlanul letiltani az előfizetést.

- Ha már regisztrált egy [ingyenes Azure](https://azure.microsoft.com/free/)-fiókra, [a legnépszerűbb Azure-szolgáltatások közül néhányat 12 hónapig ingyenesen használhat](billing-create-free-services-included-free-account.md). Az alább felsorolt javaslatok mellett olvassa el az [ingyenes fiók](billing-avoid-charges-free-account.md)felszámítása című témakört.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Becsült költségek beszerzése az Azure-szolgáltatások hozzáadása előtt

Íme néhány további információ a költségek becsléséről a következő eszközök használatával:
- Azure díjkalkulátor
- Azure Portal
- Költségkeret

A következő szakaszban szereplő képek az USA dollárban történő díjszabását mutatják be.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>A költség online becslése a díjszabási kalkulátor használatával

Tekintse meg [](https://azure.microsoft.com/pricing/calculator/) a díjszabási számológépet, és szerezze be az Önt érdeklő szolgáltatás becsült havi költségét. A becsült költségek megszerzéséhez hozzáadhat bármely első fél Azure-erőforrást is. A díjszabási számológépben módosíthatja a pénznem típusát.

![Képernyőfelvétel a díjszabási kalkulátor menüjéről](./media/billing-getting-started/pricing-calc.png)

A díjszabási számológépben például egy a1-es Windows rendszerű virtuális gép (VM) várhatóan egy bizonyos mennyiség/hónap költségét számítja ki a számítási órákban, ha a teljes idő alatt elhagyja:

![Képernyőfelvétel a díjszabási Számológépről, amely egy a1-es Windows virtuális gép becsült költségét mutatja havonta](./media/billing-getting-started/pricing-calcvm.png)

A díjszabással kapcsolatos további információkért tekintse meg a díjszabással kapcsolatos [gyakori kérdéseket](https://azure.microsoft.com/pricing/faq/). Ha egy Azure-értékesítővel szeretne kommunikálni, hívja meg a GYIK oldal tetején megjelenő telefonszámot.

### <a name="review-estimated-costs-in-the-azure-portal"></a>A Azure Portal becsült költségeinek áttekintése

A szolgáltatásnak a Azure Portalban való hozzáadásakor általában egy olyan nézet jelenik meg, amely havi becsült díjat jelenít meg a számlázott pénznemben. Ha például a Windows rendszerű virtuális gép méretét választja, a számítási órák becsült havi díja látható:

![Példa: egy a1-es Windows rendszerű virtuális gép, amelyen a becsült költségek havonta láthatók](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a>Ellenőrizze, hogy van-e költségkerete

Ha olyan előfizetéssel rendelkezik, amely krediteket használ, akkor alapértelmezés szerint be van kapcsolva a költségkeret. Így az összes kredit elköltése után a bankkártya nem kerül felszámításra. Tekintse meg az [Azure-ajánlatok teljes listáját és a költségkeret rendelkezésre állását](https://azure.microsoft.com/support/legal/offer-details/).

Ha azonban eléri a költségkeretet, a szolgáltatások le vannak tiltva. Ez azt jelenti, hogy a virtuális gépek fel vannak foglalva. A szolgáltatás leállásának elkerülése érdekében ki kell kapcsolni a költségkeretet. A túlterhelést a rendszer a fájlra vonatkozó bankkártyára terheli.

Ha szeretné megtekinteni, hogy van-e költségkerete, nyissa meg a [Account Center](https://account.windowsazure.com/Subscriptions)előfizetések nézetét. Megjelenik egy szalagcím, ha a költségkeret a következőhöz hasonló:

![Képernyőkép, amely figyelmeztetést jelenít meg az Account Center-beli költségkeret betöltéséről](./media/billing-getting-started/spending-limit-banner.png)

Kattintson a szalagcímre, és kövesse az utasításokat a költségkeret eltávolításához. Ha a regisztráció során nem adta meg a bankkártya adatait, meg kell adnia azt a költségkeret eltávolításához. További információ: az Azure költségkerete [– Hogyan működik, és hogyan engedélyezhető vagy távolítható el](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségek riasztások használata

Költségvetéseket hozhat [](../cost-management/tutorial-acm-create-budgets.md) létre a költségek kezeléséhez, és [](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) olyan riasztásokat hozhat létre, amelyek automatikusan értesítik az érintetteket a kiesések elköltéséről és a kockázatok túllépéséről A riasztások a költségvetéshez és a költségekhez viszonyított kiadásokon alapulnak.

## <a name="monitor-costs-when-using-azure-services"></a>Az Azure-szolgáltatások használata során felmerülő költségek figyelése
A költségeket a következő eszközök segítségével figyelheti:

- Tags
- Költségcsökkentés és írási arány
- Költségelemzés

### <a name="tags"></a>Címkék hozzáadása az erőforrásokhoz a számlázási információk csoportosításához

A címkék segítségével csoportosíthatja a támogatott szolgáltatások számlázási szolgáltatásait. Ha például több virtuális gépet futtat különböző csapatok számára, akkor a címkék segítségével kategorizálhatja a költségeket a Cost Center használatával (például: HR, marketing, pénzügy stb.) vagy környezet (például éles környezetben, üzem előtti, tesztelés).

![A címkék beállítását bemutató képernyőkép a portálon](./media/billing-getting-started/tags.png)

A címkék a különböző Cost Reporting-nézetek alatt jelennek meg. Például azonnal megjelennek a Cost Analysis- [nézetben](#costs) , és az első számlázási időszak után a részletes használati CSV-fájlban.

További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>A költségcsökkentés és az írási sebesség figyelése

Miután futtatta az Azure-szolgáltatásokat, rendszeresen tekintse meg a díjakat. Az aktuális kiadások és az írási sebesség a Azure Portalban látható.

1. Látogasson el a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) előfizetésekre, és válasszon egy előfizetést.

2. Ha támogatja az előfizetését, megjelenik a költségcsökkentés és az írási sebesség.

    ![Képernyőkép az írási arányról és a bontásról a Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. A bal oldali listában kattintson a [Cost Analysis](../cost-management/quick-acm-cost-analysis.md) elemre, és tekintse meg az erőforrás által lebontott költségeket. A szolgáltatás hozzáadása után várjon 24 órát, hogy megjelenjenek az információk.

    ![Képernyőkép a Cost Analysis nézetről Azure Portal](./media/billing-getting-started/cost-analysis.png)

4. A szűrést különböző tulajdonságok, például [címkék](#tags), erőforrástípus, erőforráscsoport és TimeSpan alapján végezheti el. Kattintson az **alkalmaz** gombra a szűrők megerősítéséhez és a **letöltéshez** , ha a nézetet vesszővel tagolt (. csv) fájlba szeretné exportálni.

5. Emellett az erőforrásokra kattintva megtekintheti a napi kiadások előzményeit, és az erőforrások költségét az egyes napokon.

    ![Képernyőfelvétel az előzmények nézetről Azure Portal](./media/billing-getting-started/costhistory.png)

Hasonlítsa össze a szolgáltatások kiválasztásakor megjelenő becslésekkel ellátott költségeket. Ha a költségek jelentősen eltérnek a becslések, ellenőrizze az erőforrásokhoz kiválasztott díjszabási tervet.

## <a name="optimize-and-reduce-costs"></a>A költségek optimalizálása és csökkentése
Ha nem ismeri a Cost Management alapelveit, olvassa el, [hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management/cost-mgt-best-practices.md).

A Azure Portal a virtuális gépek és az Advisor-javaslatok automatikus leállításával is optimalizálhatja és csökkentheti az Azure-költségeket.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Vegye fontolóra a virtuális gépek automatikus leállításához hasonló költségkímélő funkciókat

A forgatókönyvtől függően beállíthatja a virtuális gépek automatikus leállítását a Azure Portalban. További információ: [a virtuális gépek automatikus leállítása Azure Resource Manager használatával](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Képernyőfelvétel az automatikus leállítás lehetőségről a portálon](./media/billing-getting-started/auto-shutdown.png)

Az automatikus leállítás nem ugyanaz, mint amikor a virtuális gépen bekapcsolja az energiagazdálkodási lehetőségeket. Az automatikus leállítás leállítja és felszabadítja a virtuális gépeket a további használati díjak leállításához. További információ: a Linux rendszerű virtuális [gépek](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és a virtuálisgép-állapotokkal kapcsolatos [Windows-alapú virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) díjszabása.

A fejlesztési és tesztelési környezetek további költséghatékony szolgáltatásaiért tekintse meg [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor javaslatok bekapcsolása és áttekintése

[Azure Advisor](../advisor/advisor-overview.md) segít csökkenteni a költségeket az alacsony kihasználtságú erőforrások azonosításával. Látogasson el az Advisorra a Azure Portal:

![Képernyőkép Azure Advisor gomb Azure Portal](./media/billing-getting-started/advisor-button.png)

Az Advisor irányítópultjának **Cost (Cost** ) lapján a gyakorlatban alkalmazható ajánlásokat kaphat:

![Az Advisor Cost-javaslat képernyőképe – példa](./media/billing-getting-started/advisor-action.png)

Tekintse át az [optimalizálási költségeket a javaslatok](../cost-management/tutorial-acm-opt-recommendations.md) oktatóanyagban egy interaktív oktatóanyagért, amely a Cost-megtakarítás Advisor javaslatait ismerteti.

## <a name="review-costs-against-your-latest-invoice"></a>A legutóbbi számla költségeinek áttekintése

A számlázási ciklus végén a legutóbbi számla elérhető. A [számlák és a részletes használati fájlok letöltésével](billing-download-azure-invoice-daily-usage-date.md) meggyőződhet arról, hogy megfelelő díjat adott meg. A napi használatnak a számlával való összehasonlításával kapcsolatos további információkért lásd: [Microsoft Azure számlájának ismertetése](billing-understand-your-bill.md).

### <a name="billing-api"></a>Számlázási API

Használja az Azure számlázási API-t a használati adatok programozott beszerzéséhez. A RateCard API és a használati API együttes használatával megszerezheti a számlázott használatot. További információ: betekintést [nyerhet a Microsoft Azure erőforrás](billing-usage-rate-card-overview.md)-felhasználásba.

## <a name="other-offers"></a>További források és különleges esetek

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP-és szponzorálási ügyfelek
Az első lépésekhez forduljon a fiók-kezelőhöz vagy az Azure-partnerhez.

| Ajánlat | További források |
|-------------------------------|-----------------------------------------------------------------------------------|
| Nagyvállalati Szerződés (EA) | [EA portál](https://ea.azure.com/), [súgó docs](https://ea.azure.com/helpdocs)és [Power bi jelentés](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (Felhőszolgáltató) | Beszélgetés a szolgáltatóval |
| Azure Sponsorship | [Szponzorálási portál](https://www.microsoftazuresponsorships.com/) |

Ha nagyméretű szervezet számára felügyeli, javasoljuk, hogy olvassa el az [Azure Enterprise állványt](/azure/architecture/cloud-adoption-guide/subscription-governance) és a [vállalati IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) -tanulmányt (. pdf letöltés, csak angol nyelven).

#### <a name="EA"></a>Nagyvállalati Szerződés a Azure Portal

A vállalati Cost views szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Megjegyezhető elemek:

- Az előfizetési költségek használaton alapulnak, és nem tartalmazzák az előre fizetett összegeket, a túllépéseket, a benne foglalt mennyiségeket, a beállításokat és az adókat. A tényleges díjakat a beléptetési szinten számítjuk ki.
- A Azure Portalban megjelenő összegek eltérőek lehetnek, mint az Enterprise Portalon. Az Enterprise Portal frissítései eltarthat néhány percig, mielőtt a módosítások megjelennek a Azure Portalban.
- Ha nem látja a költségeket, a következő okok egyike lehet:
    - Nincs engedélye az előfizetési szinten. A vállalati Cost-nézetek megjelenítéséhez számlázási olvasó, olvasó, közreműködő vagy tulajdonosnak kell lennie az előfizetési szinten.
    - Ön a fiók tulajdonosa, és a beléptetési rendszergazda letiltotta az "AO View charges" beállítást.  Vegye fel a kapcsolatot a regisztrációs rendszergazdával, hogy hozzáférjen a költségekhez.
    - Ön egy részleg rendszergazdája, és a beléptetési rendszergazda letiltotta a **da View díjak** beállítását.  A hozzáférés megszerzéséhez forduljon a beléptetési rendszergazdához.
    - Egy Channel partneren keresztül vásárolta meg az Azure-t, és a partner nem adta meg az árképzési információkat.  
- Ha a vállalati portálon a Cost Accesshez kapcsolódó beállításokat frissíti, néhány perc késéssel jár, mielőtt a módosítások megjelennek a Azure Portalban.
- A költségkeret és a számlázási útmutató nem vonatkozik az EA-előfizetésekre.

### <a name="check-your-subscription-and-access"></a>Előfizetés és hozzáférés keresése

A költségek megtekintéséhez [előfizetés szintű hozzáféréssel kell rendelkeznie a számlázási adatokhoz](billing-manage-access.md). Csak a fiók rendszergazdája férhet hozzá az [Account Centerhez](https://account.azure.com/Subscriptions), módosíthatja a számlázási adatokat, és kezelheti az előfizetéseket. A fiók rendszergazdája az a személy, aki átment a regisztrációs folyamaton. További információ: [az előfizetést vagy szolgáltatásokat kezelő Azure-rendszergazdai szerepkörök hozzáadása vagy módosítása](billing-add-change-azure-subscription-administrator.md).

Ha szeretné megtekinteni, hogy a fiók rendszergazdája-e, nyissa meg [az Előfizetések Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Megtekintheti az előfizetések listáját, és megkeresheti a szerepkört. Ha a *fiók rendszergazdája* a szerepkör, akkor teljes körű jogosultsággal rendelkezik. Ha más, például tulajdonosi jogokkal is rendelkezik, nem rendelkezik teljes körű jogosultságokkal.

![Képernyőkép a szerepkörről a Azure Portal előfizetések nézetében](./media/billing-getting-started/sub-blade-view.PNG)

Az előfizetések kezeléséhez és a számlázási adatok módosításához [Keresse meg a fiók rendszergazdáját](billing-subscription-transfer.md#whoisaa). Kérje meg a fiók rendszergazdáját, hogy hajtsa végre a feladatokat, vagy [vigye át az](billing-subscription-transfer.md)előfizetést.

Ha a fiók rendszergazdája már nem rendelkezik a szervezettel, és Önnek kell kezelnie a számlázást, [vegye fel velünk a kapcsolatot](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>szolgáltatói szerződés kredit igénylése szolgáltatási incidens esetén

A szolgáltatói szerződések (SLA-k) rögzítik a Microsoft elkötelezettségét a folyamatos rendelkezésre állás és stabil kapcsolatok iránt. A szolgáltatási incidens akkor jelent meg, ha az Azure-szolgáltatások olyan problémát tapasztalnak, amely hatással van az üzemidőre vagy a kapcsolatra, gyakran kimaradásnak is nevezik. Ha az SLA-ban leírtak szerint nem érjük el és nem tartjuk fenn az egyes szolgáltatásokhoz tartozó szolgáltatási szinteket, akkor a havi szolgáltatási díjak egy részének jóváírására is jogosult lehet.

Kredit igénylése:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Ha több fiókja is van, ügyeljen arra, hogy az Azure-állásidő által érintettet használja. 
2. Hozzon létre egy új támogatási kérelmet.
3. A **probléma típusa**területen válassza a **számlázás**lehetőséget.
4. A **probléma típusa**területen válassza a **visszatérítési kérelem**lehetőséget.
5. Adja meg a részleteket annak megadásához, hogy egy SLA-kreditet kér, megemlítve a dátum/idő/idő zónát, valamint az érintett szolgáltatásokat (virtuális gépek, webhelyek stb.).
6. Erősítse meg a kapcsolattartási adatait, és válassza a **Létrehozás** lehetőséget a kérelem elküldéséhez.

Az SLA-küszöbértékek szolgáltatás szerint változnak. Például az SQL web-szint 99,9%-os SLA-val rendelkezik, a virtuális gépek a 99,95%-os SLA-val rendelkeznek, a standard szintű SQL-csomag pedig 99,99%-os SLA-val rendelkezik.

Egyes szolgáltatások esetében előfeltétel a SLA alkalmazása. A virtuális gépeknek például legalább két példányban kell lenniük ugyanabban a rendelkezésre állási csoportba.

További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) és az [Azure-szolgáltatások](https://azure.microsoft.com/support/legal/sla/summary/) dokumentációjának SLA-összegzése.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- További tudnivalók a [költségkeretek használatának](billing-spending-limit.md) elkerüléséről.
- Megkezdheti [Az Azure-költségek elemzését](../cost-management/quick-acm-cost-analysis.md).
