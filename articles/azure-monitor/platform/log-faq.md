---
title: Log Analytics GYIK | Microsoft Docs
description: Válaszok a Azure Monitor logs Analytics szolgáltatással kapcsolatos gyakori kérdésekre.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394782"
---
# <a name="log-analytics-faq"></a>Log Analytics – gyakori kérdések

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Microsoft gyakori kérdései a Azure Monitor Log Analytics munkaterülettel kapcsolatos gyakori kérdések listája. Ha további kérdése van a Log Analytics kapcsolatban, látogasson el a [vitafórumra](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) , és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.


## <a name="new-logs-experience"></a>Új naplók – felhasználói élmény

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>K: mi a különbség az új naplók tapasztalata és a Log Analytics között?

A: ugyanaz a dolog. A [log Analytics a Azure monitor szolgáltatásként van integrálva](../../azure-monitor/azure-monitor-rebrand.md) , hogy egységesített figyelési élményt nyújtson. Az új naplók Azure Monitor pontosan ugyanazok, mint a sok ügyfél által már használt Log Analytics lekérdezések.

### <a name="q-can-i-still-use-log-search"></a>K: továbbra is használhatom a naplóbeli keresést? 

A: a naplóbeli keresés jelenleg továbbra is elérhető a OMS-Portálon és a Azure Portal a név **naplók alatt (klasszikus)** . A OMS-portált hivatalosan 2019 január 15-én visszavonjuk. A klasszikus naplók Azure Portal fokozatosan megszűnnek, és felváltják az új naplók felhasználói élményét. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>K. Továbbra is használhatom a speciális elemzési portált? 
A Azure Portalban az új naplók a fejlett elemzési portálon alapulnak, de továbbra is elérhetők a Azure Portaln kívül. A külső portál kivonására vonatkozó ütemterv hamarosan bejelentve lesz.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>K. Miért nem látom a Query Explorer és a Save gombokat az új naplók felületén?

A **lekérdezési tallózó**, a **Mentés** és a **riasztási gombok beállítása** nem érhető el, ha egy adott erőforrás kontextusában keres naplókat. A riasztások létrehozásához vagy a lekérdezések betöltéséhez a naplókat hatókörbe kell állítani egy munkaterületre. Ha meg szeretné nyitni a naplókat a munkaterület környezetében, válassza a **minden szolgáltatás** > **figyelő** > **naplók**lehetőséget. A legutóbb használt munkaterület van kiválasztva, de más munkaterületet is kijelölhet. További információért lásd: [adatok megtekintése és elemzése log Analyticsban](../log-query/portals.md) .

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>K. Hogyan kinyerni az egyéni mezőket az új naplók felületén? 

A: az egyéni mezők kinyerése jelenleg a klasszikus naplók felületén támogatott. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>K. Hol találhatok listanézet-nézetet az új naplókon? 

A: listanézet nem érhető el az új naplókban. Az eredmények tábla minden rekordjának bal oldalán van egy nyíl. Erre a nyílra kattintva megnyithatja egy adott rekord részleteit. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>K. A lekérdezés futtatása után a javasolt szűrők listája elérhető. Hogyan tekinthetem meg a szűrőket? 

A: kattintson a bal oldali ablaktábla szűrők elemére az új szűrők megvalósításának megtekintéséhez. Ez mostantól a teljes eredményhalmaz alapján történik, és nem korlátozza a felhasználói felület 10 000-as rekordjának korlátját. Ez jelenleg a legnépszerűbb szűrők listája, valamint az egyes szűrők 10 leggyakoribb értéke. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>K. Miért kapok hibaüzenetet: "erőforrás-szolgáltató regisztrálása" Microsoft. bepillantást ehhez az előfizetéshez, hogy engedélyezze ezt a lekérdezést a naplókban a virtuális gép befúrása után? 

A: alapértelmezés szerint számos erőforrás-szolgáltató automatikusan regisztrálva van, azonban előfordulhat, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. Ezzel konfigurálja az előfizetését az erőforrás-szolgáltatóval való együttműködésre. A regisztráció hatóköre mindig az előfizetés. További információ: [Erőforrás-szolgáltatók és típusaik](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>K. Miért nem kapok hozzáférési hibaüzenetet, amikor Hozzáférek a naplókhoz a virtuális gép oldaláról? 

A: a virtuális gépek naplófájljainak megtekintéséhez olvasási engedéllyel kell rendelkeznie a virtuálisgép-naplókat tároló munkaterületekhez. Ezekben az esetekben a rendszergazdának Önnek kell megadnia Önnek az Azure-beli engedélyeket.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>K. Miért tudok hozzáférni a munkaterülethez a OMS-portálon, de a "nincs hozzáférése" hibaüzenet jelenik meg a Azure Portalban?  

A: az Azure-munkaterületek eléréséhez Azure-engedélyekkel kell rendelkeznie. Bizonyos esetekben előfordulhat, hogy nem rendelkezik megfelelő hozzáférési engedélyekkel. Ezekben az esetekben a rendszergazdának engedélyeket kell biztosítania az Azure-ban. További információért lásd: [OMS-portál az Azure](oms-portal-transition.md) -ba való áttéréshez.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>K. Miért nem látom a Tervezőbeli bejegyzést a naplókban?

A: a tervező csak a közreműködői engedélyekkel vagy annál magasabb szintű felhasználók naplóiban érhető el.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>K. Továbbra is használhatom az Azure-on kívüli elemzési portált?

A. Igen, a naplók lap az Azure-ban és a speciális elemzési portálon ugyanarra a kódra épül. A Log Analytics a Azure Monitor szolgáltatásként van integrálva, hogy egységesített figyelési élményt nyújtson. Továbbra is elérheti az elemzési portált az URL-cím használatával: https:\/\/Portal. loganalytics. IO/Subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/munkaterületek/{workspaceName}.



## <a name="general"></a>Általános

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>K. Hogyan láthatom a nézeteiket és a megoldásokat a Azure Portal? 

A: a nézetek és a telepített megoldások listája Azure Portal érhető el. Kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában válassza a **figyelés**, majd a **... lehetőséget. Továbbiak**. A legutóbb használt munkaterület van kiválasztva, de más munkaterületet is kijelölhet. 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>K. Log Analytics ugyanazt az ügynököt használja, mint Azure Security Center?

A: a 2017 júniusának elején Azure Security Center a Microsoft monitoring Agent használatával gyűjti össze és tárolja az adatokat. További információ: [Azure Security Center platform migrálása – gyakori kérdések](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>K. Milyen ellenőrzéseket hajtanak végre az AD és a SQL Assessment megoldások?

A: a következő lekérdezés az összes jelenleg elvégzett ellenőrzés leírását tartalmazza:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Az eredmények az Excel programba exportálhatók további áttekintés céljából.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>K. Miért látok valami mást, mint a OMS a System Center Operations Manager-konzolon?

A: attól függően, hogy a Operations Manager milyen kumulatív frissítéssel rendelkezik, megjelenhet a *System Center Advisor*, a *Operational Insights*vagy a *log Analytics*csomópontja.

A *OMS* -re való frissítés egy felügyeleti csomagban szerepel, amelyet manuálisan kell importálni. Az aktuális szöveg és funkció megjelenítéséhez kövesse a legújabb System Center Operations Manager kumulatív frissítés TUDÁSBÁZISa című cikk utasításait, és frissítse a konzolt.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>K: van Log Analytics helyszíni verziója?

V: Nem. A Log Analytics egy skálázható felhőalapú szolgáltatás, amely nagy mennyiségű adattal dolgoz fel és tárol. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>K. Hogyan lehet értesítést kapni, ha az adatgyűjtés leáll?

A: az [új naplózási riasztás létrehozása](../../azure-monitor/platform/alerts-metric.md) című témakörben ismertetett lépéseket követve értesítheti az adatgyűjtés leállásáról.

Amikor az adatgyűjtés leáll, a riasztás létrehozásakor állítsa be a következőt:

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - **Jel neve** válassza az **egyéni naplók keresése**lehetőséget.
   - A **Keresési lekérdezés** legyen a következő: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **A riasztás logikája** az *eredmények* és a **feltétel** **alapján** *meghaladja* a *0* **küszöbértéket**
   - *30* perces **időtartam** , a **riasztások gyakorisága** *10* percenként
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - Az *adatgyűjtési név leállítva*
   - A **Súlyosság** legyen *Figyelmeztetés*

Válasszon egy meglévőt, vagy hozzon létre egy új [műveleti csoportot](../../azure-monitor/platform/action-groups.md) , hogy amikor a naplózási riasztás megfelel a feltételeknek, értesítést kap, ha 15 percnél hosszabb szívverés van megadva.

## <a name="configuration"></a>Konfiguráció

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>K. Módosíthatom a Azure Diagnostics (WAD) olvasására használt tábla/blob-tároló nevét?

A. Nem, jelenleg nem lehet beolvasni az Azure Storage-ban lévő tetszőleges táblákból vagy tárolóból.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>K. Milyen IP-címeket használ a Log Analytics szolgáltatás? Hogyan gondoskodjon arról, hogy a tűzfal csak a Log Analytics szolgáltatás felé irányuló forgalmat engedélyezze?

A. A Log Analytics szolgáltatás az Azure-ra épül. Log Analytics IP-címek az [Microsoft Azure Datacenter IP-tartományokban](https://www.microsoft.com/download/details.aspx?id=41653)találhatók.

A szolgáltatás telepítése során a Log Analytics szolgáltatás tényleges IP-címei módosulnak. A tűzfalon keresztül engedélyezett DNS-neveket a [hálózati követelmények](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)dokumentálják.

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>K. ExpressRoute használok az Azure-hoz való csatlakozáshoz. A Log Analytics-forgalmom használom az ExpressRoute-kapcsolatokat?

A. A ExpressRoute különböző típusú forgalmát a [ExpressRoute dokumentációja](../../expressroute/expressroute-faqs.md#supported-services)tartalmazza.

A Log Analytics felé irányuló forgalom a nyilvános társ-ExpressRoute áramkört használja.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>K. Van egy egyszerű és egyszerű módja egy meglévő Log Analytics munkaterület áthelyezésének egy másik Log Analytics-munkaterületre vagy Azure-előfizetésre?

A. A `Move-AzResource` parancsmaggal áthelyezheti egy Log Analytics munkaterületet és egy Automation-fiókot is egy másik Azure-előfizetésből. További információ: [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Ezt a változást a Azure Portal is elvégezheti.

Nem helyezhetők át adatok az egyik Log Analytics munkaterületről a másikra, vagy megváltoztathatják azt a régiót, amelyben az adatok Log Analytics tárolódnak.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>K: Hogyan Log Analytics hozzáadása a System Center Operations Managerhoz?

A: a legújabb kumulatív frissítésre és a felügyeleti csomagok importálására való frissítés lehetővé teszi Operations Manager összekapcsolását Log Analyticshoz.

>[!NOTE]
>A Operations Manager Log Analyticshoz való kapcsolódás csak System Center Operations Manager 2012 SP1 és újabb rendszerekhez érhető el.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>K: Hogyan tudom ellenőrizni, hogy az ügynök képes-e kommunikálni Log Analytics?

A: annak biztosítása érdekében, hogy az ügynök képes legyen kommunikálni az Log Analytics munkaterülettel, lépjen a következőre: Vezérlőpult, biztonsági & beállítások, **Microsoft monitoring Agent**.

Az **Azure log Analytics (OMS)** lapon keresse meg a zöld pipa jelet. A zöld pipa ikon megerősíti, hogy az ügynök képes kommunikálni az Azure szolgáltatással.

A sárga figyelmeztető ikon azt jelenti, hogy az ügynök problémába ütközik a Log Analyticsával folytatott kommunikáció során. Ennek egyik gyakori oka, hogy a Microsoft monitoring Agent szolgáltatás leállt. A Service Control Manager használatával indítsa újra a szolgáltatást.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>K: Hogyan leállítani az ügynököt a Log Analyticsval való kommunikációra?

A: System Center Operations Managerban távolítsa el a számítógépet a Log Analytics felügyelt számítógépek listából. Operations Manager frissíti az ügynök konfigurációját, hogy a továbbiakban ne jelentsen Log Analytics. Az Log Analytics közvetlenül csatlakozó ügynökök esetében leállíthatja őket a következő módon: Vezérlőpult, biztonsági & beállítások, **Microsoft monitoring Agent**.
Az **Azure log Analytics (OMS)** területen távolítsa el a felsorolt munkaterületeket.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>K: Miért kapok hibaüzenetet, amikor megpróbálom áthelyezni a munkaterületet egy Azure-előfizetésből egy másikba?

A: Ha egy munkaterületet másik előfizetéshez vagy erőforráscsoporthoz szeretne áthelyezni, először le kell kapcsolni az Automation-fiókot a munkaterületen. Az Automation-fiók leválasztásához szükség van a megoldások eltávolítására, ha azokat a munkaterületre telepítették: Update Management, Change Tracking vagy Start/Stop VMs during off-hours el lesznek távolítva. A megoldások eltávolítása után az Automation-fiók erőforrás bal oldali ablaktábláján válassza a **csatolt munkaterületek** lehetőséget, majd kattintson a menüszalagon a **munkaterület** leválasztása elemre.
 > Az eltávolított megoldásokat a munkaterületen újra kell telepíteni, és a munkaterületre mutató automatizálási hivatkozást az áthelyezést követően újra kell állítani.

Ellenőrizze, hogy rendelkezik-e engedéllyel mindkét Azure-előfizetésben.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>K: Miért kapok hibaüzenetet, amikor megpróbálok frissíteni egy SavedSearch?

A: hozzá kell adnia a "ETAG" parancsot az API törzsében, vagy a Azure Resource Manager sablon tulajdonságait:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Ügynöki adatkészletek
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>K. Milyen mennyiségű adatküldhető az ügynökön keresztül a Log Analytics? Van maximális adatmennyiség felhasználónként?
A. A feltöltött adatmennyiségnek nincs korlátja, a kiválasztható díjszabási vagy utólagos elszámolási lehetőségen alapul. A Log Analytics munkaterület úgy lett kialakítva, hogy automatikusan felskálázásra kerüljön az ügyféltől érkező kötetek kezelésére, még akkor is, ha naponta terabájtos. További információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/monitor/).

A Log Analytics ügynök úgy lett kialakítva, hogy a kis helyigényű legyen. Az adatmennyiség az Ön által engedélyezett megoldások alapján változhat. Részletes információkat találhat az adatkötetről, és megtekintheti a [használat](../../azure-monitor/platform/data-usage.md) oldalon a megoldás bontása című témakört.

További információért olvassa el a Log Analytics ügynök erőforrás-kihasználtságának (lábnyomának) kiértékelése után az eredményeket bemutató [ügyfél-blogot](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) .

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>K. Mennyi hálózati sávszélességet használ a Microsoft Management agent (MMA), amikor adatokat küld a Log Analytics?

A. A sávszélesség a továbbított adatmennyiség függvénye. Az adat tömörítve van, mert a hálózaton keresztül küldi el a rendszer.

### <a name="q-how-much-data-is-sent-per-agent"></a>K. Mennyibe kerül az adatküldés/ügynök?

A. Az ügynökök által elküldett adatok mennyisége a következőktől függ:

* Az engedélyezett megoldások
* A begyűjtött naplók és teljesítményszámlálók száma
* A naplókban tárolt adatmennyiség

A [használat](../../azure-monitor/platform/data-usage.md) lapon a teljes használat látható.

A WireData-ügynököt futtató számítógépek esetében a következő lekérdezéssel tekintheti meg az adatküldés mennyiségét:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [Azure Monitorekkel](../../azure-monitor/overview.md) , és percek alatt megtudhatja, hogyan kezdheti meg a log Analytics.
