---
title: Azure Machine Learning – gyakori kérdések (GYIK) | Microsoft Docs
description: 'Azure Machine Learning bevezetés: a zökkenőmentes prediktív modellezést támogató felhőalapú szolgáltatással kapcsolatos számlázásra, képességekre és korlátozásokra vonatkozó GYIK.'
keywords: bevezetés a gépi tanulásba, prediktív modellezés, mi az a gépi tanulás
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/02/2017
ms.openlocfilehash: 9545c9a0cf77274b4ea68dea7a6640453a849093
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263426"
---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Az Azure Machine Learning szolgáltatásra vonatkozó gyakori kérdések (GYIK): Számlázás, képességek, korlátozások és támogatás
Az alábbiakban néhány gyakori kérdést (GYIK) és azok válaszait olvashatja az Azure Machine Learning szolgáltatással kapcsolatban, amely egy, a webszolgáltatásokon keresztül végrehajtott prediktív modellezést és a megoldások üzembe helyezését célzó felhőalapú szolgáltatás. Ezek a gyakori kérdések a szolgáltatás használatával kapcsolatos kérdéseket fedik le, beleértve a számlázási modell, a képességek, a korlátozások és a támogatás témaköreit.

**Kérdése van, és nem találja meg itt?**

Az Azure Machine Learning egy közösségi fórummal is rendelkezik az MSDN-en, az adatelemzési közösség tagjai kérdéseket tehetnek fel az Azure Machine Learninggel kapcsolatban. A fórumot az Azure Machine Learning fejlesztőcsapata figyeli. Lépjen az [Azure Machine Learning fórumra](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning), és keresse meg a kívánt válaszokat, vagy tegyen fel egy új kérdést.

## <a name="general-questions"></a>Általános kérdések
**Mi az Azure Machine Learning?**

Az Azure Machine Learning egy olyan teljes körűen felügyelt szolgáltatás, amelynek segítségével megtervezheti, tesztelheti, üzembe helyezheti és felügyelheti a felhőalapú prediktív elemzési megoldásokat. Csupán egy böngésző szükséges ahhoz, hogy bejelentkezzen, feltöltse adatait, és azonnal nekiláthasson a gépi tanulási kísérletekhez. Az áthúzással működtethető prediktív modellezés, a modulok széles skálájának és a kezdősablonok gyűjteményének segítségével egyszerűen és gyorsan elvégezhetők az általános gépi tanulási feladatok. További információk: [Az Azure Machine Learning szolgáltatás áttekintése](https://azure.microsoft.com/services/machine-learning/). A gépi tanulás fontosabb szakkifejezéseit és alapfogalmait ismertető bevezetéshez olvassa el az [Azure Machine Learning használatának első lépéseit](what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**Mi a Machine Learning Studio?**

A Machine Learning Studio egy böngészőn keresztül elérhető munkaterület-környezet. A Machine Learning Studio vizuális összeállítási interfészekkel rendelkező modulok sorát tartalmazza, amelyek lehetővé teszik, hogy egy teljes körű adatelemezési munkafolyamatot építsen egy kísérlet formájában.

A Machine Learning Studióval kapcsolatos további információkért olvassa el a [Mi a Machine Learning Studio](what-is-ml-studio.md) című fejezetet.

**Mi a Machine Learning API szolgáltatás?**

A Machine Learning API szolgáltatás segítségével méretezhető, hibatűrő webszolgáltatásokként helyezhet üzembe prediktív, a Machine Learning Studióba beépítettekhez hasonló modelleket. A Machine Learning API szolgáltatás által létrehozott webszolgáltatások olyan REST API-k, amelyek egy, a külső alkalmazások és az Ön prediktív elemzési modelljei közötti kommunikációt lehetővé tevő interfészt biztosítanak.

További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).

**Hol találom meg a klasszikus webszolgáltatások listáját? Hol találom az Azure Resource Manageren alapuló új webszolgáltatások listáját?**

A klasszikus üzemi modell, valamint az új Azure Resource Manager-alapú üzemi modell használatával létrehozott webszolgáltatások listája a [Microsoft Azure Machine Learning-webszolgáltatások](https://services.azureml.net/) portálján található.

A klasszikus webszolgáltatások listája a [Machine Learning Studio](http://studio.azureml.net) alatt, a **Webszolgáltatások** lapon is megtalálható.

## <a name="azure-machine-learning-questions"></a>Kérdések az Azure Machine Learning szolgáltatással kapcsolatban
**Mik azok a Microsoft Azure Machine Learning webszolgáltatások?**

A Machine Learning webszolgáltatások illesztőfelületet biztosítanak az alkalmazások és a Machine Learning munkafolyamatának pontozási modelljei között. Egy külső alkalmazás az Azure Machine Learning szolgáltatás használatával valós időben kommunikálhat a Machine Learning egyik munkafolyamatának pontozási modelljével. A Machine Learning webszolgáltatás felé irányuló hívások visszaadják az előrejelzés eredményét a külső alkalmazásnak. A webszolgáltatások felé irányuló hívások indításához át kell adnia azt az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. A Machine Learning webszolgáltatások a webprogramozási projektekben népszerű REST architektúrán alapulnak.

Az Azure Machine Learning két különböző típusú webszolgáltatást tud biztosítani:

* Kérés-válasz szolgáltatás (RRS): alacsony késésű, nagy mértékben skálázható szolgáltatás, amely interfészt biztosít a Machine Learning Studióban létrehozott, és annak használatával üzembe helyezett állapot nélküli modellekhez.
* Kötegelt végrehajtási szolgáltatás (BES): aszinkron szolgáltatás, amely adatrekordok szerint pontozza a kötegeket.

A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Írhat például egy alkalmazást C#, R vagy Python nyelven azzal a mintakóddal, amelyet a rendszer a webszolgáltatás üzembe helyezésekor hozott létre.

A mintakód elérhető a következő helyről:
- A webszolgáltatás Consume (Felhasználás) lapja az Azure Machine Learning-webszolgáltatások portálján
- A Machine Learning Studio webszolgáltatási irányítópultján található API-súgóoldal

Az Önnek létrehozott Microsoft Excel-munkafüzetmintát is használhatja, amely a Machine Learning Studióban, a webszolgáltatás irányítópultján érhető el.

**Mik az Azure Machine Learning főbb újdonságai?**

A legújabb frissítésekkel kapcsolatban lásd [az Azure Machine Learning újdonságait](../../active-directory/fundamentals/whats-new.md) ismertető cikket.

## <a name="machine-learning-studio-questions"></a>A Machine Learning Studióra vonatkozó kérdések
### <a name="import-and-export-data-for-machine-learning"></a>Adatok importálása és exportálása a Machine Learninghez
**Milyen adatforrásokat támogat a Machine Learning?**

A Machine Learning Studióval való kísérletezéshez háromféleképpen tölthet le adatokat:

- Helyi fájl feltöltése adatkészletként
- Modul használata az adatok felhőalapú adatszolgáltatásokból való importálásához
- Egy másik kísérletből mentett adatkészlet importálása

További információ a támogatott fájlformátumokról: [Import training data into Machine Learning Studio](import-data.md) (Betanítási adatok betöltése a Machine Learning Studióba).

#### <a id="ModuleLimit"></a>Mekkora adatkészleteket kezelhetek a moduljaimmal?
A Machine Learning Studióban található modulok 10 GB-nyi számadatot tartalmazó adatkészletet támogatnak a gyakori alkalmazási esetekben. Ha egy modul egynél több bemenetből fogad adatokat, a bemenet összesített mérete nem haladhatja meg a 10 GB-ot. A nagyobb adatkészletekből Hive- vagy Azure SQL Database-lekérdezések, illetve az adatfeldolgozást megelőzően a Learning by Counts előzetes feldolgozás segítségével lehet mintát venni.  

A szolgáltatásnormalizálás során a következő, 10 GB alá korlátozott adattípusok bővíthetők nagyobb adatkészletekké:

* Ritka
* Kategorikus
* Sztringek
* Bináris adatok

A következő modulok 10 GB-nál kisebb adatkészletekre vannak korlátozva:

* Ajánló modulok
* SMOTE (Synthetic Minority Oversampling Technique) modul
* Parancsfájlkezelési modulok: R, Python, SQL
* Olyan modulok, amelyeknél a kimeneti adatok mérete meghaladhatja a bemeneti adatok méretét; például az egyesítés vagy a szolgáltatáskivonatolás
* Kereszt-ellenőrzés, modell-hiperparaméterek beállítása, sorszámregresszió és multi-osztályú osztályozás nagyszámú ismétlés esetében

#### <a id="UploadLimit"></a>Mik az adatfeltöltés korlátai?
A néhány GB-nál nagyobb adatkészletek esetében a helyi fájlokból való közvetlen feltöltés helyett tanácsos az adatokat az Azure Storage-ba, illetve az Azure SQL Database-be feltöltenie, vagy az Azure HDInsightot használnia erre a célra.

**Az Amazon S3-ból is lehetséges az adatbeolvasás?**

Ha kisebb mennyiségű adatot szeretne közzétenni egy HTTP URL-címen keresztül, használja az [Adatok importálása][import-data] modult. Nagyobb mennyiségű adat esetén először vigye át az adatokat az Azure Storage-ba, majd az [Adatok importálása][import-data] modullal adja hozzá az adatokat a kísérlethez.
<!--

<SEE CLOUD DS PROCESS>
-->

**Létezik beépített képbeviteli képesség?**

A képbeviteli képességről a [Képek importálása][image-reader] útmutatóban talál további információt.

### <a name="modules"></a>Modulok
**Az általam keresett algoritmus, adatforrás, adatformátum vagy adat-átalakítási művelet nem található az Azure Machine Learning Studióban. Milyen lehetőségeim vannak?**

Az általunk követett funkciókéréseket a [felhasználó-visszajelzési fórumot](https://go.microsoft.com/fwlink/?LinkId=404231) meglátogatva tekintheti meg. Amennyiben a keresett képességet már kérelmezték, adja hozzá szavazatát a már meglévő kérelemhez. Ha a keresett képesség még nem létezik, hozzon létre egy új kérelmet. Kérelmének állapotát is ezen a fórumon tekintheti meg. A listát folyamatosan követjük, és rendszeresen frissítjük a funkciók rendelkezésre állásának állapotát. Továbbá, a beépített R- és Python-támogatással szükség esetén egyénre szabott átalakításokat is létrehozhat.

**Hozzáadhatom a már létező kódomat a Machine Learning Studióhoz?**

Igen, hozzáadhatja a már meglévő R vagy Python kódját a Machine Learning Studióhoz, lefuttathatja azt az Azure Machine Learning tanulókkal együtt ugyanazon kísérletben, és az Azure Machine Learningen keresztül webszolgáltatásként is üzembe helyezheti a megoldást. További információk: [Kísérletének kiterjesztése az R nyelv használatával](extend-your-experiment-with-r.md), valamint [A Python gépi tanulási parancsfájlok végrehajtása az Azure Machine Learning Studióban](execute-python-scripts.md).

**Lehetséges egy [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)-hez hasonló dolgot használni a modellek meghatározásához?**

Nem, a PMML (Predictive Model Markup Language) használata nem támogatott. Egyéni R- és Python-kódokat használhat a modulok meghatározásához.

**Hány modult hajthatok végre párhuzamosan a kísérletemben?**  

Egy kísérletben legfeljebb négy modult hajthat végre párhuzamosan.

### <a name="data-processing"></a>Adatfeldolgozás
**Van olyan képesség (az R megjelenítéseken kívül), amellyel lehetséges az adatok interaktív megjelenítése egy kísérleten belül?**

A modul kimenetére kattintva megjelenítheti az adatokat és megtekintheti a statisztikákat.

**Az eredmények és az adatok áttekintésekor a sorok és oszlopok száma korlátozott a böngészőben. Hogy miért?**

Mivel nagy mennyiségű adatokat küldhet a böngészőbe, a Machine Learning Studio lelassulásának elkerülése érdekében korlátozva van az adatok mérete. Az összes adat/eredmény megjelenítéséhez célszerű letölteni az adatokat, és Excelt vagy hasonló eszközt használni erre a célra.

### <a name="algorithms"></a>Algoritmusok
**Mely létező algoritmusokat támogatja a Machine Learning Studio?**

A Machine Learning Studio a legkorszerűbb algoritmusokat biztosítja, többek között például a továbbfejlesztett méretezhető döntési fákat, a Bayes ajánlási rendszereket, a neurális hálózatokat és a Microsoft Research által fejlesztett Decision Jungle algoritmust. Az olyan méretezhető, nyílt forráskódú gépi tanulási csomagokat, mint a Vowpal Wabbit, szintén támogatja a Machine Learning Studio. A Machine Learning Studio támogatja a multiclass és bináris osztályzásra, a regresszióra és a fürtszolgáltatásra használt gépi tanulási algoritmusokat. Tekintse meg a [Machine Learning-modulok][machine-learning-modules] teljes listáját.

**Automatikusan az adataim alapján legmegfelelőbbnek vélt Machine Learning algoritmus használatát ajánlják?**

Nem, de a Machine Learning Studio több módszert is tartalmaz az egyes algoritmusok eredményeinek összehasonlítására, és így az Ön problémájának megoldására megfelelő algoritmus meghatározására.

**Létezik bármiféle irányelv, amely alapján eldönthető, melyiket válasszuk az elérhető algoritmusok listájából?**

Lásd: [How to choose an algorithm](algorithm-choice.md) (Hogyan válasszunk algoritmust?).

**R vagy Python nyelven írták az elérhető algoritmusokat?**

Egyik sem. Ezeket az algoritmusokat a jobb teljesítmény érdekében lefordított nyelveken írták.

**Elérhető az algoritmusokkal kapcsolatban részletes tájékoztató?**

A dokumentáció tartalmaz némi információt az algoritmusokról, és ismerteti a finomhangoláshoz megadott paramétereket az algoritmus optimalizált használata érdekében.  

**Létezik online tanulási támogatás?**

Nem, jelenleg csak a szoftveres átképezés támogatott.

**Megjeleníthetők a Neural Net Modell (Neurális háló modell) rétegei a beépített modul segítségével?**

Nem.

**Létrehozhatom a saját moduljaimat C# vagy más nyelvet használva?**

Jelenleg csak az R nyelvet használhatja új egyéni modulok létrehozásához.

### <a name="r-module"></a>R modul
**Milyen R csomagok érhetők el a Machine Learning Studióban?**

A Machine Learning Studio jelenleg több mint 400 CRAN R-csomagot támogat. Itt megtalálja az összes elérhető csomag [aktuális listáját](http://az754797.vo.msecnd.net/docs/RPackages.xlsx). A lista önálló lekérését az [Extend your experiment with R](extend-your-experiment-with-r.md) (Kísérlet kiterjesztése az R nyelv használatával) című cikkből sajátíthatja el. Ha nem találja a listában a szükséges csomagot, adja meg a csomag nevét a [felhasználó-visszajelzési fórumon](https://go.microsoft.com/fwlink/?LinkId=404231).

**Létre lehet hozni egy egyéni R modult?**

Igen, ehhez az [Egyéni R modul létrehozása az Azure Machine Learningben](custom-r-modules.md) című fejezetben talál további információt.

**Létezik REPL környezet az R nyelvhez?**

Nem létezik REPL- (Read-Eval-Print-Loop) környezet az R nyelvhez a stúdióban.

### <a name="python-module"></a>Python modul
**Létre lehet hozni egy egyéni Python modult?**

Jelenleg nem, de a [Python-szkript végrehajtására][python] szolgáló modulok használatával elérhető ugyanaz az eredmény.

**Létezik REPL környezet a Python nyelvhez?**

A Jupyter notebookok használata lehetséges a Machine Learning Studióban. További információ: [Jupyter notebookok használatának bemutatása az Azure Machine Learning Studióban](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Webszolgáltatás
### <a name="retrain"></a>Újratanítás
**Hogyan működik az Azure Machine Learning modellek szoftveres átképezése?**

A megőrzési API-k használatával. További információ: [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Machine Learning-modellek szoftveres átképezése). A [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/) (Microsoft Azure Machine Learning átképezési bemutató) című cikkben egy mintakód is elérhető.

### <a name="create"></a>Létrehozás
**Helyileg vagy internetkapcsolat nélküli alkalmazással is üzembe helyezhetem a modellt?**

Nem.

**Minden webszolgáltatás esetében számolni kell egy alapkésleltetéssel?**

Tekintse meg az [Azure előfizetésre vonatkozó korlátozások](../../azure-subscription-service-limits.md) című fejezetet.

### <a name="use"></a>Használat
**Milyen esetekben futtassam a prediktív modellemet kötegelt végrehajtási szolgáltatásként kérelem-válasz szolgáltatás helyett?**

Az RRS egy rövid késleltetésű, jelentősen méretezhető webszolgáltatás, amely olyan állapot nélküli modellekhez biztosít felületet, amelyeket a kísérleti környezetben hoztak létre és helyeztek üzembe. A kötegelt végrehajtási szolgáltatás (BES) az adatrekordkötegek aszinkron módon történő pontozását végző szolgáltatás. A BES bemeneti adatai az RRS-ben használt bemeneti adatokhoz hasonlóak. A fő különbség az, hogy a BES több különböző forrásból olvassa be az adatköteget, például az Azure Blob Storage-ból és Azure Table Storage-ből, az Azure SQL Database-ből, a HDInsightból (Hive-lekérdezés) és HTTP-forrásokból. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).

**Hogyan frissíthetem az üzembe helyezett webszolgáltatáshoz használt modellt?**

A prediktív modellek egy már üzembe helyezett szolgáltatás esetében való frissítéséhez módosítsa és futtassa újra a betanított modell létrehozásához és mentéséhez használt kísérletet. Miután a betanított modell újabb verziója elérhetővé válik, a Machine Learning Studio rákérdez, hogy szeretné-e frissíteni a webszolgáltatást. A már üzembe helyezett webszolgáltatás frissítésével kapcsolatos információkért olvassa el a [Machine Learning-webszolgáltatások üzembe helyezését](publish-a-machine-learning-web-service.md) ismertető cikket.

Továbbá használhatja az átképezési API-kat is.
További információ: [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Machine Learning-modellek szoftveres átképezése). A [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/) (Microsoft Azure Machine Learning átképezési bemutató) című cikkben egy mintakód is elérhető.

**Hogyan követhetem figyelemmel az éles környezetben telepített webszolgáltatásaimat?**

Miután telepített egy prediktív modellt, monitorozhatja azt az Azure Machine Learning-webszolgáltatások portálján. Minden telepített szolgáltatásnak van egy saját irányítópultja, ahol az adott szolgáltatásra vonatkozó információkat láthatja. A telepített webszolgáltatások kezelésével kapcsolatos további információkért lásd [a webszolgáltatások az Azure Machine Learning-webszolgáltatások portáljának segítségével történő kezelését](manage-new-webservice.md) és [az Azure Machine Learning-munkaterület kezelését](manage-workspace.md) ismertető cikket.

**Láthatom valahol az RRS/BES elemeim kimenetét?**

Az RRS esetében általában a webszolgáltatási válaszoknál láthatja az eredményt. Emellett az Azure Blob Storage-be is kiírhatja azokat. A BES esetében a kimenet alapértelmezetten egy blobba íródik. Az [Adatok exportálása][export-data] modullal egy adatbázisba vagy egy táblázatba is kiírhatja a kimenetet.

**Lehetséges kizárólag a Machine Learning Studióban létrehozott modelleket használva webszolgáltatást készíteni?**

Nem lehetséges, azonban közvetlenül Jupyter-notebookok és az RStudio segítségével is létrehozhat webszolgáltatásokat.

**Hol találok információkat a hibakódokkal kapcsolatban?**

A hibakódok listáját és azok leírását a [Machine Learning modulok hibakódjai](https://msdn.microsoft.com/library/azure/dn905910.aspx) című fejezetben találja.

## <a name="scalability"></a>Méretezhetőség
**Milyen a webszolgáltatások méretezhetősége?**

Jelenleg az alapértelmezett végpont 20 egyidejű RRS-kérelemmel rendelkezik végpontonként. Ezt végpontonként 200 egyidejű kérelemre is méretezheti, valamint a [Webszolgáltatások méretezése](scaling-webservice.md) alapján minden webszolgáltatást 10 000 végpontra méretezhet webszolgáltatásonként. A BES esetében minden végpont 40 kérelem feldolgozását tudja elvégezni egyidejűleg, illetve, amennyiben a kérelmek száma meghaladja a 40-et, az új kérelmek várólistára kerülnek. A várólistára került kérelmeket automatikusan futtatja a rendszer, amint a lista elkezd kiürülni.

**Az R-munkák csomópontokon vannak elosztva?**

Nem.  

**Mekkora mennyiségű adatot használhatok a betanításhoz?**

A Machine Learning Studióban található modulok 10 GB-nyi számadatot tartalmazó adatkészletet támogatnak a gyakori alkalmazási esetekben. Ha egy modul egynél több bemenetből fogad adatokat, az összes bemenet mérete nem haladhatja meg a 10 GB-ot. A nagyobb adatkészletből a Hive-lekérdezések vagy az Azure SQL Database-lekérdezések, illetve az adatfeldolgozás előtt a [statisztikai tanulási (Learning with Counts)][counts] modulokkal végrehajtott előzetes feldolgozással lehet mintát venni.  

A szolgáltatásnormalizálás során a következő, 10 GB alá korlátozott adattípusok bővíthetők nagyobb adatkészletekké:

* Ritka
* Kategorikus
* Sztringek
* Bináris adatok

A következő modulok 10 GB-nál kisebb adatkészletekre vannak korlátozva:

* Ajánló modulok
* SMOTE (Synthetic Minority Oversampling Technique) modul
* Parancsfájlkezelési modulok: R, Python, SQL
* Olyan modulok, amelyeknél a kimeneti adatok mérete meghaladhatja a bemeneti adatok méretét; például az egyesítés vagy a szolgáltatáskivonatolás
* Nagyszámú ismétlődés esetében végezzen kereszt-ellenőrzést, állítsa be a modell-hiperparamétereket, továbbá végezzen sorszám-regressziót és multi-osztályú osztályozási műveletet

A néhány GB-nál nagyobb adatkészletek esetében a helyi fájlokból való közvetlen feltöltés helyett tanácsos az adatokat az Azure Storage-ba, illetve az Azure SQL Database-be feltöltenie, vagy a HDInsightot használnia erre a célra.

**Létezik bármilyen korlátozás a vektorok méretét illetően?**

A sorokra és oszlopokra mind a Maximális Int .NET korlátozás vonatkozik: 2, 147, 483, 647.

**Módosítható a webszolgáltatások futtatásához használt virtuális gép mérete?**

Nem.  

## <a name="security-and-availability"></a>Biztonság és elérhetőség
**Alapértelmezés szerint kiknek van hozzáférése a webszolgáltatások HTTP-végpontjához? Hogyan korlátozhatom a végponthoz való hozzáférést?**

A webszolgáltatások telepítése után egy alapértelmezett végpont kerül létrehozásra az adott szolgáltatáshoz. Az alapértelmezett végpont az API-kulcs használatával hívható meg. Saját kulcsukat használva további végpontokat adhat hozzá a Web Services portálon keresztül vagy a Web Service Management API-k programozott használatával. A webszolgáltatásokhoz való meghíváshoz hívóbetűk szükségesek. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).

**Mit kell tenni, ha nem találom az Azure Storage-fiókomat?**

A munkafolyamat végrehajtása során a Machine Learning Studio a felhasználók által megadott Azure Storage-fiók segítségével menti a köztes adatokat. Ez a tárfiók egy munkaterület létrehozása során kerül a Machine Learning Studióba. Ha a munkaterület létrehozását követően a tárfiókot törlik és többé nem lesz elérhető, a munkaterület működése minden benne futó kísérlettel együtt leáll.

Ha véletlenül törölte a tárfiókot, hozzon létre egy új tárfiókot ugyanazzal a névvel, a törölt tárfiókkal megegyező régióban. Ezt követően szinkronizálja újra a hívóbetűt.

**Mi történik, ha a tárfiókom hívóbetűje nincs szinkronizálva?**

A munkafolyamat végrehajtása során a Machine Learning Studio a felhasználók által megadott Azure Storage-fiók segítségével tárolja a köztes adatokat. Ez a tárfiók egy munkaterület létrehozása során kerül a Machine Learning Studióba, és a hívóbetűk ehhez a munkaterülethez lesznek társítva. Ha a munkaterület létrehozását követően módosítják a hívóbetűket, a munkaterület többé nem lesz képes hozzáférni a tárfiókhoz. A munkamenet működése minden benne futó kísérlettel együtt leáll.

Ha módosította a tárfiók hozzáférési kulcsát, az Azure Portal használatával szinkronizálja újra a hozzáférési kulcsot a munkaterületen.  

## <a name="support-and-training"></a>Támogatás és betanítás
**Hol kaphatok képzést az Azure Machine Learning használatáról?**

Az [Azure Machine Learning dokumentációs központjában](https://azure.microsoft.com/services/machine-learning/) oktatóvideókat és útmutatókat talál az Azure Machine Learning használatáról. Ezek a részletes útmutatók bemutatják a szolgáltatásokat, és elmagyarázzák az adatimportálás adatelemzési életciklusát, az adattisztítást, a prediktív modellek létrehozását és az Azure Machine Learning segítségével történő üzembe helyezésüket.

A Machine Learning központját folyamatosan új anyagokkal bővítjük. A Machine Learning központba további oktatóanyagok feltöltését a [felhasználó-visszajelzési fórumon](https://windowsazure.uservoice.com/forums/257792-machine-learning) kérelmezheti.

A [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning) további oktatóanyagokat is kínál.

**Hol kaphatok támogatást az Azure Machine Learninghez?**

Az Azure Machine Learninghez való műszaki támogatásért lépjen az [Azure-támogatás](https://azure.microsoft.com/support/options/) oldalára, és válassza a **Machine Learning** opciót.

Az Azure Machine Learning egy közösségi fórummal is rendelkezik az MSDN-en, ahol az Azure Machine Learninggel kapcsolatos kérdéseit teheti fel. A fórumot az Azure Machine Learning fejlesztőcsapata figyeli. Tekintse meg az [Azure-fórumokat](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Számlázással kapcsolatos kérdések
**Milyen díjszámítási módszer vonatkozik a Machine Learning szolgáltatásra?**

Az Azure Machine Learning két összetevőből áll: a Machine Learning Studióból és a Machine Learning-webszolgáltatásokból.

Amíg kipróbálja a Machine Learning Studiót, használhatja az ingyenes tarifacsomagot. Az Ingyenes szint lehetőséget kínál korlátozott kapacitású klasszikus webszolgáltatások üzembe helyezésére is.

Ha úgy véli, hogy az Azure Machine Learning felel meg a legjobban igényeinek, előfizethet a Standard szintre. Az előfizetéshez regisztrálnia kell egy Microsoft Azure-előfizetést.

A Standard szinten havonta kell fizetnie a Machine Learning Studióban megadott egyes munkaterületekért. Amikor kísérletet futtat a Studióban, fizetnie kell a kísérlet elvégzéséhez felhasznált számítási erőforrásokért. Amikor klasszikus webszolgáltatást helyez üzembe, a használat alapján kell fizetnie a tranzakciókért és az üzemórákért.

Az új (Resource Manager-alapú) webszolgáltatások esetében bevezettük a számlázási csomagokat, amelyek megkönnyítik a költségek tervezését. A szintenként meghatározott díjak kedvezményesebb árakat kínálnak a nagy kapacitást igénylő ügyfelek számára.

A csomag létrehozásakor rögzített összeg fizetését kell vállalnia, amelyért az API-számítási üzemórákat és -tranzakciókat adott mértékig fedező szolgáltatási keretet kap. Ha később nagyobb keretre van szüksége, további példányokat adhat a csomaghoz. Ha lényegesen nagyobb keretre van szüksége, érdemes magasabb szinthez tartozó csomagra áttérni, mivel így kedvezményesebb áron juthat hozzá a szükséges erőforrásokhoz.

Ha felhasználta a meglévő példányok szolgáltatási keretét, a további használatért a csomagszint szerinti többlethasználati díjat kell fizetnie.

> [!NOTE]
A szolgáltatási keretet a rendszer 30 naponta újra kiosztja, és a fel nem használt szolgáltatási keret nem vihető át a következő időszakra.

A számlázással és a díjszabással kapcsolatos további információkért olvassa el a [Machine Learning díjszabás](https://azure.microsoft.com/pricing/details/machine-learning/) című fejezetet.

**Létezik a Machine Learningnek ingyenes próbaverziója?**

 Az Azure Machine Learning ingyenes előfizetési lehetőséget is kínál, amelyről további információkat a [Machine Learning díjszabását](https://azure.microsoft.com/pricing/details/machine-learning/) ismertető cikkben talál. A Machine Learning Studióhoz 8 órás, gyors kipróbálási időszak érhető el, amikor bejelentkezik a [Machine Learning Studióba](https://studio.azureml.net/?selectAccess=true&o=2).

 Emellett az Azure ingyenes próbaverziójára történő regisztrációt követően bármely Azure-szolgáltatást kipróbálhatja egy hónapig. Ha többet szeretne tudni az Azure ingyenes próbaverziójáról, olvassa el az [Azure Ingyenes próbaverziójára vonatkozó GYIK-et](https://azure.microsoft.com/pricing/free-trial-faq/).

**Mit nevezünk tranzakciónak?**

A tranzakciók azok az API-hívások, amelyekre az Azure Machine Learning válaszol. A kérés-válasz szolgáltatás (RRS) és a kötegelt végrehajtási szolgáltatás (BES) által küldött hívások alapján létrejövő tranzakciókat a rendszer összesíti; ezekért a számlázási csomag szerint fizetnie kell.

**A csomagba foglalt tranzakciómennyiséget RRS- és BES-tranzakciókra is felhasználhatom?**

Igen, a rendszer összesíti az RRS- és BES-tranzakciókat, és ezekért kell fizetnie a számlázási csomag szerint.

**Mik azok az API számítási órák?**

Az API-üzemóra az a számlázási egység, amely azon az időtartamon alapul, amennyibe az API-hívásnak beletelik, hogy a Machine Learning számítási erőforrásait felhasználva lefusson. A hívásokat a rendszer a számlázás céljából összesíti.

**Általában milyen hosszú időt vesz igénybe egy jellemző éles üzemi API-hívás?**

A különböző éles üzemi API-hívások időtartama között jelentős eltérés lehet, néhány száz ezredmásodperctől több másodpercig is terjedhetnek. Egyes API-hívások az adatfeldolgozási és a gépi tanulási modell összetettségétől függően akár perceket is igénybe vehetnek. Az éles üzemi API-hívások időtartamának megbecsülésére a legjobb módszer, ha a Machine Learning szolgáltatás segítségével teszteli a modellt.

**Mik a számítási órák a Studióban?**

A számítási óra a Studio számlázási egysége, amelynek alapján fizetnie kell azért az összesített időtartamért, amíg kísérletei használták a Studio számítási erőforrásait.

**Mit jelent az új (Azure Resource Manager-alapú) webszolgáltatásokban a fejlesztés + tesztelés szint?**

A Resource Manager-alapú webszolgáltatások több szintet biztosítanak, amelyet felhasználhat számlázási csomagjai kialakításához. A fejlesztés + tesztelés tarifacsomag korlátozott szolgáltatási keretet biztosít, amely lehetővé teszi, hogy webszolgáltatásként tesztelje a kísérletét, további költségek nélkül. Ez lehetőséget nyújt a gyakorlati kipróbálásra.

**Kell külön fizetnem a tárterületért?**

A Machine Learning Ingyenes szintje nem igényel különálló tárterületet, illetve nem is teszi lehetővé annak használatát. A Machine Learning Standard szintjénél a felhasználóknak Azure-tárfiókkal kell rendelkezniük. Az Azure-tárterületért [külön kell fizetnie](https://azure.microsoft.com/pricing/details/storage/).

**Támogatja a Machine Learning a magas szintű rendelkezésre állást?**

Igen. Részletekért és a szolgáltatói szerződés (SLA) ismertetéséért lásd a [Machine Learning díjszabását](https://azure.microsoft.com/pricing/details/machine-learning/) ismertető cikket.

**Konkrétan milyen számítási erőforrásokon fognak futni az éles üzemi API-hívásaim?**

A Machine Learning szolgáltatás egy több-bérlős szolgáltatás. A háttérrendszeren ténylegesen felhasznált számítási erőforrások eltérőek, és a teljesítmény és az előre látható használat alapján lettek optimalizálva.

### <a name="management-of-new-resource-manager-based-web-services"></a>Az új (Resource Manager-alapú) webszolgáltatások kezelése
**Mi történik, ha törlöm a csomagot?**

A csomagot eltávolítjuk az előfizetéséből, a használatot pedig időarányosan számlázzuk ki.

> [!NOTE]
Ha a csomagot használja egy webszolgáltatás, nem lehet törölni. Ha törölni szeretné a csomagot, rendelje új csomaghoz a webszolgáltatást, vagy törölje a webszolgáltatást.

**Mik azok a csomagpéldányok?**

A csomagpéldány a szolgáltatási keret egysége, amelyet a számlázási csomaghoz adhat. Amikor kiválasztja a számlázási szintet a számlázási csomaghoz, egy darab példányt kap. Ha többre van szüksége, további példányokat adhat csomagjához a kiválasztott számlázási szintről.

**Legfeljebb hány csomagpéldányt használhatok?**

A fejlesztés + tesztelés tarifacsomagból egy példány szerepelhet az előfizetésben.

A Standard S1, a Standard S2 és a Standard S3 szinten annyi példányt vehet fel, amennyire csak szüksége van.

> [!NOTE]
A tervezett használat mértékétől függően költséghatékonyabb megoldás lehet, ha ahelyett, hogy újabb példányokat adna az aktuális szinthez, inkább magasabb keretszintre tér át.

**Mi történik, ha módosítom a csomagom szintjét (magasabbra vagy alacsonyabbra)?**

Töröljük a régi csomagot, a használatot pedig időarányosan kiszámlázzuk. A számlázási időszak fennmaradó időtartamára az új, magasabb vagy alacsonyabb szint teljes keretét tartalmazó új csomag jön létre.

> [!NOTE]
A szolgáltatási keret az adott időszakra vonatkozik, a fel nem használt erőforrások nem vihetők át a következő időszakra.

**Mi történik, ha növelem a csomagban lévő példányok számát?**

A keretek arányos alapon érhetők el, és az érvénybe lépésük akár 24 órát is igénybe vehet.

**Mi történik, ha törlök egy példányt a csomagból?**

A példányt eltávolítjuk az előfizetéséből, a használatot pedig időarányosan számlázzuk ki.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Regisztráció új (Resource Manager-alapú) webszolgáltatás-csomagokra
**Hogyan tudok regisztrálni a csomagokra?**

Számlázási csomagot két különböző módon hozhat létre.

A Resource Manager-alapú webszolgáltatás első üzembe helyezésekor választhat egy meglévő csomagot, vagy létrehozhat egy új csomagot.

Az így létrehozott csomagok a felhasználó alapértelmezett régiójához fognak tartozni, és a webszolgáltatást is ebben a régióban helyezzük üzembe.

Ha az alapértelmezett régión kívül szeretne szolgáltatásokat üzembe helyezni, a szolgáltatás üzembe helyezése előtt célszerű meghatározni a számlázási csomagokat.

Ebben az esetben jelentkezzen be az Azure Machine Learning-webszolgáltatások portáljára, és nyissa meg a Csomagok lapot. Itt hozzáadhat és törölhet csomagokat, valamint módosíthatja a meglévő csomagokat.

**Melyik csomaggal érdemes kezdeni?**

Javasoljuk, hogy először válassza a Standard S1 csomagot, és kövesse figyelemmel, hogy mennyi erőforrást használnak szolgáltatásai. Ha úgy találja, hogy gyorsan kimerül a szolgáltatási keret, adjon hozzá további példányokat, vagy a kedvezményesebb fizetés érdekében váltson magasabb szintre. A számlázási csomagot bármikor tetszés szerint módosíthatja.

**Mely régiókban érhetők el az új csomagok?**

Az új számlázási csomagok abban a három éles régióban érhetők el, amelyekben támogatjuk az új webszolgáltatásokat:

* USA déli középső régiója
* Nyugat-Európa
* Délkelet-Ázsia

**Több régióban is működnek webszolgáltatásaim. Minden régióhoz külön csomagot kell létrehoznom?**

Igen. A csomagok ára régiónként változik. Ha egy webszolgáltatást egy másik régióban is üzembe helyez, az adott régióhoz tartozó csomagot kell hozzárendelnie. További információért lásd a [régiónként elérhető termékeket]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>Új webszolgáltatások: Többletköltségek
**Hogyan tudom ellenőrizni, hogy túlléptem-e a webszolgáltatások használati keretét?**

Az Azure Machine Learning webszolgáltatások portál Csomagok lapján az összes csomag által használt erőforrásokat megtekintheti. Jelentkezzen be a portálra, és kattintson a **Csomagok** menüpontra.

A táblázat **Tranzakciók** és **Számítás** oszlopában láthatja a csomag szolgáltatási keretét, és azt, hogy az adott pillanatig annak hány százalékát használta fel.

**Mi történik, ha a fejlesztés + tesztelés szint esetében kimerítem a szolgáltatási keretet?**

A fejlesztés + tesztelés tarifacsomaghoz rendelt szolgáltatások ilyenkor leállnak a következő periódusig, vagy amíg át nem helyezi azokat egy fizetős szintre.

**Hogyan számítják ki a kérés-válasz (RRS) és a kötegelt (BES) számítási feladatokért fizetendő összeget a klasszikus webszolgáltatásoknál és az új (Resource Manager-alapú) webszolgáltatások többletköltségeinél?**

RRS számítási feladat esetén fizetni kell minden API-tranzakciós hívásért, és a kérelmekhez szükséges számítási időért. Az éles RRS API-tranzakciók díjának a számítása a következőképpen történik: az API-hívások teljes száma szorozva 1000 tranzakció díjával (az egyes tranzakciók szerint arányosítva). Az éles RRS API-számítási üzemórák díjának a számítása a következőképpen történik: az egyes API-hívások futtatásához szükséges idő szorozva az API-tranzakciók teljes számával szorozva az egyes éles API számítási órák díjával.

A Standard S1 csomagban érvényes többletköltségekkel számolva 1 000 000, egyenként 0,72 másodpercen át futó API-tranzakcióért 500 USD-t (1 000 000 * 0,50 USD/1000 API-hívás) kell fizetni az éles API-tranzakciókért magukért, és 400 USD-t (1 000 000 * 0,72 másodperc * 2 USD/óra) az éles üzemi API-k számítási üzemidejéért, azaz az összköltség 900 USD.

A BES-alapú számítási feladatokért ugyanígy kell fizetni. Az API-tranzakciók költsége azonban az elküldött kötegelt feladatok számához igazodik, a számítási költségek pedig az ezen kötegelt feladatok elvégzéséhez szükséges számítási időn alapulnak. Az éles BES API-tranzakciók díjának a számítása a következőképpen történik: az elküldött feladatok teljes száma szorozva 1000 tranzakció díjával (az egyes tranzakciók szerint arányosítva). Az éles BES API-számítási üzemórák díjának a számítása a következőképpen történik: a feladat egyes sorainak a futtatásához szükséges idő szorozva a feladat egyes sorainak a számával szorozva a feladatok teljes számával szorozva az egyes éles API számítási órák díjával. A Machine Learning számológépének használata során a tranzakcióhoz tartozó érték a beküldeni tervezett feladatok számát jelöli, a tranzakciónkénti időt tartalmazó mező pedig az egyes feladatokban szereplő összes sor lefutásához szükséges időt mutatja.

Tételezzük fel, hogy a Standard S1 csomagban érvényes többletköltségekkel számolva napi 100 feladatot ad be, és ezek mindegyike 500, egyenként 0,72 másodpercen át futó sort tartalmaz. A havi többletköltség 1,55 USD (napi 100 feladat = 3100 feladat/hónap * 0,50 USD/1000 API-tranzakció) az éles API-tranzakciókért, és 620 USD (500 sor * 0,72 másodperc * 3100 feladat * 2 USD/óra) az éles üzemi API-k üzemórájáért, azaz összesen 621,55 USD.

### <a name="azure-machine-learning-classic-web-services"></a>Az Azure Machine Learning klasszikus webszolgáltatásai
**Elérhető még a használatalapú modell?**

Igen, az Azure Machine Learning továbbra is használható a klasszikus webszolgáltatásokkal is.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Az Azure Machine Learning Ingyenes és Standard szintje
**Mit tartalmaz az Azure Machine Learning Ingyenes szintje?**

Az Azure Machine Learning Ingyenes szintje arra szolgál, hogy részletesen bemutassa az Azure Machine Learning Studio szolgáltatást. A bejelentkezéshez mindössze egy Microsoft-fiókra van szükség. Az Ingyenes szint [Microsoft-fiókonként](https://account.microsoft.com/account) egy Azure Machine Learning Studio-munkaterülethez nyújt ingyenes hozzáférést. Ez a szint 10 GB méretű tárhely használatát, valamint a modellek átmeneti API-ként történő üzembe helyezését teszi lehetővé. Az Ingyenes szinthez tartozó számítási feladatokra nem vonatkozik SLA, ezek csak fejlesztési és személyes célokra használhatók. 

Az ingyenes szinthez tartozó munkaterületekre az alábbi korlátozások érvényesek:

* A számítási feladatok nem tudják elérni az adatokat az SQL Servert futtató helyszíni kiszolgálóhoz való csatlakozással.
* Nem helyezhet üzembe új Resource Manager-alapú webszolgáltatást.


**Mit tartalmaznak az Azure Machine Learning Standard szintjei és csomagjai?**

Az Azure Machine Learning Standard szintje az Azure Machine Learning Studio fizetős változatát tartalmazza. Az Azure Machine Learning Studio havi díjának számlázása munkaterület/hónap alapon történik, és a részhónapokért időarányos módon kell fizetni. Az Azure Machine Learning Studióban eltöltött kísérletezési idő (óra) után az aktív kísérletezéssel töltött üzemóránként számítunk fel díjat. A nem teljes órákért időarányos módon kell fizetni.  

Az Azure Machine Learning API szolgáltatásnál a fizetendő összeget az határozza meg, hogy a klasszikus webszolgáltatásról vagy az új (Resource Manager-alapú) webszolgáltatásról van szó.

Az alábbi díjakat munkaterületenként összesítik az előfizetéshez kapcsolódóan.

* Machine Learning-munkaterület előfizetés: a Machine Learning-munkaterület egy havi díjas előfizetés, amely hozzáférést biztosít egy Machine Learning Studio-munkaterülethez. Az előfizetés szükséges a kísérleteknek a Studióban történő futtatásához és az éles API-k használatához.
* Studio-kísérletezési idő (óra): ez az érték a Machine Learning Studióban futtatott kísérletek, valamint az éles üzemi API-hívások átmeneti környezetben való futtatása során elvégzett számítási feladatokkal kapcsolatos díjakat összesíti.
* Az adatokat elérheti a tanítási és pontozási modellekben egy SQL Servert futtató helyszíni kiszolgálóhoz való csatlakozással.
* Klasszikus webszolgáltatásoknál:
  * Számítási üzemidő éles üzemi API-n (óra): ez az érték az éles üzemben futó webszolgáltatásokért fizetendő számítási díjakat adja meg.
  * Éles üzemi API-tranzakciók (ezer db): ez az érték az éles üzemi webszolgáltatás felé irányuló hívásonkénti díjakat adja meg.

A fenti díjakon kívül, a Resource Manager-alapú webszolgáltatás esetén a díjak a választott csomagban vannak összesítve:

* Standard S1/S2/S3 API-csomag (egység): Ez az érték a Resource Manager-alapú webszolgáltatásokhoz kiválasztott példány típusát adja meg.
* Standard S1/S2/S3 keretét túllépő API-számítási üzemóra: Ez az érték azt adja meg, hogy mennyi számítási feladatokkal kapcsolatos díj merült fel azt követően, hogy az éles üzemben futó Resource Manager-alapú webszolgáltatással felhasználta a meglévő példányokban elérhető szolgáltatási keretet. A kereten felüli használatért az S1/S2/S3 csomagszinthez tartozó feláras díj alapján kell fizetni.
* Standard S1/S2/S3 keretét túllépő API-tranzakciószám (1000-es egységekben): Ez az érték azt adja meg, hogy mennyi díj merült fel az éles üzemben futó Resource Manager-alapú webszolgáltatás felé irányuló hívásonként azt követően, hogy a meglévő példányokban elérhető szolgáltatási keret kimerült. A kereten felüli használatért az S1/S2/S3 csomagszinthez tartozó feláras díj alapján kell fizetni.
* Szolgáltatási keretbe foglalt API-számítási üzemóra: A Resource Manager-alapú webszolgáltatásoknál ez az érték jelzi a szolgáltatási keretbe foglalt API-számítási üzemórákat.
* Szolgáltatási keretbe foglalt API-tranzakció (1000-es egységekben): A Resource Manager-alapú webszolgáltatásoknál ez az érték jelzi a szolgáltatási keretbe foglalt API-tranzakciókat.

**Hogy tudok regisztrálni az Azure Machine Learning Ingyenes szintjére?**

Mindössze egy Microsoft-fiókra van szükség. Nyissa meg az [Azure Machine Learning kezdőlapját](https://azure.microsoft.com/services/machine-learning/), majd kattintson az **Első lépések** elemre. Jelentkezzen be Microsoft-fiókjával, és a rendszer létrehozza az Ön számára az Ingyenes szinthez tartozó munkaterületet. Ezt követően azonnal elkezdheti felfedezni a szolgáltatást, és létrehozni Machine Learning-kísérleteit.

**Hogy tudok regisztrálni az Azure Machine Learning Standard szintjére?**

Ahhoz, hogy Standard Machine Learning-munkaterületet hozhasson létre, Azure-előfizetéssel kell rendelkeznie. Regisztrálhat az Azure 30 napos, ingyenes próba-előfizetésére, és később frissíthet fizetős Azure-előfizetésre, vagy akár egyből megvásárolhatja az Azure-előfizetést. Ha megkapta a hozzáférést az előfizetéshez, a Microsoft Azure Portalból létrehozhatja a Machine Learning-munkaterületet. Tekintse meg a [részletes útmutatót](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Másik lehetőség, ha egy már meglévő Standard Machine Learning-munkaterület tulajdonosa elérést ad Önnek a munkaterületéhez.

**Beállíthatom az Ingyenes szinten saját Azure Blob Storage-fiókom használatát?**

Nem. A Standard szint a Machine Learning azon változata, amely a szintek bevezetése előtt elérhető volt.

**Van rá lehetőség az Ingyenes szinten, hogy API-ként üzembe helyezzem gépi tanulási modelljeimet?**

Igen, az Ingyenes szinten átmeneti API-szolgáltatásokba helyezheti üzembe gépi tanulási modelljeit. Ahhoz azonban már a Standard szintre lesz szüksége, hogy az átmeneti API-szolgáltatást éles üzemben használhassa, illetve hogy beszerezhesse az üzembe helyezett szolgáltatáshoz kapcsolódó éles végpontot.

**Mi a különbség az Azure ingyenes próbaverziója és az Azure Machine Learning Ingyenes szintje között?**

A [Microsoft Azure ingyenes próbaverziója](https://azure.microsoft.com/free/) révén az Azure bármely szolgáltatásában egy hónapig felhasználható kreditekhez juthat. Az Azure Machine Learning szolgáltatás Ingyenes szintjével pedig folyamatosan hozzáférhet az Azure Machine Learninghez nem éles számítási feladatok esetén.

**Hogy tudom átvinni a kísérleteimet az Ingyenes szintről a Standard szintre?**

Kísérletek átmásolása az Ingyenes szintről a Standard szintre:

1. Jelentkezzen be az Azure Machine Learning Studióba, és győződjön meg róla, hogy a felső navigációs sávban, a munkaterület-választóban mind az Ingyenes, mind a Standard munkaterület látható.
2. Ha a Standard munkaterületen tartózkodik, váltson át az Ingyenes munkaterületre.
3. A kísérletek listanézetében válassza ki a másolni kívánt kísérletet, majd kattintson a **Másolás** parancsgombra.
4. A megnyíló párbeszédpanelen jelölje ki a Standard munkaterületet, majd kattintson a **Másolás** gombra.
   A kísérlettel együtt minden kapcsolódó adatkészlet, betanított modell stb. másolása megtörténik a Standard munkaterületre.
5. Futtassa újra a kísérletet, és tegye közzé újból a webszolgáltatást a Standard munkaterületen.

### <a name="studio-workspace"></a>Studio-munkaterület
**A különböző munkaterületekhez különböző számlák tartoznak?**

A munkaterületek díjai az egyes vonatkozó értékek szerint vannak részletezve a számlán.

**Konkrétan milyen számítási erőforrásokon fognak futni a kísérleteim?**

A Machine Learning szolgáltatás egy több-bérlős szolgáltatás. A háttérrendszeren ténylegesen felhasznált számítási erőforrások eltérőek, és a teljesítmény és az előre látható használat alapján lettek optimalizálva.

### <a name="guest-access"></a>Vendéghozzáférés
**Mit jelent a vendéghozzáférés az Azure Machine Learning Studióban?**

A vendéghozzáférés a szolgáltatás korlátozott kipróbálására nyújt lehetőséget. Ingyenesen, hitelesítő adatok megadása nélkül hozhat létre és futtathat kísérleteket az Azure Machine Learning Studióban. A vendégként végzett munkamenetek nem perzisztensek (nem lehet őket menteni), maximális időtartamuk pedig nyolc óra. További korlátozások: nincs R- és Python-támogatás, nincs lehetőség átmeneti API-k használatára, illetve az adatkészletek mérete és a tárolókapacitás is korlátozott. Ezzel szemben a Microsoft-fiókkal bejelentkező felhasználók teljes hozzáférést kapnak a Machine Learning Studio korábban leírt Ingyenes szintjéhez, amely egy perzisztens munkaterületet és átfogóbb képességeket tartalmaz. Az ingyenes Machine Learning szolgáltatást a következőképpen veheti igénybe: kattintson a **Get started** (Első lépések) elemre a [https://studio.azureml.net](https://studio.azureml.net) webhelyen, majd válassza a **Guest Access** (Vendéghozzáférés) lehetőséget, vagy jelentkezzen be egy Microsoft-fiókkal.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
