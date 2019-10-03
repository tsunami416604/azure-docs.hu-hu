---
title: Váratlan költségek megelőzése és számlázás kezelése az Azure-ban
description: Ismerje meg, hogyan kerülheti el a váratlan költségeket Azure-számláján. Az Azure-előfizetés költségkövetési és -kezelési funkciói.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: d6c287d5ead0095a4f7bb5ad754212b134f7103c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719804"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Hogyan kerülheti el a váratlan költségeket az Azure-számlázás és -költségkezelés használatával

Az Azure-ra való regisztráció során sokat tehet azért, hogy jobban megismerje a várható költségeket:

- A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a költségeket az Azure-erőforrások létrehozása előtt. 

- Az [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) tartalmazza az előfizetéshez tartozó aktuális költséglebontást és -előrejelzést. 

- Ha csoportosítani szeretné, és meg szeretné érteni a különböző projektek és csapatok költségeit, tekintse meg az [erőforrás-címkézést](../azure-resource-manager/resource-group-using-tags.md) bemutató témakört. Ha szervezete rendelkezik jelentéskészítési rendszerrel, és azt szeretné használni, tekintse meg a [számlázási API-kat](billing-usage-rate-card-overview.md) bemutató témakört.

- Ha előfizetése Nagyvállalati Szerződésből (EA) lett létrehozva, költségeit az Azure Portalon tekintheti meg. Ha előfizetése egy felhőszolgáltatón (CSP) vagy az Azure Sponsorshipen keresztül működik, előfordulhat, hogy az alábbi funkciók némelyike nem vonatkozik Önre. További információkért tekintse meg a [További források a Nagyvállalati Szerződésekről, a felhőszolgáltatókról és a Sponsorshipről](#other-offers).

- Ha az előfizetés egy ingyenes próbaverziós ajánlat, a [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), az Azure in Open (AIO) vagy a BizSpark keretében működik, az előfizetés az összes kredit felhasználásakor automatikusan le lesz tiltva. Ismerje meg a [költségkereteket](#spending-limit), hogy elkerülhesse az előfizetés váratlan letiltását.

- Ha [ingyenes Azure-fiókkal](https://azure.microsoft.com/free/) rendelkezik, [12 hónapon keresztül ingyenesen használhatja a legnépszerűbb Azure-szolgáltatások némelyikét](billing-create-free-services-included-free-account.md). Az alább felsorolt ajánlásokon kívül tekintse meg, [hogyan kerülheti el, hogy díjak merüljenek fel ingyenes fiókja használatakor](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Becsült költségek lekérése az Azure-szolgáltatások hozzáadása előtt

A következő szakaszok további információkkal szolgálnak a költségbecslésről a következő eszközök használatával:
- Azure-díjkalkulátor
- Azure Portal
- Költségkeret

A következő szakaszokban található képek példa díjszabásokat jelenítenek meg amerikai dollárban kifejezve.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>A díjkalkulátor használatával online becsülheti meg a költségeket

Tekintse meg a [díjkalkulátort](https://azure.microsoft.com/pricing/calculator/) a kívánt szolgáltatás becsült havi költségeinek lekéréséhez. A becsült költségek lekéréséhez hozzáadhat bármely belső Azure-erőforrást is. A díjkalkulátorban módosíthatja a pénznem típusát.

![Képernyőkép a díjkalkulátor menüjéről](./media/billing-getting-started/pricing-calc.png)

A díjkalkulátorban például az A1 Windows rendszerű virtuális gép üzemórában mért becsült költsége látható összeg/hónap lebontásban, ha a virtuális gép az időszak során végig üzemel:

![Képernyőkép a díjkalkulátorról, amely az A1 Windows rendszerű virtuális gép becsült havi költségét mutatja](./media/billing-getting-started/pricing-calcvm.png)

A díjszabással kapcsolatos további információkért tekintse meg a [díjszabással kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/pricing/faq/). Ha beszélni szeretne egy Azure-értékesítővel, hívja fel a GYIK oldal tetején található telefonszámot.

### <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

Egy szolgáltatás hozzáadásakor az Azure Portalon általában egy olyan nézet jelenik meg, amely a havi becsült díjat jeleníti meg a számlázott pénznemben. Amikor például a Windows rendszerű virtuális gép méretét kiválasztja, az üzemórák becsült havi díja látható:

![Példa: az A1 Windows rendszerű virtuális gép becsült havi költségének bemutatása](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Ellenőrizze, hogy állított-e be költségkeretet

Ha olyan előfizetése van, amely krediteket használ, akkor a költségkeret alapértelmezés szerint bekapcsol. Így a teljes kreditösszeg elköltése után hitelkártyája nem lesz megterhelve. Lásd: [Az Azure-ajánlatok teljes listája és a költségkeret elérhetősége](https://azure.microsoft.com/support/legal/offer-details/).

A költségkeret elérésekor azonban szolgáltatásai le lesznek tiltva. Ez azt jelenti, hogy virtuális gépei felszabadulnak. A szolgáltatáskiesést a költségkeret kikapcsolásával tudja elkerülni. Az esetleges többletköltségek a nyilvántartásba vett hitelkártyát terhelik.

Ha meg szeretné tekinteni, hogy van-e beállítva költségkeret, lépjen a [Fiókközpont előfizetési nézetéhez](https://account.windowsazure.com/Subscriptions). Ha be van állítva költségkeret, a következőhöz hasonló szalagcím jelenik meg:

![Képernyőkép a bekapcsolt fiókközpontbeli költségkeretről szóló figyelmeztetésről](./media/billing-getting-started/spending-limit-banner.png)

Kattintson a szalagcímre, és kövesse az utasításokat a költségkeret eltávolításához. Ha a regisztráció során nem adta meg hitelkártyája adatait, a költségkeret eltávolításához meg kell adnia azokat. További információkért lásd: [Az Azure-költségkeret – Hogyan működik, hogyan engedélyezhető és távolítható el](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségriasztások használata

A költségek kezeléséhez [költségvetéseket](../cost-management/tutorial-acm-create-budgets.md) és [riasztásokat](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek.

## <a name="monitor-costs-when-using-azure-services"></a>Költségek monitorozása az Azure-szolgáltatások használatakor
A költségek a következő eszközökkel monitorozhatók:

- Címkék
- Költséglebontás és keretfelhasználási ráta
- Költségelemzés

### <a name="tags"></a> Címkék hozzáadása az erőforrásokhoz a számlázási adatok csoportosítása érdekében

Címkék használatával csoportosíthatja a támogatott szolgáltatások számlázási adatait. Ha például több virtuális gépet futtat különböző csapatok számára, akkor a címkék segítségével kategorizálhatja a költségeket költséghely (például: HR, marketing, pénzügy stb.) vagy környezet (például éles környezet, üzem előtti, tesztelés) szerint.

![Képernyőkép a címkék beállításáról a portálon](./media/billing-getting-started/tags.png)

A címkék a különböző költségjelentési nézetekben jelennek meg. Például azonnal megjelennek a [költségelemzési nézetben](#costs), és az első számlázási időszak után a részletes használatot tartalmazó CSV-fájlban.

További információkért lásd: [Az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md) című cikket.

### <a name="costs"></a> A költséglebontás és a keretfelhasználási ráta monitorozása

Az Azure-szolgáltatások elindítása után rendszeresen tekintse meg a díjakat. Az aktuális költség- és keretfelhasználási ráta az Azure Portálon látható.

1. Látogasson el az [Előfizetések oldalra az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), és válasszon egy előfizetést.

2. Ha előfizetése támogatja, megjelenik a költséglebontás és a keretfelhasználási ráta.

    ![Képernyőkép az Azure Portalon látható keretfelhasználási rátáról és lebontásról](./media/billing-getting-started/burn-rate.PNG)

3. A bal oldali listában található [Költségelemzés](../cost-management/quick-acm-cost-analysis.md) elemre kattintva megtekintheti az erőforrásonként lebontott költségeket. A szolgáltatások hozzáadása után várjon 24 órát az adatok megjelenéséig.

    ![Költségelemzés nézet képernyőképe az Azure Portalon](./media/billing-getting-started/cost-analysis.png)

4. A szűrést különböző tulajdonságok, például [címkék](#tags), erőforrástípus, erőforráscsoport és időtartomány alapján végezheti el. Kattintson az **Alkalmaz** lehetőségre a szűrők megerősítéséhez, majd a **Letöltés** lehetőségre a nézet vesszővel tagolt (.csv kiterjesztésű) fájlba való exportálásához.

5. Emellett az erőforrásokra kattintva megtekintheti a napi kiadások előzményeit és az erőforrások költségét az egyes napokon.

    ![A költési előzmények nézet képernyőképe az Azure Portalon](./media/billing-getting-started/costhistory.png)

Hasonlítsa össze a megjelenített költségeket a szolgáltatások kiválasztásakor becsült költségekkel. Ha a költségek jelentősen eltérnek a becsült értéktől, ellenőrizze az erőforrásokhoz kiválasztott díjszabási tervet.

## <a name="optimize-and-reduce-costs"></a>Költségek optimalizálása és csökkentése
Ha nem ismeri a költségkezelés alapelveit, olvassa el [A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával](../cost-management/cost-mgt-best-practices.md) cikket.

Az Azure Portalon optimalizálhatja és csökkentheti is az Azure-ral kapcsolatos költségeket a virtuális gépek automatikus leállításával és az Advisor-javaslatokkal.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Vegye fontolóra a virtuális gépek automatikus leállításához hasonló költségkímélő funkciókat

A forgatókönyvtől függően a virtuális gépek automatikus leállítását is konfigurálhatja az Azure Portalon. További információ: [Virtuális gépek automatikus leállítása az Azure Resource Manager használatával](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Képernyőkép a portálon látható automatikus leállítás lehetőségről](./media/billing-getting-started/auto-shutdown.png)

Az automatikus leállítás nem ugyanaz, mint amikor a virtuális gépet az energiagazdálkodási lehetőségekkel állítja le. Az automatikus leállítás leállítja és felszabadítja a virtuális gépeket, így nem keletkeznek további használati díjak. További információt a [Linux rendszerű virtuális gépekre](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és a [Windows rendszerű virtuális gépekre](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vonatkozó díjszabással kapcsolatos gyakori kérdések oldalon talál.

A fejlesztési és tesztelési környezetekkel kapcsolatos további költségkímélő funkciókért tekintse meg az [Azure DevTest Labst](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Kapcsolja be és tekintse át az Azure Advisor javaslatait

Az [Azure Advisor](../advisor/advisor-overview.md) az alacsony kihasználtságú erőforrások azonosításával segítséget nyújt a költségek csökkentésében. Látogasson el az Azure Portalon az Advisor oldalra:

![Képernyőkép az Azure Portalon látható Azure Advisor gombról](./media/billing-getting-started/advisor-button.png)

Az Advisor irányítópultjának **Költségek** lapján a végrehajtható javaslatokat kaphat:

![Képernyőkép az Advisor költségekkel kapcsolatos javaslatainak egy példájáról](./media/billing-getting-started/advisor-action.png)

Tekintse át a [Javaslatok alapján történő költségoptimalizálás](../cost-management/tutorial-acm-opt-recommendations.md) oktatóanyagot a költségeket csökkentő Advisor-javaslatokról szóló irányított útmutatásért.

## <a name="review-costs-against-your-latest-invoice"></a>Költségek összehasonlítása a legutóbbi számlával

A legfrissebb számla a számlázási ciklus végén érhető el. Emellett [letöltheti a számlákat és a használatot részletező fájlokat is](billing-download-azure-invoice-daily-usage-date.md), hogy megbizonyosodhasson a kiszámlázott összeg helyességéről. A napi használati adatok és a számla összehasonlítására vonatkozó további információkért lásd: [Microsoft Azure-számlájának értelmezése](billing-understand-your-bill.md).

### <a name="billing-api"></a>Számlázási API

A használati adatok programozott módon való lekéréséhez használja az Azure számlázási API-t. A RateCard API és a Usage API együttes használatával lekérhető a számlázott használat. További információkért tekintse meg az [Információk a Microsoft Azure-erőforrások igénybevételéről](billing-usage-rate-card-overview.md) szakaszt.

## <a name="other-offers"></a> További forrásanyagok és különleges esetek

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP- és Sponsorship-ügyfelek
Az induláshoz lépjen kapcsolatba fiókja kezelőjével vagy Azure-partnerével.

| Ajánlat | További források |
|-------------------------------|-----------------------------------------------------------------------------------|
| Nagyvállalati Szerződés (EA) | [EA Portal](https://ea.azure.com/), [súgódokumentumok](https://ea.azure.com/helpdocs) és [Power BI-jelentések](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Felhőszolgáltató (CSP) | Beszéljen szolgáltatójával |
| Azure Sponsorship | [Sponsorship Portal](https://www.microsoftazuresponsorships.com/) |

Ha egy nagy méretű szervezet informatikai működését felügyeli, javasoljuk az [Azure Enterprise-szerkezet](/azure/architecture/cloud-adoption-guide/subscription-governance) és a [nagyvállalati informatikai tanulmány](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) elolvasását (letölthető .pdf-ként, csak angol nyelven).

#### <a name="EA"></a> Nagyvállalati Szerződések költségnézetei az Azure Portalon

A vállalati költségnézetek jelenleg nyilvános előzetes verzióban érhetők el. Megjegyzések:

- Az előfizetési költségek használaton alapulnak, és nem tartalmazzák az előre fizetett összegeket, a túllépéseket, a szolgáltatási kereteket, a helyesbítéseket és az adókat. A tényleges díjak kiszámítása a regisztráció szintjén történik.
- Az Azure Portalon megjelenített összegek eltérhetnek az Enterprise Portalon látható összegektől. Eltarthat pár percig, amíg az Enterprise Portal frissítései megjelennek az Azure Portalon.
- Ha nem látja a költségeket, az a következő okok valamelyike miatt lehet:
    - Nincsenek engedélyei az előfizetés szintjén. A vállalati költségnézetek megjelenítéséhez számlázási olvasó, olvasó, közreműködő vagy tulajdonos szereppel kell rendelkeznie előfizetési szinten.
    - Ön fióktulajdonos, és a regisztrációs adminisztrátor letiltotta a „díjtételek megtekintésének engedélyezését a fióktulajdonos számára”.  A költségekhez való hozzáférésért vegye fel a kapcsolatot a regisztrációs adminisztrátorral.
    - Ön részlegszintű tulajdonos, és a regisztrációs adminisztrátor letiltotta a **díjtételek megtekintésének engedélyezését a részlegszintű rendszergazda számára**.  Hozzáférésért vegye fel a kapcsolatot a regisztrációs adminisztrátorral.
    - Egy csatornapartneren keresztül vásárolta meg az Azure-t, és a partner nem adta meg a díjszabási információkat.  
- Ha az Enterprise Portalon frissíti a költséghozzáféréshez kapcsolódó beállításokat, néhány percig eltart, amíg a módosítások megjelennek az Azure Portalon.
- A költségkeret és a számlázási útmutató nem vonatkozik a Nagyvállalati Szerződéssel rendelkező előfizetésekre.

### <a name="check-your-subscription-and-access"></a>Az előfizetés és a hozzáférés ellenőrzése

A költségek megtekintéséhez [előfizetési szintű hozzáféréssel kell rendelkeznie a számlázási információkhoz](billing-manage-access.md). Csak a fiókadminisztrátor férhet hozzá a [Fiókközponthoz](https://account.azure.com/Subscriptions), változtathatja meg a számlázási információkat, illetve kezelheti az előfizetéseket. A fiókadminisztrátor az a személy, aki regisztrációt elvégezte. További tájékoztatásért tekintse meg az [Előfizetést vagy szolgáltatásokat kezelő Azure-rendszergazdai szerepkörök hozzáadása vagy módosítása](billing-add-change-azure-subscription-administrator.md) szakaszt.

Annak ellenőrzéséhez, hogy fiókadminisztrátor-e, lépjen az [Előfizetések az Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) szakaszhoz. Tekintse meg az előfizetések listáját, és keresse meg a **Saját szerepkör** elemet. Ha a *Fiókadminisztrátor* szerepkört látja, akkor teljes körű jogosultságokkal rendelkezik. Ha mást, például a *Tulajdonos* szerepkört, akkor nem rendelkezik teljes körű jogosultságokkal.

![Képernyőkép az Azure Portal Előfizetések nézetében feltüntetett szerepköréről](./media/billing-getting-started/sub-blade-view.PNG)

Az előfizetések kezeléséhez és számlázási adatok módosításához [forduljon a fiókadminisztrátorhoz](billing-subscription-transfer.md#whoisaa). Kérje meg a fiókadminisztrátort, hogy fejezze be a feladatokat, vagy [adja át az előfizetést Önnek](billing-subscription-transfer.md).

Ha a fiókadminisztrátor többé nem része a szervezetnek, és Önnek kell kezelnie a számlálást, [lépjen kapcsolatba velünk](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Szolgáltatói szerződési jóváírás kérelmezése egy szolgáltatási esemény miatt

A szolgáltatási szerződés (SLA) ismerteti a Microsoft az üzemidővel és hálózati elérhetőséggel kapcsolatos vállalásait. A szolgáltatási eseményeket a rendszer akkor jelenti, ha az Azure-szolgáltatások olyan problémát tapasztalnak, amely hatással van az üzemidőre vagy a kapcsolatra. Ezt gyakran *kimaradásnak* is nevezik. Ha nem érjük el és nem tartjuk fenn az egyes szolgáltatásokhoz tartozó, az SLA-ban leírt szolgáltatási szinteket, akkor Ön jogosult lehet a havi szolgáltatási díjak egy részének jóváírására.

Jóváírás kérése:

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com/). Ha több fiókja is van, ügyeljen arra, hogy azt a fiókot használja, amelyet az Azure-állásidő érintett. 
2. Hozzon létre egy új támogatási kérést.
3. A **Probléma típusa** területen válassza a **Számlázás** lehetőséget.
4. A **Probléma típusa** területen válassza a **Visszatérítési kérelem** lehetőséget.
5. A részletek megadásával egyértelműsítse, hogy SLA-jóváírást szeretne, említse meg a dátumot/időt/időzónát is, valamint az érintett szolgáltatásokat (virtuális gépek, webhelyek stb.)
6. Ellenőrizze a kapcsolattartási adatait, majd a kérés elküldéséhez válassza a **Létrehozás** elemet.

Az SLA-küszöbértékek szolgáltatásonként eltérőek. Például az SQL webszint 99,9%-os SLA-val, a virtuális gépek 99,95%-os SLA-val, a standard szintű SQL-csomag pedig 99,99%-os SLA-val rendelkezik.

Egyes szolgáltatások esetében az SLA alkalmazása előfeltételektől is függ. A virtuális gépeknek például azonos rendelkezésreállási csoportban két vagy több példányszámban kell üzemelniük.

További információkért tekintse meg a [Szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) és az [Azure-szolgáltatások SLA-jának összegzése](https://azure.microsoft.com/support/legal/sla/summary/) dokumentációt.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- A túlköltekezés elkerülése érdekében ismerkedjen meg a [költségkeretekkel](billing-spending-limit.md).
- Indítson [Azure-költségelemzést](../cost-management/quick-acm-cost-analysis.md).
