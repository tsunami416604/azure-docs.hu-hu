---
title: Az Azure Naplóelemzés adatok költség kezelése |} Microsoft Docs
description: Ismerje meg, a tarifacsomag módosítása, és az Azure-ban a Naplóelemzési munkaterület adatok mennyisége és az adatmegőrzési házirendjének kezeléséhez.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: magoedte
ms.openlocfilehash: 865b0e485480f5ee7d676d3a6c90cb51fd50d19c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Költség kezelése adatmennyiség és a Naplóelemzési megőrzési vezérlése
A Naplóelemzési úgy tervezték, hogy a skála és támogatási összegyűjtésének, indexelő és nagy mennyiségű adatot naponta forrásból tárolása a vállalati vagy Azure szolgáltatásba telepített.  Amikor ez lehet, hogy a szervezete elsődleges illesztőprogram, költséghatékonyságának végső soron az alapul szolgáló illesztőprogram. Ebből a célból, fontos ismerni, hogy a napló Analytisc munkaterület költségét nem csupán mennyisége alapján adatgyűjtés, azt is a kiválasztott csomag függ, és mennyi ideig úgy döntött, hogy a csatlakoztatott forrásból adatainak tárolásához.  

Ez a cikk azt tekintse át, hogyan proaktív módon figyelheti kötet és a tárolási megfelelően, és azok a társított költségek szabályozásához vonatkozó korlátok beállításához. 

Lehet, hogy adatokat költségét jelentős attól függően, hogy a következő tényezőket: 

- Rendszerek, infrastruktúra-összetevőihez, felhőalapú erőforrásokat, stb. a gyűjtött száma 
- A forrás, például az üzenetsorok, a naplók, a események, a biztonsági adatok vagy a teljesítménymutatók által létrehozott adatok 
- A források által létrehozott, és a munkaterületre okozhatnak adatok mennyisége 
- Az időszak adatok megőrződnek a munkaterületen  
- Megoldások engedélyezve van, az adatforrás és a gyűjtemény gyakorisága 

Tekintse meg az egyes megoldások dokumentációjában mennyi adatot összegyűjti az becsült biztosít.   

Ha az "ingyenes" tarifacsomag, adatok korlátozódik 7 napos adatmegőrzési. A "/ GB-os (önálló)" vagy "Csomópontonként (OMS)" rétegek gyűjtött adatok az elmúlt 31 napra vonatkozó és megőrzési a 2 éves növelhető. Díjak vonatkoznak, ha egy hosszabb megőrzési időtartam. Az ingyenes csomagban rendelkezik 500 MB-os napi adatfeldolgozást korlátot, és ha talál meg, hogy következetesen túllépi az engedélyezett mennyiségi összegek, a munkaterület módosíthatja a Per GB-os vagy Per-csomópontok szintjeinek meghaladja ezt a határt adatok gyűjtéséért felelős ügyfélfeladatot. A terv típusának módosítása, tetszőleges időpontban, és az árakkal kapcsolatos további információt, lásd: [díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/). 

> [!NOTE]
> A április 2018 azt [bevezetett](https://azure.microsoft.com/en-us/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) Azure figyelési új árképzési modellt. Ez a modell egy egyszerű "használatalapú" modell a teljes kaphat a szolgáltatások figyelése fogad el. További információ a [új árképzési modellt](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hogy a [ebben a modellben való áthelyezése hatásának értékelése](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) a használati minták alapján és [az új modell programba történő](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model). 

A díjszabási modell vagy a réteg, függetlenül az adatok mennyisége kezelésére fundmental számára a költségek szabályozása. A választott és bizonyos megoldást belül van, amelyben az adatok mennyisége korlátozódik, és segítségével kétféleképpen szabályozhatja a költség, Naplóelemzés konfigurációja vezérelt ezek a napi cap és az adatok megőrzési.  

## <a name="review-estimated-cost"></a>Tekintse át a becsült költség
Napló Analytics elérhetővé válnak, hogy megértse, mi a költségek valószínűleg legutóbbi használati minták alapul.  Ehhez hajtsa végre a következő lépéseket.  

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com). 
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. A Naplóelemzési előfizetések ablaktáblán jelölje ki a munkaterület, és kattintson **használati és a becsült költség** a bal oldali ablaktáblán.<br><br> ![Használati és a becsült költség lap](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

Itt a hónap megtekintheti a adatmennyiség. Ez magában foglalja a fogadott és a Naplóelemzési munkaterület minden adat.  Kattintson a **használat részleteiről** származó adatokkal használati irányítópultjának megjelenítése adatok kötet trendek forrás, a számítógépek és a kérésajánlat a lap tetején. Megtekintéséhez és napi eszközigény beállítva és módosítható a megőrzési idő kattintson **adatok kötetkezelés**.
 
Napló Analytics költségek az Azure számlázásának hozzáadódnak. Az Azure számlázás elszámolási szakaszban az Azure portál vagy a részleteit láthatja a [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Napi maximális
Ha a Naplóelemzési munkaterület létrehozása az Azure-portálon, és válassza ki a *szabad* terv, értékűre van állítva egy nap-korlát 500 MB. A más árképzési tervek korlátozva van. Napi cap konfigurálása és a napi adatfeldolgozást korlátozza a munkaterület, de körültekintően járjon el, a cél nem lehet érni a napi korlátot.  Ellenkező esetben ezen a ponton adat elvész a nap hátralevő, és a támogató informatikai szolgáltatásai erőforrások állapotát feltételek be van képességére.  A napi kap olyan adatmennyiség váratlan növekedését kezelése a felügyelt erőforrások és a határidőn belül, vagy ha egyszerűen a nem tervezett díjak munkaterület korlátozni kívánja marad is használható.  

A napi korlát elérésekor a gyűjtemény számlázható adattípusok a nap további leáll.  A kijelölt Naplóelemzési munkaterület a lap tetején megjelenik egy figyelmeztetés Szalagcím és művelet esemény érkezik a *művelet* a tábla **LogManagement** kategóriát. Adatgyűjtés folytatása után a visszaállítás ideje alatt *napi korlátot állít a*. Azt javasoljuk, hogy egy konfigurált értesítése, ha az napi korlátot elérte az ezen művelet események alapján riasztási szabályt. 

### <a name="identify-what-daily-data-limit-to-define"></a>Milyen napi adatok korlát meghatározásához azonosítása 
Felülvizsgálati [Naplóhasználatra elemzés és a becsült költség](log-analytics-usage.md) megtudhatja, hogy az adatok adatfeldolgozást trend, és mi az, hogy a napi kötet kap meghatározásához. Kell tekinteni, gondosan, mivel nem lehet majd figyelését, az erőforrások a határérték elérése után. 

### <a name="manage-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése 
A következő lépések azt ismertetik, hogyan konfigurálhatja a megadott korlát a kötet Naplóelemzési naponta fog betöltési adatok kezelése.  

1. Válassza ki a munkaterületről **használati és a becsült költség** a bal oldali ablaktáblán.
2. Az a **használati és a becsült költség** a kijelölt munkaterülethez lapján kattintson **adatok kötetkezelés** az oldal tetején. 
5. Napi tengelysapka **OFF** – alapértelmezés szerint kattintson **ON** engedélyezheti, majd állítsa be az kötet korlátot GB/nap.<br><br> ![Naplóelemzési adatok korlát beállítása](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Riasztás, ha határértékét
Azt jelen visual köteg az Azure-portálon az adatok korlát küszöbérték teljesülésekor, amíg ez a viselkedés nem feltétlenül igazítás működési azonnali figyelmet igénylő problémák kezelése.  Riasztási értesítést kapni, Új riasztási szabályt hozhat létre az Azure-figyelőben.  További tudnivalókért lásd: [létrehozása, megtekintése és kezelheti a riasztásokat](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).      

Az első lépésekhez, az alábbiakban a riasztásra vonatkozó ajánlott beállítások:

* Cél: A Naplóelemzési erőforrás kiválasztása
* Feltétel: 
   * Jel neve: egyéni napló keresése
   * Keresési lekérdezés: a művelet |} Ha részletes rendelkezik "OverQuota"
   * Alapján: találatok száma
   * Feltétel: Nagyobb, mint
   * Küszöbérték: 0
   * Időszak: 5 (perc)
   * Gyakoriság: 5 (perc)
* Riasztási szabály neve: napi adatok határértékét
* Súlyosság: Figyelmeztetés (Sev 1)

Riasztás van definiálva, és a határt, egy riasztás akkor váltódik ki, és hajtja végre a válasz, a művelet csoportban meghatározott. Az e-mailek és SMS-EK keresztül csapata értesíti, vagy műveletek webhookokkal, Automation-forgatókönyv használatával automatizálhatja vagy [egy külső ITSM megoldás integrálása](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Módosítsa az Adatmegőrzés időtartama 
Az alábbi lépéseket a munkaterületen őriznie adatokat mennyi ideig napló konfigurálását ismertetik.
 
1. Válassza ki a munkaterületről **használati és a becsült költség** a bal oldali ablaktáblán.
2. A a **használati és a becsült költség** kattintson **adatok kötetkezelés** az oldal tetején.
5. A panelen, a csúszkával növelheti vagy csökkentheti a napok számát, és kattintson a **OK**.  Ha a *szabad* réteget, nem tudják módosítani az Adatmegőrzés időtartama, és frissítenie kell a fizetős csomagra ezzel a beállítással szabályozása érdekében.<br><br> ![Munkaterület adatmegőrzési beállításának módosítása](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>Hibaelhárítás
**Kérdés**: hogyan ezeket elhárítani Ha Log Analyticshez már nem gyűjt adatokat? 
**Válasz**: Ha vannak a az ingyenes tarifacsomag, és naponta elküldött 500 MB-nál több adat, adatgyűjtés leáll, a nap további. Napi korlát elérése gyakori oka, hogy Naplóelemzési leállítja az adatgyűjtést, vagy az adatok akkor jelenik meg, hogy hiányzik.  
A Naplóelemzési művelet típusú eseményt hoz létre, amikor adatgyűjtés indítása és leállítása.  
Futtassa a következő lekérdezést a keresést, hogy ellenőrizze, hogy vannak napi korlát elérése és hiányzó adatok: a művelet |} Ha OperationCategory == "Adatgyűjtés állapota"   
Amikor leállítja az adatgyűjtést, a OperationStatus figyelmet igényel. Amikor elindul az adatgyűjtés, akkor a OperationStatus sikeresen befejeződött.  
A következő táblázat ismerteti az oka, hogy leállítja az adatgyűjtést, és folytathatja az adatgyűjtést a javasolt művelet:  

|Leállítja a OK gyűjtemény| Megoldás| 
|-----------------------|---------|
|Elérte az ingyenes adatok napi korlátot<sup>1</sup>|Várjon, amíg a következő napon automatikusan újraindítja a gyűjteményhez, vagy módosítsa egy fizetős tarifacsomagra.|
|Napi adatok kötetkezelés megadott határértékét|Várjon, amíg a következő napon automatikusan újraindítja a gyűjteményhez, vagy növelje az napi kötet korlátot ismertetett [napi maximális adatmennyiség kezelése](#manage-the-maximum-daily-volume)|
|Azure-előfizetés miatt felfüggesztett állapotban van:<br> Ingyenes próbaverzió befejeződött<br> Az Azure hozzáférési lejárt<br> Havi költségkeret érhető el (például a az MSDN webhelyen vagy a Visual Studio előfizetői)|A szolgáltatás fizetős átalakítása<br> Távolítsa el a korlátot, vagy várjon, amíg a korlát alaphelyzetbe állítása|

<sup>1</sup> Ha a munkaterületet az ingyenes tarifacsomag, tehát legfeljebb 500 MB naponkénti adat küldése a szolgáltatást. Ha eléri a napi korlátot, adatgyűjtés leáll, a következő napig. Adatgyűjtés leállítása küldött adatok nem indexelt, és nem érhető el a kereséshez. Adatok gyűjtése folytatódik, amikor feldolgozás csak az új elküldött adatok mennyisége. 

A Naplóelemzési UTC időt használja. A visszaállítási idő platformjától függően eltérő munkaterületek adatok bevitele egyszerre az összes határértéket meghaladó munkaterületek start megelőzése érdekében. Ha a munkaterületet eléri a napi korlátot, feldolgozása után folytatja a meghatározott a visszaállítási idő **napi korlátot állít a**.<br><br> ![A Naplóelemzési korlátozza az UTC időzónára](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Kérdés**: Hogyan tudom értesítést kaphat leállítja az adatok gyűjtését? 
**Válasz**: leírt lépésekkel *létrehozás napi adatok cap* riasztási értesítés az adatgyűjtés leállítása és a leírt lépések lépéseit követi hozzáadása a riasztási szabályok célzó műveletek az e-mail, a webhook vagy a runbook konfigurálása a riasztási szabály művelet. 

## <a name="next-steps"></a>További lépések  

Annak eldöntéséhez, hogy mennyi adatot gyűjt, mely források és a használat és a költséghatékonyság kezeléséhez küldött adatok különböző típusú üzenetet küld, lásd: [Naplóelemzési a használati adatok elemzése](log-analytics-usage.md).