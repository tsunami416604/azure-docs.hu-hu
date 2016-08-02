<properties
    pageTitle="Mire használható az Azure Machine Learning funkciója? | Microsoft Azure"
    description="A cikk a teljes körűen felügyelt Machine Learning szolgáltatás alapjait mutatja be. Ez egy felhőtechnológia, amelynek segítségével különböző megoldásokat hozhat létre, léptethet működésbe, valamint bevételszerzésre is használhat."
    keywords="what is machine learning,cloud technology,predictive,what is predictive analytics,operationalize"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/28/2016"
    ms.author="cgronlun;tedway;olgali"/>


# A Microsoft Azure Machine Learning bemutatása

## Mit jelent a gépi tanulás funkció?

A gépi tanulás számítógépeken futó prediktív modelleket futtatva és a meglévő adatokból tanulva képes előre jelezni jövőbeli történéseket, eredményeket vagy trendeket.

A gépi tanulás által biztosított előrejelzéseket felhasználva aztán intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárlói felületeknél alkalmazzák, a gépi tanulás képes a korábban megvásárolt cikkek alapján olyan termékeket ajánlani, amelyek esetleg szintén érdekelhetik Önt. Vagy amikor hitelkártyával vásárol, a gépi tanulási funkció összeveti a tranzakciót egy tranzakciókat tartalmazó adatbázissal, és jelzi a banknak, ha csalás gyanúja merül fel.

## Mire használható a Microsoft Azure Machine Learning?

Az Azure Machine Learning funkciókban gazdag felhőalapú prediktív elemzési szolgáltatás, amelynek segítségével elemzési megoldásként használható prediktív modelleket hozhat létre és helyezhet üzembe.

Az Azure Machine Learning nem csupán eszközöket biztosít a modellek prediktív elemzéséhez, de teljes körűen felügyelt szolgáltatást nyújt, amelynek segítségével felhasználásra kész webszolgáltatásként üzembe helyezheti prediktív modelljeit. Az Azure Machine Learning eszközeivel ezenfelül teljes prediktív elemzési megoldásokat hozhat létre a felhőben is, amelyeket könnyedén tesztelhet, működésbe léptethet, és felügyelhet. A szolgáltatás használatához nem kell hardvereket vásárolnia, sem virtuális gépeket felügyelnie.

![Mit jelent a gépi tanulás funkció? A prediktív elemzés működésbe léptetésének alapvető munkafolyamata az Azure Machine Learning szolgáltatásban.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Mi az a prediktív elemzés?

A prediktív elemzés különféle statisztikai módszerek (ebben az esetben gépi tanulás) segítségével elemzi az összegyűjtött vagy aktuális adatokat, és keresi a szabályszerűségeket vagy a trendeket, amelyek segítségével előre jelezhetők a jövőbeni események.

Az Azure Machine Learning a prediktív elemzés különösen hatékony módját kínálja: a szolgáltatás használatra kész algoritmusok tárát tartalmazza, a modellek létrehozásához ráadásul mindössze egy internetkapcsolattal rendelkező számítógép szükséges, nem kell további berendezéseket és infrastruktúrát vásárolnia hozzá, így könnyedén és gyorsan üzembe helyezheti prediktív megoldásait. A [Microsoft Azure Piactéren](https://datamarket.azure.com/browse?query=machine+learning) és a [Cortana Intelligence Galleryben](http://gallery.cortanaintelligence.com/) számos használatra kész példát és megoldást talál.

## Teljes gépi tanulási megoldások létrehozása a felhőben

Az Azure Machine Learning szolgáltatásban minden megtalálható, amire szüksége lehet a prediktív elemzési megoldások felhőben való létrehozásához: nagy méretű algoritmustár, létrehozási modelleket tartalmazó stúdió, a modellek webszolgáltatásként való üzembe helyezését megkönnyítő funkciók és még számos hasznos lehetőség.

### Machine Learning Studio: Prediktív modellek létrehozása

A böngészőalapú [Machine Learning Studióban](machine-learning-what-is-ml-studio.md) a különböző modulok húzásával és összekötésével hozhatja létre saját prediktív modelljeit.

![A prediktív elemzés bemutatása: prediktív elemzési kísérlet bemutatása az Azure Machine Learning Studióban](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* A Machine Learning Studióban olyan [Machine Learning-algoritmusok és -modulok](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) gazdag tára áll rendelkezésre, amelyek jelentősen felgyorsítják a prediktív modell létrehozását. Válogathat a különböző Microsoft-részlegektől, például az Xboxtól vagy a Bingtől származó mintakísérleteket, R- és Python-csomagokat, valamint kategóriájukban vezető minőségű algoritmusokat tartalmazó tárból. A Studio moduljait saját [R-](machine-learning-r-quickstart.md) és [Python-](machine-learning-execute-python-scripts.md)parancsprogramjaival bővítheti ki.
* A [Cortana Intelligence Galleryben](machine-learning-gallery-how-to-use-contribute-publish.md) más felhasználók által készített elemzési megoldásokat szerezhet be és próbálhat ki, vagy feltöltheti saját, különböző Azure-szolgáltatásokat (például Machine Learning, HDInsight (Hadoop), Stream Analytics vagy Data Lake Analytics), Azure Big Data-tárolókat vagy adatkezelési szolgáltatásokat használó alkotásait.  A közösség szívesen segít választ találni kísérletekkel kapcsolatos kérdéseire vagy megjegyzéseire. A kísérletekre mutató hivatkozásait különféle közösségi hálózatokon (például LinkedInen vagy Twitteren) is megoszthatja.  

    ![Kipróbálhatja mások prediktív kísérleteit, vagy feltöltheti sajátjait az Azure Cortana Intelligence Gallerybe.](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

### Prediktív elemzési megoldások működésbe léptetése: Webszolgáltatások vásárlása vagy saját megoldások közzététele

* A [Microsoft Azure Piactéren](https://datamarket.azure.com/browse?query=machine+learning) használatra kész webszolgáltatásokat vásárolhat például a következő területeken: javaslatok, szövegelemzések, anomáliadetektálás.

* Hozza működésbe prediktív elemzési modelljeit:
    * [Webszolgáltatások üzembe helyezése](machine-learning-publish-a-machine-learning-web-service.md)
    * [A modellek betanítása és újratanítása API-k segítségével](machine-learning-retrain-models-programmatically.md)
    * [Webszolgáltatás-végpontok kezelése](machine-learning-create-endpoint.md)
    * [Webszolgáltatások skálázása](machine-learning-scaling-endpoints.md)
    * [Webszolgáltatások felhasználása](machine-learning-consume-web-services.md)

## A gépi tanulás fontos szakszavai és koncepciói
### Az adatok feltárása, deskriptív elemzés és prediktív elemzés

Az **adatok feltárásának** azt a folyamatot nevezzük, amikor egy nagy és általában nem strukturált adathalmaz adatait összegyűjtve megkeressük a részletesebb elemzéshez felhasználható általános jellemzőket. Az **adatbányászat** kifejezés az adatfeltárás automatizált formájára utal.

**Deskriptív elemzésnek nevezzük**, amikor az adathalmaz elemzésével azt szeretnénk megkapni, hogy pontosan mi történt az adatokkal. Az üzleti elemzések nagy része (például az értékesítési jelentések, webes metrikák és közösségihálózat-elemzések) deskriptív elemzésnek számít.

**Prediktív elemzésnek** azt a folyamatot nevezzük, amikor előzményadatok vagy aktuális adatok alapján modelleket hozunk létre, amelyek segítségével előre lehet jelezni valamilyen jövőbeli eredményt.


### Felügyelt és nem felügyelt tanítás
 A **felügyelt tanítással** képzett algoritmusokat címkézett adatokkal, azaz a kívánt válaszokat példázó adatokkal tanítják. A hitelkártyacsalást felismerni hivatott modelleket például olyan adathalmazokkal tanítják, amelyek adatpontjainál egyértelmű címke jelöli, hogy az adott terhelési tétel legális, vagy csalás céljából született. A gépi tanulás általában felügyelt módon történik.

 A **nem felügyelt tanítást** címkéket nem tartalmazó adatokon alkalmazzák, és a folyamat célja, hogy megtalálja az adathalmazon belüli összefüggéseket. Ez akkor hasznos, ha például vásárlói körében hasonló vásárlási szokásokat követő csoportokat szeretne felállítani.

### A modellek betanítása és kiértékelése
A gépi tanulási modell a megválaszolandó kérdések vagy az előre jelezni kívánt eredmények absztrakciója. A modellek tanítását és kiértékelését meglévő adatok alapján lehet elvégezni.

#### Adatalapú tanítás
Az Azure Machine Learning szolgáltatásban egy, a tanítási adatokat feldolgozó algoritmusmodul, illetve funkcionális modulok, például pontozási modulok szükségek a modell létrehozásához.

Felügyelt tanítás során, például a csalásészlelő modell tanítása esetén egy tranzakciókat tartalmazó halmazt fog használni, amelyben minden tranzakciónál meg van jelölve, hogy az csalási célú vagy érvényes. Az adathalmazt ilyenkor véletlenszerűen fel kell darabolni: az egyik rész a modell betanítására, a másik a modell kiértékelésére használatos.

#### Az adatok kiértékelése
Ha elvégezte a modell betanítását, a fennmaradó tesztadatok segítségével értékelje ki a modellt. Ilyenkor olyan adatokat kell használni, amelyeknél tisztában van az eredményekkel, mivel így állapítható meg, hogy milyen pontosságú előrejelzésre képes a modell.

### A gépi tanulás tárgykörében gyakran használt egyéb kifejezések

* **algoritmus**: önállóan működő szabálykészlet, amely adatok feldolgozási, számítási feladatok elvégzésével, vagy automatikus gondolkodással képes problémák megoldására.
* **kategorizált adatok**: különböző kategóriákba sorolt, és különféle csoportokra osztható adatok. Egy gépjárműveket tartalmazó kategorizált adathalmazban például meg van adva a gyártás éve, a márka, a típus és az ár.
* **besorolás**: az adatpontok kategóriákba sorolására használható modell, amely egy kategóriacsoportokkal már ellátott adathalmazt vesz alapul.
* **jellemzőkiemelés**: az a folyamat, amikor bizonyos jellemzőket kinyerünk vagy kiválasztunk egy adathalmazból az adathalmaz és az eredmények minőségének javítása érdekében. A repülőjegyárakra vonatkozó adatokat például a hét napjainak és a munkaszüneti napoknak a kiemelésével lehet javítani. További információk: [Feature selection and engineering in Azure Machine Learning](machine-learning-feature-selection-and-engineering.md) (Jellemzőkiválasztás és -kiemelés az Azure Machine Learngingben).
* **modul**: a Machine Learning Studio-modellek funkcionális elemei. Ilyen például az Adatbevitel modul, amely kisebb adathalmazok bevitelét és szerkesztését teszi lehetővé. A Machine Learning Studióban az algoritmusok is a modulok egyik típusának számítanak.
* **modell**: felügyelt tanítás esetén a modell a tanítási adathalmazt, az algoritmusmodult és a különböző funkcionális modulokat (például a Score Model (Modell pontozása) modult) tartalmazó gépi tanulási kísérlet eredménye.
* **numerikus adatok**: mérésekként (folytonos adatok) vagy megszámlált értékekként (diszkrét adatok) értelmezhető adatok. A numerikus adatokat *kvantitatív adatoknak* is nevezzük.
* **particionálás**: az a folyamat, amikor az adatokat mintákra osztjuk. További információk: [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx) (Particionálás és minták létrehozása).
* **előrejelzés**: a gépi tanulási modellek által előrejelzett értékeket előrejelzéseknek nevezzük. Egyes helyeken az „előre jelzett pontszám” kifejezést is használják, ez azonban nem a modell végső eredményére utal. A modell értékelése a pontszám alapján történik.
* **regresszió**: folytonos érték független változók alapján történő előrejelzésére szolgáló modell. Ilyen például, amikor egy autó árát a gyártási év és a márka alapján szeretnénk megkapni.
* **pontszám**: betanított besorolási vagy regressziós modell által, a Machine Learning Studio [Score Model](https://msdn.microsoft.com/library/azure/dn905995.aspx) (Modell pontozása) modulja segítségével generált előre jelzett érték. A besorolási modell az előre jelzett érték valószínűségét jelző pontszámot is visszaad. Ha a modell már alkalmas pontszámok generálására, ellenőrizze a modell pontosságát az [Evaluate Model](https://msdn.microsoft.com/library/azure/dn905915.aspx) (Modell kiértékelése) modul segítségével.
* **minta**: az adathalmaz olyan része, amely az adathalmaz egészére vonatkozóan is reprezentatívnak tekinthető. A mintát kiválaszthatja véletlenszerűen vagy az adathalmaz konkrét jellemzői alapján.



## További lépések
A prediktív elemzés és a gépi tanulás alapjainak megismeréséhez használja [részletes oktatóanyagainkat](machine-learning-create-experiment.md) vagy [bővíthető mintáinkat](machine-learning-sample-experiments.md).  


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/



<!--HONumber=Jun16_HO2-->


