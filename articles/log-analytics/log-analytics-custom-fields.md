---
title: Az egyéni mezők Azure Naplóelemzés |} Microsoft Docs
description: Naplóelemzési egyéni mezők jellemzője hozhat létre a saját kereshető mezők összegyűjtött rekord tulajdonságainak hozzáadni a Naplóelemzési rekordból.  Ez a cikk ismerteti a folyamat létrehoz egy egyéni mezőt és részletes útmutató egy minta eseményhez.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: 59dbc4134ee11999e54d2eba51804939e7e14229
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020733"
---
# <a name="custom-fields-in-log-analytics"></a>A Naplóelemzési egyéni mezők
A **egyéni mezők** Naplóelemzési funkciója lehetővé teszi, hogy a saját kereshető mezők hozzáadásával meglévő rekordjainak Naplóelemzési bővítése.  Egyéni mezők automatikusan tölti be más tulajdonságok ugyanabban a rekordban a kivonatolt adatokon.

![Egyéni mezők – áttekintés](media/log-analytics-custom-fields/overview.png)

Az alábbi minta rekord például a hasznos adatok az esemény leírása megbúvó rendelkezik.  Csomagolja ki ezeket az adatokat külön tulajdonságok lehetővé teszi az olyan műveleteket, mint a rendezési és szűrési.

![Napló Keresés gomb](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> Az előnézetben azonban legfeljebb 100 egyéni mezők a munkaterületen.  Ezt a határt kibontásra váró, amikor ez a szolgáltatás általánosan rendelkezésre álló eléri.
> 
> 

## <a name="creating-a-custom-field"></a>Egyéni mező létrehozása
Amikor létrehoz egy egyéni mező, Log Analyticshez kell érteniük a mely adatokat tölti ki az értékét.  Gyorsan azonosíthatja ezeket az adatokat a Microsoft Research nevű FlashExtract technológiáját használja.  Ahelyett, hogy nincs szükség és explicit utasításokkal, Log Analyticshez értesül megadandó példák kibontani kívánt adatokat.

A következő szakaszokban az eljárás egyéni mező létrehozásához.  Ez a cikk alján egy minta kivonása bemutatóért van.

> [!NOTE]
> Az egyéni mező fel van töltve, rögzíti a megadott feltételnek megfelelő hozzáadása szolgáltatáshoz, így csak jelenik meg az egyéni mező létrehozása után gyűjtött rögzíti.  A egyéni mező nem adható hozzá bejegyzésekre, amelyek már szerepel az adattárban létrehozásakor.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>1. lépés – azt jelzi, hogy az egyéni mező lesz azonosítása
Az első lépés, az azt jelzi, hogy megkapja az egyéni mező azonosításához.  A kiindulási pont a [szabványos naplófájl-keresési](log-analytics-log-searches.md) , és válassza a modell, amely a Naplóelemzési bemutatja, hogyan meghatalmazottjaként járhatnak el egy olyan rekordot.  Ha megadja, hogy az adatok kinyerése egy egyéni mezőbe fog a **mező a varázsló** meg van nyitva, ahol ellenőrizze és módosítsa a feltételeket.

1. Ugrás a **naplófájl-keresési** , és egy [a rekordok lekérdezése](log-analytics-log-searches.md) kell, hogy az egyéni mező.
2. Válasszon egy rekordot, amely Naplóelemzési működjön, és töltse fel adatokkal az egyéni mező adatokat nyerő modellt használja.  Az adatokat, amelyet szeretne kivonni a rekord a határozható meg, és Naplóelemzési fog használni ezt az információt meghatározásához töltse fel adatokkal az összes hasonló rögzíti az egyéni mező logika.
3. A gombra kattintva a text tulajdonság a rekord, és válassza ki a bal oldali **bontsa ki a mezőket**.
4. A **mező a varázsló már meg van nyitva**, és megjelenik a kijelölt bejegyzést az **fő példa** oszlop.  Az egyéni mező határozza meg azokat a rekordokat a kijelölt tulajdonságainál ugyanazokat az értékeket.  
5. Ha az érték nem pontosan mit szeretne, válassza ki további mezőket szűkíthető a feltételeket.  Ahhoz, hogy a mező értékeket a feltétel módosításához szakítsa meg és válassza ki a kívánt feltételeknek megfelelő másik bejegyzést.

### <a name="step-2---perform-initial-extract"></a>2. lépés – hajtsa végre a kezdeti kivonatot.
A-rekordot a rendszer az egyéni mező állapította meg, Miután azonosította a kinyerni kívánt adatokat.  A Naplóelemzési ezen információk használatával hasonló rekordokban hasonló minták azonosításához.  Ezt követően az lépésben lesz érvényesíteni az eredményeket, és további Naplóelemzési a elemzésben használatára vonatkozó részleteket biztosítanak.

1. Jelölje ki a szöveget a minta-rekordot, amely feltölti az egyéni mező.  Majd választhat egy párbeszédpanel, adja meg a mező nevét, majd a kezdeti kivonat végrehajtásához.  A karakterek  **\_CF** automatikusan lesz hozzáfűzve.
2. Kattintson a **kibontása** összegyűjtött rekordok elemzés végrehajtásához.  
3. A **összegzés** és **keresési eredmények** szakaszok a kivonatot eredményének megjelenítése, így pontosságát vizsgálhatja meg.  **Összefoglalás** jeleníti meg a rekordok és a számát az egyes azonosított adatértékek azonosításához használt feltételeket.  **A keresési eredményekben** a feltételeknek megfelelő rekordok részletes listáját tartalmazza.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>3. lépés – ellenőrizze a kivonat pontosságát, és hozzon létre egyéni mező
Miután elvégezte a kezdeti kinyerési, Naplóelemzési megjeleníti az eredményeket már gyűjtött adatok alapján.  Ha az eredmények keresse meg a pontos majd létrehozhat az egyéni mező nincs további feladata a.  Ha nem, majd pontosíthatja az eredményeket, hogy a Naplóelemzési javíthatja a logikája.

1. Ha a kezdeti kivonatban értékeket nem megfelelő, majd kattintson a **szerkesztése** rekord, és válassza pontatlan melletti ikon **módosítása a kiemelési** ahhoz, hogy módosítja a kiválasztott.
2. A bejegyzés a rendszer átmásolja a **további példákat** szakasz alatt a **fő példa**.  Itt a kiemelési megérteni a kijelölés kell végzett Naplóelemzési segítségével állíthatja be.
3. Kattintson a **kibontása** ezen új információk segítségével kiértékelheti a meglévő rekordokat.  Az eredmények a rekordok nem az imént módosította az új eszközintelligencia alapján módosíthatja.
4. Folytassa, amíg hozzá javításokat, amíg minden rekordot a kivonatot helyesen azonosítani az új egyéni mező feltöltése az adatokat.
5. Kattintson a **mentése kibontása** Ha elégedett az eredményeket.  Az egyéni mező már meg van adva, de az nem vehető fel minden olyan rekord még.
6. Várjon, amíg az új rekordok gyűjtés és futtassa újból a napló keresése a megadott feltételnek megfelelő. Új rögzíti az egyéni mező kell rendelkeznie.
7. Használja az egyéni mező, mint bármely más rekord tulajdonság.  Összesítő és csoportosítási adatok segítségével, és még segítségével új.

## <a name="viewing-custom-fields"></a>Egyéni mezők megtekintése
Összes egyéni mezők listáját megtekintheti a a felügyeleti csoportnak a **speciális beállítások** menü a Naplóelemzési munkaterület az Azure portálon.  Válassza ki **adatok** , majd **egyéni mezők** a munkaterületen lévő összes egyéni mezők listája.  

![Egyéni mezők](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Egyéni mező eltávolítása
Két módon lehet egyéni mező eltávolítása.  Az első a **eltávolítása** lehetőséget az egyes mezők, ha a teljes listáját jeleníti meg fent leírt módon.  A más módon, hogy a rekord beolvasása és a gombra kattintva a mező a bal oldali.  A menü egy lehetőség, hogy távolítsa el az egyéni mező fog rendelkezni.

## <a name="sample-walkthrough"></a>Mintaútmutató
A következő szakasz végigvezeti az egyéni mezők létrehozása átfogó példát.  Ez a példa kibontja a szolgáltatás nevét az állapotváltás szolgáltatás jelző Windows-eseményeket.  Ez az a Windows rendszerű számítógépeken lévő szolgáltatásvezérlő által létrehozott események támaszkodik.  Ha azt szeretné, hogy kövesse az ebben a példában, kell lennie [rendszernapló információk események gyűjtése](log-analytics-data-sources-windows-events.md).

Azt adja meg a következő lekérdezés futtatásával térjen vissza az összes esemény szolgáltatásvezérlőtől 7036 egy Eseményazonosítót az esemény, amely jelzi a szolgáltatás indítása és leállítása, amely rendelkező.

![Lekérdezés](media/log-analytics-custom-fields/query.png)

Azt bármely rekord event ID 7036, majd válassza ki.

![Forrás-rekord](media/log-analytics-custom-fields/source-record.png)

Azt szeretnénk, ha a szolgáltatás neve, amely megjelenik a **RenderedDescription** tulajdonság, és válassza a gomb melletti ezt a tulajdonságot.

![Bontsa ki a mezők](media/log-analytics-custom-fields/extract-fields.png)

A **mező a varázsló** már meg van nyitva, és a **EventLog** és **EventID** mezők vannak kijelölve a a **fő példa** oszlop.  Ez azt jelzi, hogy az egyéni mező határozza meg a rendszer naplóból 7036 Eseménynapló azonosítójú események.  Ez is használhatók, így nem szükséges bármely más mezők kiválasztása.

![Fő példa](media/log-analytics-custom-fields/main-example.png)

Azt a szolgáltatás nevét, a jelölje ki a **RenderedDescription** tulajdonság és -felhasználási **szolgáltatás** szolgáltatás nevét.  Az egyéni mező neve **Service_CF**.

![Mezőnév](media/log-analytics-custom-fields/field-title.png)

Látható, hogy a szolgáltatás neve, amelynél megfelelően az egyes rekordok, de nem mások számára.   A **keresési eredmények** jelenjen meg a nevet része a **WMI Teljesítmény Adapter** nincs kiválasztva.  A **összegzés** mutat be, amely négy rögzíti a **DPRMA** szolgáltatás helytelenül része egy extra word és az azonosított két rekordok **modulok telepítője** helyett **Windows Installer-modulok**.  

![Keresési eredmények](media/log-analytics-custom-fields/search-results-01.png)

Először a **WMI Teljesítmény Adapter** rekord.  A Szerkesztés ikonra elemre kattint, majd **módosítása a kiemelési**.  

![Kiemelési módosítása](media/log-analytics-custom-fields/modify-highlight.png)

Azt a kiemelési felvenni a szó növelése **WMI** , majd futtassa újra a kivonatot.  

![További – példa](media/log-analytics-custom-fields/additional-example-01.png)

Láthatja, hogy a bejegyzéseket **WMI Teljesítmény Adapter** javítását, és Naplóelemzési is adatokat használják a javítsa ki a rekordok **Windows Installer-modul**.  A láthatja a **összegzés** szakasz azonban, hogy **DPMRA** van még nem azonosított megfelelően.

![Keresési eredmények](media/log-analytics-custom-fields/search-results-02.png)

Azt a rekord a DPMRA-szolgáltatást, görgessen, és ugyanazzal az eljárással, hogy a rekord megoldására.

![További – példa](media/log-analytics-custom-fields/additional-example-02.png)

 A kinyerési futtatásakor azt mutatják, hogy az eredmények összes most pontos.

![Keresési eredmények](media/log-analytics-custom-fields/search-results-03.png)

Láthatja, hogy **Service_CF** létrejött, de még nincs hozzáadva rekordokat.

![Kezdeti száma](media/log-analytics-custom-fields/initial-count.png)

Után egy ideig, az új eseményeket a rendszer gyűjti, láthatja, hogy a **Service_CF** mező most ad hozzá a feltételeknek megfelelő rekordok.

![Végső eredmények](media/log-analytics-custom-fields/final-results.png)

Most már használhatja azt az egyéni mező, mint bármely más rekord tulajdonság.  Ennek szemléltetésére nem, amely csoportosítja az új lekérdezést létrehozni **Service_CF** vizsgálata, mely szolgáltatások a legaktívabb mező.

![Lekérdezés csoportosítás](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) egyéni mezőkkel feltétel lekérdezések összeállításához.
* A figyelő [egyéni naplófájlok](log-analytics-data-sources-custom-logs.md) , amely elemezni a egyéni mezőkkel.

