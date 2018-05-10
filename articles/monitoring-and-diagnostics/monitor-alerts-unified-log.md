---
title: Riasztások jelentkezzen be Azure Monitor - riasztás |} Microsoft Docs
description: Eseményindító e-mailek, értesítések, a webhely URL-címek (webhookok), vagy az automation Azure riasztások megadott elemzési lekérdezés feltételek teljesülése esetén hívható.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 3a4277d2106078136cee09dfe6aefc87a73c4e08
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Napló riasztásait az Azure Monitor - riasztás 
Ez a cikk ismerteti a napló riasztások részleteit is támogatott belül az új riasztások típusú [Azure riasztások](monitoring-overview-unified-alerts.md) és a felhasználók Azure analytics platform használni alapjául riasztások... A naplók segítségével metrika riasztások leírását, [közel valós idejű metrika riasztások](monitoring-near-real-time-metric-alerts.md)


Napló riasztás áll létrehozott szabályok napló keresése [Azure Naplóelemzés](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application insights szolgáltatással](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)


## <a name="log-search-alert-rule---definition-and-types"></a>Napló keresési riasztásiszabály - definíció- és típusok

Napló keresési szabályok által megadott naplózási lekérdezések automatikusan futtatása rendszeres időközönként Azure riasztások jönnek létre.  Ha a napló lekérdezés eredményeit az adott feltételeknek megfelelő, egy riasztás rekord jön létre. A szabály automatikusan követően futtathatja egy vagy több műveleteket [művelet csoportok](monitoring-action-groups.md). 

Napló-szabályok keresése a következő adatokat határozzák meg:
- **Lekérdezés jelentkezzen**.  A lekérdezés, amely futtatja a minden alkalommal, amikor a riasztási szabály következik be.  Ez a lekérdezés által visszaadott rekordok segítségével meghatározhatja, hogy riasztás jöjjön létre. *Az Azure Application Insights* lekérdezés is tartalmazhatnak [alkalmazások közötti hívások](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), amennyiben a felhasználó rendelkezik-e hozzáférési jogosultsága ahhoz, hogy a külső alkalmazásokkal. 

    > [!IMPORTANT]
    > Támogatja a következő kapcsolattípust a [közötti alkalmazás lekérdezés az Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) előzetes - funkcióit és felhasználói élmény változhat. Használatát [közötti munkaterület lekérdezés](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) és [Naplóelemzési a kereszt-erőforrás lekérdezés](../log-analytics/log-analytics-cross-workspace-search.md) jelenleg **nem támogatott** Azure riasztásokban.

- **Időszak**.  Adja meg a lekérdezés időintervallumát. A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre. Időszakra vonatkozóan az adatokat, a napló lekérdezés visszaélés megakadályozására lehívott korlátozza, és minden alkalommal parancs megkerüli (például a következő időpontban) napló lekérdezésben használt. <br>*Például ha az adott időszakban 60 percre van beállítva, és a lekérdezés futtatása, 1:15 előtti, csak a 12:15 előtti és 1:15 előtti között létrejövő rekordok ad vissza napló lekérdezés végrehajtása. Most, ha a napló lekérdezés idő telt el például a parancs használja (7d), a napló volna kell a lekérdezés futtatása csak 12:15 előtti és: 15 előtti - 1 közötti, mintha csak az elmúlt 60 perc tartozik adat. Nem pedig az adatok a lekérdezési napló hét nap.*
- **Gyakoriság**.  Meghatározza, hogy milyen gyakran kell futtatni a lekérdezést. Bármely érték 5 perc és 24 óra közötti lehet. Az adott időszakban kisebbnek vagy azzal egyenlőnek kell lennie.  Ha az érték nagyobb, mint az adott időszakban, azzal alatt elmulasztott rekordok kockáztatja ki.<br>*Vegye figyelembe például egy időszak 30 perc és 60 perc gyakorisága.  Ha a lekérdezés futtatása, 1:00, 12:30 és 1:00 PM rekordok adja vissza.  A következő szeretné futtatni a lekérdezést ideje 2:00 amikor meghaladná a 1:30 és 2:00 között rögzíti.  1:00 és 1:30 között létrejövő rekordok volna soha nem értékelhető ki.*
- **Küszöbérték**.  A naplófájl-keresési eredmények kiértékelése annak meghatározásához, hogy riasztást kell létrehozni.  A küszöbérték nem azonos a különböző típusú riasztási szabályok napló keresése.

Naplófájl-keresési szabályok kell az [Azure Naplóelemzés](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), kétféle típusú lehet. Ezek a típusok leírását a következő szakaszok részletesen.

- **[Találatok száma](#number-of-results-alert-rules)**. Egyetlen riasztás jön létre, amikor a szám a napló keresés által visszaadott rekordok meghaladja a megadott értéket.
- **[Metrika mérési](#metric-measurement-alert-rules)**.  A megadott küszöbértéket meghaladó értékek napló keresés eredményeit az egyes objektumok létre riasztást.

Riasztási szabály típusa közötti különbségek a következők:

- *Találatok száma* riasztási szabályok mindig létrehoz egy riasztást, igénybe *metrika mérési* riasztási szabály a minden objektumon meghaladja a küszöbértéket, riasztást hoz létre.
- *Találatok száma* riasztási szabályok riasztást hoznak létre, amikor a küszöbérték elérése esetén egy alkalommal. *Metrika mérési* riasztási szabályok riasztást hozhat létre, a küszöbérték túllépésekor bizonyos számú alkalommal keresztül egy adott időintervallumban.

### <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályok száma
**Találatok száma** riasztási szabályok egyetlen riasztás létrehozása, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a küszöbértéket. Ez a riasztási szabály típus ideális, ha olyan esemény, például a Windows eseménynaplóiban keresse meg, a Syslog, webalkalmazás válasz és egyéni naplói.  Érdemes lehet a riasztás létrehozása, amikor egy adott hibaesemény jön létre, vagy egy adott időszakon belül több hibaesemények létrehozásakor.

**Küszöbérték**: a riasztási szabályok eredmények számos küszöbértéke egy adott értéknél kisebb vagy nagyobb.  Ha a napló keresés által visszaadott rekordok számát a feltételeknek, egy riasztás jön létre.

Riasztás egy adott eseményhez, az eredmények csak 0-nál nagyobb meg, és keressen egy egyszeri esemény, a lekérdezés futtatása legutóbbi indítása óta létrehozott. Egyes alkalmazások jelentkezhetnek be, amely nem feltétlenül riasztás alkalmi hiba.  Az alkalmazás például újra a folyamatot a hibaesemény létrehozott és a következő alkalommal majd sikeres.  Ebben az esetben nem érdemes lehet a riasztás létrehozása, kivéve, ha több esemény egy adott időtartamon belül jönnek létre.  

Bizonyos esetekben érdemes lehet egy esemény hiányában hozzon létre egy riasztást.  Például egy folyamat esetleg naplózása rendszeres annak jelzésére, hogy megfelelően működik-e.  Ha ez nem egy ilyen eseményt naplózni a egy adott időtartamon belül, egy riasztás kell létrehozni.  Ebben az esetben akkor értékre kell állítania a küszöbérték **1-nél kevesebb**.

#### <a name="example"></a>Példa
Egy olyan esetet, ahol szeretné megnézni, ha a webes alkalmazás biztosítja a felhasználók 500 kód válaszul (vagyis) belső kiszolgálóhiba. Riasztási szabály okozna a következő adatokkal:  
- **Lekérdezés:** kérelmek |} ahol resultCode == "500"<br>
- **Adott időszakban:** 30 perc<br>
- **Riasztási gyakoriságot:** öt perc<br>
- **Küszöbérték:** kiváló 0-nál<br>

Majd riasztást fog futni a lekérdezés 5 percenként, az adatok - rekordot keres, ahol eredménykódja volt 500 30 perc. Ha található ilyen is egy rekord, akkor következik be, a riasztást, és elindítja a műveleteket.

### <a name="metric-measurement-alert-rules"></a>Metrika mérési riasztási szabályok

- **Metrika mérési** riasztási szabályok létrehozása minden objektum egy riasztást a megadott küszöbértéket meghaladó érték lekérdezésben.  A következő különböző különbségeket rendelkeznek **eredmények száma** riasztási szabályok.
- **Összesítő függvény**: meghatározza a számítás, amely történik, és potenciálisan egy numerikus összesítendő mező.  Például **count()** rekordok számát adja vissza a lekérdezésben **avg(CounterValue)** arra az időtartamra, a ellenértéknek mező átlagát adja vissza. A lekérdezés aggregátumfüggvényt nevű/nevű kell lennie: AggregatedValue, és adjon meg számértéket. 
- **Csoport mező**: egy rekord, egy összesített értéket ebben a mezőben minden példánya létrejön, és riasztás is generálható minden.  Például, ha az egyes számítógépek riasztás létrehozása, használhatja **számítógépenként** 

    > [!NOTE]
    > Metrika mérési riasztási szabályok az Application Insights alapuló megadhatja a mező az adatok csoportosításához. Ehhez használja a **összesített a** beállítást a szabályát leíró definíció beolvasása.   
    
- **Időköz**: az időtartam alatt, amelyben az adatokat összesített értéket határoz meg.  Például, ha a megadott **öt perc**, létrehozott egy rekordot az a csoportmező, 5 perces időközönként a riasztás megadott idő alatt összesített értéket minden egyes példányánál.

    > [!NOTE]
    > Bin függvény kell használható lekérdezésben jelenít meg. Bin() egyenlőtlen időközök - eredményezhet, riasztás automatikusan átalakítja bin parancs bin_at parancs megfelelő, amikor futásidőben, és rögzített pont eredmények biztosítása
    
- **Küszöbérték**: küszöbértéke figyelmeztetési metrika mérési szabályok határozzák meg az összesített érték és a behatolások.  A naplófájl-keresési bármely adatpont meghaladja ezt az értéket, ha a jövőben éri figyelembe.  Ha megszegése az összes objektum az eredmények száma meghaladja a megadott értéket, majd riasztást hoz létre, hogy az objektum.

#### <a name="example"></a>Példa
Fontolja meg egy olyan forgatókönyvet, ahol keresett riasztást, ha a számítógép számát processzorhasználat 90 %-os háromszor több mint 30 perc.  Riasztási szabály okozna a következő adatokkal:  

- **Lekérdezés:** telj |} ahol ObjectName == "Processzor" és a CounterName == "kihasználtsága (%) |} AggregatedValue összefoglalója bin (TimeGenerated, 5 m), a számítógép által avg(CounterValue) =<br>
- **Adott időszakban:** 30 perc<br>
- **Riasztási gyakoriságot:** öt perc<br>
- **Értéket összesítő:** kiváló 90-nél<br>
- **Eseményindító riasztás alapján:** összege nagyobb, mint 5 feltöri<br>

A lekérdezés 5 perces időközönként hozna létre az egyes számítógépek átlagos értékét.  Ez a lekérdezés az előző 30 perc át 5 percenként összegyűjtött adatok kell futnak.  Mintaadatokat három számítógépek alább láthatók.

![A minta lekérdezés eredményei](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

Ebben a példában külön riasztások létrehozott KSZLG02 és srv03 mivel azok a 90 %-os küszöbértéket megszegése 3-szor keresztül az adott időszakban.  Ha a **eseményindító riasztás alapján:** értékről **folyamatos** riasztást volna létre csak a srv03, mivel azt a három egymást követő minták küszöbértéke megszegése.


## <a name="log-search-alert-rule---creation-and-modification"></a>Napló keresési riasztásiszabály - létrehozási és -módosítási

Napló riasztást, valamint a consisting napló keresési riasztási szabály megtekinthető, létrehozni, vagy a módosított:
- Azure Portal
- REST API-k (beleértve a PowerShell használatával)
- Az Azure Resource Manager-sablonok

### <a name="azure-portal"></a>Azure Portal
Bevezetése óta a [új Azure riasztások](monitoring-overview-unified-alerts.md), miután a felhasználók kezelhetik az összes típusú riasztások Azure-portálon egyetlen helyen és hasonló lépéseket. További információ [új Azure-riasztások segítségével](monitor-alerts-unified-usage.md).

Emellett a felhasználók is tökéletes Analytics platform választott Azure-ban a lekérdezések, majd *importálja a lekérdezés mentésével riasztások használható*. Lépést kell végrehajtania:
- *Az Application Insights*: Nyissa meg az Analytics-portálról, ellenőrizze a lekérdezés és az eredményeket. Mentse az egyedi névvel rendelkező *megosztott lekérdezések*.
- *A Naplóelemzési*: Nyissa meg a naplófájl-keresési érvényesítése a lekérdezés és az eredményeket. Majd menteni, ebbe a kategóriába egyedi neve.

Amikor [egy napló riasztás létrehozása a riasztások ](monitor-alerts-unified-usage.md), megjelenik a korábban mentett lekérdezés jel típusként felsorolt **napló (mentett lekérdezés)**; az alábbi példában ismertetett módon: ![mentett lekérdezés riasztások importálása](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Használatával **napló (mentett lekérdezés)** riasztások van egy importálási eredmények. Az elemzés után végzett módosításokat ezért nem lesz tükröző napló keresési riasztási szabályok és fordítva.

### <a name="rest-apis"></a>REST API-k
API-k előírt napló riasztások RESTful, és az Azure Resource Manager REST API-n keresztül érhető el. Ezért keresztül is elérhető PowerShell, kihasználhatják az API-k, valamint egyéb beállításokat.

A részletek és a REST API használatával példák hogy tekintse meg:
- [Naplófájl Analytics riasztás REST API](../log-analytics/log-analytics-api-alerts.md) - hozhat létre, és az Azure Naplóelemzés napló keresési riasztási szabályok kezelése
- [Az Azure figyelő ütemezett lekérdezési szabályok REST API](https://docs.microsoft.com/en-us/rest/api/monitorr/scheduledqueryrules/) - hozhat létre, és a Azure Application Insights napló keresési riasztási szabályok kezelése

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
A felhasználó is használhatja a által nyújtott rugalmasság [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) létrehozása és frissítése erőforrások - létrehozásához vagy frissítéséhez napló riasztásokat.

A részletek és a példák a Resource Manager-sablonok használatával hogy tekintse meg:
- [Mentett keresés és a riasztások kezelése](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) napló riasztások Azure Naplóelemzés alapján
- [Lekérdezési szabály ütemezett](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) napló riasztások Azure Application Insights alapján
 

## <a name="next-steps"></a>További lépések
* Megértéséhez [riasztások jelentkezzen be Azure](monitor-alerts-unified-log-webhook.md).
* További tudnivalók az új [Azure riasztások](monitoring-overview-unified-alerts.md).
* További információ [Application Insights](../application-insights/app-insights-analytics.md).
* További információ [Naplóelemzési](../log-analytics/log-analytics-overview.md).    
