<properties
    pageTitle="Azure Machine Learning GYIK | Microsoft Azure"
    description="Azure Machine Learning bevezetés: a zökkenőmentes prediktív modellezést támogató felhőalapú szolgáltatással kapcsolatos számlázásra, képességekre és korlátozásokra vonatkozó GYIK."
    keywords="bevezetés a gépi tanulásba, prediktív modellezés, mi az a gépi tanulás"
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
    ms.date="07/14/2016"
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

**Hol találom meg a klasszikus webszolgáltatások listáját? Hol találom meg az új ARM-alapú webszolgáltatások listáját?**

A klasszikus webszolgáltatások listája a [Machine Learning Studio](http://studio.azureml.net) alatt, a Webszolgáltatások lapon található. Az új, ARM-alapú webszolgáltatások listája a [Microsoft Azure Machine Learning- webszolgáltatások](https://services.azureml.net/) portálján található. Egy elem csak egy listán szerepelhet.

## A Microsoft Azure Machine Learning webszolgáltatással kapcsolatos kérdések

**Mik azok az Azure ML-webszolgáltatások?**

Az Azure Machine Learning webszolgáltatásánál egy külső alkalmazás valós időben kommunikál a Machine Learning munkafolyamatának pontozási modelljével. A Machine Learning webszolgáltatás felé irányuló hívások visszaadják az előrejelzés eredményét a külső alkalmazásnak. Machine Learning webszolgáltatási hívás létrehozásához át kell adnia az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. A Machine Learning webszolgáltatás a webprogramozási projektekben népszerű REST architektúrán alapul.

Az Azure Machine Learning két különböző típusú szolgáltatást tud biztosítani:

* Kérés-válasz szolgáltatás (RRS): alacsony késésű, nagy mértékben skálázható szolgáltatás, amely interfészt biztosít a Machine Learning Studióban létrehozott, és onnan üzembe helyezett állapot nélküli modellekhez.
* Kötegelt végrehajtási szolgáltatás (BES): aszinkron szolgáltatás, amely adatrekordok szerint pontozza a kötegeket.

A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Megírhatja például C#, R vagy Python nyelven az alkalmazást, és felhasználhatja hozzá a webszolgáltatás üzembe helyezésekor kapott mintakódot (ez a Machine Learning Studióban, a webszolgáltatás irányítópultján, az API Help (API-súgó) lapon érhető el). Vagy az Önnek létrehozott Microsoft Excel-munkafüzetmintát is használhatja (ez szintén a Studióban, a webszolgáltatás irányítópultján érhető el).

**Milyen újdonságokat hoz az új Azure ML-webszolgáltatások funkció?**

Az új New Azure Machine Learning-webszolgáltatások funkcióval kapcsolatos részletes információkért olvassa el a [kapcsolódó dokumentációt](machine-learning-whats-new.md). 

## A Machine Learning Studióra vonatkozó kérdések

### Kísérlet létrehozása

**Létezik verziókövetés vagy Git integráció a kísérleti diagramok esetében?**

Nem, a Machine Learning Studio azonban megőriz minden egyes megismételt kísérletet, amelyek más felhasználók által nem módosíthatóak.
További információ: [Kísérletismétlések kezelése a Machine Learning Studióban](machine-learning-manage-experiment-iterations.md).


### Kísérlet üzembe helyezése

**Üzembe helyezhetek egy prediktív kísérletet új (ARM-alapú) webszolgáltatásként, ha korábban már egyszer klasszikus webszolgáltatásként üzembe helyeztem?**

Nem, a korábban már klasszikus webszolgáltatásként üzembe helyezett kísérleteket nem lehet ismét üzembe helyezni. Hozzon létre egy új prediktív kísérletet, és azt helyezze üzembe.


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

A telepítés után a klasszikus Azure portálon keresztül figyelheti a prediktív modellt. Minden telepített szolgáltatásnak van egy saját irányítópultja, ahol az adott szolgáltatásra vonatkozó információkat láthatja. Az üzembe helyezett webszolgáltatásokkal kapcsolatos további információkért lásd: [Manage an Azure Machine Learning workspace](machine-learning-manage-workspace.md) (Az Azure Machine Learning-munkaterület kezelése).

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

## Számlázással kapcsolatos kérdések

**Milyen díjszámítási módszer vonatkozik a Machine Learning szolgáltatásra?**

Az Azure Machine Learning-szolgáltatás két összetevőből áll: a Machine Learning Studióból és a Machine Learning webszolgáltatásokból.

Amíg kipróbálja a Machine Learning Studiót, használhatja az ingyenes tarifacsomagot.  Az ingyenes szint lehetőséget kínál korlátozott kapacitású klasszikus webszolgáltatások üzembe helyezésére is.

Ha úgy véli, hogy az Azure Machine Learning felel meg a legjobban igényeinek, előfizethet a standard szintre. Az előfizetéshez regisztrálnia kell egy Microsoft Azure-előfizetést.

A standard szinten munkaállomásonként, havonta kell fizetnie a Machine Learning Studio használatáért. Amikor kísérletet futtat a Studióban, fizetnie kell a kísérlet elvégzéséhez felhasznált számítási erőforrásokért. Amikor klasszikus webszolgáltatást helyez üzembe, a használat alapján kell fizetnie a tranzakciókért és a számítási órákért. 

Az új Machine Learning webszolgáltatások esetében bevezettük a számlázási csomagokat, amelyek megkönnyítik a költségek tervezését. A szintekre bontott árképzés azoknak az ügyfeleknek kínál kedvezményes díjakat, akiknek jelentős kapacitásra van szükségük.

A csomag létrehozásakor fix összeget kell lekötnie, amelyért az API-számítási órákat és -tranzakciókat adott mértékig fedező szolgáltatási keretet kap. Ha később nagyobb keretre van szüksége, további példányokat adhat a csomaghoz. Ha lényegesen nagyobb keretre van szüksége, érdemes magasabb szinthez tartozó csomagra áttérni, mivel így kedvezményesebb áron juthat hozzá a szükséges erőforrásokhoz.

Ha kimerül a meglévő példány(ok) kerete, a további használatért a tarifacsomag szintje szerinti többlethasználati díjon kell fizetnie.

Megjegyzés: a keretet a rendszer 30 naponta újra kiosztja, a fel nem használt erőforrások nem vihetők át a következő időszakra.

A számlázással és a díjszabással kapcsolatos további információkért olvassa el a [Machine Learning díjszabás](https://azure.microsoft.com/pricing/details/machine-learning/) című fejezetet.

**Létezik a Machine Learningnek ingyenes próbaverziója?**

 Az Azure Machine Learning ingyenes előfizetési lehetőséget is kínál (tovább információkért lásd: [Machine Learning díjszabás](https://azure.microsoft.com/pricing/details/machine-learning/)), a Machine Learning Studióhoz pedig 8 órás, gyors kipróbálási időszak érhető el (a próbaverzió eléréséhez jelentkezzen be a [Machine Learning Studióba](https://studio.azureml.net/?selectAccess=true&o=2)).
 
 Emellett az Azure ingyenes próbaverziójára történő regisztrációt követően bármely Azure-szolgáltatást kipróbálhatja egy hónapig. Ha többet szeretne tudni az Azure ingyenes próbaverziójáról, olvassa el az [Azure Ingyenes próbaverziójára vonatkozó GYIK-et](/pricing/free-trial-faq/).

**Mit nevezünk tranzakciónak?**

A tranzakciók azok az API-hívások, amelyekre az Azure Machine Learning válaszol. A kérés-válasz szolgáltatás (RRS) és a kötegelt végrehajtási szolgáltatás (BES) által küldött hívások alapján létrejövő tranzakciókat a rendszer összesíti; ezekért a számlázási csomag szerint fizetnie kell.

**A csomagba foglalt tranzakciómennyiséget RRS- és BES-tranzakciókra is felhasználhatom?**

Igen, a rendszer összesíti az RRS- és BES-tranzakciókat, és ezekért kell fizetnie a számlázási csomag szerint.

**Mik azok az API számítási órák?**

Az API üzemóra számlázási egység, amely azon az időtartamon alapul, amennyibe az API-hívásnak beletelik, hogy az ML számítási erőforrásait felhasználva lefusson. A hívásokat a rendszer a számlázás céljából összesíti. 

**Általában milyen hosszú időt vesz igénybe egy jellemző éles üzemi API-hívás?**

A különböző éles üzemi API-hívások időtartama között jelentős eltérés lehet, a hívások az adatfeldolgozási és a gépi tanulási modell összetettségétől függően néhány száz ezredmásodperctől több másodpercig is terjedhetnek, de akár perceket is igénybe vehetnek. Az éles üzemi API-hívások időtartamának megbecsülésére a legjobb módszer, ha a Machine Learning szolgáltatás segítségével teszteli a modellt.

**Mik a számítási órák a Studióban?**

A számítási óra a Studio számlázási egysége, amelynek alapján fizetnie kell azért az összesített időtartamért, amíg kísérletei használták a Studio számítási erőforrásait. 

**Mit jelent az új webszolgáltatásokban a fejlesztés + tesztelés szint?**

Az Azure ML új webszolgáltatások több szintet biztosítanak, amelyet felhasználhat számlázási csomagjai kialakításához. A fejlesztés + tesztelés szint korlátozott keretet kínál, amely lehetővé teszi, hogy további költségek felmerülése nélkül tesztelje kísérletét új webszolgáltatásként. Ez lehetőséget nyújt a kísérlet gyakorlati kipróbálására.

**Kell külön fizetnem a tárterületért?** 

A Machine Learning Ingyenes szintje nem igényel különálló tárterületet, illetve nem is teszi lehetővé annak használatát. A Machine Learning Standard szintjénél a felhasználóknak Azure-tárfiókkal kell rendelkezniük. Az Azure-tárterületért [külön kell fizetnie](https://azure.microsoft.com/pricing/details/storage/).

**Hogyan támogatja a Machine Learning a magas rendelkezésre állást?** 

A különböző éles üzemi API-hívások időtartama között jelentős eltérés lehet, a hívások az adatfeldolgozási és a gépi tanulási modell összetettségétől függően néhány száz ezredmásodperctől több másodpercig is terjedhetnek, de akár perceket is igénybe vehetnek. Az éles üzemi API-hívások időtartamának megbecsülésére a legjobb módszer, ha a Machine Learning szolgáltatás segítségével teszteli a modellt.

**Konkrétan milyen számítási erőforrásokon fognak futni az éles üzemi API-hívásaim?**

A Machine Learning többvállalatos szolgáltatás, a háttérben használt tényleges számítási erőforrások ezért változóak lehetnek: mindig a legoptimálisabb és legkiszámíthatóbb működést részesítjük előnyben.

### Az új webszolgáltatások kezelése 

**Mi történik, ha törlöm a csomagot?**

A csomagot eltávolítjuk az előfizetéséből, a használatot pedig időarányosan számlázzuk ki.

Megjegyzés: ha a csomagot használja egy webszolgáltatás, nem lehet törölni. Ha törölni szeretné a csomagot, rendelje új csomaghoz a webszolgáltatást, vagy törölje a webszolgáltatást.

**Mik azok a csomagpéldányok?**

A csomagpéldány a szolgáltatási keret egysége, amelyet a számlázási csomaghoz adhat. Amikor kiválasztja a számlázási szintet a számlázási csomaghoz, egy darab példányt kap. Ha többre van szüksége, további példányokat adhat csomagjához a kiválasztott számlázási szintről. 

**Legfeljebb hány csomagpéldányt használhatok?**

A fejlesztés + tesztelés szinten egy példány szerepelhet az előfizetésben.

Az S1, az S2 és az S3 szinten annyi példányt vehet fel, ahányra csak szüksége van. 

Megjegyzés: a tervezett használat mértékétől függően költséghatékonyabb megoldás lehet, ha ahelyett, hogy újabb példányokat adna az aktuális szinthez, inkább magasabb keretszintre tér át.

**Mi történik, ha módosítom a csomagom szintjét (magasabbra vagy alacsonyabbra)?**

Töröljük a régi csomagot, a használatot pedig időarányosan kiszámlázzuk. A számlázási időszak fennmaradó időtartamára az új, magasabb vagy alacsonyabb szint teljes keretét tartalmazó új csomag jön létre. 

Megjegyzés: a szolgáltatási keret az adott időszakra vonatkozik, a fel nem használt erőforrások nem vihetők át a következő időszakra.

**Mi történik, ha növelem a csomagban lévő példányok számát?**

A szolgáltatási keretet időarányos alapon tesszük elérhetővé, a váltás életbe léptetése pedig akár 24 órába is beletelhet. 

**Mi történik, ha törlök egy példányt a csomagból?**

A példányt eltávolítjuk az előfizetéséből, a használatot pedig időarányosan számlázzuk ki. 


### Regisztrálás új webszolgáltatás-csomagokra

**Hogyan tudok regisztrálni a csomagokra?**

Számlázási csomagot két különböző módon hozhat létre.

Az új webszolgáltatás első üzembe helyezésekor választhat egy meglévő csomagot, vagy létrehozhat egy új csomagot. 

Az itt létrehozott csomagok a felhasználó alapértelmezett régiójához fognak tartozni, és a webszolgáltatást is ebben a régióban helyezzük üzembe. 

Érdemes lehet még a szolgáltatás üzembe helyezése előtt létrehozni a számlázási csomagot, ha például az alapértelmezettől eltérő régióban szeretné üzembe helyezni a szolgáltatást.

Ebben az esetben jelentkezzen be az Azure Machine Learning webszolgáltatások portáljára, és nyissa meg a tervekkel foglalkozó lapot. Itt lehetősége van csomagokat felvenni és törölni, valamint módosítani a meglévő csomagokat.

**Melyik csomaggal érdemes kezdeni?**

Javasoljuk, hogy először válassza a Standard S1 csomagot, és kövesse figyelemmel, hogy mennyi erőforrást használnak szolgáltatásai. Ha úgy találja, hogy gyorsan kimerül a szolgáltatási keret, adjon hozzá további példányokat, vagy a kedvezményesebb fizetés érdekében váltson magasabb szintre. A számlázási csomagot bármikor tetszés szerint módosíthatja. 

**Mely régiókban érhetők el az új csomagok?**

Az új számlázási csomagok abban a három éles régióban érhetők el, amelyekben támogatjuk az új webszolgáltatásokat:

* USA déli középső régiója
* Nyugat-Európa
* Délkelet-Ázsia

**Több régióban is működnek webszolgáltatásaim. Minden régióhoz külön csomagot kell létrehoznom?**

Igen. A csomagok ára régiónként változik. Ha egy webszolgáltatást az eredetitől eltérő régióban is szeretne üzembe helyezni, az adott régióhoz tartozó csomagot kell hozzárendelnie.

### Új webszolgáltatások – Többletköltségek

**Hogyan tudom ellenőrizni, hogy felmerültek-e többletköltségek a webszolgáltatás használata miatt?**

Az Azure Machine Learning webszolgáltatások portál Csomagok lapján az összes csomag által használt erőforrásokat megtekintheti. Jelentkezzen be a portálra, és kattintson a Csomagok menüpontra. 

A táblázat Tranzakciók és Számítás oszlopában láthatja a csomag szolgáltatási keretét, és azt, hogy az adott pillanatig annak hány százalékát használta fel. 

**Mi történik, ha a fejlesztés + tesztelés szint esetében kimerítem a szolgáltatási keretet?**

A fejlesztés + tesztelés szinttel ellátott szolgáltatásokat ilyenkor a következő periódus elérkeztéig leállítja, illetve Önnek lehetősége van áthelyezni őket az egyik fizetett szint alá.

**Hogyan számítják ki a kérés-válasz (RRS) és a kötegelt (BES) számítási feladatokért fizetendő összeget a klasszikus webszolgáltatásoknál és az új webszolgáltatások többleteinél?** 

Az RRS számítási feladatoknál minden tranzakciós API-hívásért, valamint a kérésekhez kapcsolódó számítási időért fizetnie kell. Az éles üzemi RRS API-tranzakciókért fizetendő díjat a következőképp számítjuk ki: elvégzett API-hívások száma, szorozva az 1000 tranzakciónkénti árral (az egyes tranzakciókhoz képest arányosítva). Az éles üzemi RRS API számítási órák díját a következőképp számítjuk ki: az egyes API-hívások lefutásához szükséges idő, szorozva az API-tranzakciók számával, szorozva az egyes éles API számítási órák díjával. A Standard S1 csomagban érvényes többletköltségekkel számolva 1 000 000, egyenként 0,72 másodpercen át futó API-tranzakcióért 500 USD-t (1 000 000 * 0,50 USD/1000 API-hívás) kell fizetni az éles API-tranzakciókért magukért, és 400 USD-t (1 000 000 * 0,72 másodperc * 2 USD/óra) az éles üzemi API-k számítási üzemidejéért, azaz az összköltség 900 USD.

A BES számítási feladatokért ugyanígy kell fizetni, azonban az API-tranzakciók költsége a beküldött kötegelt feladatok számához igazodik, a számítási költségek pedig az ezekhez a kötegelt feladatok elvégzéséhez szükséges számítási időn alapulnak. Az éles üzemi BES API-tranzakciók költségét ezért a következőképp lehet kiszámítani: beküldött feladatok száma, szorozva az 1000 tranzakciónként fizetendő díjjal (az egyes tranzakciókhoz képest arányosítva). Az éles üzemi BES API számítási órák költségét a következőképp lehet kiszámítani: a feladatban szereplő sorok lefutásához szükséges időtartam, szorozva a feladatban szereplő sorok számával, szorozva a feladatok számával, szorozva az éles üzemi API számítási órák díjával. A Machine Learning számológépének használata során a tranzakcióhoz tartozó érték a beküldeni tervezett feladatok számát jelöli, a tranzakciónkénti időt tartalmazó mező pedig az egyes feladatokban szereplő összes sor lefutásához szükséges időt mutatja. A Standard S1 csomagban érvényes többletköltségekkel számolva ha napi 100 feladatot ad be, és ezek mindegyike 500, egyenként 0,72 másodpercen át futó sort tartalmaz, a havi átlagköltség 1,55 USD (napi 100 feladat = 3100 feladat/hónap * 0,50 USD/1000 API-tranzakció) az éles API-tranzakciókért, és 620 USD (500 sor * 0,72 másodperc * 3100 feladat * 2 USD/óra) az éles üzemi API-k számítási üzemidejéért, azaz összesen 621,55 USD.

### Azure ML klasszikus webszolgáltatások

**Elérhető még a használatalapú modell?**
Igen, az Azure Machine Learning továbbra is használható a klasszikus webszolgáltatásokkal is.  

### Az Azure Machine Learning Ingyenes és Standard szintje

**Mit tartalmaz az Azure Machine Learning Ingyenes szintje?**

Az Azure Machine Learning Ingyenes szintje arra szolgál, hogy részletesen bemutassa az Azure Machine Learning Studio szolgáltatást. A bejelentkezéshez mindössze egy Microsoft-fiókra van szükség. Az Ingyenes szint [Microsoft-fiókonként](https://www.microsoft.com/account/default.aspx) egy Azure Machine Learning Studio-munkaterülethez nyújt ingyenes hozzáférést. A felhasználónak lehetősége van 10 GB tárhely használatára, valamint a modellek átmeneti API-ként történő üzembe helyezésére. Az Ingyenes szinthez tartozó számítási feladatokra nem vonatkozik SLA, ezek csak fejlesztési és személyes célokra használhatók. Az Ingyenes szinthez tartozó számítási feladatok nem tudják elérni az adatokat a helyszíni SQL-kiszolgálókhoz való kapcsolódással. A fenti táblázatban az Ingyenes és a Standard szintek közötti különbségeket ismertetjük, azonban más eltérések is létezhetnek, az Ingyenes szint funkciói pedig idővel változhatnak.

**Mit tartalmaznak az Azure Machine Learning Standard szintjei és csomagjai?**

Az Azure Machine Learning Standard szintje az Azure Machine Learning Studio fizetős változatát tartalmazza. Az Azure ML szolgáltatásai közé tartozó Studio havidíját munkaállomásonként számítjuk fel. A nem teljes hónapokért időarányosan kell fizetni. Az Azure ML Studio-kísérletezési idő (óra) után az aktív kísérletezéssel töltött számítási óránként számítunk fel díjat. A nem teljes órákért időarányos módon kell fizetni.  

Az Azure ML API szolgáltatásnál a fizetendő összeget az határozza meg, hogy a klasszikus vagy az új webszolgáltatásról van szó. 

Az alábbi díjak a munkaállomásonként összesített költségeket jelzik az előfizetésben. 

* ML munkaállomásonkénti előfizetés: az ML munkaállomásonkénti előfizetés az a havidíj, amelynek megfizetéséért cserébe eléri az ML Studio munkaterületet, és kísérleteket futtathat a Studióban, valamint használhatja az éles üzemi API-kat.
* Studio-kísérletezési idő (óra): ez az érték az ML Studióban futtatott kísérletek, valamint az éles üzemi API-hívások átmeneti környezetben való futtatása során elvégzett számítási feladatokkal kapcsolatos díjakat összesíti.
* Érje el adatait a tanítási és pontozási modellekben egy helyszíni SQL-kiszolgálóhoz való csatlakozással.
* Klasszikus webszolgáltatásoknál: 
    * Számítási üzemidő éles üzemi API-n (óra) – Ez az érték az éles üzemben futó webszolgáltatásokért fizetendő számítási díjakat adja meg.
    * Éles üzemi API-tranzakciók (ezer db) – Ez az érték az éles üzemi webszolgáltatás felé irányuló hívásonkénti díjakat adja meg.

A fenti díjakon felül az új webszolgáltatások esetében a rendszer a kiválasztott csomaghoz tartozó díjakat is összesíti: 

* Standard S1/S2/S3 API-használati csomag (egység) – Ez az érték az új webszolgáltatáshoz kiválasztott példány típusát adja meg.
* Standard S1/S2/S3 keretét túllépő API-számítási üzemóra – Ez az érték azt adja meg, hogy mennyi számítási feladatokkal kapcsolatos díj merült fel azt követően, hogy az éles üzemben futó új webszolgáltatással felhasználta a meglévő példány(ok)ban elérhető szolgáltatási keretet. A kereten felüli használatért az S1/S2/S3 csomagszinthez tartozó feláras díj alapján kell fizetni.
* Standard S1/S2/S3 keretét túllépő API-tranzakciószám (ezer db) – Ez az érték azt adja meg, hogy mennyi díj merült fel az éles üzemben futó új webszolgáltatás felé irányuló hívásonként azt követően, hogy a meglévő példány(ok)ban elérhető szolgáltatási keret kimerült. A kereten felüli használatért az S1/S2/S3 csomagszinthez tartozó feláras díj alapján kell fizetni.
* Szolgáltatási keretbe foglalt API-számítási üzemóra – Az új webszolgáltatásokban ez az érték adja meg az API számítási órákra vonatkozó szolgáltatási keretet. 
* Szolgáltatási keretbe foglalt API-tranzakció (1000 db) – Az új webszolgáltatásokban ez az érték adja meg az API-tranzakciókra vonatkozó szolgáltatási keretet. 


**Hogy tudok regisztrálni az Azure ML Ingyenes szintjére?**

Mindössze egy Microsoft-fiókra van szükség. Nyissa meg az [Azure Machine Learning szolgáltatás honlapját](https://azure.microsoft.com/services/machine-learning/), és kattintson az Első lépések gombra. Jelentkezzen be Microsoft-fiókjával, és a rendszer létrehozza az Ön számára az Ingyenes szinthez tartozó munkaterületet. Ezt követően azonnal elkezdheti felfedezni a szolgáltatást, és létrehozni Machine Learning-kísérleteit.

**Hogy tudok regisztrálni az Azure ML Standard szintjére?**

Ahhoz, hogy Standard ML-munkaterületet hozhasson létre, Azure-előfizetéssel kell rendelkeznie. Regisztrálhat az Azure 30 napos, ingyenes próba-előfizetésére, és később frissíthet fizetős Azure-előfizetésre, vagy akár egyből megvásárolhatja az Azure-előfizetést. Ha megkapta a hozzáférést az előfizetéshez, a Microsoft klasszikus Azure portáljából létrehozhatja a Machine Learning-munkaterületet. Olvassa el a [lépéseket részletes bemutató utasításokat](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Másik lehetőség, ha egy már meglévő Standard ML-munkaterület tulajdonosa elérést ad Önnek a munkaterületéhez.

**Beállíthatom az Ingyenes szinten saját Azure Blob Storage-fiókom használatát?**

Nem. A Standard szint a Machine Learning azon változata, amely a szintek bevezetése előtt elérhető volt.

**Van rá lehetőség az Ingyenes szinten, hogy API-ként üzembe helyezzem gépi tanulási modelljeimet?**

Igen, az Ingyenes szinten átmeneti API-szolgáltatásokba helyezheti üzembe gépi tanulási modelljeit. Ahhoz azonban már a Standard szintre lesz szüksége, hogy az átmeneti API-szolgáltatás éles üzembe kapcsolhasson, illetve, hogy beszerezhesse az üzembe helyezett szolgáltatáshoz kapcsolódó éles üzemi végpontot. 

**Mi a különbség az Azure ingyenes próbaverziója és az Azure Machine Learning Ingyenes szintje között?**

A [Microsoft Azure ingyenes próbaverziója](https://azure.microsoft.com/free/) krediteket kínál, amelyeket egy hónapig bármelyik Azure-szolgáltatásban felhasználhat, míg az Azure Machine Learning Ingyenes szintje kifejezetten az Azure Machine Learning szolgáltatáshoz nyújt folyamatos hozzáférést, amelyet nem éles üzemi számítási feladatok futtatására használhat.

**Hogy tudom átvinni a kísérleteimet az Ingyenes szintről a Standard szintre?**

Ha szeretné az Ingyenes szintről a Standard szintre másolni kísérleteit, kövesse az alábbi lépéseket.

1.  Jelentkezzen be az Azure Machine Learning Studióba, és győződjön meg róla, hogy a felső navigációs sávban, a munkaterület-választóban mind az Ingyenes, mind a Standard munkaterület látható.
2.  Ha a Standard munkaterületen tartózkodik, váltson át az Ingyenes munkaterületre.
3.  A kísérletek listanézetében válassza ki a másolni kívánt kísérletet, majd kattintson a Másolás parancs gombjára.
4.  A felugró párbeszédpanelen válassza a Standard munkaterületet, majd kattintson a Másolás gombra.
5.  Tájékoztatjuk, hogy ezt követően a rendszer a kísérletet, valamint a hozzá tartozó összes adatkészletet, betanított modellt és egyéb elemet a Standard munkaterületre másolja.
6.  A kísérletet újra le kell futtatnia, a webszolgáltatást pedig ismét közzé kell tennie a Standard munkaterületen.

### Studio-munkaterület

**Mi az a Machine Learning munkaállomás-előfizetés, és mikor van ilyenre szükségem?**

A Machine Learning munkaállomás-előfizetése egy munkaállomásra vonatkozik. Javasoljuk, hogy minden olyan felhasználóhoz szerezzen be Machine Learning munkaállomás-előfizetést, aki kísérleteket fog futtatni az ML Studióban, vagy éles üzemi API-szolgáltatást fog működtetni.

**A különböző munkaterületekhez különböző számlák tartoznak?**

A munkaterületekért felszámított költségek részletezését közös számlán, de mérőszámonként külön-külön fogja megkapni.

**Konkrétan milyen számítási erőforrásokon fognak futni a kísérleteim?**

A Machine Learning többvállalatos szolgáltatás, a háttérben használt tényleges számítási erőforrások ezért változóak lehetnek: mindig a legoptimálisabb és legkiszámíthatóbb működést részesítjük előnyben.

### Vendéghozzáférés

**Mit jelent a vendéghozzáférés az Azure Machine Learning Studióban?**

A vendéghozzáférés a szolgáltatás korlátozott kipróbálására nyújt lehetőséget: ingyenesen hozhat létre és futtathat kísérleteket az Azure Machine Learning Studióban, és még hitelesítő adatokat sem kell megadnia. A vendégként végzett munkamenetek nem perzisztensek (nem lehet őket menteni), maximális időtartamuk 8 óra. További korlátozások: nincs R- és Python-támogatás, nincs lehetőség átmeneti API-k használatára, illetve az adatkészletek mérete és a tárolókapacitás is korlátozott. Ezzel szemben ha bejelentkezik Microsoft-fiókjával, teljes hozzáférést kap a Machine Learning Studio fentiekben leírt Ingyenes szintjéhez, amely perzisztens munkaterületet és bővebb funkciókészletet tartalmaz. Nyissa meg a [https://studio.azureml.net](https://studio.azureml.net) oldalt, kattintson az „Első lépések” gombra, és válassza ki, hogy melyik ingyenes próbaverziót szeretné használni: kattintson a Vendéghozzáférés vagy a Bejelentkezés a Microsoft-fiókba lehetőségre.

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



<!--HONumber=sep16_HO1-->


