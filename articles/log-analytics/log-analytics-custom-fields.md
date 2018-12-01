---
title: Egyéni mezők az Azure Log Analyticsben |} A Microsoft Docs
description: Az egyéni mezők funkcióval Log Analytics lehetővé teszi, hogy a saját kereshető mezőket hozhat létre, amely az összegyűjtött rekord tulajdonságaira hozzáadása a Log Analytics rekordból.  Ez a cikk azt ismerteti, hogyan hozhat létre egy egyéni mezőt, és a egy minta eseménnyel részletes bemutatóját tartalmazza.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 62af45d8e093b985d8158b22e369307d8c288493
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721306"
---
# <a name="custom-fields-in-log-analytics"></a>A Log Analytics egyéni mezők
A **egyéni mezők** Log Analytics szolgáltatása lehetővé teszi, hogy a saját kereshető mezők hozzáadásával a Log Analytics meglévő rekordok.  Egyéni mezők automatikusan tölti be más tulajdonságokat ugyanazt a rekordot a kinyert adatokkal.

![Egyéni mezők áttekintése](media/log-analytics-custom-fields/overview.png)

Például a minta az alábbi rekordnak az esemény leírása feltárásával hasznos adatokat.  Csomagolja ki ezeket az adatokat külön tulajdonságok lehetővé teszi az olyan műveleteket, mint a rendezési és szűrési.

![Naplóbeli Keresés gomb](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> Az előzetes verzióban érhető el Ön legfeljebb 100 egyéni mezőket a munkaterületén.  Ez a korlátozás kibontásra váró, amikor ez a funkció eléri elérhetőség.
> 
> 

## <a name="creating-a-custom-field"></a>Egyéni mezők létrehozása
Amikor létrehoz egy egyéni mezőt, a Log Analytics ismernie kell adatok tölti ki az értékét.  A Microsoft Research nevű FlashExtract technológia használatával gyorsan azonosíthatja ezeket az adatokat.  Ahelyett, hogy, hogy explicit utasításokkal, a Log Analytics értesül az Ön által megadott példákból kiolvasni kívánt adatokat.

A következő szakaszok az eljárás az egyéni mezőt hoz létre.  Ez a cikk alján van egy minta kivonása bemutató.

> [!NOTE]
> Az egyéni mező fel van töltve, mert a megadott feltételnek megfelelő rekordok kerülnek a Log Analytics, így csak megjelenik egyéni mező létrehozása után összegyűjtött rekordokkal.  Az egyéni mező nem lehet hozzáadni rekordokat, amelyek már szerepel az adattárban a létrehozásakor.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>1. lépés – azonosíthatja a bejegyzéseket, amelyek az egyéni mező
Az első lépéseként azonosítani az egyéni mező lekéri a rekordokat.  Kezdjen egy [standard naplóbeli keresés](log-analytics-queries.md) , majd egy rekordot, hogy működjön, a Log Analytics tekintjük át, a modell.  Megadhatja, hogy fog adatokat nyerhet ki az egyéni mezők, amikor a **mező kinyerése varázsló** van megnyitva, ahol ellenőrzése és finomítsa a feltétel.

1. Lépjen a **naplóbeli keresés** , és egy [lekérdezése a rekordok](log-analytics-queries.md) , amely lesz az egyéni mező.
2. Válassza ki egy rekordot, amely a Log Analytics segítségével egy modellt használ az adatok feltöltése az egyéni mező kinyerése fogja használni.  Az adatok kinyerése a rekord kívánt határozható meg, és a Log Analytics ezt az információt fogja használni, töltse ki az egyéni mező összes hasonló rekordok logikát meghatározni.
3. A gombra kattintva bármely a text tulajdonság a rekordot, majd válassza a balra **mezők kinyerése a következőből**.
4. A **mező a varázsló megnyitásakor**, és a kiválasztott rekord jelenik meg a **fő példa** oszlop.  Az egyéni mező azokat a rekordokat, ugyanazokat az értékeket a tulajdonságokat, amelyeket kiválasztott a meghatározva.  
5. Ha az érték nem pontosan mit szeretne, válassza ki a szűkítheti a feltételek további mezőket.  Annak érdekében, hogy módosítja a mezőt a feltételek pontos értékeit, szakítsa meg és válassza ki a kívánt feltételeknek megfelelő másik rekordot.

### <a name="step-2---perform-initial-extract"></a>2. lépés – kezdeti kinyerési végrehajtani.
A rekordokat, amelyek az egyéni mező azonosítása, után meg kell határoznia a kinyerni kívánt adatok.  A log Analytics ezen információk használatával hasonló rekordok a hasonló minták azonosításához.  Ezt követően az lépésben lesz ellenőrzik az eredményeket, és további részleteket az elemzés használata a Log Analytics nyújt.

1. Jelölje ki a minta-rekordot, amely az egyéni mező feltölteni kívánt szöveg.  Majd megjelenik egy párbeszédpanel, adja meg a mező nevét és a kezdeti kivonat végrehajtásához.  A karakterek  **\_CF** automatikusan hozzá lesznek fűzve.
2. Kattintson a **kinyerése** összegyűjtött rekordok elemzés végrehajtásához.  
3. A **összefoglalás** és **keresési eredmények** szakaszok a kivonatot eredményeinek megjelenítéséhez, így vizsgálhatja meg az adatok pontosságát.  **Összefoglalás** jeleníti meg a rekordok és a egy száma az egyes azonosított az adatértékek azonosításához használt feltételeket.  **Keresési eredmények** részletes listáját a feltételeknek megfelelő rekordokat tartalmazza.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>3. lépés – a kinyerés pontosságának ellenőrzéséhez és egyéni mező létrehozása
Miután elvégezte a kezdeti kivonat, a Log Analytics megjeleníti az eredményeket, amely már begyűjtött adatok alapján.  Ha az eredmények pontos keresés majd is létrehozhat az egyéni mező nincs további munkahelyi.  Ha nem, majd pontosíthatja az eredményeket, hogy a Log Analytics javíthatja a saját logikáját.

1. Ha bármilyen érték szerepel a kezdeti kivonat nem megfelelőek, majd kattintson a **szerkesztése** rekord, és válassza ki egy pontatlan melletti ikonra **kiemelés módosítása** annak érdekében, hogy módosítsa a kijelölést.
2. A bejegyzés kerül a **további példákat** szakasz alatt a **fő példa**.  A kiemelés Itt módosíthatja a kijelölt végzett kell megérteni a Log Analytics segítségével.
3. Kattintson a **kinyerése** az új információk használatáról a meglévő rekordok kiértékeléséhez.  Az eredmények rekordok nem az imént módosította az új elemzéseket alapján módosítható.
4. Folyamatosan javításokat mindaddig, amíg a kivonatot összes rekordja helyesen azonosítani az adatok feltöltése az új egyéni mező.
5. Kattintson a **mentése kinyerése** Ha elégedett az eredményeket.  Az egyéni mező már definiálva van, de azt nem lehet hozzáadni azokat a rekordokat még.
6. Várjon, amíg gyűjthetők össze és futtassa újra a naplóbeli keresés a megadott feltételeknek megfelelő új rekordokat. Új rekordok rendelkeznie kell az egyéni mező.
7. Használja az egyéni mezőt, mint bármely más rekord tulajdonság.  Segítségével, összesített és csoport az adatokat, és akkor is, amellyel új elemzéseket készít.

## <a name="viewing-custom-fields"></a>Egyéni mezők megtekintése
A felügyeleti csoportban az összes egyéni mezők listáját megtekintheti a **speciális beállítások** a Log Analytics-munkaterületet az Azure Portal menüjében.  Válassza ki **adatok** , majd **egyéni mezők** a munkaterület összes egyéni mezők listája.  

![Egyéni mezők](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Egyéni mező eltávolítása
Kétféleképpen egyéni mező eltávolítása.  Az első a **eltávolítása** lehetőséget az egyes mezőkhöz, amikor a teljes lista megtekintése a fent leírtak szerint.  A más módon, hogy egy rekord lekérése, és kattintson a gombra a mező balra.  A menüben a egyéni mező eltávolítása lehetőség lesz.

## <a name="sample-walkthrough"></a>Mintaútmutató
A következő szakasz bemutatja egy egyéni mezőt hoz létre teljes példát.  Ebben a példában kibontja a szolgáltatás nevének a Windows-eseményeket, amelyek jelzik a szolgáltatás állapotának módosítása.  Ez a Service Control Manager által létrehozott Windows-számítógépeken a rendszernapló-események támaszkodik.  Ha azt szeretné, kövesse az ebben a példában, kell lennie [rendszernapló információk események gyűjtése a](../azure-monitor/platform/data-sources-windows-events.md).

Azt adja meg a következő lekérdezés az összes esemény vissza a szolgáltatásvezérlő 7036-esemény azonosítója, amelyre az esemény azt jelzi, hogy a szolgáltatás indítása vagy leállítása rendelkező.

![Lekérdezés](media/log-analytics-custom-fields/query.png)

Majd kiválasztjuk az event ID 7036 rendelkező rekordot.

![Forrás rekord](media/log-analytics-custom-fields/source-record.png)

A szolgáltatás neve jelenik meg szeretnénk a **RenderedDescription** tulajdonságot, és válassza a gomb mellett ez a tulajdonság.

![Mezők kinyerése](media/log-analytics-custom-fields/extract-fields.png)

A **mező kinyerése varázsló** meg van nyitva, és a **EventLog** és **eseményazonosító** mezők vannak kijelölve a a **fő példa** oszlop.  Ez azt jelzi, hogy az egyéni mező határozza meg a rendszer naplóból 7036 Eseményazonosítóval rendelkező eseményeket.  Ez a megfelelő, így nem szükséges egyéb mezők kiválasztásához.

![Fő példa](media/log-analytics-custom-fields/main-example.png)

Hogy jelölje ki a szolgáltatás nevére a **RenderedDescription** tulajdonság és -felhasználási **szolgáltatás** azonosíthatja a szolgáltatás nevét.  Az egyéni mező neve lesz **Service_CF**.

![Mező neve](media/log-analytics-custom-fields/field-title.png)

Láthatjuk, hogy a szolgáltatás neve azonosítja megfelelően az egyes bejegyzések, de nem a mások.   A **keresési eredmények** megjelenítése neve része a **WMI Teljesítmény Adapter** nem lett kiválasztva.  A **összefoglalás** mutat be, amely négy rögzíti a **DPRMA** szolgáltatás helytelenül tartalmaz egy extra word és az azonosított két rekordot **modulok telepítő** helyett **Windows modulok telepítő**.  

![Keresési eredmények](media/log-analytics-custom-fields/search-results-01.png)

A kezdődik a **WMI Teljesítmény Adapter** rekord.  A Szerkesztés ikonra kattint, majd **kiemelés módosítása**.  

![Kiemelés módosítása](media/log-analytics-custom-fields/modify-highlight.png)

Hogy növelje a kiemelés, a keresőkifejezésben **WMI** , majd futtassa újból a kivonatot.  

![További példa](media/log-analytics-custom-fields/additional-example-01.png)

Láthatjuk, hogy a bejegyzéseket **WMI Teljesítmény Adapter** javítását, és a Log Analytics adatokat is használják a rekordok kijavításához **Windows modul telepítője**.  Láthatjuk, hogy az a **összefoglalás** szakasz azonban, hogy **DPMRA** van még mindig nem azonosított megfelelően.

![Keresési eredmények](media/log-analytics-custom-fields/search-results-02.png)

Hogy görgessen egy rekord, a DPMRA-szolgáltatást, és ugyanazzal az eljárással, javítsa ki az adott rekord.

![További példa](media/log-analytics-custom-fields/additional-example-02.png)

 A kivonás futtatásakor láthatjuk, hogy az eredmények összes most pontos.

![Keresési eredmények](media/log-analytics-custom-fields/search-results-03.png)

Láthatjuk, hogy **Service_CF** hoz létre, de még nem adja hozzá azokat a rekordokat.

![Kezdeti száma](media/log-analytics-custom-fields/initial-count.png)

Új így már eltelt némi idő után gyűjtött eseményeket, láthatjuk, hogy a **Service_CF** mező most ad hozzá a feltételeknek megfelelő rekordokat.

![A végeredmény](media/log-analytics-custom-fields/final-results.png)

Mi most már használhatja az egyéni mező, mint bármely más rekord tulajdonság.  Ennek szemléltetésére hozunk létre egy lekérdezést, amely által az új csoportok **Service_CF** vizsgálata, melyek a legaktívabb mező.

![Lekérdezés csoportosítás](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg [naplókereséseket](log-analytics-queries.md) építhetők fel lekérdezések egyéni mezők feltétel használatával.
* A figyelő [egyéni naplófájlok](../azure-monitor/platform/data-sources-custom-logs.md) , amely elemezni a egyéni mezőkkel.

