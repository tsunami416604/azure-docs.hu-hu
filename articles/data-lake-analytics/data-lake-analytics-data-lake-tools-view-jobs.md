---
title: A Feladatböngésző & a feladatnézet ben – Azure Data Lake Analytics
description: Ez a cikk ismerteti, hogyan használhatja a Feladatböngésző és a Feladatnézet az Azure Data Lake Analytics-feladatok.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309940"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>A feladatböngésző és a Feladat nézet használata az Azure Data Lake Analyticsben
Az Azure Data Lake Analytics szolgáltatás archívumok elküldött feladatok egy lekérdezési tárolóban. Ebben a cikkben megtudhatja, hogyan használhatja a Feladatböngésző és a Feladatnézet használatával az Azure Data Lake Tools for Visual Studio-ban a korábbi feladatinformációk megkereséséhez. 

Alapértelmezés szerint a Data Lake Analytics szolgáltatás 30 napig archiválja a feladatokat. A lejárati időszak konfigurálható az Azure Portalon a testreszabott elévülési szabályzat konfigurálásával. A lejárat után nem fog tudni hozzáférni a feladat adataihoz. 

## <a name="prerequisites"></a>Előfeltételek
Lásd: [Data Lake Tools for Visual Studio előfeltételei](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>A Feladatböngésző megnyitása
A Feladattalló-böngésző tazlononaaaai **kiszolgálókezelőn keresztül>azure>->a Visual Studio Data Lake Analytics>jobs** szolgáltatását.  A Feladat böngésző használatával elérheti a Data Lake Analytics-fiók lekérdezési tárolóját. A Feladattallózó a bal oldalon megjeleníti a Lekérdezéstárolót, amely az alapvető feladatinformációkat, a jobb oldali Feladatnézet pedig a részletes feladatinformációkat jeleníti meg.

## <a name="job-view"></a>Feladat nézet
A Projekt nézet egy feladat részletes adatait jeleníti meg. Feladat megnyitásához kattintson duplán egy feladatra a Feladatböngészőben, vagy nyissa meg a Data Lake menüből a Feladatnézet parancsra kattintva. A feladat URL-címével felvan töltve egy párbeszédablaknak.

![Data Lake Tools Visual Studio feladat böngésző](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

A Feladat nézet a következőket tartalmazza:

* Feladat összegzése
  
    Frissítse a feladatnézetet a futó feladatok újabb információinak megtekintéséhez.
  
  * Feladat állapota (grafikon):
    
      A feladat állapota a feladatfázisokat ismerteti:
    
      ![Az Azure Data Lake Analytics feladatfázisok állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Elkészítés: Töltse fel a parancsfájlt a felhőbe, a fordítási szolgáltatás használatával a parancsfájl összeállítása és optimalizálása.
    * Várólistára helyezett: A feladatok várólistára kerülnek, amikor elegendő erőforrásra várnak, vagy ha a feladatok meghaladják a fiókkorlátozásonkénti egyidejű feladatok maximális egyidejű feladatát. A prioritási beállítás határozza meg a várakozási sorba állított feladatok sorrendjét - minél alacsonyabb a szám, annál magasabb a prioritás.
    * Futás: A feladat valójában fut a Data Lake Analytics-fiókban.
    * Véglegesítés: A feladat befejeződik (például a fájl véglegesítése).
      
      A feladat minden fázisban sikertelen lehet. Például fordítási hibák a felkészülési fázisban, idő-megfordítási hibák a várakozási fázisban, és végrehajtási hibák a Futtatás fázisban stb.
  * Alapvető információk
    
      Az alapvető feladatinformációk a Feladat összegzése panel alsó részén láthatók.
    
      ![Az Azure Data Lake Analytics feladatfázisok állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Feladat eredménye: Sikeres vagy sikertelen. A feladat minden fázisban sikertelen lehet.
    * Teljes időtartam: Falióra ideje (időtartama) a beküldési idő és a befejezési idő között.
    * Teljes számítási idő: Az összeg minden csúcspont végrehajtási idő, akkor tekintse meg az időt, hogy a feladat végrehajtása csak egy csúcspont. A csúcspontról további információt a Teljes csúcspontok című részben talál.
    * Küldés/kezdés/befejezés időpontja: Az az időpont, amikor a Data Lake Analytics szolgáltatás megkapja a feladat elküldése/elindítja a feladat futtatását/befejezi a feladatot sikeresen vagy sem.
    * Összeállítás/várólistára helyezett/futás: Falióra az előkészítés/várólistára helyezett/futás fázis alatt töltött idő.
    * Fiók: A Feladat futtatásához használt Data Lake Analytics-fiók.
    * Szerző: A feladatot beküldő felhasználó lehet valós személy fiókja vagy rendszerfiókja.
    * Prioritás: A feladat prioritása. Minél alacsonyabb a szám, annál magasabb a prioritás. Csak a várólistában lévő feladatok sorrendjét befolyásolja. A magasabb prioritás beállítása nem előzi meg a futó feladatokat.
    * Párhuzamosság: A kért maximális számú egyidejű Azure Data Lake Analytics egységek (ADLAUs), más néven csúcspontok. Jelenleg egy csúcspont egyenlő egy virtuális gép két virtuális maggal és hat GB RAM-mal, bár ez frissíthető a jövőbeli Data Lake Analytics-frissítésekben.
    * Balra hagyott bájtok: Olyan bájtok, amelyeket a feladat befejezéséig fel kell dolgozni.
    * Olvasott/írt bájtok: A feladat futásának kezdete óta olvasott/írt bájtok.
    * Összes csúcs: A munka sok munkadarabra van felosztva, minden egyes munkát csúcspontnak neveznek. Ez az érték azt írja le, hogy a feladat hány munkadarabból áll. A csúcspont alapvető folyamategységnek, más néven az Azure Data Lake Analytics Unit (ADLAU) elemnek tekinthető, és a csúcspontok párhuzamosan futtathatók. 
    * Befejezett/futó/Sikertelen: A befejezett/futó/sikertelen csúcspontok száma. Csúcsok sikertelen lehet mind a felhasználói kód és a rendszer hibák, de a rendszer újrapróbálkozások sikertelen csúcspontok automatikusan néhány alkalommal. Ha a csúcspont az újrapróbálkozás után sem sikerül, az egész feladat sikertelen lesz.
* Feladatgrafikon
  
    Az U-SQL parancsfájl a bemeneti adatok kimeneti adatokká történő átalakításának logikáját jelöli. A parancsfájl fordítása és a felkészülési fázisban fizikai végrehajtási tervre van optimalizálva. A Feladatgrafikon a fizikai végrehajtási terv megjelenítése.  Az alábbi ábra a folyamatot mutatja be:
  
    ![Az Azure Data Lake Analytics feladatfázisok állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Egy munka sok munkadarabra van felosztva. Minden egyes munkát Vertex-nek neveznek. A csúcspontok Szuper csúcspont (más néven szakasz) néven vannak csoportosítva, és feladatgrafikonként vannak elrendezve. A munkagrafikonon látható zöld színpadi plakátok a szakaszokat mutatják.
  
    Egy fázisminden csúcspontja ugyanazt a munkát végzi ugyanazon adatok különböző részeivel. Ha például egy tb-adattal rendelkező fájllal rendelkezik, és több száz csúcsot olvas fel belőle, akkor mindegyik egy adattömböt olvas. Ezek a csúcspontok ugyanabban a szakaszban vannak csoportosítva, és ugyanazt a munkát végzik ugyanazon bemeneti fájl különböző darabjain.
  
  * <a name="state-information"></a>Szakaszra vonatkozó információk
    
      Egy adott szakaszban néhány szám látható a plakáton.
    
      ![Az Azure Data Lake Analytics feladatgrafikon-diagramja](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 kivonat: Egy szakasz neve, amelyet egy szám és a működési módszer nevez el.
    * 84 csúcspont: A csúcsok teljes száma ebben a szakaszban. Az ábra azt jelzi, hogy hány munka van osztva ebben a szakaszban.
    * 12.90 s/csúcspont: Az átlagos csúcspont végrehajtási idő ebben a szakaszban. Ezt az értéket a SZUM (minden csúcspont végrehajtási ideje) / (teljes csúcspontszám) alapján számítja ki. Ami azt jelenti, hogy ha az összes párhuzamoscsúcsot hozzá tudná rendelni, az egész szakasz 12,90 s-ben fejeződik be. Ez azt is jelenti, ha az összes munkát ebben a szakaszban történik sorozatosan, a költség lenne #vertices * AVG idő.
    * 850 895 sor írva: Ebben a szakaszban írt sorok száma összesen.
    * R/W: Az ebben a szakaszban beolvasott/megírt adatok mennyisége bájtban.
    * Színek: A színek a fázisban különböző csúcsállapotot jeleznek.
      
      * A zöld azt jelzi, hogy a csúcspont sikeres.
      * A narancssárga szín azt jelzi, hogy a csúcspont újra próbálkozik. Az újrapróbált csúcspont nem sikerült, de a rendszer automatikusan és sikeresen újrapróbálkozott, és a teljes szakasz sikeresen befejeződött. Ha a csúcspont újra próbálkozott, de még mindig nem sikerült, a szín pirosra vált, és az egész feladat sikertelen volt.
      * A piros azt jelzi, hogy nem sikerült, ami azt jelenti, hogy a rendszer néhányszor újrapróbálkozott egy bizonyos csúcspontgal, de még mindig nem sikerült. Ez a forgatókönyv az egész feladat sikertelenséget eredményez.
      * A kék azt jelenti, hogy egy bizonyos csúcspont fut.
      * A fehér azt jelzi, hogy a csúcspont várakozik. Előfordulhat, hogy a csúcspont ütemezése akkor lesz ütemezve, amikor az ADLAU elérhetővé válik, vagy a bemeneti adatok ra vár, mivel előfordulhat, hogy a bemeneti adatok nem állnak készen.
      
      A színpadtovábbi részleteit az egérmutató egy állapotban történő rámutatásával találhatja meg:
      
      ![Az Azure Data Lake Analytics feladatgrafikon-diagramrészletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Csúcspontok: Leírja a csúcsok részleteit, például azt, hogy összesen hány csúcsot, hány csúcsot teljesítettek, sikertelenek vagy még mindig futnak/várnak stb.
  * Adatok olvasása kereszt/intra pod: A fájlok és az adatok több podban tárolódnak elosztott fájlrendszerben. Az itt megadott érték azt ismerteti, hogy mennyi adatot olvasott be ugyanabban a podban vagy keresztpodban.
  * Teljes számítási idő: A fázis minden csúcspont-végrehajtási idejének összege, akkor azt az időt, amelyet a fázisban végzett összes munka csak egy csúcspontban hajt végre.
  * Írt/olvasott adatok és sorok: Azt jelzi, hogy mennyi adatot vagy sort olvastak/írtak, vagy mennyi adatot kell olvasni.
  * Csúcspont olvasási hibák: Azt ismerteti, hogy hány csúcspont sikertelen az adatok olvasása közben.
  * Csúcspont ismétlődő elvetések: Ha a csúcspont túl lassan fut, a rendszer több csúcspontot is ütemezhet ugyanarra a munkára. A reduktív csúcspontok elvesznek, amint az egyik csúcs sikeresen befejeződik. A Vertex duplikált liketlik a fázisban duplikált csúcspontok számát rögzíti.
  * Vertex-visszavonások: A csúcspont sikeres volt, de bizonyos okok miatt később újrafuttatva. Ha például az alsóbb rétegbeli csúcspont elveszíti a köztes bemeneti adatokat, a felsőbb rétegbeli csúcspont újrafuttatást kér.
  * Csúcspont ütemezése végrehajtások: A csúcspontok ütemezésének teljes ideje.
  * Min/Average/Max Vertex adatok olvashatók: Minden csúcspont olvasási adatának minimális/átlagos/maximuma.
  * Időtartam: A falióra idő egy szakaszban vesz, be kell tölteni a profilt, hogy ezt az értéket.
  * Feladat visszajátszása
    
      A Data Lake Analytics feladatokat futtat, és archiválja a feladatok adatait futtató csúcsokat, például a csúcspontok indításakor, leállításában, sikertelensítésében és azok újrakísérlésének módjában stb. A rendszer automatikusan naplózza az összes információt a lekérdezéstárolóban, és a feladatprofilban tárolja. A feladatprofilt a "Profil betöltése" nézetben töltheti le, és a Feladatprofil letöltése után megtekintheti a feladatlejátszást.
    
      A feladatlejátszás a fürtben történtek megtestesítője. Ez segít nézni a feladat végrehajtása folyamatban van, és vizuálisan érzékeli a teljesítmény anomáliák és szűk keresztmetszetek nagyon rövid idő alatt (kevesebb, mint 30s általában).
  * Job Heat térkép megjelenítése 
    
      A Feladathőtérkép a Feladatgrafikon Megjelenítés legördülő menüjében választható ki. 
    
      ![Az Azure Data Lake Analytics feladatdiagram-térképének megjelenítése](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Ez azt mutatja, az I/O, idő és áteresztőhő térkép egy feladat, amelyen keresztül megtalálhatja, ahol a feladat tölti a legtöbb időt, vagy hogy a feladat egy I/O határ feladat, és így tovább.
    
      ![Példa az Azure Data Lake Analytics feladatdiagram-térképre](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Előrehaladás: A feladat végrehajtásának folyamata, lásd: Információ a szakaszban információk.
    * Olvasott/írott adatok: Az egyes fázisokban olvasott/írt összes adat hőtérképe.
    * Számítási idő: A SZUM hőtérképe (minden csúcspont végrehajtási ideje), ezt úgy tekintheti meg, hogy mennyi ideig tartana, ha a fázisban lévő összes munkát csak 1 csúcspontlal hajtják végre.
    * Átlagos végrehajtási idő csomópontonként: A SZUM hőtérképe (minden csúcspont végrehajtási ideje) / (Csúcspont szám). Ami azt jelenti, hogy ha hozzá tudná rendelni az összes párhuzamosságú csúcsot, az egész szakasz ebben az időkeretben lesz kész.
    * Bemeneti/kimeneti átviteli: Az egyes fázisok bemeneti/kimeneti átviteli teljesítményének hőtérképe, megerősítheti, hogy a feladat egy I/O-kötésű feladat ezen keresztül.
* Metaadat-műveletek
  
    Az U-SQL parancsfájlban végrehajthat néhány metaadat-műveletet, például létrehozhat egy adatbázist, eldobhat egy táblát stb. Ezek a műveletek a metaadat-műveletben jelennek meg fordítás után. Előfordulhat, hogy állításokat talál, entitásokat hoz létre, és itt elejti az entitásokat.
  
    ![Azure Data Lake Analytics feladatnézet metaadat-műveletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Állam története
  
    Az állapotelőzmények a Feladatösszegzés ben is megjelennek, de további részleteket itt kaphat. Megtalálhatja a részletes információkat, például amikor a feladat előkészíti, várólistára, futás megkezdése, befejeződött. Azt is megtalálhatja, hogy hányszor állították össze a feladatot (a CcsAttempts: 1), mikor a feladatot ténylegesen elküldték a fürthöz (a Részletek: Feladat elosztása a fürthöz), stb.
  
    ![Az Azure Data Lake Analytics feladatmegtekintéseállapot-előzményei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnosztika
  
    Az eszköz automatikusan diagnosztizálja a feladatvégrehajtást. Figyelmeztetéseket fog kapni, ha hibák vagy teljesítményproblémák merülnek fel a feladatokban. Kérjük, vegye figyelembe, hogy le kell töltenie a Profilt, hogy teljes körű tájékoztatást kapjon itt. 
  
    ![Az Azure Data Lake Analytics feladatmegtekintések diagnosztikája](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Figyelmeztetések: Egy riasztás jelenik meg itt a fordító figyelmeztetéssel. Az "x probléma(ok)" hivatkozásra kattintva további részleteket kaphat, ha a riasztás megjelenik.
  * A csúcspont túl hosszú ideig fut: Ha bármelyik csúcspont kifut az időből (mondjuk 5 óra), a problémák itt találhatók.
  * Erőforrás-használat: Ha a szükségesnél többet vagy nem elegendő párhuzamossághoz rendelt ki, a problémák itt találhatók. Az Erőforrás-használat elemre kattintva további részleteket is megkaphat, és lehetőségesetén ekként végezhetjobb erőforrás-elosztást (további részletekért lásd ezt az útmutatót).
  * Memóriaellenőrzés: Ha bármelyik csúcspont 5 GB-nál több memóriát használ, a problémák itt találhatók. A feladat végrehajtását a rendszer megölheti, ha több memóriát használ, mint a rendszerkorlátozás.

## <a name="job-detail"></a>Feladat részletei
A Feladat részletei a feladat részletes adatait jeleníti meg, beleértve a Parancsfájlt, az Erőforrásokat és a Csúcspont végrehajtási nézetet.

![Az Azure Data Lake Analytics feladat részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Parancsfájl
  
    A feladat U-SQL parancsfájlja a lekérdezéstárolóban tárolódik. Megtekintheti az eredeti U-SQL parancsfájlt, és szükség esetén újra elküldheti.
* Források
  
    A feladat fordítási kimenetek a lekérdezési tárolóban tárolt erőforrásokon keresztül. Például, megtalálja "algebra.xml", amely arra használják, hogy bemutassa a Job Graph, a szerelvények regisztrált, stb itt.
* Csúcspont végrehajtási nézet
  
    Ez azt mutatja, csúcsok végrehajtási részleteket. A feladatprofil archiválja a csúcspont végrehajtási naplóját, például az összes olvasott/írt adatot, a futásidejűt, az állapotot stb. Ezen a nézeten keresztül további részleteket kaphat arról, hogyan futott egy feladat. További információt [a Vertex végrehajtási nézet használata a Visual Studio Data Lake Tools alkalmazásban című témakörben talál.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

## <a name="next-steps"></a>Következő lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A csúcspont-végrehajtási nézet használatához olvassa [el A Csúcspont végrehajtási nézet használata a Visual Studio Data Lake Tools alkalmazásban című témakört.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

