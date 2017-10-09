---
title: Mi az Azure Machine Learning? | Microsoft Docs
description: "Az Azure Machine Learning-kísérletezés és Modellkezelés egy, az adatszakértők számára a fejlett elemzési alkalmazások fejlesztéséhez, a kísérletek kidolgozásához és a felhőszinten való üzembe helyezéshez létrehozott átfogó, integrált és fejlett adatelemzési megoldás."
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c4919fb679eeb4d25eb0066b9bf617b057d44354
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---

# <a name="what-is-azure-machine-learning"></a>Mi az Azure Machine Learning?

Az Azure Machine Learning egy integrált, átfogó és fejlett adatelemzési megoldás. Az adatszakértők számára lehetővé teszi az adatok előkészítését, a kísérletek kidolgozását és a modellek felhőszinten való üzembe helyezését.

Az Azure Machine Learning fő összetevői a következők:
- Azure Machine Learning Workbench
- Azure Machine Learning-kísérletezés szolgáltatás
- Azure Machine Learning Modellkezelés szolgáltatás
- Microsoft Machine Learning Libraries for Apache Spark (MMLSpark kódtár)
- Visual Studio Code Tools for AI

Ezek az alkalmazások és szolgáltatások együttesen jelentősen segítenek felgyorsítani az adatelemzési projektek fejlesztését és üzembe helyezését. 

![Azure Machine Learning-fogalmak](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>Kompatibilis a nyílt forráskóddal

Az Azure Machine Learning teljes körűen támogatja a nyílt forráskódú technológiákat. Több tízezer nyílt forráskódú Python-csomagot használhat, például az alábbi gépi tanulási keretrendszereket:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Felügyelt környezetben végezheti el kísérleteit, például a Docker-tárolókban vagy Spark-fürtökben. Ezenkívül speciális hardvereket is használhat a kísérletek elvégzésének felgyorsítására, ilyenek például a [GPU-kompatibilis virtuális gépek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

Az Azure Machine Learning az alábbi nyílt forráskódú technológiákra épül:

- [Jupyter notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Tartalmazza továbbá a Microsoft saját nyílt forráskódú technológiáit. Ilyen például a [Microsoft Machine Learning Library for Apache Spark](https://github.com/Azure/mmlspark) és a Cognitive Toolkit.

Emellett kihasználhatja a legfejlettebb, jól bevált, a Microsoft által nagyméretű problémák megoldására fejlesztett gépi tanulási technológiák előnyeit. Ezeket élesben tesztelték számos Microsoft-termékben, például a következők fejlesztése során:

- Windows
- Bing
- Xbox
- Office
- SQL Server

Többek között az alábbi microsoftos gépi tanulási technológiák találhatók meg az Azure Machine Learningben:

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Az Azure Machine Learning Workbench egy asztali alkalmazás, valamint parancssori eszközök együttese, amely Windows és macOS rendszerű gépeken is használható. A teljes adatkezelési életcikluson keresztül lehetővé teszi a gépi tanulási megoldások kezelését:

- Adatfeldolgozás és -előkészítés
- Modellfejlesztés és kísérletkezelés
- Modell üzembe helyezése különböző célkörnyezetekben

Az Azure Machine Learning Workbench által kínált főbb funkciók a következők:

- Adatelőkészítési eszközök, amelyek például képesek megtanulni az adatátalakítási logikát.
- UX- és a Python-kódon keresztül elérhető adatforrás-absztrakció.
- Python SDK a vizuálisan összeállított adatelőkészítési csomagok meghívásához.
- Beépített Jupyter Notebook szolgáltatás és ügyfél-UX.
- Kísérlet monitorozása és kezelése futtatási előzmények nézeteinek használatával.
- Szerepköralapú hozzáférés-vezérlés, amely lehetővé teszi a megosztást és együttműködést az Azure Active Directoryn keresztül.
- Automatikusan létrehozott projektpillanatképek minden futtatásról, valamint a Git-integráció által lehetővé tett explicit verziókezelés. 
- Integráció a népszerű Python IDE-kkel.

További információkért tekintse meg a következő cikkeket:
- [Adatok előkészítésének használati útmutatója](data-prep-user-guide.md)
- [Git használata az Azure Machine Learninggel](using-git-ml-project.md)
- [Jupyter Notebook használata az Azure Machine Learningben](how-to-use-jupyter-notebooks.md)
- Barangolás és megosztás
- [Futtatási előzmények útmutatója](how-to-use-run-history-model-metrics.md)
- [IDE-integráció](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Azure Machine Learning-kísérletezés szolgáltatás
A kísérletezési szolgáltatás kezeli a gépi tanulási kísérletek elvégzését. Emellett a projektkezelés, Git-integráció, hozzáférés-vezérlés, barangolás és megosztás biztosításával támogatja a Workbench használatát. 

Az egyszerű konfigurációval kísérleteket végezhet el számos számítási környezetben:

- Helyi natív
- Helyi Docker-tároló
- Docker-tároló egy távoli virtuális gépen
- Spark-fürt horizontális felskálázása az Azure-ban

A kísérletezési szolgáltatás virtuális környezeteket hoz létre annak érdekében, hogy biztosítsa a szkriptek elkülönített, reprodukálható eredményekkel szolgáló végrehajtását. Futtatási előzményekről szóló információkat rögzít, és gondoskodik az előzmények vizuális megjelenítéséről. Egyszerűen kiválaszthatja a legjobb modellt a kísérletezési futtatások közül. 

További információkért tekintse meg a [kísérlet-végrehajtás konfigurálását](experiment-execution-configuration.md) ismertető cikket.

## <a name="azure-machine-learning-model-management-service"></a>Azure Machine Learning Modellkezelés szolgáltatás

A modellkezelési szolgáltatás lehetővé teszi az adatszakértők és a fejlesztő-operátor csapatok számára a prediktív modellek különböző környezetekben történő üzembe helyezését. Nyomon követi a modellverziókat és a leszármaztatást a betanítási futtatástól az üzembe helyezésig. A modellek a felhőben vannak tárolva, regisztrálva és kezelve. 

Egyszerű parancssori felületi parancsokkal tárolhatja a modelleket, a pontozó szkripteket és a függőségeket a Docker-rendszerképekben. Ezek a rendszerképek a saját, az Azure-ban (Azure Container Registryben) futtatott Docker-beállításjegyzékében vannak regisztrálva. Megbízhatóan üzembe helyezhetők az alábbi célokhoz:

- Helyi gépek
- Helyszíni kiszolgálók
- A felhő
- IoT Edge-eszközök

Az Azure Container Service-ben (ACS) futó Kubernetes felhőbeli horizontálisan felskálázott telepítésre használható. A modellvégrehajtás telemetriája az AppInsightsban lesz rögzítve a vizuális elemzéshez. 

További információkat a modellkezelési szolgáltatásról a [Modellkezelés áttekintésében](model-management-overview.md) találhat.


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Microsoft Machine Learning Library for Apache Spark

Az [MMLSpark](https://github.com/Azure/mmlspark)(Microsoft Machine Learning Library for Apache Spark) egy nyílt forráskódú Spark-csomag, amely mélytanulási és adatelemzési eszközöket biztosít az Apache Spark számára. Integrálja a [Spark Machine Learning-folyamatokat](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) a [Microsoft Cognitive Toolkittel](https://www.microsoft.com/en-us/cognitive-toolkit/) és az [OpenCV](http://opencv.org/) kódtárral. Lehetővé teszi nagy teljesítményű, hatékonyan méretezhető, prediktív és analitikai modellek gyors létrehozását nagyméretű képeket és szöveget tartalmazó adatkészletekhez. Főbb jellemzők:

- Képek egyszerű betöltése a HDFS-ből a Spark DataFrame-be
- Képadatok előzetes feldolgozása OpenCV-ből származó átalakítók használatával
- Képjellemzők felismerése előre betanított neurális hálózatokkal a Microsoft Cognitive Toolkit használatával 
- Előre betanított kétirányú, Kerasból származó LSTM-ek használata orvosi entitások kinyeréséhez
- DNN-alapú képbesorolási modellek tanítása N-sorozatú GPU-val rendelkező virtuális gépeken az Azure-ban
- Szabadkézi szöveges adatok jellemzőinek felismerése könnyen használható API-kkal a SparkML primitívjein felül, egyetlen átalakítón keresztül
- Besorolási és regressziós modellek egyszerű tanítása az adatok implicit jellemzőinek megállapításán keresztül
- Értékelési mérőszámok széles választékának számítása (például példányonkénti mérőszámok)

További információkért tekintse meg [az MMLSpark az Azure Machine Learningben történő használatát](how-to-use-mmlspark.md) ismertető cikket.

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
A Visual Studio Code Tools for AI a Visual Studio Code egy bővítménye, amely mélytanulási és AI-megoldások összeállítását, tesztelését és üzembe helyezését teszi lehetővé. Számos integrációs pontot tartalmaz az Azure Machine Learninggel, például a következőket:
- A betanítási futtatás és a naplózott mérőszámok teljesítményét megjelenítő futtatásielőzmény-nézet.
- Az új projektek Microsoft Cognitive Toolkittel, TensorFlow-val és számos egyéb mélytanulási keretrendszerrel történő tallózását és elindítását lehetővé tévő katalógusnézet. 
- Tallózó nézet a végrehajtandó szkriptek számítási céljainak kiválasztásához.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Milyen gépi tanulási lehetőségeket kínál a Microsoft?
Az Azure Machine Learning mellett a lehetőségek széles köre áll rendelkezésre az Azure-ban gépi tanulási modellek összeállítására, üzembe helyezésre és kezelésére. 
* Microsoft Machine Learning-szolgáltatások az SQL Serveren
* Microsoft Machine Learning-kiszolgáló
* Adatelemzési virtuális gép
* Spark MLLib a HDInsightban
* Batch AI Training szolgáltatás
* Microsoft Cognitive Toolkit
* Microsoft Cognitive Services


### <a name="microsoft-machine-learning-services-in-sql-server"></a>Microsoft Machine Learning-szolgáltatások az SQL Serveren
A [Microsoft Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) lehetővé teszik a gépi tanulási modellek R vagy Python használatával való futtatását, betanítását és üzembe helyezését. Helyszíni vagy SQL Server-adatbázisokban található adatokat is használhat. 

Használja a Microsoft Machine Learning-szolgáltatásokat, ha modelleket szeretne betanítani vagy üzembe helyezni a helyszínen vagy a Microsoft SQL Serverben. A Machine Learning-szolgáltatásokkal összeállított modellek az Azure Machine Learning Modellkezelővel helyezhetők üzembe. 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning-kiszolgáló 
A [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) egy vállalati kiszolgáló, amely R- és Python-folyamatok párhuzamos és elosztott munkaterheléseinek futtatására és kezelésére szolgál. A Microsoft Machine Learning-kiszolgáló Linux és Windows rendszeren, valamint Hadoopon és Apache Sparkon fut. Ezenkívül elérhető a [HDInsighton](https://azure.microsoft.com/services/hdinsight/r-server/) is. Végrehajtómotorokat biztosít a [Microsoft Machine Learning-csomagokkal](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) összeállított megoldásokhoz, és kiterjeszti a nyílt forráskódú R és Python támogatását az alábbi forgatókönyvekre:

- nagy teljesítményű elemzés
- statisztikai elemzés
- gépi tanulás
- nagyméretű adatkészletek

Az értéknövelt funkciókat a kiszolgálóval telepített jogvédett csomagok biztosítják. Fejlesztéshez olyan IDE-ket használhat, mint például az [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) és a [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Akkor érdemes a Microsoft Machine Learning-kiszolgálót használnia, ha:

- R vagy Python használatával összeállított modelleket szeretne összeállítani vagy üzembe helyezni egy kiszolgálón
- R- vagy Python-betanítást szeretne nagy méretben kiosztani egy Hadoop- vagy Spark-fürtön

### <a name="data-science-virtual-machine"></a>Adatelemzési virtuális gép
Az [Adatelemzési virtuális gép (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) egy személyre szabott virtuálisgép-lemezkép a Microsoft Azure-felhőben, amelyet kifejezetten adatelemzésre hoztak létre. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását. Elérhető Windows Server és Linux rendszeren. A DSVM Windows-kiadását Server 2016 és Server 2012 rendszeren tesszük elérhetővé. A DSVM Linux-kiadását Ubuntu 16.04 LTS rendszeren és az OpenLogic 7.2 CentOS-alapú Linux-disztribúcióin tesszük elérhetővé. 

Akkor érdemes az Adatelemzési virtuális gépet használnia, ha egyetlen csomóponton kell futtatnia a feladatait. Illetve ha távolról kell virtuálisan felskáláznia a feldolgozást egyetlen gépen. Az Azure Machine Learning-kísérletezés és az Azure Machine Learning Modellkezelés is célként támogatja az Adatelemzési virtuális gépet. 

### <a name="spark-mllib-in-hdinsight"></a>Spark MLLib a HDInsightban
A [Spark MLLib a HDInsightban](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) lehetővé teszi a modellek olyan Spark-feladatok részeként való létrehozását, amelyek Big Data típusú adatokon futnak. A Spark egyetlen feladatban lehetővé teszi adatok egyszerű átalakítását és előkészítését, majd horizontálisan felskálázható modellek létrehozását. A Spark MLLib segítségével létrehozott modellek üzembe helyezhetők, kezelhetők és monitorozhatók az Azure Machine Learning Modellkezelés segítségével. A betanítási futtatások továbbíthatók és kezelhetők az Azure Machine Learning-kísérletezéssel. A Spark a Machine Learning Workbenchben létrehozott adatelőkészítési feladatok horizontális felskálázására is használható. 

Akkor érdemes a Sparkot használnia, ha horizontálisan kell felskáláznia az adatfeldolgozást, valamint egy adatfolyamat részeként kell modelleket létrehoznia. Spark-feladatokat a következőkben hozhat létre: Scala, Java, Python vagy R. 

### <a name="batch-ai-training"></a>Batch AI Training 
Az [Azure Batch AI Training](https://aka.ms/batchaitraining) az AI-modellekkel párhuzamos kísérletezésben segít bármely keretrendszer használatakor, majd nagy számban betanítja őket csoportosított GPU-kon keresztül. Írja le a futtatandó feladat követelményeit és konfigurációját, a többit mi intézzük. 

A Batch AI Training lehetővé teszi mélytanulási feladatok horizontális felskálázását csoportosított GPU-k között, például az alábbi keretrendszerek használatával:

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Az Azure Machine Learning Modellkezelés használható modellek átvételére a Batch AI Trainingtől a modellek üzembe helyezése, kezelése és monitorozása céljából.  A Batch AI Training később integrálva lesz az Azure Machine Learning-kísérletezésbe. 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
A [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) egy egységesített mélytanulási eszközkészlet, amely számítási lépésekként írja le a neurális hálózatokat egy irányított gráfban. Ebben az irányított gráfban a levélcsomópontok a bemeneti értékeket vagy a hálózati paramétereket, míg az egyéb csomópontok a mátrixműveleteket jelölik a bemenetekkor. A Cognitive Toolkit segítségével egyszerűen létrehozhat és egyesíthet népszerű adatmodell-típusokat, például feed-forward DNN-eket, konvolúciós hálózatokat (CNN-ek), és ismétlődő hálózatokat (RNN-ek és LSTM-ek). Sztochasztikus gradiens módszeres (SGD, hiba-visszaterjesztés) tanulást hajt végre automatikus megkülönböztetéssel és párhuzamos folyamatkezeléssel több GPU-n és kiszolgálón.

A Cognitive Toolkitet mély tanulást használó modell összeállításához érdemes használnia.  A Cognitive Toolkit a korábbi szolgáltatások bármelyikében használható.

### <a name="microsoft-cognitive-services"></a>Microsoft Cognitive Services
A Microsoft Cognitive Services egy 30 API-ból álló készlet, amely lehetővé teszi a természetes kommunikációs módszereket használó alkalmazások összeállítását. Az API-k csupán néhány sornyi kód alapján lehetővé teszik az alkalmazások számára, hogy lássanak, halljanak, beszéljenek, megértsék és értelmezzék szükségleteinket. Egyszerűen hozzáadhat intelligens funkciókat az alkalmazáshoz, például a következőket: 

- Érzelemfelismerés
- Látvány- és beszédfelismerés
- Hangfelismerés
- Ismeretek és keresés

A Microsoft Cognitive Services különböző eszközökre és platformokra történő alkalmazásfejlesztésre használható. Az API-k egyre fejlettebbek lesznek, és könnyű őket beállítani. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Következő lépések
* [Az Azure Machine Learning telepítése és létrehozása](quickstart-installation.md)

