---
title: Feladat böngésző és a feladatok használja az Azure Data Lake Analytics-feladatok
description: Ez a cikk ismerteti az Azure Data Lake Analytics-feladatok feladat böngésző és feladatok segítségével.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 6d7ebc8d14e82281e074ff52abd41a16871470c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623230"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Használja feladat böngésző és az Azure Data Lake Analytics feladat nézetére
Az Azure Data Lake Analytics szolgáltatás archiválja az elküldött feladatok egy [lekérdezéstár](#query-store). Ebből a cikkből megismerheti, hogyan feladat böngésző és a feladatok használatát az Azure Data Lake Tools for Visual Studio korábbi adatok kereséséhez. 

Alapértelmezés szerint a Data Lake Analytics szolgáltatás archiválja a feladatok 30 napig. A lejárati időszak konfigurálhatja az Azure-portálon, a testre szabott elévülési szabályzatának konfigurálása. Pedig nem fogják tudni elérni a lejárat után a feladat adatait. 

## <a name="prerequisites"></a>Előfeltételek
Lásd: [Data Lake Tools for Visual Studio Előfeltételek](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Nyissa meg a feladat böngészőben
A feladat böngésző keresztül elérni **Server Explorer > Azure > a Data Lake Analytics > feladatok** a Visual Studióban.  A feladat böngészőben érheti el a lekérdezéstár egy Data Lake Analytics-fiók. Feladat böngésző megjeleníti a Lekérdezéstár a bal oldali alapvető feladatok adatait, valamint részletes feladatinformációkat feladat megjelenítése a megfelelő megjelenítő.

## <a name="job-view"></a>Feladatok megtekintése
Feladat megjelenítése a feladat a részletes leírását tartalmazza. Egy feladat megnyitásához kattintson duplán egy feladatot, a feladat böngészőben, vagy nyissa meg a Data Lake menüből feladat megtekintése gombra kattintva. Meg kell jelennie egy párbeszédpanelen, beírja a feladat URL-cím.

![A Data Lake Visual Studio-projekt böngésző eszközei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Feladat-nézet tartalmazza:

* Feladat összegzése
  
    Frissítse a feladatok nézetet a futó feladatok a legfrissebb információk megjelenítéséhez.
  
  * Feladat állapota (diagramhoz):
    
      Feladat állapota a Projekt fázisok ismerteti:
    
      ![Az Azure Data Lake Analytics-feladat fázisok állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Előkészítése: A parancsfájl töltse fel a felhőbe, fordítása, és a parancsfájl a fordítási szolgáltatás használatakor optimalizálására.
    * Várólistára helyezett: Feladatok várólistára elegendő erőforrást vár, vagy a feladatok haladhatja meg egy fiókot a korlátozás a maximális egyidejűleg futó feladatainak. A prioritási beállítás határozza meg az aszinkron feladatok - minél alacsonyabb a szám, annál magasabb a prioritás.
    * Futó: A feladat ténylegesen fut a Data Lake Analytics-fiók.
    * Véglegesítése: A feladat befejeződik (például a fájl véglegesítése).
      
      A feladat minden fázisban sikertelen lehet. Például fordítási hibákat a előkészítése fázis, a várakozik fázisban időtúllépést és végrehajtási hibák a futó fázis stb.
  * Alapvető adatok
    
      Az alapvető feladatok adatait jeleníti meg a feladat összegzése panel alsó részén.
    
      ![Az Azure Data Lake Analytics-feladat fázisok állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Feladat eredmény: Sikeres vagy sikertelen volt. A feladat minden fázisban sikertelenek lehetnek.
    * Teljes időtartam: Fali idő (időtartam) elküldésekor időt és befejezési időpont között.
    * Számítási teljes időtartam: Minden csomópont-végrehajtási idő összege, érdemes lehet azt, hogy a feladat végrehajtása a csak egy-egy csúcsának idő. Tekintse meg a teljes csúcsban található a csúcspont további információt.
    * Küldje el/kezdő/záró idő: Ha a Data Lake Analytics szolgáltatás megkapja a feladat elküldése/elkezdte a feladat végpontok a feladat futtatása sikeresen vagy sikertelenül ideje.
    * Fordítási/várakozik/fut: Valós idő telik előkészítése/várakozik/fut fázis során.
    * Fiók: Az a feladat futtatásához használt Data Lake Analytics-fiók.
    * Szerző: A felhasználó a feladatot egy tényleges személy vagy a system fiók lehet.
    * Prioritás: A feladat prioritása. Az alacsonyabb a szám, annál magasabb a prioritás. Csak a sorban lévő feladatok sorozatát van hatással. A magasabb prioritású virtuális gép beállítása nem futó feladatok megelőzik az.
    * Párhuzamossági: A kért maximális száma párhuzamos Azure Data Lake Analytics egységek (ADLAUs), más néven csúcsban. Jelenleg egy-egy csúcsának megegyezik egy virtuális gép, és két virtuális core és 6 GB RAM, bár ez sikerült frissíteni a jövőben a Data Lake Analytics frissíti.
    * Bal oldali bájt: Bájtok kell feldolgozni, amíg a feladat befejeződik.
    * Olvasás/írt bájtok: bájt lett olvasása/írása a feladat indulása óta.
    * Teljes csúcsban: A feladat van felosztva hány darab munka, minden egyes munkákat csúcspont nevezik. Ez az érték a feladat áll hány darab munka ismerteti. Csúcspont egységként folyamat alapvetően, más néven Azure Data Lake Analytics egység (ADLAU), érdemes lehet, és a párhuzamosság csúcsban is futtatható. 
    * Befejeződött/futó/nem sikerült: A csúcsban befejeződött/futó/sikertelen száma. Csúcsban mindkét felhasználói kód és a rendszer hibák miatt sikertelen lehet, de a rendszer újrapróbálkozások sikertelenek csúcsban automatikusan néhány alkalommal. Ha a csúcspont továbbra is sikertelen újrapróbálkozás után, a teljes feladat sikertelen lesz.
* A Feladatgrafikon
  
    A U-SQL parancsfájl kimeneti adatok bemeneti adatok átalakítása logikájának jelöli. A parancsprogram fordítása és optimalizálták, hogy a fizikai végrehajtási terv a előkészítése fázisában. A Feladatgrafikon-hoz a fizikai végrehajtási terv megjelenítése.  A következő ábra illusztrálja a folyamatot:
  
    ![Az Azure Data Lake Analytics-feladat fázisok állapota](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Egy feladat hány darab munka van felosztva. Minden egyes munkákat csúcspont nevezik. A csúcsban Super csúcspont (más néven előkészítés) szerint csoportosítva, és mint Feladatgrafikon ábrázolt. A feladat Graph a zöld szakasz felirat megjelenítése a szakaszt.
  
    Minden csomópont egy szakaszban ugyanezeket az azonos adatok használata a műveletet. Például ha egy TB adatot tartalmazó fájlt, és azt olvasásakor csúcsban több száz, azok éppen olvas a rendszer. Ezek a csúcsban az azonos szakaszt és ezzel azonos működnek a különböző részeinek ugyanazon bemeneti fájl vannak csoportosítva.
  
  * <a name="state-information"></a>Szakasz információkat
    
      Egy adott szakaszban számok jelennek meg a metaadattábla.
    
      ![Azure Data Lake Analytics feladat graph szakasz](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Bontsa ki: egy-egy számot, és a műveleti metódusának nevű szakaszban nevét.
    * 84 csúcsban: Ebben a szakaszban csúcsban teljes száma. Az ábra azt jelzi, hogy hány darab munka oszlik meg ebben a szakaszban.
    * 12.90 s/csúcspont: A csomópont átlagos végrehajtási idő ebben a szakaszban. Ez a szám összege (minden csomópontra végrehajtási idő) kiszámítása / (teljes csúcspont száma). Tehát ha párhuzamossági végrehajtott összes csúcsban rendeli, a teljes szakasza befejeződött 12.90 s. Azt is jelenti, ha ebben a szakaszban a munkát a Feladattervek történik, a költség lenne #vertices * átlagos idő.
    * írt 850,895 sorok: Ebben a szakaszban írt sorainak száma összesen.
    * R/W: adatmennyiség bájtban ebben a szakaszban olvasható/Written.
    * Színek: Színek segítségével szakaszában másik csomópont állapotát jelzi.
      
      * Zöld állapot azt jelzi, hogy a csúcspont van sikeres volt.
      * Narancssárga azt jelzi, hogy a csomópont a rendszer ismét megkísérli. A rendszer csomópontra nem sikerült, de a rendszer ismét megkísérli automatikusan és sikeresen a rendszer, és a teljes fázis sikeresen befejeződött. Ha a csomópont újra megpróbálja, de továbbra is sikertelen, a szín bekapcsolása piros és a teljes feladat sikertelen volt.
      * Vörös állapot azt jelzi sikertelen, ami azt jelenti, hogy egy bizonyos csúcspont volt néhány alkalommal újra a rendszer azonban továbbra is sikertelen volt. Ebben a forgatókönyvben azt eredményezi, a teljes feladat sikertelen lesz.
      * Kék azt jelenti, hogy egy bizonyos csúcspont fut-e.
      * Fehér jelzi a csomópont-jére vár. A csúcspont vár ütemezhető, miután egy ADLAU elérhetővé válik, vagy az lehet, hogy lehet vár, mert a bemeneti adatok nem áll készen.
      
      További részletekért megtalálhatja a szakaszában az egérmutatót egy állapot szerint rámutató:
      
      ![Az Azure Data Lake Analytics-feladat graph szakasz részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Csúcsban: Ismerteti a csúcsban részleteit, például összesen hány csúcsban készültek el, hogy hány csúcsban-e a sikertelen volt, vagy még fut/Várakozás stb.
  * Adatok olvasása határokon/belüli pod: fájlokat és adatokat tárolódnak több három munkaállomás-csoporttal elosztott fájlrendszerben. Az itt szereplő érték ismerteti azonos fogyasztanak vagy közötti pod beolvasva mennyi adatot.
  * Teljes compute idő: minden csomópont-végrehajtási idő szakaszában összege, érdemes lehet azt, csak egy-egy csúcsának a végrehajtása a időbe telne ha összes szakaszában működik.
  * Adatok és a sorok írhatók/olvashatók: azt jelzi, mennyi adat- vagy sor olvasása/írása, vagy olvassa el kell.
  * Csúcspont olvasása sikertelen: ismerteti, hogy hány csúcsban adatolvasási során nem sikerült.
  * Ismétlődő csúcspont elveti: csúcspont működése túl lassú, ha a rendszer több csúcsban az azonos munkákat futtatásra is ütemezheti. Reductant csúcsban elvesznek, amennyiben az egyik a csúcsban sikeresen befejeződik. Csúcspont ismétlődő elvetések lévő adatismétlődések, elveti a szakaszban számát rögzíti.
  * Csomópont által okozott hibával találkozzanak: A csúcspont sikeres volt, de az beszerzése futtassa újra a később valamilyen oknál. Például ha az alárendelt csomópont köztes bemeneti adatok megszakad, a program kéri a felsőbb rétegbeli csomópontra kattintva futtassa újra.
  * Ütemezés végrehajtások csúcspont: a csúcsban ütemezett fordított időt.
  * Minimális és átlagos/maximális csúcspont beolvasott adatok: A minimális/átlagos/maximális minden csomópont-adatok olvasása.
  * Időtartam: A valós idő fázist időt vesz igénybe, be kell tölteni a profil megtekintéséhez ezt az értéket.
  * Feladat visszajátszása
    
      A Data Lake Analytics feladatok fut, és az információk a feladatok, például a futtató csúcspont archiválja a csúcsban indítása, leállítása sikertelen volt, és hogyan azokat újra vannak, stb. Minden olyan információt, automatikusan jelentkezett be a lekérdezéstárat és a rendszer a feladat profilban. Letöltheti a feladat profilhoz "Profil betöltése" feladat nézetben, és a feladat visszajátszása tekintheti meg a feladat profil letöltése után.
    
      Feladat visszajátszása egy epitome képi megjelenítés, mi történt a fürtben. Segít tekintse meg a feladat végrehajtásának menetét, és vizuálisan észlelheti a teljesítményanomáliákat és a szűk keresztmetszeteket nagyon rövid időn belül (kevesebb mint 30s általában).
  * Feladat tűz térkép megjelenítési 
    
      A Feladatgrafikon megjelenítési legördülő keresztül feladat Hőtérkép választhatók ki. 
    
      ![Azure Data Lake Analytics feladat graph halommemória térkép megjelenítési](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Azt mutatja, hogy az i/o, időpont és átviteli hőtérkép feladat, amelyen keresztül található, ahol a feladat túlnyomó az idő, vagy a feladatot egy i/o-határ feladat, és így tovább.
    
      ![Azure Data Lake Analytics feladat graph halommemória térkép – példa](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Folyamatban van: A feladat végrehajtása előrehaladás, az információ [szakasz információkat](#stage-information).
    * Az adatok olvasása/írása: A hőtérkép teljes adatok az egyes fázisokban olvasása/írása.
    * Számítási idő: SUM (minden csomópontra végrehajtási idő) hőtérkép, érdemes lehet ez, hogyan hosszú időt vesz igénybe Ha szakaszában minden munkahelyi csak 1 csúcspont végrehajtásához.
    * Csomópontonkénti átlagos végrehajtási idő: A hőtérkép Sum (minden csomópontra végrehajtási idő) / (csúcspont számát). Ami azt jelenti, hogy párhuzamossági végrehajtott összes csúcsban rendelheti, ha a teljes szakasza a időkereten belül megtörténik.
    * Bemeneti/kimeneti átviteli sebesség: bemeneti/kimeneti sebességét, minden szakasza hőtérkép, ellenőrizheti a feladat esetén egy i/o-ezen kötött feladat.
* Metaadat-művelet
  
    Néhány metaadatok műveleteinek elvégzéséhez a U-SQL parancsfájlt, mint például az adatbázis létrehozása, dobja el a tábla, stb. Ezek a műveletek fordítás után jelennek meg a metaadat-művelet. Akkor lehet, hogy keresés helyességi feltételek, entitásokat hozhatnak létre, húzza ide a entitások.
  
    ![Az Azure Data Lake Analytics feladat megtekintése metaadat-művelet](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Állapotelőzmények
  
    A State History is formájában jelenik meg a feladat összegzése, de a további részletekért itt. A részletes információk található, mint például a feladat kész, várakoznia, elindított fut le. Is található a feladat le van fordítva hány alkalommal (a CcsAttempts: 1), ha a feladatot indít a fürt ténylegesen (a részletes: fürt feladat terjesztéséhez) stb.
  
    ![Az Azure Data Lake Analytics feladat megtekintése állapot előzményeinek](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnosztika
  
    Az eszköz automatikusan diagnosztizálja a feladat végrehajtása. Riasztásokat fog kapni, amikor az egyes hibák vagy teljesítményproblémák a feladatok. Vegye figyelembe, hogy le kell töltenie a profil teljes körű információkat itt eléréséhez. 
  
    ![Az Azure Data Lake Analytics feladat megtekintése diagnosztika](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Figyelmeztetés: Riasztást jeleníti meg itt a fordító figyelmeztetést jelenít meg. Szeretné, hogy további részleteket a riasztás megjelenése után "x hiba/hibák" hivatkozásra kattintva.
  * Futtassa a túl hosszú csúcspont: Ha bármilyen csúcspont elfogy idő (azaz 5 óra), problémák itt található.
  * Erőforrás-használat: Ha mint kell lefoglalni további vagy nem elég párhuzamossági problémák itt található. Is kattinthat a további részleteket, és hajtsa végre a lehetőségelemzések forgatókönyvek jobb erőforrás-elosztás található erőforrás-használat (További részletekért lásd a jelen útmutató).
  * Memória ellenőrzése: Ha bármely csúcspont 5 GB-nál több memóriát használ, problémák itt található. Feladat végrehajtását is beolvasása következtében leállt rendszer rendszerre vonatkozó korlátozás-nál több memóriát használ.

## <a name="job-detail"></a>Feladat részletei
Feladat részletei a feladat, beleértve a parancsfájl, erőforrások és a Vertex végrehajtási nézetet a részletes leírását tartalmazza.

![Azure Data Lake Analytics-feladat részletei](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Szkript
  
    A feladat a U-SQL parancsfájl tárolása a lekérdezést. Az eredeti U-SQL parancsfájl megtekintése, és küldje el újra, ha szükséges.
* További források
  
    A feladat fordítási kimenetek erőforrások révén a lekérdezés tárolóban található. Például található "algebra.xml" megjelenítése a feladat ábra szolgáló, a regisztrált szerelvényeket, itt stb.
* Vertex végrehajtási nézetet
  
    Csúcsban végrehajtási részleteit mutatja. A feladat profil archiválja minden csúcspont végrehajtási napló, például az összes adat olvasása/írása, runtime, állapot, stb. Ebben a nézetben keresztül kaphat további részleteket a módját a feladat futott-e. További információkért lásd: [a Vertex végrehajtási nézetet használja a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Az Azure Data Lake Analytics diagnosztikai naplóinak elérése).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Vertex végrehajtási nézetet használ, tekintse meg [a Vertex végrehajtási nézetet használja a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

