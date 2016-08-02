<properties
    pageTitle="Azure Machine Learning GYIK | Microsoft Azure"
    description="Azure Machine Learning bevezetés: a zökkenőmentes prediktív modellezést támogató felhőalapú szolgáltatással kapcsolatos számlázásra, képességekre és korlátozásokra vonatkozó GYIK."
    keywords="machine learning introduction,predictive modeling,what is machine learning"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/18/2016"
    ms.author="garye"/>

# Az Azure Machine Learning szolgáltatásra vonatkozó gyakori kérdések (GYIK): Számlázás, képességek, korlátozások és támogatás

Ez a GYIK az Azure Machine Learning szolgáltatással kapcsolatos kérdésekre ad választ, amely egy, a webszolgáltatásokon keresztül végrehajtott prediktív modellezést és a megoldások üzembe helyezését célzó felhőalapú szolgáltatás. Ez a GYIK a szolgáltatás használatával kapcsolatos kérdéseket fedi le, beleértve a számlázási modell, a képességek, a korlátozások és a támogatás témaköreit.

## Általános kérdések

**Mi az Azure Machine Learning?**

Az Azure Machine Learning egy olyan teljes körűen felügyelt szolgáltatás, amelynek segítségével megtervezheti, tesztelheti, üzembe helyezheti és felügyelheti a felhőalapú prediktív elemzési megoldásokat. Csupán egy böngésző szükségez ahhoz, hogy bejelentkezzen, feltöltse adatait, és azonnal nekiláthasson a gépi tanulási kísérletekhez. Az áthúzással működtethető prediktív modellezés, a modulok széles skálájának és a kezdősablonok gyűjteményének segítségével egyszerűen és gyorsan elvégezhetők az általános gépi tanulási feladatok.  További információk: [Az Azure Machine Learning szolgáltatás áttekintése](https://azure.microsoft.com/services/machine-learning/). A gépi tanulás fontosabb szakkifejezéseit és alapfogalmait tartalmazó bevezetéshez olvassa el a [Bevezetés az Azure Machine Learning használatához](machine-learning-what-is-machine-learning.md) című fejezetet.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Mi a Machine Learning Studio?**

A Machine Learning Studio egy webböngészőn keresztül elérhető munkaterület környezet.  A Machine Learning Studio vizuális-összeállítási interfészekkel rendelkező modulok sorát tartalmazza, amelyek lehetővé teszik, hogy egy teljes körű adatelemezési munkafolyamatot építsen egy kísérlet formájában.

A Machine Learning Studióval kapcsolatos további információkért olvassa el a [Mi a Machine Learning Studio](machine-learning-what-is-ml-studio.md) című fejezetet.

**Mi a Machine Learning API szolgáltatás?**

A Machine Learning API szolgáltatás segítségével méretezhető, hibatűrő webszolgáltatásokként helyezhet üzembe prediktív modelleket, mint amilyenek a Machine Learning Studióba vannak beépítve. A Machine Learning API szolgáltatás által létrehozott webszolgáltatások olyan REST API-k, amelyek egy, a külső alkalmazások és az Ön prediktív elemzési modelljei közötti kommunikációt lehetővé tevő interfészt biztosítanak.

További információ: [Csatlakozás egy Machine Learning webszolgáltatáshoz](machine-learning-connect-to-azure-machine-learning-web-service.md).


## Számlázással kapcsolatos kérdések

**Milyen díjszámítási módszer vonatkozik a Machine Learning szolgáltatásra?**

A számlázással és a díjszabással kapcsolatos információkért olvassa el a [Machine Learning díjszabás](https://azure.microsoft.com/pricing/details/machine-learning/) című fejezetet.

**Létezik a Machine Learningnek ingyenes próbaverziója?**

 Az Azure ingyenes próbaverziójára történő regisztrációt követően bármely Azure-szolgáltatást kipróbálhatja egy hónapig. Ha többet szeretne tudni az Azure ingyenes próbaverziójáról, olvassa el az [Azure Ingyenes próbaverziójára vonatkozó GYIK-et](/pricing/free-trial-faq/).

## A Machine Learning Studióra vonatkozó kérdések

### Kísérlet létrehozása

**Létezik verziókövetés vagy Git integráció a kísérleti diagramok esetében?**

Nem, a Machine Learning Studio azonban megőriz minden egyes megismételt kísérletet, amelyek más felhasználók által nem módosíthatóak.
További információ: [Kísérletismétlések kezelése a Machine Learning Studióban](machine-learning-manage-experiment-iterations.md).

### Adatok importálása és exportálása a Machine Learning szolgáltatáshoz

**Milyen adatforrásokat támogat a Machine Learning?**

A Machine Learning Studióban végrehajtott kísérletekhez a következő három módon lehet adatokat betölteni: helyi fájlok adatkészletként való feltöltésével, felhőalapú adatszolgáltatásokból származó adatok importálásával egy modul segítségével vagy egy másik kísérlet során elmentett adatkészlet importálásával. További információ a támogatott fájlformátumokról: [Betanítási adatok betöltése a Machine Learning Studióba](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Mekkora adatkészleteket kezelhetek a moduljaimmal?

A Machine Learning Studióban található modulok 10 GB-nyi számadatot tartalmazó adatkészletet támogatnak a gyakori alkalmazási esetekben. Ha egy modul egynél több bemenetből fogad adatokat, az összes bemenet mérete nem haladhatja meg a 10 GB-ot. A nagyobb adatkészletekből a Hive vagy az Azure SQL Database lekérdezések, illetve az adatfeldolgozást megelőzően a Learning by Counts előzetes feldolgozás segítségével lehet mintát venni.  

A szolgáltatásnormalizálás során a következő, 10 GB alá korlátozott adattípusok bővíthetők nagyobb adatkészletekké:

- Ritka
- Kategorikus
- Karakterláncok
- Bináris adatok

A következő modulok 10 GB-nál kisebb adatkészletekre vannak korlátozva:

- Ajánló modulok
- SMOTE modul
- Parancsfájlkezelési modulok: R, Python, SQL
- Olyan modulok, amelyeknél a kimeneti adatok mérete meghaladhatja a bemeneti adatok méretét; például az egyesítés vagy a szolgáltatáskivonatolás.
- Kereszt-ellenőrzés, modell-hiperparaméterek beállítása, sorszámregresszió és multi-osztályú osztályozás nagyszámú ismétlés esetében.

A néhány GB-nál nagyobb adatkészletek esetében a helyi fájlokból való közvetlen feltöltés helyett tanácsos az adatokat az Azure-tárolóba, illetve az Azure SQL Database-be feltölteni, vagy HDInsight-ot használni erre a célra.


####<a id="UploadLimit"></a>Mik az adatfeltöltés korlátai?
A néhány GB-nál nagyobb adatkészletek esetében a helyi fájlokból való közvetlen feltöltés helyett tanácsos az adatokat az Azure-tárolóba, illetve az Azure SQL Database-be feltöltenie, vagy HDInsightot használnia erre a célra.

**Az Amazon S3-ból is lehetséges az adatbeolvasás?**

Amennyiben kisebb mennyiségű adatot szeretne közzétenni egy http URL-en keresztül, használhatja az [Adatok importálása][adatimportálás] modult. Nagyobb mennyiségű adat esetében először vigye át az adatokat az Azure Storage-ba, majd használja az [Adatok importálása][adatimportálás]modult az adatok kísérletbe való beviteléhez.
<!--
<SEE CLOUD DS PROCESS>
-->

**Létezik beépített képbeviteli képesség?**

A képbeviteli képességről többet is megtudhat a [Képek importálása][képolvasó] útmutatóban.

### Modulok

**Az általam keresett algoritmus, adatforrás, adatformátum vagy adat-átalakítási művelet nem található az Azure Machine Learning Studióban. Milyen lehetőségeim vannak?**

Az általunk követett funkciókéréseket a [felhasználó-visszajelzési fórumot](http://go.microsoft.com/fwlink/?LinkId=404231) meglátogatva tekintheti meg. Amennyiben a keresett képességet már kérelmezték, adja hozzá szavazatát a már meglévő kérelemhez. Ha a keresett képesség még nem létezik, hozzon létre egy új kérelmet. Kérelmének állapotát ezen a fórumon tekintheti meg. A listát folyamatosan követjük, és rendszeresen frissítjük a funkciók rendelkezésre állásának állapotát. Továbbá, a beépített R és Python támogatással szükség esetén egyénre szabott átalakítások is létrehozhatóak.


**Hozzáadhatom a már létező kódomat a Machine Learning Studióhoz?**

Igen, hozzáadhatja a már meglévő R vagy Python kódját a Machine Learning Studióhoz, lefuttathatja azt az Azure Machine Learning tanulókkal együtt ugyanazon kísérletben, és az Azure Machine Learningen keresztül webszolgáltatásként is üzembe helyezheti a megoldást. További információk: [Kísérletének kiterjesztése az R nyelv használatával](machine-learning-extend-your-experiment-with-r.md), valamint [A Python gépi tanulási parancsfájlok végrehajtása az Azure Machine Learning Studióban](machine-learning-execute-python-scripts.md).

**Lehetséges egy [PMML-hez](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) hasonló dolgot használni a modellek meghatározásához?**

Nem, ez nem támogatott, az egyéni R és Python kódok azonban használhatók a modulok meghatározásához.

**Hány modult hajthatok végre párhuzamosan a kísérletemben?**  

Egy kísérletben legfeljebb 4 modult hajthat végre párhuzamosan.


### Adatfeldolgozás

**Van olyan képesség (az R megjelenítéseken kívül), amellyel lehetséges az adatok interaktív megjelenítése egy kísérleten belül?**

A modul kimenetére kattintva megjelenítheti az adatokat és megtekintheti a statisztikákat.

**Az eredmények és az adatok áttekintésekor a sorok és oszlopok száma korlátozott a böngészőben. Miért?**

Mivel potenciálisan nagy mennyiségű adat átvitele zajlik a böngészőbe, a Machine Learning Studio lelassításának elkerülése érdekében korlátozott az adatok mérete. Az összes adat/eredmény megjelenítéséhez célszerű letölteni az adatokat, és Excelt vagy hasonló eszközt használni erre a célra.

### Algoritmusok

**Mely létező algoritmusokat támogatja a Machine Learning Studio?**

A Machine Learning Studio a legkorszerűbb algoritmusokat biztosítja, többek között például a továbbfejlesztett méretezhető döntési fákat, a Bayes ajánlási rendszereket, a neurális hálózatokat és a Microsoft Research által fejlesztett Decision Jungle algoritmust. Az olyan méretezhető, nyílt forráskódú gépi tanulási csomagokat, mint a Vowpal Wabbit, szintén támogatja a Machine Learning Studio. A Machine Learning Studio támogatja a multiclass és bináris osztályzásra, a regresszióra és a fürtszolgáltatásra használt gépi tanulási algoritmusokat. Tekintse meg a [Machine Learning-modulok][gépi tanulási modulok] teljes listáját.

**Automatikusan az adataim alapján legmegfelelőbbnek vélt Machine Learning algoritmus használatát ajánlják?**

Nem, több módszer is létezik azonban a Machine Learning Studióban az egyes algoritmusok eredményeinek összehasonlítására, és így az Ön problémájának megoldására legmegfelelőbb algoritmus meghatározására.

**Létezik bármiféle irányelv, amely alapján eldönthető, melyiket válasszuk az elérhető algoritmusok listájából?**
Lásd: [Hogyan válasszunk algoritmust ](machine-learning-algorithm-choice.md).

**R vagy Python nyelven írták az elérhető algoritmusokat?**

Egyik sem. Ezeket az algoritmusokat a magasabb teljesítmény érdekében lefordított nyelveken írták.

**Elérhető az algoritmusokkal kapcsolatban részletes tájékoztató?**

A dokumentáció tartalmaz némi információt az algoritmusokról, a finomhangoláshoz megadott paramétereket pedig az algoritmus optimalizált használata érdekében ismerteti.  

**Létezik online tanulási támogatás?**

Nem, jelenleg csak a szoftveres átképezés támogatott.

**Megjeleníthetők a Neural Net Modell (Neurális háló modell) rétegei a beépített modul segítségével?**

Nem.

**Létrehozhatom a saját moduljaimat C# vagy más nyelvet használva?**

Jelenleg új, egyéni modulokat csak R nyelven lehet létrehozni.

### R modul

**Milyen R csomagok érhetők el a Machine Learning Studióban?**

A Machine Learning Studio jelenleg a 400+ CRAN R csomagokat támogatja, itt megtalálja az összes elérhető csomag [jelenlegi listáját](http://az754797.vo.msecnd.net/docs/RPackages.xlsx). A lista önálló lekérését a [Kísérlet kiterjesztése az R nyelv használatával](machine-learning-extend-your-experiment-with-r.md) című fejezetben sajátíthatja el. Ha nem találja a listában a szükséges csomagot, adja meg a csomag nevét a [felhasználó-visszajelzési fórumon](http://go.microsoft.com/fwlink/?LinkId=404231).

**Létre lehet hozni egy egyéni R modult?**

Igen, ehhez az [Egyéni R modul létrehozása az Azure Machine Learningben](machine-learning-custom-r-modules.md) című fejezetben talál további információt.

**Létezik REPL környezet az R nyelvhez?**

Nem létezik REPL környezet az R nyelvhez a stúdióban.

### Python modul

**Létre lehet hozni egy egyéni Python modult?**

Jelenleg nem, de a [Python-parancsfájl végrehajtására][python] szolgáló modulok használatával elérhető ugyanaz az eredmény.

**Létezik REPL környezet a Python nyelvhez?**

A Jupyter notebookok használata lehetséges a Machine Learning Studióban. További információ: [Jupyter notebookok használatának bemutatása az Azure Machine Learning Studióban] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## Webszolgáltatás

###Szoftveres modellátképezés

**Hogyan működik az Azure Machine Learning modellek szoftveres átképezése?**

A megőrzési API-k használatával. További információk: [Machine Learning-modellek szoftveres átképezése](machine-learning-retrain-models-programmatically.md). A [Microsoft Azure Machine Learning átképezési bemutatóban](https://azuremlretrain.codeplex.com/) egy mintakód is elérhető.

### Létrehozás

**Helyileg vagy internetkapcsolat nélküli alkalmazással is üzembe helyezhetem a modellt?**

Nem.


**Minden webszolgáltatás esetében számolni kell egy alapkésleltetéssel?**

Tekintse meg az [Azure előfizetésre vonatkozó korlátozások](../azure-subscription-service-limits.md) című fejezetet.

### Használat

**Milyen esetekben futtassam a prediktív modellemet kötegelt végrehajtási szolgáltatásként kérelem-válasz szolgáltatás helyett?**

Az RRS egy rövid késleltetésű, jelentősen méretezhető webszolgáltatás, amely olyan állapot nélküli modellekhez biztosít felületet, amelyeket a kísérleti környezetben hoztak létre és helyeztek üzembe. A kötegelt végrehajtási szolgáltatás (BES) egy, az adatrekordkötegek aszinkron módon történő pontozását végző szolgáltatás. A BES bemeneti adatai az RRS-ben használt bemeneti adatokhoz hasonlóak. A fő különbség az, hogy a BES több olyan különböző forrásból olvassa be az adatköteget, mint például az Azure-beli Blob és Table szolgáltatások, az Azure SQL Database, a HDInsight (hive lekérdezés) és a HTTP-források. További információ: [A Machine Learning webszolgáltatásainak használata](machine-learning-consume-web-services.md).

**Hogyan frissíthetem az üzembe helyezett webszolgáltatáshoz használt modellt?**

A prediktív modellek egy, már üzembe helyezett szolgáltatáshoz egyszerűen frissíthetők a létrehozott kísérlet módosításával és újrafuttatásával, majd a betanított modell elmentésével. Miután a betanított modell egy újabb verziója elérhetővé válik, a Machine Learning Studio rákérdez, hogy szeretné-e frissíteni webszolgáltatását. Egy, már üzembe helyezett webszolgáltatás frissítésével kapcsolatos információkért olvassa el [A Machine Learning webszolgáltatások üzembe helyezése](machine-learning-publish-a-machine-learning-web-service.md) című fejezetet.

Továbbá használhatja az átképezési API-kat is.
További információk: [Machine Learning-modellek szoftveres átképezése](machine-learning-retrain-models-programmatically.md). A [Microsoft Azure Machine Learning átképezési bemutatóban](https://azuremlretrain.codeplex.com/) egy mintakód is elérhető.

**Hogyan követhetem figyelemmel az éles környezetben telepített webszolgáltatásaimat?**

A telepítés után a klasszikus Azure portálon keresztül figyelheti a prediktív modellt. Minden telepített szolgáltatásnak van egy saját irányítópultja, ahol az adott szolgáltatásra vonatkozó információkat láthatja.

**Láthatom valahol az RRS/BES elemeim kimenetét?**

Az RRS esetében általában a webszolgáltatási válaszoknál láthatja az eredményt. Emellett az Azure blob-tárolóba is kiírhatja azokat.  A BES esetében a kimenet alapértelmezetten egy blobba íródik. Az [Adatok exportálása][adatexportálás] modult használva egy adatbázisba vagy egy táblázatba is kiírhatja a kimenetet.

**Lehetséges kizárólag a Machine Learning Studióban létrehozott modelleket használva webszolgáltatást készíteni?**

Nem lehetséges, azonban közvetlenül a Jupyter notebookokból és az RStudióból is létrehozhat webszolgáltatásokat.

**Hol találok információkat a hibakódokkal kapcsolatban?**

A hibakódok listáját és azok leírását a [Machine Learning modulok hibakódjai](https://msdn.microsoft.com/library/azure/dn905910.aspx) című fejezetben találja.

## Méretezhetőség

**Milyen a webszolgáltatások méretezhetősége?**

Jelenleg az alapértelmezett végpont 20 egyidejű RRS-kérelemmel rendelkezik végpontonként. Ezt végpontonként 200 egyidejű kérelemre is méretezheti, valamint az [API-végpontok méretezése](machine-learning-scaling-endpoints.md) alapján minden webszolgáltatást 10 000 végpontra méretezhet webszolgáltatásonként. A BES esetében minden végpont 40 kérelem feldolgozását engedélyezi egyidejűleg, illetve, amennyiben a kérelmek száma meghaladja a 40-et, az új kérelmek várólistára kerülnek. A várólistára került kérelmeket automatikusan futtatja a rendszer, amint a lista elkezd kiürülni.


**Az R-munkák csomópontokon vannak elosztva?**

Nem.  


**Mekkora mennyiségű adatot használhatok a betanításhoz?**

A Machine Learning Studióban található modulok 10 GB-nyi számadatot tartalmazó adatkészletet támogatnak a gyakori alkalmazási esetekben. Ha egy modul egynél több bemenetből fogad adatokat, az összes bemenet mérete nem haladhatja meg a 10 GB-ot. A nagyobb adatkészletekből a Hive vagy az Azure SQL Database lekérdezések, illetve az adatfeldolgozást megelőzően a [statisztikai tanulási (Learning with Counts)][counts] modulokkal végrehajtott előzetes feldolgozás segítségével lehet mintát venni.  

A szolgáltatásnormalizálás során a következő, 10 GB alá korlátozott adattípusok bővíthetők nagyobb adatkészletekké:

- ritka
- kategorikus
- karakterláncok
- bináris adatok

A következő modulok 10 GB-nál kisebb adatkészletekre vannak korlátozva:

- Ajánló modulok
- SMOTE modul
- Parancsfájlkezelési modulok: R, Python, SQL
- Olyan modulok, amelyeknél a kimeneti adatok mérete meghaladhatja a bemeneti adatok méretét; például az egyesítés vagy a szolgáltatáskivonatolás.
- Nagyszámú ismétlődés esetében végezzen kereszt-ellenőrzést, állítsa be a modell-hiperparamétereket, továbbá végezzen sorszám-regressziót és multi-osztályú osztályozási műveletet.

A néhány GB-nál nagyobb adatkészletek esetében a helyi fájlból való közvetlen feltöltés helyett tanácsos az adatokat az Azure-tárolóba, illetve az Azure SQL Database-be feltölteni, vagy a HDInsight rendszert használni erre a célra.


**Létezik bármilyen korlátozás a vektorok méretét illetően?**

A sorokra és oszlopokra mind a Maximális Int .NET korlátozás vonatkozik: 2, 147, 483, 647.

**Módosítható a webszolgáltatások futtatásához használt virtuális gép mérete?**

Nem.  

## Biztonság és elérhetőség

**Alapértelmezés szerint kiknek van hozzáférése a webszolgáltatások végpontjához? Hogyan korlátozhatom a végponthoz való hozzáférést?**

A webszolgáltatások telepítése után egy alapértelmezett végpont kerül létrehozásra az adott szolgáltatáshoz. Az alapértelmezett végpont az API-kulcs használatával hívható meg. Saját kulcsukat használva további végpontok hozzáadása is lehetséges a klasszikus Azure portálon keresztül, vagy a webszolgáltatás-kezelési API-k szoftveres használatával. A webszolgáltatásokhoz való meghíváshoz hívóbetűk szükségesek. További információ: [Csatlakozás egy Machine Learning webszolgáltatáshoz](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Mit kell tenni, ha nem találom az Azure Storage-fiókom?**

A munkafolyamat végrehajtása során a Machine Learning Studio a felhasználók által megadott Azure Storage-fiók segítségével menti a köztes adatokat. Ez a tárfiók egy munkaterület létrehozása során kerül a Machine Learning Studióba. Ha a munkaterület létrehozását követően a tárfiókot törlik és többé nem lesz elérhető, a munkaterület működése minden benne futó kísérlettel együtt leáll.

Ha véletlenül törli a tárfiókot, a helyreállítás egyetlen módja, ha a törölt tárfiókkal megegyező régióban ugyanazzal a névvel létrehoz egy új tárfiókot. Ezt követően szinkronizálja újra a hívóbetűt.


**Mi történik, ha a tárfiókom hívóbetűje nincs szinkronizálva?**

A munkafolyamat végrehajtása során a Machine Learning Studio a felhasználók által megadott Azure Storage-fiók segítségével menti a köztes adatokat. Ez a tárfiók egy munkaterület létrehozása során kerül a Machine Learning Studióba. és a hívóbetűk ehhez a munkaterülethez lesznek társítva. Ha a munkaterület létrehozását követően módosítják a hívóbetűket, a munkaterület többé nem lesz képes hozzáférni a tárfiókhoz, valamint működése minden benne futó kísérlettel együtt leáll.

Ha módosította a tárfiók hívóbetűjét, a klasszikus Azure portált használva szinkronizálja újra a hívóbetűt a munkaterületben.  


## Azure Piactér

Lásd: [Alkalmazások kiadása és használata a Machine Learning-piactéren GYIK](machine-learning-marketplace-faq.md).

## Támogatás és betanítás

**Hol kaphatok képzést az Azure Machine Learning használatáról?**

Az [Azure Machine Learning Dokumentációs központban](https://azure.microsoft.com/services/machine-learning/) oktatóvideókat és útmutatókat is talál az Azure Machine Learning használatáról. Ezek a lépésenkénti útmutatók a szolgáltatásokról szóló bevezetést tartalmaznak, és végigvezetik Önt az adatimportálás adatelemzési életciklusán, az adattisztításon, a prediktív modellek létrehozásán és az Azure Machine Learning segítségével történő üzembe helyezésükön.

A Machine Learning központját folyamatosan új anyagokkal bővítjük. A Machine Learning központba további oktatóanyagok feltöltését a [felhasználó-visszajelzési fórumon](https://windowsazure.uservoice.com/forums/257792-machine-learning) kérelmezheti.

A [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning) további oktatóanyagokat is kínál.

**Hol kaphatok támogatást az Azure Machine Learninghez?**

Az Azure Machine Learninghez való műszaki támogatásért lépjen az [Azure-támogatás](/support/options/) oldalára, és válassza a **Machine Learning** opciót.

Az Azure Machine Learning egy közösségi fórummal is rendelkezik az MSDN-en, ahol az Azure Machine Learninggel kapcsolatos kérdéseit teheti fel. A fórumot az Azure Machine Learning szolgáltatás fejlesztőcsapata figyeli. Látogasson el az [Azure fórumra](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[képolvasó]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[csatlakozás]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[gépi tanulási modulok]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partíció és minta]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[adatimportálás]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[adatexportálás]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[felosztás]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx



<!--HONumber=Jun16_HO2-->


