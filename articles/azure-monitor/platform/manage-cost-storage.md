---
title: Az Azure Log Analytics data költsége kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan módosíthatja, a díjszabással és kezelése az adatok mennyisége és a megőrzési házirend a Log Analytics-munkaterület az Azure-ban.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: magoedte
ms.openlocfilehash: 1ea99c045d5f1bfaacaefab04322b2d4f1123c84
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183506"
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>A Log Analytics és az adatmennyiség szabályozásával költségek kezelése

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan tarthatja a költségeket, a Log Analytics által az adatok megőrzési időszak beállítása.  További kapcsolódó információt a következő cikkekben talál.
> - [Adathasználat elemzése a Log Analytics](manage-cost-storage.md) azt ismerteti, hogyan elemezheti, és riasztást küldjön az adathasználatot.
> - [Használat és becsült költségek figyelése](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) ismerteti, hogyan lehet megtekinteni a használati és becsült költségek figyelési funkciók eltérő díjszabási modelleket a több Azure-ban. Emellett bemutatja, hogyan lehet módosítani a díjszabási modellt.

A log Analytics méretezési és támogatási gyűjtése, az indexelés és a vállalati adatok naponta bármilyen forrásból származó nagy mennyiségű tárolására tervezték vagy üzembe helyezve az Azure-ban.  Ez lehet egy elsődleges illesztőprogram, a szervezet számára, miközben költséghatékonyságot végső soron az alapul szolgáló illesztőprogram. Ebből a célból, fontos ismerni a nem a Log Analytics-munkaterület költségét csak alapján összegyűjtött adatok mennyisége is a kiválasztott csomag függ, és mennyi ideig úgy döntött, hogy a csatlakoztatott források által létrehozott adatokat tárolni.  

Ez a cikk áttekintettük, hogyan proaktívan figyelheti adatok mennyisége és a storage növekedési, és azok a kapcsolódó költségek szabályozására vonatkozó korlátok beállításához. 

Lehet, hogy a költség az adatok jelentős függően az alábbi tényezőket: 

- Rendszerek, infrastruktúra-összetevőket, a felhőbeli erőforrások, stb. a gyűjtött száma 
- Írja be a forrás, például az üzenetsorok, naplók, események, a biztonsággal kapcsolatos adatokat vagy a teljesítmény-mérőszámok által létrehozott adatok 
- Az adatforrásokban hoz létre, a munkaterület betöltött adatok mennyiségét 
- Az időszak adatait őrzi meg a munkaterület  
- Engedélyezett felügyeleti megoldások száma, az adatforrás és a gyűjtés gyakorisága 

> [!NOTE]
> Tekintse meg az egyes megoldásokra vonatkozó dokumentáció, mennyi adatot gyűjt becsült biztosít.   

Nagyvállalati szerződéssel rendelkező ügyfelek 2018. július 1-aláírását, és akik már létrehozott Log Analytics-munkaterület az előfizetéshez, akkor továbbra is hozzáférhetnek a a *ingyenes* tervet. Ha az előfizetés nem kötődik meglévő EA-regisztrációhoz, a *ingyenes* szint nem érhető el, amikor egy új előfizetést a 2018. április 2. után hozzon létre egy munkaterületet.  7 napos megőrzés az adatok korlátozódik a *ingyenes* szint.  Az a *önálló* vagy *fizetős* szinten gyűjtött adatok érhető el az elmúlt 31 napra vonatkozó. A *ingyenes* csomag esetében a napi korlátja 500 MB-ot a betöltési, és ha folyamatosan túllépi az engedélyezett mennyiségi összegeket, munkaterületét fizetős díjcsomagra adatgyűjtéshez meghaladja ezt a korlátot módosíthatja. 

> [!NOTE]
> A díjak vonatkoznak, ha úgy dönt, hogy a fizetős szint esetében hosszabb megőrzési időt. Most módosíthatja az adatforgalmi díjcsomag típusa bármikor és további információ a díjszabásról, lásd: [díjszabás](https://azure.microsoft.com/pricing/details/log-analytics/). 

Létezik, amelyben az adatok mennyisége korlátozott és segítségével kétféleképpen szabályozhatja költségeit, ezek a napi korlátot és az adatmegőrzést.  

## <a name="review-estimated-cost"></a>Tekintse át a becsült költség
Log Analytics teszi egyszerűvé, mire a költségek várhatóan legutóbbi használati minták alapján.  Ehhez hajtsa végre az alábbi lépéseket.  

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com). 
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.<br><br> ![Azure Portal](media/manage-cost-storage/azure-portal-01.png)<br><br>  
3. A Log Analytics-előfizetések panelen válassza ki a munkaterületet, és kattintson a **felhasználás és becsült költségek** a bal oldali panelen.<br><br> ![Használat és becsült költségek lapon](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

Itt áttekintheti az adatok mennyiségét a hónapban. Ez magában foglalja a fogadott és a Log Analytics-munkaterület összes adatot.  Kattintson a **használatról** adatok mennyiségi trendek forrás, a számítógépek és az ajánlat az adatokat a használati irányítópult megtekintése a lap tetején. Megtekintheti, és állítsa be a maximális napi adatmennyiséget, vagy módosíthatja a megőrzési időszak kattintson **adatmennyiség-kezelés**.
 
Log Analytics díjak az Azure-elszámolások kerülnek. A számlázás szakaszában az Azure Portal vagy a számlázás az Azure részleteit láthatja a [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Napi korlát
Ha a Log Analytics-munkaterület létrehozása az Azure Portalon, és válassza ki a *ingyenes* terv értékre van állítva a napi 500 MB-ot. Nincs korlátozva a a más árképzési tervekhez. Konfigurálja a maximális napi adatmennyiséget, és korlátozhatja a napi bevitelt a munkaterületen, de körültekintően járjon el, mert a cél nem lehet eléri a napi korlátot.  Ellenkező esetben az a nap fennmaradó részében, ami hatással lehet más Azure-szolgáltatások és megoldások, amelynek funkció függhet, naprakész adatok legyenek elérhetők a munkaterület az adatok elvesznek.  Ennek eredményeképpen figyelje meg, és fogadjon megítélnie riasztást küld, ha is hatással van az informatikai szolgáltatásokat támogató erőforrásokban egészségügyi feltételeit.  A maximális napi adatmennyiséget célja, hogy a felügyelt erőforrásoktól érkező adatmennyiség váratlan növekedés kezeléséhez, és maradjon a határidőn belül, vagy ha egyszerűen korlátozhatja a munkaterületre vonatkozó nem tervezett költségek mennyiségét szeretné használni.  

A napi korlátot, a gyűjtemény számlázható adattípusok leállítja az a nap hátralevő. Egy figyelmeztetés szalagcím jelenik meg a kiválasztott Log Analytics-munkaterületet a lap tetején, és a egy műveletet a rendszer a *művelet* tábla alatt **LogManagement** kategória. Az adatgyűjtés után a visszaállítási idő alatt meghatározott folytatja *napi korlát lesz beállítva*. Azt javasoljuk, hogy arra az esetre, ha a napi korlátot elérte a konfigurált Ez a művelet események alapján riasztási szabály meghatározása. 

### <a name="identify-what-daily-data-limit-to-define"></a>Milyen napi korlátot meghatározásához azonosítása 
Felülvizsgálat [Log Analytics-használat és becsült költségek](../../azure-monitor/platform/data-usage.md) megtudhatja, hogy az adatok betöltési trend és mi az a napi mennyiségi korlát meghatározásához. Kell tekinteni, körültekintően, mivel nem lehet az erőforrások figyeléséhez, a korlát elérése után. 

### <a name="manage-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése 
A következő lépések bemutatják, hogyan konfigurálása a Log Analytics lesz képes feldolgozni naponta adatmennyiség kezelése korlátozva.  

1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. Az a **felhasználás és becsült költségek** a kijelölt munkaterület oldalára, kattintson **adatmennyiség-kezelés** az oldal tetején. 
5. Napi korlát a következő **OFF** – alapértelmezés szerint kattintson **ON** az engedélyezéshez, és állítsa az mennyiségi korlát a GB/nap.<br><br> ![A log Analytics konfigurálása adatkorlát](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Riasztás, ha elérte a korlátot
Hogy jelen visual köteg az Azure Portalon, ha az adatok korlát küszöbértéket, amíg ez a viselkedés nem feltétlenül igazítás azonnali figyelmet igénylő működési problémák kezelése.  Az Azure Monitor riasztási értesítés fogadására, létrehozhat egy új riasztási szabály.  További tudnivalókért lásd: [létrehozása, megtekintése és kezelése a riasztások](../../azure-monitor/platform/alerts-metric.md).      

Az első lépésekhez, az alábbiakban a riasztás az ajánlott beállításokat:

* Cél: Válassza ki a Log Analytics-erőforrás
* Feltételek: 
   * Jel neve: Egyéni naplók keresése
   * Keresési lekérdezés: A művelet |} Ha részletes rendelkezik-e "termékváltozatként használja:
   * Alapján: Eredmények száma
   * Feltétel: Nagyobb, mint
   * Küszöbérték: 0
   * Időszak: 5 (perc)
   * Gyakorisága: 5 (perc)
* Riasztási szabály neve: Elérte a napi korlátot
* Súlyosság: Figyelmeztetés (Sev 1)

Riasztás van definiálva, és a eléri a korlátot, riasztás akkor aktiválódik, és hajtja végre a választ a műveletcsoport meghatározott. Küldjön értesítést munkatársainak e-mailek és SMS-EK, valamint automatizálja a műveleteket webhookok, Automation-runbookok használatával vagy [integrálása egy külső ITSM-megoldással](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Módosítsa az Adatmegőrzés időtartama 
Az alábbi lépéseket adatért által a munkaterületen milyen hosszú log konfigurálását ismertetik.
 
1. A munkaterületen válassza a **Felhasználás és becsült költségek** lehetőséget a bal oldali panelen.
2. A **Felhasználás és becsült költségek** oldalon kattintson az oldal tetején található **Adatmennyiség-kezelés** elemre.
5. A panelen a csúszka segítségével növelheti vagy csökkentheti a napok számát, és kattintson a **OK**.  Ha a *ingyenes* szint, nem tudja módosítani az Adatmegőrzés időtartama, és annak érdekében, hogy ez a beállítás szabályozza a fizetős csomagra frissíteni szeretne.<br><br> ![Munkaterület megőrzése beállításának módosítása](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>Hibaelhárítás
**Kérdés**: Hogyan háríthatom el, ha a Log Analytics már nem gyűjt adatokat? 
**válasz**:  Ha vannak a az ingyenes tarifacsomag, és a egy nap alatt több mint 500 MB mennyiségű adatot küldő, a nap hátralevő leállítja az adatgyűjtés. Napi korlát elérése a gyakori oka, hogy a Log Analytics leállítja az adatgyűjtést, vagy adatokat úgy tűnik, hogy hiányzik.  
A log Analytics művelet típusú eseményt hoz létre, amikor adatgyűjtés indítása és leállítása.  
A keresés, ellenőrizze, hogy vannak napi korlát elérése és adatok hiányoznak a következő lekérdezés futtatásával: A művelet |} ahol OperationCategory == 'adatgyűjtés állapota:   
Ha leállítja az adatgyűjtést, a OperationStatus figyelmeztetés. Amikor megkezdi az adatgyűjtést, akkor a OperationStatus sikeres volt.  
A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést javasolt művelet:  

|OK gyűjtemény leáll| Megoldás| 
|-----------------------|---------|
|Elérte az ingyenes adatmennyiség napi korlátot<sup>1</sup>|Várja meg, amíg a gyűjtemény indítja újra automatikusan a következő napon, vagy módosítása fizetős tarifacsomagra.|
|Elérte az adatmennyiség-kezelés definiált napi korlát|Várja meg, amíg a gyűjtemény indítja újra automatikusan a következő napon, vagy növelje a napi mennyiségi korlátot ismertetett [a maximális napi adatmennyiség kezelése](#manage-the-maximum-daily-volume)|
|Azure-előfizetés miatt felfüggesztett állapotban van:<br> Ingyenes próbaidőszak véget ért<br> Az Azure pass lejárt<br> Havonta költségkeret elérése (például az MSDN vagy a Visual Studio előfizetési)|Fizetős előfizetéssé alakítani<br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása|

<sup>1</sup> Ha a munkaterület az ingyenes tarifacsomag, Ön legfeljebb napi 500 MB-os küld a szolgáltatásnak. Amikor eléri a napi korlátot, az adatgyűjtés leáll, csak a következő napon. Adatgyűjtés leállítása elküldött adatok nem indexelt, és nem áll rendelkezésre a kereséshez. Amikor visszatér az adatgyűjtést, ha feldolgozása befejeződik, csak az új elküldött adatok számára. 

A log Analytics UTC időt használja. Visszaállítás függ, hogy egy időben tölt be adatot az összes díjkorlát munkaterületek kezdő munkaterületek között. Ha a munkaterület eléri a napi korlátot, feldolgozás után folytatja a meghatározott a visszaállítási idő **napi korlát lesz beállítva**.<br><br> ![A log Analytics korlátozza az UTC időzónára](media/manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Kérdés**: Hogyan kaphatok értesítést adatgyűjtés leállása esetén? 
**válasz**: Az ismertetett lépésekkel *létrehozás napi adatkorlátjának* , ami arról értesíti, ha leállítja az adatgyűjtést, és kövesse a lépéseket használja leírt lépéseket követve adja hozzá a riasztási szabályokhoz műveletek egy e-mail, webhook vagy runbook műveletet a riasztási szabály konfigurálása . 

## <a name="next-steps"></a>További lépések  

Annak eldöntéséhez, hogy mennyi adatot gyűjteni, mely források küldenek, és a különböző típusú adatokat küldtek használat és költségek kezelését segítik, lásd: [adathasználat elemzése a Log Analytics](../../azure-monitor/platform/data-usage.md).
