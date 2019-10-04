---
title: A böngésző & Job View-Azure Data Lake Analytics használata
description: Ez a cikk azt ismerteti, hogyan használható a Azure Data Lake Analytics feladatokhoz tartozó feladat-tallózó és feladat nézet.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309940"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Azure Data Lake Analyticshoz tartozó Job Browser és Job nézet használata
A Azure Data Lake Analytics szolgáltatás archivált feladatokat küld egy lekérdezési tárolóban. Ebből a cikkből megtudhatja, hogyan használhatja a feladatok böngészőjét és a feladatok nézetét a Visual studióhoz készült Azure Data Lake-eszközökben a korábbi feladatok adatainak megkereséséhez. 

Alapértelmezés szerint a Data Lake Analytics szolgáltatás 30 napig archiválja a feladatokat. A lejárati időszak a Azure Portal konfigurálható a testreszabott elévülési szabályzat konfigurálásával. A lejárati idő lejárta után nem fog tudni hozzáférni a feladathoz. 

## <a name="prerequisites"></a>Előfeltételek
Lásd: [Data Lake Tools for Visual Studio előfeltételek](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>A feladatokhoz tartozó böngésző megnyitása
A **> Azure > Data Lake Analytics > feladatok** a Visual Studióban való eléréséhez nyissa meg a feladatot a böngészőn keresztül.  A böngésző használatával elérheti egy Data Lake Analytics fiók lekérdezési tárolóját. A feladat böngészője a bal oldalon jeleníti meg a lekérdezési tárolót, amely az alapszintű feladatok adatait és a feladat nézetét mutatja be a részletes feladatra vonatkozó információk

## <a name="job-view"></a>Feladatok nézet
A feladatok nézet a feladatok részletes információit jeleníti meg. A feladatok megnyitásához kattintson duplán a feladatokra a böngészőben, vagy nyissa meg a Data Lake menüből a feladatok nézet elemre kattintva. Ekkor megjelenik egy párbeszédpanel, amely a feladatok URL-címével van feltöltve.

![Data Lake Tools Visual Studio-feladatok böngészője](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

A feladatok nézete a következőket tartalmazza:

* Feladat összegzése
  
    Frissítse a feladat nézetet a futó feladatok újabb információinak megtekintéséhez.
  
  * Feladatok állapota (gráf):
    
      A feladatok állapota a feladatok fázisait ismerteti:
    
      ![Azure Data Lake Analytics a feladatok fázisainak állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Előkészítése Töltse fel a szkriptet a felhőbe, a szkript fordításával és optimalizálásával a fordítási szolgáltatás használatával.
    * Aszinkron A feladatok várólistára kerülnek, amikor elegendő erőforrásra várnak, vagy ha a feladatok túllépik az egyidejű feladatok maximális számát. A prioritási beállítás határozza meg a várólistán lévő feladatok sorrendjét – minél kisebb számot, annál magasabb prioritást.
    * Fut A feladatok valójában a Data Lake Analytics-fiókban futnak.
    * Véglegesítése A feladatot befejezi (például véglegesíti a fájlt).
      
      A feladat minden fázisban sikertelen lehet. Például fordítási hibák az előkészítési fázisban, időtúllépési hibák a várólistán lévő fázisban, illetve végrehajtási hibák a futó fázisban stb.
  * Alapvető adatok
    
      Az alapszintű feladat adatai a feladat összegzése panel alsó részén láthatók.
    
      ![Azure Data Lake Analytics a feladatok fázisainak állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * A feladatok eredménye: Sikeres vagy sikertelen. Előfordulhat, hogy a feladat minden fázisban meghiúsul.
    * Teljes időtartam: A fal órája (időtartam) az idő beküldése és a Befejezés időpontja között.
    * Számítási idő összesen: A csúcspontok végrehajtási idejének összege, ha a feladatot csak egy csúcsponton hajtja végre, azt az időpontot is megtekintheti. A csúcspontokkal kapcsolatos további információkért tekintse meg a teljes csúcspontokat.
    * Küldés/indítás/Befejezés időpontja: Az az idő, amikor a Data Lake Analytics-szolgáltatás beküldi a feladatot, vagy elindítja a feladatot, vagy a feladatot sikeresen befejezi.
    * Fordítás/üzenetsor-kezelési/Futtatás: Az előkészítési/várólistán lévő/futó fázisban töltött falióra ideje.
    * Fiók A feladatok futtatásához használt Data Lake Analytics fiók.
    * Szerző Az a felhasználó, aki elküldte a feladatot, valós személy fiókja vagy rendszerfiók lehet.
    * Fontosság: A feladat prioritása. Minél kisebb a szám, annál magasabb a prioritás. Ez csak a várólistán lévő feladatok sorrendjére van hatással. A magasabb prioritású beállítás nem megelőzik futó feladatokat.
    * Párhuzamosság Az egyidejű Azure Data Lake Analytics egységek (ADLAUs-EK) kért maximális száma (más néven csúcsok). Jelenleg az egyik csúcspont a két virtuális mag és a hat GB RAM-mal rendelkező virtuális gépekkel egyenlő, bár ez a későbbi Data Lake Analytics frissítésekben is frissíthető.
    * Bal oldali bájtok: A feladatok befejezéséig feldolgozandó bájtok.
    * Olvasott/írt bájtok: A művelet elindítása óta olvasott/írt bájtok száma.
    * Összes csúcspont: A feladatnak sok munkadarabja van, és minden egyes munkafolyamatot csúcspontnak nevezzük. Ez az érték azt írja le, hogy a feladatból hány darab működik. A csúcspontokat alapszintű feldolgozási egységként, más néven Azure Data Lake Analytics egységként (ADLAU) is megtekintheti, és a csúcspontokat párhuzamosan is futtathatja. 
    * Befejezett/futó/sikertelen: A befejezett/futó/sikertelen csúcspontok száma. A csúcspontok a felhasználói kód és a rendszerhibák miatt sikertelenek lehetnek, de a rendszer néhányszor újrapróbálkozik a sikertelen csúcspontok újrapróbálkozásával. Ha az Újrapróbálkozás után is sikertelen volt a csúcspont, a teljes feladat sikertelen lesz.
* Feladatok gráfja
  
    A U-SQL-szkript a bemeneti adatok kimeneti adatokra alakításának logikáját jelöli. A szkript fordítása és optimalizálása az előkészítési fázisban található fizikai végrehajtási tervre történik. A feladatok gráfja a fizikai végrehajtási terv megjelenítése.  Az alábbi ábra a folyamatot szemlélteti:
  
    ![Azure Data Lake Analytics a feladatok fázisainak állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    A feladatok sok munkára vannak bontva. Minden egyes munkadarabot Csúcspontnak nevezünk. A csúcspontokat a rendszer a Super Vertex (más néven szakasz) szerint csoportosítja, és a feladatok Gráfként való megjelenítését. A feladatütemezés zöld szakasza megjeleníti a szakaszokat.
  
    Egy adott szakasz minden csúcspontja ugyanolyan jellegű munkát végez, mint az azonos adatok különböző darabjai. Ha például van egy TB-adatokkal rendelkező fájlja, és több száz csúcspontja van, akkor mindegyik egy adatrészletet olvas. Ezek a csúcspontok ugyanabba a szakaszba vannak csoportosítva, és ugyanazon a bemeneti fájl különböző részein működnek.
  
  * <a name="state-information"></a>Szakasz adatai
    
      Egy adott szakaszban néhány szám jelenik meg a plakáton.
    
      ![Azure Data Lake Analytics a feladatok gráfjának fázisa](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1-kivonat: Egy szám és a művelet metódusa által elnevezett szakasz neve.
    * 84 csúcspontok: A csúcspontok teljes száma ebben a szakaszban. Az ábra azt jelzi, hogy a rendszer hány munkadarabot oszt szét ebben a fázisban.
    * 12,90 s/csúcspont: A szakasz átlagos csúcspont-végrehajtási ideje. Ezt az értéket a SUM (minden csúcspont végrehajtási idő)/(teljes csúcspontok száma) alapján számítjuk ki. Ez azt jelenti, hogy ha a párhuzamosan végrehajtott összes csúcspontot hozzárendelheti, a teljes szakasz 12,90 s-ban fejeződik be. Ez azt is jelenti, hogy az ebben a szakaszban található összes munka sorosan van-e, a díj #vertices * átlagos idő.
    * 850 895 sor írva: Ebben a szakaszban írt teljes sorok száma.
    * R/W: Az ebben a szakaszban olvasott/írt adatmennyiség bájtban megadva.
    * Színek A fázisban a színek a különböző csúcspontok állapotának jelzésére használatosak.
      
      * A zöld érték azt jelzi, hogy a csúcspont sikeres.
      * A narancssárga érték azt jelzi, hogy a csúcspont újból próbálkozik. Az újrapróbált csúcspont meghiúsult, de a rendszer automatikusan újrapróbálkozik, és a teljes szakasz sikeresen befejeződött. Ha a csúcspont újrapróbálkozik, de a hiba továbbra is sikertelen, a szín pirosra vált, és az egész feladatot nem sikerült végrehajtani.
      * A piros jelzi a hibát, ami azt jelenti, hogy egy bizonyos csúcspont többször is próbálkozott a rendszeren, de még sikertelen volt. Ez a forgatókönyv azt eredményezi, hogy a teljes feladat meghiúsul.
      * A kék érték azt jelenti, hogy egy adott csúcspont fut.
      * A fehér érték azt jelzi, hogy a csúcspont várakozik. Előfordulhat, hogy a csúcspont a ADLAU elérhetővé válását követően ütemezhető, vagy a bemenetre vár, mert a bemeneti adatok esetleg nem állnak készen.
      
      A fázissal kapcsolatban további részleteket talál, ha az egérmutatót az egyik állam fölé helyezi:
      
      ![Azure Data Lake Analytics a feladatok gráf szakaszának részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Csúcspontok Ismerteti a csúcspontok részleteit, például azt, hogy hány csúcspontot, hány csúcspontot töltöttek le, vagy amelyek végrehajtása sikertelen volt, vagy továbbra is fut/várakozik stb.
  * Adatolvasási kereszt/belső pod: A fájlok és az adatfájlok tárolása több hüvelyben történik az elosztott fájlrendszerben. Az itt megadott érték azt írja le, hogy az adott Pod-vagy Cross Pod-ban mennyi információ van beolvasva.
  * Számítási idő összesen: A fázisban a csúcspontok végrehajtási idejének összege azt veszi figyelembe, hogy a szakasz minden munkafolyamata csak egy csúcsponton fut.
  * Írás/olvasás: Azt jelzi, hogy az adatmennyiség és a sorok olvasása/írása megtörtént-e.
  * Vertex olvasási hibák: Azt írja le, hogy az adatolvasás során hány csúcspontra volt sikertelen.
  * Töréspontok ismétlődő elvetései: Ha egy csúcspont túl lassan fut, a rendszer több csúcspontot ütemezhet úgy, hogy ugyanazt a munkát futtassa. Ha a csúcspontok egyike sikeresen befejeződött, a rendszer elveti a visszavonási csúcspontokat. A töréspontok ismétlődő elvetései rögzítik a fázisban ismétlődésként elvetett csúcspontok számát.
  * Csúcspont-visszavonások: A csúcspont sikeresen megtörtént, de néhány ok miatt később újra kell futtatni. Ha például az alsóbb szintű csúcspont elveszti a köztes bemeneti adatokat, a rendszer a felsőbb rétegbeli csúcspontot kéri újra.
  * Csúcspont-ütemezett végrehajtások: A csúcspontok ütemezett teljes ideje.
  * Minimális/átlagos/maximális csúcspont-adatolvasás: A csúcspontok összes olvasási értékének minimális/átlagos/maximális száma.
  * Időtartama A fal órájának megkezdési ideje, be kell töltenie a profilt, hogy láthassa ezt az értéket.
  * Feladat visszajátszása
    
      Data Lake Analytics futtatja a feladatokat, és archiválja a feladatok információit futtató csúcspontokat, például a csúcspontok elindítását, leállítását, leállását és azok újrapróbálkozását stb. Az összes információ automatikusan bekerül a lekérdezési tárolóba, és a profiljában tárolódik. A feladattípust letöltheti a "betöltési profil" használatával a feladatok nézetben, és a feladattípus letöltése után megtekintheti a feladatok lejátszását.
    
      A feladatok lejátszási listája a fürtben történtek megtestesülését ábrázolja. Segít megtekinteni a feladatok végrehajtási folyamatát, és a teljesítménybeli rendellenességek és szűk keresztmetszetek vizuális észlelése nagyon rövid idő alatt (kevesebb, mint 30-as általában).
  * Feladatokhoz tartozó hő Térkép megjelenítése 
    
      A feladatokhoz tartozó Heat Map a feladatütemezés megjelenítési legördülő menüjéből választható ki. 
    
      ![Azure Data Lake Analytics a feladatok diagramjának heap térképének megjelenítése](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Megjeleníti egy adott feladathoz tartozó I/O-, idő-és átviteli hőmérsékleti térképet, amelyen megtalálhatja, hogy a feladatnak mennyi idő telik el, vagy hogy a feladatnak van-e egy I/O-határa, és így tovább.
    
      ![Példa a Azure Data Lake Analytics Job Graph heap térképre](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Haladás A feladatok végrehajtásának folyamata a szakasz információi szakaszban olvasható.
    * Olvasott/írt adatbevitel: Az egyes fázisokban beolvasott/írt teljes adatmennyiség.
    * Számítási idő: A SUM (minden csúcspont-végrehajtási idő) Heat térképével megtekintheti, hogy mennyi ideig tart, ha a fázisban lévő összes munkát csak 1 csúcsponttal hajtja végre.
    * Átlagos végrehajtási idő/csomópont: A SUM (minden csúcspont végrehajtási idő)/(csúcspont-szám) típusú hő térképe Ez azt jelenti, hogy ha az összes csomópontot párhuzamosan hajtja végre, a teljes szakasz ebben az időkeretben lesz végrehajtva.
    * Bemeneti/kimeneti átviteli sebesség: Az egyes fázisok bemeneti/kimeneti teljesítményének leképezése esetén megerősítheti, hogy a feladata egy I/O-kötésű feladatot használ-e.
* Metaadat-műveletek
  
    Az U-SQL-parancsfájlban bizonyos metaadatokat is végrehajthat, például adatbázis létrehozását, tábla eldobását stb. Ezek a műveletek a metaadatokkal kapcsolatos műveletekben jelennek meg a fordítás után. Itt megtalálhatja az állításokat, entitásokat hozhat létre, és eldobni az entitásokat.
  
    ![Azure Data Lake Analytics feladatok nézet metaadat-műveletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Állapotelőzmények
  
    A korábbi állapotok is megjeleníthetők a feladatok összegzésében, de itt további részleteket is megtudhat. Megtalálhatja azokat a részletes információkat, mint a feladatok előkészítése, várólistára helyezése, elindítva, befejezve. Azt is megtudhatja, hogy hányszor állították le a feladatot (a CcsAttempts: 1.) Ha a feladatot ténylegesen a fürtnek küldi el (a részletek: Feladatok elküldése a fürtbe) stb.
  
    ![Azure Data Lake Analytics Job View állapot előzményei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnosztika
  
    Az eszköz automatikusan diagnosztizálja a feladatok végrehajtását. A rendszer riasztást küld, amikor valamilyen hiba vagy teljesítménnyel kapcsolatos probléma merül fel a feladatokban. Vegye figyelembe, hogy a profil letöltéséhez le kell töltenie a teljes körű információkat. 
  
    ![Azure Data Lake Analytics Job View diagnosztika](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Figyelmeztetések A fordító figyelmeztetést jelenít meg itt. Ha a riasztás megjelenik, kattintson a "x probléma (ok)" hivatkozásra.
  * A csúcspont futtatása túl hosszú: Ha bármelyik csúcsponton elfogynak az idő (mondjuk 5 óra), a problémák itt jelennek meg.
  * Erőforrás-használat: Ha a szükségesnél több vagy nem elég párhuzamosságot foglalt le, a probléma itt található. Emellett az erőforrás-használat lehetőségre kattintva további részleteket tekinthet meg, és elvégezheti, hogy milyen helyzetekben érdemes jobb erőforrás-kiosztást találni (további részletekért lásd az útmutatót).
  * Memória-ellenőrzési: Ha bármelyik csúcspont több mint 5 GB memóriát használ, itt talál problémát. A feladat végrehajtása a rendszeren is megszakadhat, ha a rendszer több memóriát használ, mint a rendszerkorlátozás.

## <a name="job-detail"></a>Feladatok részletei
A feladatok részletei a feladatok részletes adatait jelenítik meg, beleértve a parancsfájlt, az erőforrásokat és a csúcspontok végrehajtási nézetét.

![Azure Data Lake Analytics feladatok részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Parancsfájl
  
    A feladatok U-SQL-szkriptjét a rendszer a lekérdezési tárolóban tárolja. Megtekintheti az eredeti U-SQL-parancsfájlt, és szükség esetén újra elküldheti.
* További források
  
    A lekérdezési tárolóban tárolt feladatok fordítási kimeneteit erőforrásokon keresztül érheti el. Például megkeresheti az "algebra. xml" fájlt, amely a feladatok Gráfjának, a regisztrált szerelvényeknek stb. megjelenítésére szolgál.
* Csúcspont-végrehajtási nézet
  
    A csúcspontok végrehajtásának részleteit jeleníti meg. A feladatok profilja archivál minden csúcspont-végrehajtási naplót, például a teljes adatolvasási/írási, futtatókörnyezeti, állami stb. Ebben a nézetben további részleteket tudhat meg arról, hogyan futottak a feladatok. További információ: [Data Lake Tools for Visual Studio csúcs-végrehajtási nézetének használata](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A csúcspont-végrehajtási nézet használatához tekintse meg [a Data Lake Tools for Visual Studio csúcs-végrehajtási nézetének használata](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) című témakört.

