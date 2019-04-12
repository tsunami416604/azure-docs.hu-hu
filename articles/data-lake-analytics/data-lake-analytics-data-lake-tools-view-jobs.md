---
title: A Feladatböngésző és a feladat nézet használata Azure Data Lake Analytics-feladatok
description: Ez a cikk ismerteti a Feladatböngésző és a feladat nézet használata Azure Data Lake Analytics-feladatokhoz.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 905100f8a1444f6f6ee18d3bf9e9eab2ede8c805
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815200"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Használja a Feladatböngésző és a feladat nézet számára az Azure Data Lake Analytics
Az Azure Data Lake Analytics szolgáltatás archiválja a query store az elküldött feladatot. Ebből a cikkből elsajátíthatja a Feladatböngésző és a feladat nézet használata Azure Data Lake Tools for Visual Studio korábbi feladat információ megtalálását. 

Alapértelmezés szerint a Data Lake Analytics szolgáltatást a feladatok archívumok 30 napig. A lejárati idő a testre szabott elévülési szabályzatának konfigurálásával konfigurálható az Azure Portalról. Nem lehet tudni hozzáférni a lejárat után az adatok. 

## <a name="prerequisites"></a>Előfeltételek
Lásd: [Data Lake Tools for Visual Studio Előfeltételek](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Nyissa meg a feladat böngészőben
A Feladatböngésző keresztül eléréséhez **Server Explorer > Azure > a Data Lake Analytics > feladatok** a Visual Studióban.  A feladat böngészővel a lekérdezéstár egy Data Lake Analytics-fiók eléréséhez. A Feladatböngésző megjeleníti Query Store a bal oldali alapszintű feladatinformációkat, valamint a megfelelő bemutató. Ez a feladat nézet részletes feladatinformációkat.

## <a name="job-view"></a>Feladat megtekintése
Feladat megtekintése egy feladat részletes információit jeleníti meg. Nyissa meg a feladatot, kattintson duplán egy feladatot a feladat böngészőben, vagy nyissa meg a Data Lake menüből feladat megtekintése gombra kattintva. Megjelenik egy párbeszédpanel, a feladat URL-cím használatával.

![Data Lake Tools Visual Studio Job Browser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Feladat-nézet tartalmazza:

* Feladat összegzése
  
    A feladat nézet frissítése a legújabb információkat a futó feladatok.
  
  * Feladat állapota (gráf):
    
      Feladat állapota a feladat fázisból ismerteti:
    
      ![Az Azure Data Lake Analytics-feladat fázisból állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Előkészítés: A szkript feltöltése a felhőbe, a kódja lefordításának és optimalizálás a parancsfájl a fordítási szolgáltatás segítségével.
    * Várólistán: Feladatok várólistára kerülnek, amikor elegendő erőforrást várnak, vagy a feladatok meghaladja a maximális párhuzamos feladatok száma fiók korlátozás. A prioritás beállítása határozza meg a várakozási sorban lévő feladatok - sorozata a kisebb a szám, annál magasabb a prioritás.
    * Fut: A feladat ténylegesen fut a Data Lake Analytics-fiókban.
    * Véglegesítése: A feladat befejeződik (például a fájl véglegesítése).
      
      A feladat minden fázisban meghiúsulhat. Ha például fordítási hibát jelez a Preparing fázis, a várólistán fázisban időtúllépési hibákat, és végrehajtási hibák a futó fázis stb.
  * Alapvető adatok
    
      A feladat alapvető információ a feladat összegzése panel alsó részén jelenik meg.
    
      ![Az Azure Data Lake Analytics-feladat fázisból állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Feladat eredménye: Sikeres vagy sikertelen volt. A feladat minden fázisban meghiúsulhat.
    * Teljes időtartam: Valós idő (időtartam) elküldése időt és befejezési időpont között.
    * Összes számítási idő: Minden csúcspont Futtatás végrehajtási idejének összege, érdemes lehet azt, hogy a feladat végrehajtása a csak egy-egy csúcsának idő. Tekintse meg a teljes csúcspontok csúcspont további információkat találhat.
    * Küldje el/kezdő/záró idő: Az az időpont, amikor a Data Lake Analytics szolgáltatás fogad-e feladat beküldése/elindul, és a feladat/véget ér a feladat futtatása sikeresen lezajlott -e.
    * Compilation/Queued/Running: Valós idő töltött a Preparing/várólistán/futó fázis során.
    * Fiók: A feladat futtatásához használt Data Lake Analytics-fiók.
    * Szerző: A felhasználó, aki a feladat elküldése egy valódi személy vagy a system fiók lehet.
    * Prioritás: A feladat prioritása. Az alacsonyabb a szám, annál magasabb a prioritás. Csak a várólistában a feladatok sorozatát van hatással. Egy magasabb prioritású beállítása nem futó feladatok megelőzik az.
    * Parallelism: Egyidejű Azure Data Lake Analytics egységek (ADLAUs), más néven csúcspontok kért maximális száma. Jelenleg egy-egy csúcsának megegyezik egy virtuális Gépet, a két virtuális mag, és 6 GB RAM, bár ez sikerült frissíteni a jövőben a Data Lake Analytics frissíti.
    * Balra bájtok: Bájtok száma, amelyek kell feldolgozni, amíg a feladat befejeződik.
    * Írt vagy olvasott bájtok: Bájtok száma, amelyeket írt vagy olvasott a feladat indulása óta.
    * Teljes csúcsok: A feladat hány darab munka van osztva, minden egyes munkákat a csúcspont neve. Ez az érték áll a feladat hány darab munka ismerteti. Csúcs érdemes lehet egy alapvető folyamat egységként, más néven Azure Data Lake elemzési egység (ADLAU), és a csúcspontok párhuzamosság környezetben is futtatható. 
    * Befejezett/futó/nem sikerült: Befejezett/futó/nem sikerült a csúcspontok száma. Csúcspontok mindkét felhasználói kód és a rendszer hibák miatt sikertelen lehet, de a rendszer próbálkozások csúcspontok automatikusan néhány alkalommal sem sikerült. A csúcspont még mindig nem sikerült, újrapróbálkozás után, ha a teljes feladat sikertelen lesz.
* A Feladatgrafikon
  
    A U-SQL parancsfájl logikájának átalakítja a bemeneti adatokat kimeneti adatokat jelöli. A parancsfájl lefordított, és a egy fizikai végrehajtási terv a Preparing fázisában optimalizált. A Feladatgrafikon, hogy a fizikai végrehajtási terv megjelenítése.  Az alábbi ábra illusztrálja a folyamatot:
  
    ![Az Azure Data Lake Analytics-feladat fázisból állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Egy feladat hány darab munka van osztva. Minden egyes munkákat a csúcspont neve. A csúcspontok Super csúcspont (más néven fázis) szerint csoportosítva, és mint Feladatgrafikon vizualizálja. A zöld fázis felirat a feladat grafikon megjelenítése a szakaszokat.
  
    Minden csúcspont egy lépésben hajtaná különböző részei ugyanazokat az adatokat a használata ugyanolyan típusú állapotát. Például ha egy fájl egy TB adatokkal rendelkezik, és a csúcspontok olvasásakor, több száz, azok van olvasásakor a rendszer. Ezek a csúcspontok szerint vannak csoportosítva, azonos fázisban és ezzel azonos működik különböző részein ugyanazt a bemeneti fájlt.
  
  * <a name="state-information"></a>Szakasz információkat
    
      Bizonyos számok egy adott szakaszban, a metaadattábla láthatók.
    
      ![Az Azure Data Lake Analytics-feladat graph fázis](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 kivonat: Egy-egy számot, és a műveleti metódus nevű szakaszban neve.
    * 84 csúcsok: Az ebben a szakaszban a csúcspontok száma. Az ábra azt jelzi, hogy hány darab munka ennek a szakasznak van osztva.
    * 12.90 s csúcspont: Ebben a szakaszban átlagos a vertex végrehajtási idejét. Ez az ábra számítja ki a SUM (minden a vertex végrehajtási idő) / (teljes csúcspont száma). Ami azt jelenti, hogy a párhuzamosság végrehajtott összes csúcspontjának hozzárendelhet, ha a teljes szakaszban 12.90 befejezett s. Azt is, hogy ebben a szakaszban az összes munkahelyi a Feladattervek történik, ha a díj a következő lesz #vertices * átlagos idő.
    * a sorok írásának 850,895: Ebben a szakaszban írt sorok teljes számát.
    * R/W: Adatmennyiség bájtban ebben a szakaszban olvasható/Written.
    * Színek: Színek szakaszában használják, amely különböző csúcspont állapotát jelzi.
      
      * Zöld jelzi a csúcspont van sikeres volt.
      * Narancssárga azt jelzi, hogy a csúcspont rendszer. A rendszer csúcspont sikertelen volt, de a rendszer automatikusan, és sikeresen rendszer, és az általános szakasz sikeresen befejeződött. Ha a rendszer megpróbálja újból végrehajtani a csúcspont, de továbbra is sikertelen, a színt kikapcsolja a piros és a teljes feladat sikertelen volt.
      * Piros szín jelzi a sikertelen, ami azt jelenti, hogy egy bizonyos csúcspont volt lett a rendszer újra megpróbálja a néhányszor, de továbbra is sikertelen volt. Ebben a forgatókönyvben sikertelen lesz a teljes feladat hatására.
      * Kék azt jelenti, hogy egy bizonyos csúcspont fut-e.
      * Fehér azt jelzi, hogy a csúcspont vár. A csúcspont vár ütemezni, miután egy ADLAU elérhetővé válik, vagy azt is lehet vár, mivel a bemeneti adatait nem áll készen.
      
      További részleteket a szakasz a viszi az egérmutatót egy állam szerint található:
      
      ![Az Azure Data Lake Analytics-feladat graph fázis részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Csúcsok: Ismerteti a csúcspontok részleteit, például teljes, hány csúcspontok befejeződött, hogy hány csúcspontok, sikertelen, vagy továbbra is fut/Várakozás stb.
  * Határokon/belüli pod olvasott adatok: Fájlok és adatok vannak tárolva több podok elosztott fájlrendszerben. Az itt szereplő érték ismerteti, hogy mennyi adatot olvasott a azonos podot vagy közötti pod.
  * Összes számítási idő: Minden a vertex végrehajtási idő a fázisban a sum, érdemes lehet azt az időt vesz igénybe, ha minden működik, a szakaszban a csak egy-egy csúcsának hajtja végre.
  * Adatok és a sorok írhatók/olvashatók: Azt jelzi, hogy mennyi adatokat vagy a sorok olvasása/írása történt, vagy szeretné olvasni.
  * Csúcspont olvassa el a hibák: Ismerteti, hogy hány csúcspontok vannak sikertelen volt az adatok olvasása közben.
  * Ismétlődő csúcs elvetése: Ha egy csúcspont fut túl lassú, a rendszer az azonos munkákat futtatása több csúcspontok előfordulhat, hogy ütemezése. Reductant csúcsokat a rendszer elveti, a rendszer egyik sikeres befejeződése után. Csúcspont ismétlődő elvetések rögzíti, amelyek a fázisban adatismétlődések, elvesznek a csúcspontok száma.
  * Csúcspont által okozott hibával találkozzanak: A csúcspont sikeres volt, de később első Újrafuttatás valamilyen oknál. Például alárendelt csúcspont elveszti a köztes bemeneti adatokat, ha rendszer rákérdez, a felsőbb rétegbeli csúcspont ismételt futtatásához használható.
  * Csúcspont ütemezés végrehajtás: A rendszer ütemezett teljes ideje.
  * Olvassa el a minimális és átlagos és maximális csúcspontok hibakeresésére vonatkozó adatok: A minimális és átlagos és maximális minden csúcspont adatokat olvasni.
  * Időtartam: A valós idő fázist vesz igénybe, be kell tölteni a profil, tekintse meg ezt az értéket.
  * Feladat visszajátszása
    
      A Data Lake Analytics feladatokat futtat, és archiválja az információ a feladatok, például a futó csúcspontok a csúcspontok indítása, leállítása, sikertelen volt, és hogyan azokat a rendszer újra próbálkozik, stb. Az adatok automatikusan bejelentkezve a lekérdezéstár és a feladat profilja tárolja. Letöltheti a feladat profil keresztül "Profil betöltése" feladat nézetben, és a feladat profil letöltése után megtekintheti a feladat visszajátszása.
    
      Feladat visszajátszása egy epitome képi megjelenítés, a fürt kimenetelét. Ez segít feladat végrehajtási folyamatának megtekintése és vizuálisan észlelheti a teljesítményanomáliákat és a szűk keresztmetszeteket nagyon rövid időn belül (kevesebb mint 30s általában).
  * Feladat Heat Map megjelenítése 
    
      Feladat Hőtérkép segítségével lehet kiválasztani a Feladatgrafikon megjelenített legördülő menüt. 
    
      ![Az Azure Data Lake Analytics feladat graph halommemória térkép megjelenítése](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Megmutatja az i/o-, idő- és átviteli sebesség hőtérkép egy feladat, amelyen keresztül található, ahol a feladat futtatásával töltött a legtöbbször, vagy a feladat egy i/o-határ feladat, és így tovább.
    
      ![Az Azure Data Lake Analytics feladat graph halommemória térkép példa](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Folyamatban van: A feladat-végrehajtási halad, információkat lásd a szakasz információkat.
    * Írt vagy olvasott adatok: Összes írt vagy olvasott adatok az egyes fázisokban hőtérképe.
    * Számítási idő: A hőtérkép egy SUM (minden a vertex végrehajtási idő), érdemes lehet ezt, hogy hogyan hosszú időbe Ha minden munkahelyi szakaszában csak 1 csúcspont az.
    * Csomópontonkénti átlagos végrehajtási idő: A hőtérkép egy SUM (minden a vertex végrehajtási idő) / (csúcspont szám). Ami azt jelenti, hogy a rendszer a párhuzamosság végrehajtása sikerült hozzárendelni, ha a teljes szakaszában végezheti el ezen az időn.
    * Bemeneti/kimeneti átviteli sebesség: A bemeneti és kimeneti átviteli sebesség és az egyes szintek hőtérkép, ellenőrizheti a Ha a feladat nem egy i/o kötött ezen feladat.
* Metaadat-műveletek
  
    Hajtsa végre a U-SQL-szkript néhány metaadat-műveletek, például hozzon létre egy adatbázist, dobja el a táblát stb. Ezek a műveletek fordítás után a metaadat-művelet látható. Előfordulhat, hogy keresse meg a helyességi feltételek, hozza létre entitásokat, húzza ide a entitásokat.
  
    ![Az Azure Data Lake Analytics Feladatnézetben metaadat-műveletek](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Állapotelőzmények
  
    Az állapot előzményeket is webalkalmazásban jelennek meg a feladat összegzése, de további részleteit itt kérheti le. Megtalálhatja a részletes információkat, mint például a feladat kész, queued elindított fut le. A projekt lefordítása hány alkalommal is talál (a CcsAttempts: 1.), amikor a feladat elküldése a fürthöz ténylegesen (részletek: Fürt feladat elküldése), stb.
  
    ![Az Azure Data Lake Analytics Feladatnézetben csomópontállapotok előzményei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnosztika
  
    Az eszköz automatikusan diagnosztizálja a feladat végrehajtása. Értesítést fog kapni, amikor bizonyos hibák vagy teljesítményproblémák a feladatok. Vegye figyelembe, hogy le kell töltenie a profilt az első teljes körű információkat itt. 
  
    ![Az Azure Data Lake Analytics Feladatnézetben diagnostics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Figyelmeztetés: Egy riasztás jelenik meg itt a figyelmeztetéssel. Részletesebb információkat tartalmaz, a riasztás megjelenése után az "x hiba/hibák meghatározásához" hivatkozásra is kattinthat.
  * A csúcspont futtatása túl hosszú: Ha minden csúcspont elfogy az idő (például: 5 óra), problémák itt található.
  * Erőforrás-használat: Ha mint kell lefoglalni további vagy nem elég párhuzamosság problémákat itt található. Is kattinthat a további részleteket, és végezze el a Lehetőségelemzési forgatókönyvek jobb erőforrás-elosztás található erőforrás-használat (További részletekért lásd a jelen útmutató).
  * Memória-ellenőrzés: Ha minden csúcspont 5 GB-nál több memóriát használ, akkor problémák itt található. Feladat végrehajtása előfordulhat, hogy első leállított rendszer, ha rendszerre vonatkozó korlátozás több memóriát használ.

## <a name="job-detail"></a>Feladat részletei
Feladat részletei a feladat, beleértve a parancsfájl, erőforrások és a Vertex végrehajtási nézet részletes információit jeleníti meg.

![Az Azure Data Lake Analytics-feladat részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Szkript
  
    A U-SQL parancsfájl a feladat a lekérdezéstár tárolja. Az eredeti U-SQL parancsfájl megtekintheti és újra elküldeni, ha szükséges.
* További források
  
    Megtalálhatja a fordítási feladatkimenetek-erőforrások használatával a lekérdezésben tárolja. Például "algebra.xml" a Feladatgrafikon megjelenítéséhez használt, a regisztrált szerelvényeket, stb. Itt is megtalálhatja.
* A vertex végrehajtási nézet
  
    Csúcspontok végrehajtási részleteit mutatja. A feladat profil archívumok minden a vertex végrehajtási napló, például a teljes írt vagy olvasott adatok, futásidejű, állam stb. Ebben a nézetben keresztül kaphat további részleteket hogyan egy feladatot futtatott. További információkért lásd: [a Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A vertex végrehajtási nézet használatához lásd: [a Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

