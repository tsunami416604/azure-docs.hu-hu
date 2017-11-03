---
title: "Naplófájl Analytics – gyakori kérdések |} Microsoft Docs"
description: "Az Azure Naplóelemzés szolgáltatással kapcsolatos gyakran feltett kérdésekre adott válaszokat."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte
ms.openlocfilehash: 0b27386cd0f9f3ae50314b8c5d7708aea3e3d028
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-faq"></a>Log Analytics – gyakori kérdések
A Microsoft FAQ a Microsoft Azure Naplóelemzés gyakran feltett kérdésekre listáját. Amennyiben a Naplóelemzési kapcsolatos további kérdése van, a [vitafóruma](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) és kérdéseit. Ha kérdése van gyakori, azt adja hozzá a cikkben, hogy gyorsan és könnyen megtalálhatók.

## <a name="general"></a>Általános kérdések

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. Log Analytics használata ugyanaz az ügynök, az Azure Security Center?

A. Korai. június 2017 az Azure Security Center megkezdte a Microsoft Monitoring Agent használatával gyűjt, és adatokat. További tudnivalókért lásd: [Azure Security Center Platform áttelepítési gyakran ismételt kérdések](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. A rendszer, mely ellenőrzi az AD és az SQL-értékelési megoldás?

A. A következő lekérdezés az összes elvégzett jelenleg leírását jeleníti meg:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Az eredmények majd exportálhatja Excel további ellenőrzésre.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Kérdés: Miért látom azt egy másik, mint a System Center Operations Manager konzolon OMS?

A: attól függően milyen frissítés összesítése az Operations Manager van, megjelenik egy csomópont *System Center Advisor*, *Operational Insights*, vagy *Naplóelemzési*.

A szöveges karakterlánc frissítés *OMS* szerepel a felügyeleti csomagot kézzel kell importálni. Az aktuális szöveggel és -funkcióinak megtekintéséhez kövesse az utasításokat a legújabb System Center Operations Manager frissítés összesítő KB cikk, és frissítse a konzolt.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>K: van Log Analytics egy helyszíni verzióját?

V: nem. A Naplóelemzési egy méretezhető felhőalapú szolgáltatás, amely feldolgozza, és nagy mennyiségű adatot tárolja. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>Q. Hogyan hibaelhárítása Ha Log Analyticshez már nem gyűjt adatokat?

A:, ha vannak a az ingyenes tarifacsomag, és naponta elküldött 500 MB-nál több adat, adatgyűjtés leáll, a többi nap. Napi korlát elérése gyakori oka, hogy Naplóelemzési leállítja az adatgyűjtést, vagy az adatok akkor jelenik meg, hogy hiányzik.

A Naplóelemzési hoz létre eseményt, típusú *művelet* amikor adatgyűjtés indítása és leállítása. 

Futtassa a következő lekérdezést a keresést, hogy ellenőrizze, hogy vannak napi korlát elérése és hiányzó adat:`Type=Operation OperationCategory="Data Collection Status"`

Amikor leállítja az adatgyűjtést, a *OperationStatus* van **figyelmeztetés**. Adatgyűjtés indításakor, a *OperationStatus* van **sikeres**. 

A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést a javasolt művelet:

| OK adatgyűjtés leállítása                       | Adatgyűjtés folytatása |
| -------------------------------------------------- | ----------------  |
| Elérte az ingyenes adatok napi korlátot<sup>1</sup>       | Várja meg, amíg a következő napon gyűjtemény automatikus újraindítása, vagy<br> A fizetős tarifacsomag módosítása |
| Azure-előfizetés miatt felfüggesztett állapotban van: <br> Ingyenes próbaverzió befejeződött <br> Az Azure hozzáférési lejárt <br> Havi költségkeret érhető el (például a az MSDN webhelyen vagy a Visual Studio előfizetői)                          | A szolgáltatás fizetős átalakítása <br> A szolgáltatás fizetős átalakítása <br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása |

<sup>1</sup> Ha a munkaterületet az ingyenes tarifacsomag, tehát legfeljebb 500 MB naponkénti adat küldése a szolgáltatást. Ha eléri a napi korlátot, adatgyűjtés leáll, a következő napig. Adatgyűjtés leállítása küldött adatok nem indexelt, és nem érhető el a kereséshez. Adatok gyűjtése folytatódik, ha csak az új adatok feldolgozása következik be. 

A Naplóelemzési UTC időt használja, és minden nap UTC idő szerint éjfélkor kezdődik. Ha a munkaterületet eléri a napi korlátot, a feldolgozási folytatja a következő UTC nap első órában.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. Hogyan tudom értesítést kaphat leállítja az adatok gyűjtését?

A: a leírt lépésekkel [riasztási szabályt létrehozni](log-analytics-alerts-creating.md#create-an-alert-rule) értesítést leállítja az adatgyűjtést.

Amikor leállítja az adatgyűjtést kapcsolatos riasztás létrehozása, ha a:
- **Név** való *adatgyűjtés leállítása*
- A **Súlyosság** legyen *Figyelmeztetés*
- A **Keresési lekérdezés** legyen a következő: `Type=Operation OperationCategory="Data Collection Status" OperationStatus=Warning`
- **Időablak** való *2 óra*.
- A **Riasztási időköz** legyen egy óra, mivel a használati adatok csak óránként egyszer frissülnek.
- A **Riasztások létrehozása a következő alapján:** értéke legyen az *eredmények száma*
- Az **Eredmények száma** legyen *Nagyobb, mint 0*

A [műveletek a riasztási szabályokhoz adásával kapcsolatos](log-analytics-alerts-actions.md) részben leírt lépéseket követve konfigurálhat e-mailt, webhookot, vagy runbook-műveletet a riasztási szabályhoz.


## <a name="configuration"></a>Konfiguráció
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. Módosítható az az Azure Diagnostics (ÜVEGVATTA) olvasásához használt tábla/blob-tároló neve?

A. Nem, lehetetlen jelenleg tetszőleges táblák vagy a tárolókat az Azure storage olvasni.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. IP-címek nem Naplóelemzés szolgáltatás használata? Hogyan ellenőrizze, hogy a tűzfal csak a Naplóelemzés szolgáltatás-forgalmát engedélyezi?

A. A Naplóelemzési szolgáltatás Azure épül. Napló Analytics IP-címek szerepelnek a [Microsoft Azure Datacenter IP-címtartományok](http://www.microsoft.com/download/details.aspx?id=41653).

Mivel szolgáltatástelepítések történik, megváltozik a Naplóelemzés szolgáltatás a tényleges IP-címe. A tűzfalon keresztül engedélyezi a DNS-nevek szerepelnek [proxy és tűzfal beállításainak konfigurálása a Naplóelemzési](log-analytics-proxy-firewall.md).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. Kapcsolódás Azure ExpressRoute használatával. A Naplóelemzési forgalom használ a saját ExpressRoute-kapcsolatot?

A. A különböző típusú ExpressRoute forgalom ismerteti a [ExpressRoute dokumentációja](../expressroute/expressroute-faqs.md#supported-services).

Log Analytics-forgalom használ a nyilvános társviszony ExpressRoute-kapcsolatcsoportot.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. Egy egyszerű és egyszerűen elvégezhető egy meglévő Naplóelemzési munkaterület áthelyezése egy másik Naplóelemzési munkaterület vagy az Azure-előfizetés van?

A. A `Move-AzureRmResource` parancsmag lehetővé teszi, hogy áthelyezése a Naplóelemzési munkaterület, valamint az Automation-fiók egy Azure-előfizetés között. További információkért lásd: [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Ez a módosítás is az Azure portálon hajtható végre.

Adatok áthelyezése egy Naplóelemzési munkaterület másik nem, vagy módosítsa a régiót, amelyben a Naplóelemzési adatokat tárolja.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>K: hogyan Naplóelemzési hozzáadása a System Center Operations Manager?

V: frissítése a legújabb kumulatív frissítése és a felügyeleti csomagok importálása teszi csatlakoztatása az Operations Manager szolgáltatáshoz.

>[!NOTE]
>Az Operations Manager kapcsolatát szolgáltatáshoz csak érhető el, a System Center Operations Manager 2012 SP1 és újabb verzióihoz.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>K: hogyan győződhet meg, hogy egy ügynök nem tud kommunikálni a Log Analytics?

V: Győződjön meg arról, hogy az ügynök OMS kommunikál, keresse fel: Vezérlőpult, a biztonság és a beállítások, **Microsoft Monitoring Agent**.

Az a **Azure Naplóelemzés (OMS)** lapon, a zöld pipa jelzi. Egy zöld pipa ikon megerősíti, hogy az ügynök nem tud kommunikálni az Azure-szolgáltatás.

Sárga figyelmeztető ikon azt jelenti, hogy az ügynök nehézségekkel Naplóelemzési problémák kommunikál. Egy leggyakoribb oka, a Microsoft Monitoring Agent szolgáltatása leállt. Helyvezérlés-kezelő használatával indítsa újra a szolgáltatást.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>K: hogyan állítsa le a az ügynök kommunikáljon a Log Analytics?

V: a System Center Operations Manager, a számítógép eltávolítása az OMS felügyelt számítógépek listáját. Az Operations Manager már nem a jelentéskészítő szolgáltatáshoz frissíti az ügynök konfigurációját. Naplóelemzési közvetlenül csatlakoztatott ügynökök, leállíthatja azok keresztül: Vezérlőpult, a biztonság és a beállítások, **Microsoft Monitoring Agent**.
A **Azure Naplóelemzés (OMS)**, távolítsa el a felsorolt összes munkaterületet.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>K: Miért jelenik hiba a saját munkaterület áthelyezése egy Azure-előfizetés másik közben?

A:, ha az Azure-portált használja, ügyeljen arra, csak a munkaterület az áthelyezés van kijelölve. Nem, válassza a megoldások – ezek automatikusan áthelyezi a munkaterület helyezi át. 

Ellenőrizze, hogy Ön a két Azure-előfizetések.

## <a name="agent-data"></a>Ügynök adatok
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. Mennyi adatot lehet küldeni az ügynök keresztül szolgáltatáshoz? Van egy / felhasználói adatok maximális mérete?
A. Az ingyenes csomagban egy napi cap 500 MB / munkaterület állítja be. A standard és premium tervek nincs korlátozva van feltöltött adatok mennyiségét. Felhő alapú szolgáltatásként Log Analytics célja, hogy automatikusan legfeljebb leíró a kötet méretezési érkező ügyfél –, akkor is, ha TB naponta.

A Naplóelemzési ügynök úgy lett kialakítva, annak érdekében, hogy kevés erőforrást tartalmaz. Ügyfeleink egyik megírt blog kapcsolatos az elvégzett azokat az ügynök, és hogyan impressed voltak szemben. Engedélyezi a megoldások adatmennyiség függ. Részletes információk a adatmennyiség és a megoldás darabolását láthatja a [használati](log-analytics-usage.md) lap.

További információkért olvassa a [ügyfél blog](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) kapcsolatos az alacsony helyigénnyel, az OMS-ügynököt.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. Mekkora hálózati sávszélességet a Microsoft Management Agent (MMA) használatos, ha a Log Analyticshez való adatküldés?

A. Sávszélesség, akkor a függvény az elküldött adatok mennyisége. A rendszer tömöríti az adatokat, a hálózaton keresztül küldött.

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. Mennyi adatot küldött ügynök /?

A. Az ügynök / elküldött adatok mennyisége függ:

* Engedélyezte a megoldások
* A naplók és a szabály a gyűjtendő kérelemteljesítmény-számlálókat száma
* A naplókban lévő adatok mennyisége

Az ingyenes tarifacsomag jó módja a előkészítésére több kiszolgálók és a tipikus adatmennyiség mérőműszer. Általános felhasználás jelenik meg a [használati](log-analytics-usage.md) lap.

A számítógépek számára, amely a WireData ügynök futhasson használja a következő lekérdezést küld a begyűjtött adatok megjelenítéséhez:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Következő lépések
* [Ismerkedés a Naplóelemzési](log-analytics-get-started.md) tudjon meg többet a Naplóelemzési, és működik, és perc múlva.
