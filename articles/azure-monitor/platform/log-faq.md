---
title: Log Analytics – gyakori kérdések |} A Microsoft Docs
description: Az Azure Log Analytics szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: a2213ac9b31623aed18855ce87e7e00870b05247
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107227"
---
# <a name="log-analytics-faq"></a>Log Analytics – gyakori kérdések
A Microsoft FAQ a Microsoft Azure Log Analytics kapcsolatos gyakori kérdések listáját. Ha a Log Analytics szolgáltatásról további kérdése van, lépjen a [fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) és felteheti kérdéseit. Egy kérdést gyakran ismételt, amikor hozzáadjuk ehhez a cikkhez, hogy gyorsan és könnyen megtalálhatók.


## <a name="new-logs-experience"></a>Az új naplók élmény

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>KÉRDÉS: Mi a különbség az új naplók élmény és a Log Analytics?

V: Azok a ugyanaz. [A log Analytics az Azure Monitor szolgáltatásban integráció](../../azure-monitor/azure-monitor-rebrand.md) biztosít egy egységes felügyeleti kezelőfelület. Az új naplók az Azure monitorban felhasználói felület pontosan ugyanaz, mint a Log Analytics-lekérdezéseket, amelyek a számos ügyfél már használt.

### <a name="q-can-i-still-use-log-search"></a>KÉRDÉS: Továbbra is használható a naplóbeli keresés? 

V: Naplóbeli keresés jelenleg továbbra is elérhető, az OMS-portálon, és a név alatt az Azure Portalon **naplók (klasszikus)**. Az OMS-portálon a 2019. január 15 hivatalosan megszűnik. Az Azure Portalon a klasszikus naplók élményt fokozatosan megszűnik, és az új naplók felület helyett. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>K. Továbbra is használható a speciális analitika portálon? 
Az új naplók az Azure Portal felhasználói felület a speciális analitikai portálon alapul, de továbbra is elérhető az Azure Portalon kívül. Az ütemterv kivonása a külső portál hamarosan fogjuk bejelenteni.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>K. Miért nem lásd: a Query Explorer és gombok mentse az új naplók funkció?

**Lekérdezési Explorer**, **mentése** és **riasztási beállítása** gombok nem érhetők el, egy adott erőforrás kontextusában naplók vizsgálata esetén. Mentse az értesítések létrehozásakor, vagy egy lekérdezés betöltése, naplók hatókörét kell állítani egy munkaterülethez. Nyissa meg a naplók munkaterület a környezetben, jelölje be **minden szolgáltatás** > **figyelő** > **naplók**. Az utolsó használt munkaterület van kijelölve, de más munkaterületekhez is kiválaszthat. Lásd: [megtekintése és elemzése a Log Analytics data](../log-query/portals.md) további információt.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>K. Hogyan kinyerése az új naplók élmény az egyéni mezők? 

V: Egyéni mezők kinyerése a hagyományos jelenleg támogatott naplók tapasztalható. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>K. Hol találhatók az új naplók az listanézet? 

V: Lista nézet nem érhető el az új naplók. Az egyes rekordok az eredmények táblázatában balra nyíl van. Kattintson a nyílra kattintva nyissa meg a egy adott rekord részleteit. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-shows-up-but-it-doesnt-include-all-filters-how-can-i-see-the-rest"></a>K. A lekérdezés futtatása után javasolt szűrők listája megjelenik-e, de nem tartalmazza az összes szűrő. Hogyan tekinthetem meg a többi? 

V: Jelenleg látható előzetes verziója új szűrőket végrehajtására. Ez most már folyamatban van a felhasználói felület legfeljebb 10 000 rekordot korlátozza helyett a teljes eredményhalmaz alapul. Ez a funkció jelenleg a legnépszerűbb szűrőket és az egyes szűrők a 10 leggyakrabban használt értékek listáját. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>K. Miért jelenik meg a hiba: "Erőforrás-szolgáltató" a Microsoft.Insights regisztrálása"ehhez az előfizetéshez ahhoz, hogy ez a lekérdezés" naplókat, a virtuális gépről a részletező elemzés után? 

V: Alapértelmezés szerint automatikusan regisztrált sok erőforrás-szolgáltató, azonban előfordulhat, hogy néhány erőforrás-szolgáltatókat manuálisan regisztrálni kell. Ez konfigurálja a-előfizetés használata az erőforrás-szolgáltató. Regisztráció a hatókör, mindig az előfizetést. További információ: [Erőforrás-szolgáltatók és típusaik](../../azure-resource-manager/resource-manager-supported-services.md#portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>K. Miért érkeznek meg hozzám a nincs hozzáférés hibaüzenet egy virtuális gép oldaláról naplók elérésekor? 

V: Virtuális gépek naplóinak megtekintéséhez meg kell olvasási engedéllyel a munkaterülethez, amely tárolja a virtuális gépek naplóinak meg kell adni. Ezekben az esetekben a rendszergazda biztosítania kell, és az engedélyek az Azure-ban.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>K. Miért a saját munkaterület OMS-portál eléréséhez lehet, de jelenik meg a hiba "van nincs hozzáférés" az Azure Portalon?  

V: A munkaterület az Azure-ban hozzárendelt Azure-engedélyeket kell rendelkeznie. Vannak bizonyos esetekben, ahol nem lehetséges, hogy rendelkezik megfelelő hozzáférési engedélyekkel. Ezekben az esetekben a rendszergazda kell biztosítania, az engedélyeket a Azure.See [Azure-bA az OMS-portálon](/../../azure-monitor/platform/oms-portal-transition.md) további információt.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>K. Miért nem nem látok adatforrásnézet-tervezőből belépési naplók? 
V: Nézettervező csak naplók rendelkező közreműködői engedélyekkel rendelkező vagy újabb verziója érhető el.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>K. Továbbra is használható az Azure-on kívül Analytics-portálon?
A. Igen, a naplók oldalán, az Azure és a bővített analitika portál ugyanazt a kódot alapulnak. A log Analytics az Azure monitorban több egységes felügyeleti élmény érdekében szolgáltatásként való integráció. Továbbra is hozzáférhet az URL-Analytics-portálon: https://portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}.



## <a name="general"></a>Általános kérdések

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>K. Hogyan tekinthetem meg saját nézeteket és a megoldások az Azure Portalon? 

V: A nézetek és a telepített megoldások listáját az Azure Portalon érhetők el. Kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában válassza ki a **figyelő**, majd kattintson a **... További**. Az utolsó használt munkaterület van kijelölve, de más munkaterületekhez is kiválaszthat. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>K. Miért nem tudok létrehozni munkaterületek az USA nyugati középső régiójában? 

V: Ennek a régiónak a kapacitása átmenetileg korlátozva van. A korlátozást 2019 első felében tervezzük megváltoztatni.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>K. A Log Analytics használata ugyanaz az ügynök, az Azure Security Center?

V: 2017 júniusának az Azure Security Center már a Microsoft Monitoring Agent használatával gyűjti össze és tárolja az adatokat. További tudnivalókért lásd: [Azure Security Center Platform áttelepítési – gyakori kérdések](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>K. Milyen ellenőrzéseket hajtja végre az AD és az SQL-értékeléssel megoldások?

V: A következő lekérdezés az összes elvégzett jelenleg leírását jeleníti meg:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Az eredményeket ezután exportálhatók Excelbe további ellenőrzésre.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>K. Miért látok más, mint az OMS a System Center Operations Manager konzolon?

V: Attól függően, melyik frissítési kumulatív Operations Manager a, láthatja a csomópontokat a *System Center Advisor*, *Operational Insights*, vagy *Log Analytics*.

A szöveges karakterlánc frissítésének *OMS* szerepel a felügyeleti csomag, amely manuálisan importálni kell. Tekintse meg a jelenlegi szöveg és a Funkciók, kövesse az utasításokat a legújabb System Center Operations Manager frissítési kumulatív KB cikk, és frissítse a konzolt.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>KÉRDÉS: Van egy Log Analytics helyszíni verzióját?

V: Nem. A log Analytics egy méretezhető felhőalapú szolgáltatás, amely feldolgozza, és nagy mennyiségű adatot tárol a. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>K. Hogyan háríthatom el, ha a Log Analytics már nem gyűjt adatokat?

V: Egy előfizetésre, és 2018. április 2. előtt létrehozott munkaterületet, amely a *ingyenes* adatgyűjtés leállítása tarifacsomagban van, ha több mint 500 MB adatot küldik egy nap alatt, a nap hátralevő. Napi korlát elérése a gyakori oka, hogy a Log Analytics leállítja az adatgyűjtést, vagy adatokat úgy tűnik, hogy hiányzik.  

A log Analytics eseményt hoz létre típusú *szívverés* és a segítségével határozza meg, ha leállítja az adatgyűjtést. 

A keresés, ellenőrizze, hogy vannak napi korlát elérése és adatok hiányoznak a következő lekérdezés futtatásával: `Heartbeat | summarize max(TimeGenerated)`

Egy adott számítógép ellenőrzéséhez futtassa a következő lekérdezést: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Amikor az adatgyűjtés leáll, az időtartományt kiválasztva, attól függően minden visszaadott rekord nem jelenik meg.   

A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést javasolt művelet:

| OK adatgyűjtés leállítása                       | Az adatgyűjtés folytatása |
| -------------------------------------------------- | ----------------  |
| Elérte az ingyenes adatmennyiség korlátot<sup>1</sup>       | Várja meg, amíg a következő hónapban automatikusan, újraindítása, gyűjtemény vagy<br> Módosítása fizetős tarifacsomagra |
| Azure-előfizetés miatt felfüggesztett állapotban van: <br> Ingyenes próbaidőszak véget ért <br> Az Azure pass lejárt <br> Havonta költségkeret elérése (például az MSDN vagy a Visual Studio előfizetési)                          | Fizetős előfizetéssé alakítani <br> Fizetős előfizetéssé alakítani <br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása |

<sup>1</sup> Ha munkaterületét a *ingyenes* tarifacsomag, Ön legfeljebb napi 500 MB-os küld a szolgáltatásnak. Amikor eléri a napi korlátot, az adatgyűjtés leáll, csak a következő napon. Adatgyűjtés leállítása elküldött adatok nem indexelt, és nem áll rendelkezésre a kereséshez. Adatok gyűjtése folytatódik, ha csak az új adatok feldolgozása következik be. 

A log Analytics UTC időt használ, és minden nap elindul éjfélkor (UTC). Ha a munkaterület eléri a napi korlátot, a feldolgozási folytatja az első óráért a következő (UTC) nap során.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>K. Hogyan kaphatok értesítést adatgyűjtés leállása esetén?

V: Az ismertetett lépésekkel [hozzon létre egy új riasztás](../../azure-monitor/platform/alerts-metric.md) értesítést, ha leállítja az adatgyűjtést.

Amikor az adatgyűjtés leáll a riasztás létrehozásakor állítsa be a:

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - **Jel neve** kiválasztása **egyéni naplóbeli keresés**.
   - A **Keresési lekérdezés** legyen a következő: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - A **Riasztási logika** **alapja** legyen az *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték** : *0*
   - **Adott időszakban** , *30* perc és **riasztási időköz** minden *10* perc
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - **Név** való *leállította az adatgyűjtést*
   - A **Súlyosság** legyen *Figyelmeztetés*

Adjon meg egy meglévő, vagy hozzon létre egy új [műveletcsoport](../../azure-monitor/platform/action-groups.md) , hogy ha a riasztás megfelel a feltételnek, ha több mint 15 percig hiányzó szívverés értesítést.

## <a name="configuration"></a>Konfiguráció
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>K. Módosíthatom a tábla/blob-tároló az Azure Diagnostics (WAD) olvasásához használt neve?

A. Nem, akkor nem alkalmas jelenleg tetszőleges táblákat vagy az Azure BLOB storage tárolók olvasni.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>K. IP-címek nem a Log Analytics szolgáltatás használata? Hogyan biztosítja, hogy a tűzfal csak lehetővé teszi a Log Analytics szolgáltatás forgalom?

A. A Log Analytics szolgáltatás Azure épül. Log Analytics IP-címek szerepelnek a [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

Szolgáltatás központi telepítések később, a tényleges IP-címek a Log Analytics szolgáltatás módosítani. A DNS-nevek engedélyezése a tűzfalon keresztül vannak dokumentálva [hálózati követelmények](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>K. Csatlakozás az Azure-bA ExpressRoute használatával. A Log Analytics-forgalmat használ a saját ExpressRoute-kapcsolat?

A. Az ExpressRoute-forgalmat a különböző típusú ismertetett a [az ExpressRoute dokumentációja](../../expressroute/expressroute-faqs.md#supported-services).

A Log Analytics-forgalom nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoportot használ.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>K. Van egy meglévő Log Analytics-munkaterület áthelyezése egy másik Log Analytics munkaterület vagy az Azure-előfizetés egyszerű és könnyen mód?

A. A `Move-AzureRmResource` parancsmag lehetővé teszi a Log Analytics-munkaterülettel, valamint az Automation-fiók áthelyezése egy Azure-előfizetésből egy másikba. További információkért lásd: [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

Ez a változás is módosíthatók az Azure Portalon.

Adatok áthelyezése egy Log Analytics-munkaterület között nem, vagy módosítsa a régiót, amelyben a Log Analytics-adatok tárolva van.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>KÉRDÉS: Hogyan adhatok hozzá a Log Analytics a System Center Operations Manager?

V:  A legújabb kumulatív frissítés és a felügyeleti csomagok importálása lehetővé teszi, hogy az Operations Manager csatlakoztatása a Log Analytics szolgáltatásba.

>[!NOTE]
>A Log Analytics az Operations Manager-kapcsolat csak akkor használható, a System Center Operations Manager 2012 SP1 és újabb verziók.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>KÉRDÉS: Hogyan győződhet meg, hogy, hogy az ügynök képes kommunikálni a Log Analytics-e?

V: Annak érdekében, hogy az ügynök kommunikálni tudjon OMS, Ugrás: Szabályozhatja a panelen, a biztonság és a beállítások, **a Microsoft Monitoring Agent**.

Alatt a **Azure Log Analytics (OMS)** fülre, keressen egy zöld pipa. Egy zöld pipa ikon megerősíti, hogy az ügynök nem tud kommunikálni az Azure-szolgáltatás.

Egy sárga figyelmeztető ikon azt jelenti, hogy az ügynök a Log Analytics-szel kapcsolatos problémák kommunikációs tapasztalja. Egyik gyakori oka a Microsoft Monitoring Agent szolgáltatás leállt. Helyvezérlés-kezelő használatával indítsa újra a szolgáltatást.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>KÉRDÉS: Hogyan állítsam le egy ügynök kommunikáljon a Log Analytics?

V: A System Center Operations Manager távolítsa el a számítógépet az OMS a felügyelt számítógépek listája. Az Operations Manager már nem a jelentés a Log Analytics frissíti az ügynök konfigurációját. Közvetlenül csatlakozik a Log Analytics-ügynökök leállíthatja őket keresztül: Szabályozhatja a panelen, a biztonság és a beállítások, **a Microsoft Monitoring Agent**.
A **Azure Log Analytics (OMS)**, felsorolva minden munkaterület eltávolítása.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>KÉRDÉS: Miért jelenik meg hibaüzenet jelenik meg a saját munkaterület áthelyezése egy Azure-előfizetés között?

V: Munkaterület áthelyezése egy másik előfizetést vagy az erőforráscsoport, először a munkaterület Automation-fiókot kell leválasztása. Ezek a megoldások eltávolítása leválasztása az Automation-fiók szükséges, ha a munkaterülethez lesznek telepítve: Frissítéskezelés, változáskövetés vagy virtuális gépek indítása/leállítása munkaidőn kívül el lesznek távolítva. Után ezeket a megoldásokat, az Automation-fiók leválasztása kiválasztásával **társított munkaterületek** a bal oldali panelen az Automation-fiók erőforrás, és kattintson a **munkaterület leválasztása** a menüszalagon.
 > Eltávolítja a megoldások kell újra kell telepíteni a munkaterületen, és a munkaterület Automation hivatkozásának meg kell állapítani az áthelyezés után.

Győződjön meg arról, mind az Azure-előfizetések az Önnek megfelelő engedélye.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>KÉRDÉS: Miért jelenik meg hibaüzenet jelenik meg egy SavedSearch frissíteni?

V: Az API-t, vagy az Azure Resource Manager-sablon tulajdonságainak törzsében "etag" hozzá kell:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Az ügynök adatok
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>K. Mennyi adatot lehet küldeni az ügynökön keresztül a Log Analytics? Van egy minden ügyfél adatok maximális mérete?
A. Az ingyenes csomag 500 MB-os maximális napi adatmennyiséget Munkaállomásonként állítja be. A standard és prémium szintű csomag is feltöltött adatok mennyisége nincs korlátozva van. Felhőalapú szolgáltatás, a Log Analytics célja, hogy az automatikus vertikális felskálázás legfeljebb leíró a kötet érkező ügyfél –, akkor is, ha több terabájtnyi naponta.

A Log Analytics-ügynököket úgy lett kialakítva, annak érdekében, hogy egy kis erőforrás-igényű rendelkezik. Engedélyezi a megoldások adatmennyiség függvényében. Adatmennyiség részletes információkat találhat, és tekintse meg a táblázat összefoglalja a megoldással a [használati](../../azure-monitor/platform/data-usage.md) lapot.

További információt talál a [ügyfél blog](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) eredményeket megjelenítő az erőforrás-használat (jelenlét), az OMS-ügynök kiértékelése után.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>K. Amikor adatokat küld a Log Analytics mekkora hálózati sávszélesség használatát a Microsoft Management Agent (MMA)?

A. Egy függvény küldött adatok mennyisége korlátozott a sávszélesség. A hálózaton keresztül küldött adatok tömörítése.

### <a name="q-how-much-data-is-sent-per-agent"></a>K. Mennyi adatot küld, az ügynök?

A. Az ügynök / küldött adatok mennyiségét függ:

* Engedélyezte a megoldásokat
* A naplók és a gyűjtött teljesítményszámlálók számát
* A naplók adatmennyiség

Az ingyenes tarifacsomag jó módja a előkészítése több kiszolgálók és a tipikus adatmennyiség mérőműszer. Összesített használat jelenik meg a [használati](../../azure-monitor/platform/data-usage.md) lapot.

A számítógépeken, amelyek a WireData-ügynök futhasson használja a következő lekérdezést megtekintéséhez, hogy mennyi adatot küld:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>További lépések
* [Ismerkedés a Log Analytics](../../azure-monitor/overview.md) további tudnivalók a Log Analytics és az első lépésekhez percek alatt.
