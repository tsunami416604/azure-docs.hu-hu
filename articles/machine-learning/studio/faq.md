---
title: A Machine Learning Studio – gyakori kérdések (GYIK)
titleSuffix: Azure Machine Learning Studio
description: 'Azure Machine Learning Studio: Gyakori kérdések a számlázás, képességek és a zökkenőmentes prediktív modellezést támogató felhőalapú szolgáltatás korlátozásai.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462279"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Az Azure Machine Learning Studio – gyakori kérdések: képességei és korlátozásai
Az alábbiakban néhány gyakori kérdést (GYIK) és azok válaszait olvashatja az Azure Machine Learning szolgáltatással kapcsolatban, amely egy, a webszolgáltatásokon keresztül végrehajtott prediktív modellezést és a megoldások üzembe helyezését célzó felhőalapú szolgáltatás. 

## <a name="general-questions"></a>Általános kérdések
**Mi a Machine Learning Studio?**

A Machine Learning Studio egy webböngészőn keresztül elérhető, és húzza a vászonra környezet. A Machine Learning Studio vizuális összeállítási interfészekkel rendelkező modulok sorát tartalmazza, amelyek lehetővé teszik, hogy egy teljes körű adatelemezési munkafolyamatot építsen egy kísérlet formájában.

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

* Kérés-válasz szolgáltatás (RRS): Egy alacsony késleltetésű, nagy mértékben skálázható szolgáltatás, amely interfészt létrehozott és a Machine Learning Studio használatával üzembe helyezett állapot nélküli modellekhez biztosít.
* Kötegelt végrehajtási szolgáltatás (BES): Aszinkron szolgáltatás, amely adatrekordok batch.

A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Írhat például egy alkalmazást C#, R vagy Python nyelven azzal a mintakóddal, amelyet a rendszer a webszolgáltatás üzembe helyezésekor hozott létre.

A mintakód elérhető a következő helyről:
- A webszolgáltatás Consume (Felhasználás) lapja az Azure Machine Learning-webszolgáltatások portálján
- A Machine Learning Studio webszolgáltatási irányítópultján található API-súgóoldal

Az Önnek létrehozott Microsoft Excel-munkafüzetmintát is használhatja, amely a Machine Learning Studióban, a webszolgáltatás irányítópultján érhető el.

**Mik az Azure Machine Learning főbb újdonságai?**

A legújabb frissítésekkel kapcsolatban lásd [az Azure Machine Learning újdonságait](../../active-directory/fundamentals/whats-new.md) ismertető cikket.

## <a name="import-and-export-data-for-machine-learning"></a>Adatok importálása és exportálása a Machine Learninghez
**Milyen adatforrásokat támogat a Machine Learning?**

A Machine Learning Studióval való kísérletezéshez háromféleképpen tölthet le adatokat:

- Helyi fájl feltöltése adatkészletként
- Modul használata az adatok felhőalapú adatszolgáltatásokból való importálásához
- Egy másik kísérletből mentett adatkészlet importálása

További információ a támogatott fájlformátumokról: [Import training data into Machine Learning Studio](import-data.md) (Betanítási adatok betöltése a Machine Learning Studióba).

### <a id="ModuleLimit"></a>Mekkora adatkészleteket kezelhetek a moduljaimmal?
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

### <a id="UploadLimit"></a>Mik az adatfeltöltés korlátai?
A néhány GB-nál nagyobb adatkészletek esetében a helyi fájlokból való közvetlen feltöltés helyett tanácsos az adatokat az Azure Storage-ba, illetve az Azure SQL Database-be feltöltenie, vagy az Azure HDInsightot használnia erre a célra.

**Az Amazon S3-ból is lehetséges az adatbeolvasás?**

Ha kisebb mennyiségű adatot szeretne közzétenni egy HTTP URL-címen keresztül, használja az [Adatok importálása][import-data] modult. Nagyobb mennyiségű adat esetén először vigye át az adatokat az Azure Storage-ba, majd az [Adatok importálása][import-data] modullal adja hozzá az adatokat a kísérlethez.
<!--

<SEE CLOUD DS PROCESS>
-->

**Létezik beépített képbeviteli képesség?**

A képbeviteli képességről a [Képek importálása][image-reader] útmutatóban talál további információt.

## <a name="modules"></a>Modulok
**Az általam keresett algoritmus, adatforrás, adatformátum vagy adat-átalakítási művelet nem található az Azure Machine Learning Studióban. Milyen lehetőségeim vannak?**

Az általunk követett funkciókéréseket a [felhasználó-visszajelzési fórumot](https://go.microsoft.com/fwlink/?LinkId=404231) meglátogatva tekintheti meg. Amennyiben a keresett képességet már kérelmezték, adja hozzá szavazatát a már meglévő kérelemhez. Ha a keresett képesség még nem létezik, hozzon létre egy új kérelmet. Kérelmének állapotát is ezen a fórumon tekintheti meg. A listát folyamatosan követjük, és rendszeresen frissítjük a funkciók rendelkezésre állásának állapotát. Továbbá, a beépített R- és Python-támogatással szükség esetén egyénre szabott átalakításokat is létrehozhat.

**Hozzáadhatom a már létező kódomat a Machine Learning Studióhoz?**

Igen, hozzáadhatja a már meglévő R vagy Python kódját a Machine Learning Studióhoz, lefuttathatja azt az Azure Machine Learning tanulókkal együtt ugyanazon kísérletben, és az Azure Machine Learningen keresztül webszolgáltatásként is üzembe helyezheti a megoldást. További információk: [Kísérletének kiterjesztése az R nyelv használatával](extend-your-experiment-with-r.md), valamint [A Python gépi tanulási parancsfájlok végrehajtása az Azure Machine Learning Studióban](execute-python-scripts.md).

**Lehetséges egy [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)-hez hasonló dolgot használni a modellek meghatározásához?**

Nem, a PMML (Predictive Model Markup Language) használata nem támogatott. Egyéni R- és Python-kódokat használhat a modulok meghatározásához.

**Hány modult hajthatok végre párhuzamosan a kísérletemben?**  

Egy kísérletben legfeljebb négy modult hajthat végre párhuzamosan.

## <a name="data-processing"></a>Adatfeldolgozás
**Van olyan képesség (az R megjelenítéseken kívül), amellyel lehetséges az adatok interaktív megjelenítése egy kísérleten belül?**

A modul kimenetére kattintva megjelenítheti az adatokat és megtekintheti a statisztikákat.

**Az eredmények és az adatok áttekintésekor a sorok és oszlopok száma korlátozott a böngészőben. Hogy miért?**

Mivel nagy mennyiségű adatokat küldhet a böngészőbe, a Machine Learning Studio lelassulásának elkerülése érdekében korlátozva van az adatok mérete. Az összes adat/eredmény megjelenítéséhez célszerű letölteni az adatokat, és Excelt vagy hasonló eszközt használni erre a célra.

## <a name="algorithms"></a>Algoritmusok
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

## <a name="r-module"></a>R modul
**Milyen R csomagok érhetők el a Machine Learning Studióban?**

A Machine Learning Studio jelenleg több mint 400 CRAN R-csomagot támogat. Itt megtalálja az összes elérhető csomag [aktuális listáját](http://az754797.vo.msecnd.net/docs/RPackages.xlsx). A lista önálló lekérését az [Extend your experiment with R](extend-your-experiment-with-r.md) (Kísérlet kiterjesztése az R nyelv használatával) című cikkből sajátíthatja el. Ha nem találja a listában a szükséges csomagot, adja meg a csomag nevét a [felhasználó-visszajelzési fórumon](https://go.microsoft.com/fwlink/?LinkId=404231).

**Létre lehet hozni egy egyéni R modult?**

Igen, ehhez az [Egyéni R modul létrehozása az Azure Machine Learningben](custom-r-modules.md) című fejezetben talál további információt.

**Létezik REPL környezet az R nyelvhez?**

Nem létezik REPL- (Read-Eval-Print-Loop) környezet az R nyelvhez a stúdióban.

## <a name="python-module"></a>Python modul
**Létre lehet hozni egy egyéni Python modult?**

Jelenleg nem, de a [Python-szkript végrehajtására][python] szolgáló modulok használatával elérhető ugyanaz az eredmény.

**Létezik REPL környezet a Python nyelvhez?**

A Jupyter notebookok használata lehetséges a Machine Learning Studióban. További információ: [Jupyter notebookok használatának bemutatása az Azure Machine Learning Studióban](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Webszolgáltatás

**Hogyan működik az Azure Machine Learning modellek szoftveres átképezése?**

A megőrzési API-k használatával. További információ: [Retrain Machine Learning models programmatically](retrain-models-programmatically.md) (Machine Learning-modellek szoftveres átképezése). A [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/) (Microsoft Azure Machine Learning átképezési bemutató) című cikkben egy mintakód is elérhető.

**Helyileg vagy internetkapcsolat nélküli alkalmazással is üzembe helyezhetem a modellt?**

Nem.

**Minden webszolgáltatás esetében számolni kell egy alapkésleltetéssel?**

Tekintse meg az [Azure előfizetésre vonatkozó korlátozások](../../azure-subscription-service-limits.md) című fejezetet.

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

Sorok és oszlopok a következők mindegyike korlátozott maximális int .NET korlátozás: 2,147,483,647.

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


## <a name="billing-questions"></a>Számlázással kapcsolatos kérdések

A számlázással és a díjszabással kapcsolatos információkért olvassa el a [Machine Learning díjszabás](https://azure.microsoft.com/pricing/details/machine-learning/) című fejezetet.


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
