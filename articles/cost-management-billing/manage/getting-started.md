---
title: Váratlan költségek megelőzése és elemzése az Azure Cost Management and Billing eszközzel
description: Ismerje meg, hogyan kerülheti el a váratlan költségeket Azure-számláján, valamint hogyan használhatja az Azure-fiókja költségkövetési és felügyeleti funkcióit.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 61071655cfeaebd5a7e32a97abf208d03322164f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689915"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Hogyan kerülheti el a váratlan költségeket az Azure-számlázás és -költségkezelés használatával

Az Azure-ra való regisztráció során sokat tehet azért, hogy jobban megismerje a várható költségeket:

- Lekérheti a becsült költségeket a [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/), Azure-árlistával vagy szolgáltatások az Azure Portalon való hozzáadásakor.
- Monitorozhatja a költségeket [költségvetésekkel](../costs/tutorial-acm-create-budgets.md), [riasztásokkal](../costs/cost-mgt-alerts-monitor-usage-spending.md) és [költségelemzésekkel](../costs/quick-acm-cost-analysis.md).
- Áttekintheti a számlán feltüntetett díjakat a [használati adatokat tartalmazó fájlokkal](download-azure-invoice-daily-usage-date.md) összehasonlítva.
- Integrálhatja a számlázási és költségadatokat saját jelentéskészítő rendszerébe a [számlázási](https://docs.microsoft.com/rest/api/billing/) és [használati](https://docs.microsoft.com/rest/api/consumption/) API-kkal.
- A Nagyvállalati Szerződéses (EA), felhőszolgáltatói (CSP-) és Azure Sponsorship-ügyfelek részére elérhető további erőforrásokat vehet igénybe.
- [12 hónapig ingyenesen használhatja a legnépszerűbb Azure-szolgáltatások némelyikét](create-free-services.md), amelyek az [ingyenes Azure-fiókhoz](https://azure.microsoft.com/free/) érhetőek el. Az alább felsorolt ajánlásokon kívül tekintse meg, [hogyan kerülheti el, hogy díjak merüljenek fel ingyenes fiókja használatakor](avoid-charges-free-account.md).

Ha le szeretné mondani Azure-előfizetését, tekintse meg az [Az Azure-előfizetés lemondása](cancel-azure-subscription.md) című témakört.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Becsült költségek lekérése az Azure-szolgáltatások hozzáadása előtt

Az alábbi eszközöket használhatja az Azure-szolgáltatások költségeinek megbecsléséhez:
- Azure-díjkalkulátor
- Azure-árlista
- Azure Portal

A következő szakaszokban található képek példa díjszabásokat jelenítenek meg amerikai dollárban kifejezve.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>A díjkalkulátor használatával online becsülheti meg a költségeket

Tekintse meg a [díjkalkulátort](https://azure.microsoft.com/pricing/calculator/) a hozzáadni kívánt szolgáltatás becsült havi költségeinek lekéréséhez. Módosíthatja a pénznemet, hogy a helyi pénznemben kérje le a becsült költségeket.

![Képernyőkép a díjkalkulátor menüjéről](./media/getting-started/pricing-calc.png)

Megtekintheti bármely belső Azure-szolgáltatás becsült költségeit. Az alábbi képernyőképen például az A1 Windows rendszerű virtuális gép üzemórában mért 66,96 dollár/hónapos becsült költsége látható, ha a virtuális gép az időszak során végig üzemel:

![Képernyőkép a díjkalkulátorról, amely az A1 Windows rendszerű virtuális gép becsült havi költségét mutatja](./media/getting-started/pricing-calcvm.png)

A díjszabással kapcsolatos további információkért tekintse meg a [díjszabással kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/pricing/faq/). Ha beszélni szeretne egy Azure-értékesítővel, hívja fel a GYIK oldal tetején található telefonszámot.

### <a name="view-and-download-azure-price-sheet"></a>Azure-árlista megtekintése és letöltése

Ha az Azure-t Nagyvállalati Szerződéssel (EA) vagy Microsoft-ügyfélszerződéssel (MCA) használja, megtekintheti és letöltheti az árlistát az Azure-fiókjához. Az árlista egy Excel-fájl, amely tartalmazza az összes Azure-szolgáltatás árát. További információ: [Az Azure díjszabásának megtekintése és letöltése](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

Megtekintheti a becsült havi költségeket, miközben hozzáad egy szolgáltatást az Azure Portalon. Amikor például a Windows rendszerű virtuális gép méretét kiválasztja, az üzemórák becsült havi díja látható:

![Példa: az A1 Windows rendszerű virtuális gép becsült havi költségének bemutatása](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Költségek monitorozása az Azure-szolgáltatások használatakor
A költségek a következő eszközökkel monitorozhatók:

- Költségvetés- és költségriasztások
- Költségelemzés

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Költségek nyomon követése költségvetésekkel és költségriasztásokkal

A költségek kezeléséhez [költségvetéseket](../costs/tutorial-acm-create-budgets.md) és [riasztásokat](../costs/cost-mgt-alerts-monitor-usage-spending.md) hozhat létre, amelyek automatikusan figyelmeztetik Önt és az érdekelt feleket a rendellenes kiadásokról és a túlköltekezésről.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Költségek feltérképezése és elemzése a költségelemzés szolgáltatás használatával

Az Azure-szolgáltatások elindítása után rendszeresen tekintse meg a költségeket az Azure-költségek nyomon követéséhez. A költségelemzés segítségével megállapíthatja, hol keletkeznek az Azure használatából eredő költségei.

1. Nyissa meg [az Azure Portal Költségkezelés + számlázás oldalát](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. A képernyő bal oldalán kattintson a **Költségelemzés** elemre, hogy megtekinthesse az aktuális költségeket különféle területekre bontva (például szolgáltatás, hely és előfizetés). A szolgáltatások hozzáadása vagy a vásárlás végrehajtása után várjon 24 órát az adatok megjelenéséig. Alapértelmezés szerint a költségelemzés az aktuális hatókör adatait jeleníti meg. Például az alábbi képernyőképen a Contoso számlázási fiókjának költségei jelennek meg. A Hatókör elemmel másik hatókörre válthat a költségelemzésben. A hatókörökkel kapcsolatos további információért tekintse meg [A hatókörök ismertetése és használata](../costs/understand-work-scopes.md#scopes) szakaszt

    ![Költségelemzés nézet képernyőképe az Azure Portalon](./media/getting-started/cost-analysis.png)

4. A szűrést különböző tulajdonságok, például címkék, erőforrástípus, és időtartomány alapján végezheti el. Kattintson a **Szűrő hozzáadása** elemre egy szűrő tulajdonsághoz való hozzáadásához, majd válassza ki a szűrendő értékeket. Kattintson az **Exportálás** elemre a nézet vesszővel tagolt (.csv kiterjesztésű) fájlba való exportálásához.

5. Emellett a diagram címkéire kattintva megtekintheti a napi kiadások előzményeit az adott címkére vonatkozóan. Például: Ahogy az alábbi képernyőképen látható, a virtuális gépek lehetőségre kattintva megjelenik a virtuális gépek futtatásának napi költsége.

    ![A költési előzmények nézet képernyőképe az Azure Portalon](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Költségek optimalizálása és csökkentése
Ha nem ismeri a költségkezelés alapelveit, olvassa el [A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával](../costs/cost-mgt-best-practices.md) cikket.

Az Azure Portalon optimalizálhatja és csökkentheti is az Azure-ral kapcsolatos költségeket a virtuális gépek automatikus leállításával és az Advisor-javaslatokkal.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Vegye fontolóra a virtuális gépek automatikus leállításához hasonló költségkímélő funkciókat

A forgatókönyvtől függően a virtuális gépek automatikus leállítását is konfigurálhatja az Azure Portalon. További információ: [Virtuális gépek automatikus leállítása az Azure Resource Manager használatával](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Képernyőkép a portálon látható automatikus leállítás lehetőségről](./media/getting-started/auto-shutdown.png)

Az automatikus leállítás nem ugyanaz, mint amikor a virtuális gépet az energiagazdálkodási lehetőségekkel állítja le. Az automatikus leállítás leállítja és felszabadítja a virtuális gépeket, így nem keletkeznek további használati díjak. További információt a [Linux rendszerű virtuális gépekre](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és a [Windows rendszerű virtuális gépekre](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vonatkozó díjszabással kapcsolatos gyakori kérdések oldalon talál.

A fejlesztési és tesztelési környezetekkel kapcsolatos további költségkímélő funkciókért tekintse meg az [Azure DevTest Labst](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Kapcsolja be és tekintse át az Azure Advisor javaslatait

Az [Azure Advisor](../../advisor/advisor-overview.md) az alacsony kihasználtságú erőforrások azonosításával segítséget nyújt a költségek csökkentésében. Keresse meg az **Advisor** elemet az Azure Portalon:

![Képernyőkép az Azure Portalon látható Azure Advisor gombról](./media/getting-started/advisor-button.png)

A bal oldalon válassza a **Költség** lehetőséget. A**Költségek** lapon végrehajtható javaslatokat fog látni:

![Képernyőkép az Advisor költségekkel kapcsolatos javaslatainak egy példájáról](./media/getting-started/advisor-action.png)

Tekintse át a [Javaslatok alapján történő költségoptimalizálás](../costs/tutorial-acm-opt-recommendations.md) oktatóanyagot a költségeket csökkentő Advisor-javaslatokról szóló irányított útmutatásért.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integrálás számlázási és használati API-kkal

Használja az Azure [számlázási](https://docs.microsoft.com/rest/api/billing/) és [használati](https://docs.microsoft.com/rest/api/consumption/) API-jait a számlázással és a költségekkel kapcsolatos adatok programozott módon való lekéréséhez. A RateCard API és a Usage API együttes használatával lekérhető a számlázott használat. További információkért tekintse meg az [Információk a Microsoft Azure-erőforrások igénybevételéről](usage-rate-card-overview.md) szakaszt.

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> További forrásanyagok és különleges esetek

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP- és Sponsorship-ügyfelek
Az induláshoz lépjen kapcsolatba fiókja kezelőjével vagy Azure-partnerével.

| Ajánlat | További források |
|-------------------------------|-----------------------------------------------------------------------------------|
| Nagyvállalati Szerződés (EA) | [EA Portal](https://ea.azure.com/), [súgódokumentumok](https://ea.azure.com/helpdocs) és [Power BI-jelentések](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Felhőszolgáltató (CSP) | Beszéljen szolgáltatójával |
| Azure Sponsorship | [Sponsorship Portal](https://www.microsoftazuresponsorships.com/) |

Ha egy nagy méretű szervezet informatikai működését felügyeli, javasoljuk az [Azure Enterprise-szerkezet](/azure/architecture/cloud-adoption-guide/subscription-governance) és a [nagyvállalati informatikai tanulmány](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) elolvasását (letölthető .pdf-ként, csak angol nyelven).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Nagyvállalati Szerződések költségnézetei az Azure Portalon

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

A költségek megtekintéséhez fiók- vagy előfizetési szintű hozzáféréssel kell rendelkeznie a költségekkel vagy a számlázással kapcsolatos információkhoz. A hozzáférés a számlázási fiók típusától függően eltérő. A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókok az Azure Portalon történő megtekintését](view-all-accounts.md) ismertető cikket.

Amennyiben az Azure-t egy Microsoft Online Service Programba (MOSP) tartozó számlázási fiókon keresztül használja, tekintse meg az [Azure-beli számlázási információkhoz való hozzáférés kezelése](manage-billing-access.md) című szakaszt.

Amennyiben az Azure-t Nagyvállalati Szerződéssel (EA) rendelkező számlázási fiókkal használja, tekintse meg az [Azure Nagyvállalati Szerződés rendszergazdai szerepkörei az Azure-ban](understand-ea-roles.md) című szakaszt.

Amennyiben az Azure-t Microsoft-ügyfélszerződéssel (MCA) rendelkező számlázási fiókkal használja, tekintse meg [a Microsoft-ügyfélszerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Szolgáltatói szerződési jóváírás kérelmezése egy szolgáltatási esemény miatt

A szolgáltatási szerződés (SLA) ismerteti a Microsoft üzemidővel és hálózati elérhetőséggel kapcsolatos vállalásait. A szolgáltatási eseményeket a rendszer akkor jelenti, ha az Azure-szolgáltatások olyan problémát tapasztalnak, amely hatással van az üzemidőre vagy a kapcsolatra. Ezt gyakran *kimaradásnak* is nevezik. Ha nem érjük el és nem tartjuk fenn az egyes szolgáltatásokhoz tartozó, az SLA-ban leírt szolgáltatási szinteket, akkor Ön jogosult lehet a havi szolgáltatási díjak egy részének jóváírására.

Jóváírás kérése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Ha több fiókja is van, ügyeljen arra, hogy azt a fiókot használja, amelyet az Azure-állásidő érintett.
2. Hozzon létre egy új támogatási kérést.
3. A **Probléma típusa** területen válassza a **Számlázás** lehetőséget.
4. A **Probléma típusa** területen válassza a **Visszatérítési kérelem** lehetőséget.
5. A részletek megadásával egyértelműsítse, hogy SLA-jóváírást szeretne, és határozza meg a dátumot/időt/időzónát, valamint az érintett szolgáltatásokat (virtuális gépek, webhelyek stb.).
6. Ellenőrizze a kapcsolattartási adatait, majd a kérés elküldéséhez válassza a **Létrehozás** elemet.

Az SLA-küszöbértékek szolgáltatásonként eltérőek. Például az SQL webszint 99,9%-os SLA-val, a virtuális gépek 99,95%-os SLA-val, a standard szintű SQL-csomag pedig 99,99%-os SLA-val rendelkezik.

Egyes szolgáltatások esetében az SLA alkalmazása előfeltételektől is függ. A virtuális gépeknek például azonos rendelkezésreállási csoportban két vagy több példányszámban kell üzemelniük.

További információkért tekintse meg a [Szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) és az [Azure-szolgáltatások SLA-jának összegzése](https://azure.microsoft.com/support/legal/sla/summary/) dokumentációt.

## <a name="analyze-unexpected-charges"></a>Váratlan költségek elemzése

A szervezete számára készített felhőalapú erőforrás-infrastruktúra valószínűleg összetett. Számos Azure-erőforrástípus különböző típusú költségekkel járhat. Előfordulhat, hogy az Azure-erőforrásokat a szervezet különböző csoportjai birtokolják, amelyek különböző erőforrásokra vonatkozó eltérő számlázási modelltípusokkal rendelkeznek. A költségek jobb megértése érdekében kezdje az elemzést a következő szakaszokban lévő stratégiákkal.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Számla áttekintése és a költségért felelős erőforrás azonosítása

Az Azure-szolgáltatások megvásárlásának módja segít meghatározni az Ön számára elérhető módszertant és eszközöket a költséggel társított erőforrás azonosítása során. Annak megállapításához, hogy melyik módszertan vonatkozik Önre, először [határozza meg az Azure-ajánlat típusát](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Ezután azonosítsa az ügyfélkategóriát a [támogatott Azure-ajánlatok](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers) listájában.

A következő cikkek részletesen ismertetik, hogyan tekintheti át a számláját az ügyfél típusa alapján. Mindegyik cikkben útmutatást talál arról, hogyan tölthető le egy adott számlázási időszak használati és költségadatait tartalmazó CSV-fájl.

- [Használatalapú számla áttekintési folyamata](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Nagyvállalati szerződéses számla áttekintési folyamata](../understand/review-enterprise-agreement-bill.md)
- [Microsoft-ügyfélszerződés áttekintési folyamata](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft-partnerszerződés áttekintési folyamata](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Az Azure-számla _mérőeszközök_ alapján összesíti a havi költségeket. A mérőeszközökkel az erőforrás kihasználtságát követjük nyomon időben, és ezekkel számítható ki a számla. Ha egyetlen Azure-erőforrást (például virtuális gépet) hoz létre, akkor egy vagy több mérőeszközpéldány jön létre az erőforráshoz.

A használati adatokat tartalmazó CSV-fájlt az elemezni kívánt számlán található _MeterName_ alapján szűrheti a mérőeszközre vonatkozó összes sorelem megtekintéséhez. A sorelemhez tartozó _InstanceID_ a díjat okozó tényleges Azure-erőforrásnak felel meg.

Ha azonosította a szóban forgó erőforrást, az Azure Cost Management költségelemzési eszközével tovább elemezheti az erőforrással kapcsolatos költségeket. A költségelemzéssel kapcsolatos további tudnivalókért lásd: [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md).

### <a name="review-invoiced-charges-in-cost-analysis"></a>A számlázott díjtételek áttekintése a Költségelemzésben

A számlaadatok Azure Portalon való megtekintéséhez keresse meg a költségelemzést ahhoz a hatókörhöz, amely az elemezni kívánt számlához kapcsolódik. Válassza a **Számla részletei** nézetet. A száma részletei úgy jelenítik meg a díjtételeket, ahogy a számlán szerepelnek.

[![A számla részleteit bemutató példa](./media/getting-started/invoice-details.png)](./media/getting-started/invoice-details.png#lightbox)

A számlázási adatok megtekintésével azonosíthatja, hogy melyik szolgáltatás okozott váratlan költségeket, és megállapíthatja, hogy melyik erőforrások kapcsolódnak közvetlenül ehhez az erőforráshoz a Költségelemzésben. Ha például engedélyezni kívánja a Virtual Machines szolgáltatás díjait, navigáljon a **Halmozott költség** nézetre. A részletességet ezután állítsa **Napi** értékre, a díjakat szűréséhez adja meg a **Szolgáltatásnév: Virtual Machines** értéket, majd csoportosítsa a díjakat **Erőforrás** szerint.

[![A virtuális gépek halmozott költségeit bemutató példa](./media/getting-started/virtual-machines.png)](./media/getting-started/virtual-machines.png#lightbox)


### <a name="identify-spikes-in-cost-over-time"></a>Kiugró költségek azonosítása időben

Előfordulhat, hogy néha nem tudja, milyen költségek eredményezték a számlázott költségek változásait. A költségelemzéssel [láthatja a költségek napi és havi bontását](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month), így megértheti, hogy mi változott. A nézet létrehozása után csoportosíthatja a költségeket **szolgáltatás** vagy **erőforrás** alapján is a változások azonosításához. **Vonaldiagramra** is változtathatja a nézetet, hogy jobban láthassa az adatokat.

![A költségelemzésben a költségeket időben megjelenítő példa](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Az erőforrás-díjszabás meghatározása és a számlázási modell megértése

Egyetlen erőforrás több Azure-termék és -szolgáltatás díját is maga után vonhatja. [Azure-díjszabás termék alapján](https://azure.microsoft.com/pricing/#product-pricing) oldalon tudhat meg többet az egyes Azure-szolgáltatások díjszabásáról. Az Azure-ban létrehozott egyetlen virtuális gép használatának nyomon követéséhez például a következő mérőeszközök hozhatók létre. Előfordulhat, hogy mindegyiknek külön díjszabása van.

- Üzemóra
- IP-cím szolgáltatásórái
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard felügyelt lemez
- Standard felügyeltlemez-műveletek
- Standard IO-lemez
- Standard IO – blokkblob-olvasás
- Standard IO – blokkblob-írás
- Szabványos IO – blokkblob-törlés

A virtuális gép létrehozásakor minden mérőeszköz elkezd használati adatokat szolgáltatni. Ezt a használatot és a mérőeszköz díjszabását az Azure-beli mérési rendszerben követjük nyomon. A számla összegének kiszámításához használt mérőeszközök a használati adatok CSV-fájljában láthatók.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Az erőforrásért felelős személyek megkeresése

Gyakran előfordul, hogy az adott erőforrásért felelős csapat tudatában van az adott erőforrással kapcsolatos változásoknak. A velük való kapcsolattartás hasznos lehet, mivel megállapíthatja, miért jelentkeznek díjak. Előfordulhat például, hogy a tulajdonos csapat nemrég hozta létre az erőforrást, frissítette a termékváltozatot (ami megváltoztatja az erőforrás díjszabását), vagy megnövelte az erőforrás terhelését a kód módosítása miatt. Az erőforrás tulajdonosának meghatározásához a következő részekben talál további technikákat.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>Az erőforrás auditnaplóinak elemzése

Amennyiben jogosult az erőforrás megtekintésére, rendelkeznie kell hozzáféréssel az auditnaplókhoz. Tekintse át a naplókat annak érdekében, hogy megtalálja, melyik felhasználó felelős az erőforrás legfrissebb módosításaiért. További információt az [Azure-tevékenységnapló eseményeinek megtekintését és lekérését](../../azure-monitor/platform/activity-log-view.md) ismertető szakaszban talál.

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>A felhasználói engedélyek elemzése az erőforrás fölérendelt hatókörében

Az előfizetéshez vagy erőforráscsoporthoz írási hozzáféréssel rendelkező személyek általában birtokában vannak a létrehozott erőforrásokkal kapcsolatos információnak. El kell tudniuk magyarázni egy erőforrás célját, vagy tudniuk kell, hogy ki az, aki erre választ tud adni. Az előfizetési hatókör engedélyeivel rendelkező személyek azonosításához tekintse meg a [szerepkör-hozzárendelések megtekintését](../../role-based-access-control/check-access.md#view-role-assignments) ismertető szakaszt. Hasonló módszert alkalmazhat erőforráscsoportok esetében.

### <a name="get-help-to-identify-charges"></a>Segítség kérése a díjak azonosításához

Ha már használta az előző stratégiákat, és még mindig nem érti, miért léptek fel költségek, vagy ha további segítségre van szüksége a számlázási problémákkal kapcsolatban, kérjük, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- A túlköltekezés elkerülése érdekében ismerkedjen meg a [költségkeretekkel](spending-limit.md).
- Indítson [Azure-költségelemzést](../costs/quick-acm-cost-analysis.md).
