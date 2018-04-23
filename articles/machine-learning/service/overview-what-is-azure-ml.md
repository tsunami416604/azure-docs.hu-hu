---
title: Mi az Azure Machine Learning? | Microsoft Docs
description: Ismerteti a felhőalapú Machine Learning alapvető fogalmait, az alkalmazási területeket, valamint a Machine Learning kifejezéseit. Az Azure Machine Learning a fejlett elemzési alkalmazások fejlesztéséhez, a kísérletek kidolgozásához és a felhőszinten való üzembe helyezéshez létrehozott átfogó, integrált és fejlett adatelemzési megoldás adatszakértők számára.
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 868647780bafe9117a15891264a38535edff7250
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="what-is-machine-learning"></a>Mit jelent a Machine Learning?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulással a számítógépek konkrét programozás nélkül tanulhatnak.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárlói felületeknél alkalmazzák, a gépi tanulás képes a korábban megvásárolt cikkek alapján olyan termékeket ajánlani, amelyek esetleg szintén érdekelhetik Önt. A Machine Learning a bankkártya minden lehúzásakor összeveti az adott tranzakciót az adatbázisban található tranzakciókkal, így segít a csalások felismerésében. Ha robotporszívóra bízza a szoba kitakarítását, a Machine Learning segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="what-is-azure-machine-learning"></a>Mi az Azure Machine Learning?
Az Azure Machine Learning egy integrált, átfogó és fejlett adatelemzési megoldás. Az adatszakértők számára lehetővé teszi az adatok előkészítését, a kísérletek kidolgozását és a modellek felhőszinten való üzembe helyezését.

Az Azure Machine Learning fő összetevői a következők:
- Azure Machine Learning Workbench
- Azure Machine Learning-kísérletezés szolgáltatás
- Azure Machine Learning Modellkezelés szolgáltatás
- Microsoft Machine Learning Libraries for Apache Spark (MMLSpark kódtár)
- Visual Studio Code Tools for AI

Ezek az alkalmazások és szolgáltatások együttesen jelentősen segítenek felgyorsítani az adatelemzési projektek fejlesztését és üzembe helyezését. 

![Azure Machine Learning-fogalmak](./media/overview-what-is-azure-ml/aml-concepts.png)


## <a name="open-source-compatible"></a>Kompatibilis a nyílt forráskóddal

Az Azure Machine Learning teljes körűen támogatja a nyílt forráskódú technológiákat. Több tízezer nyílt forráskódú Python-csomagot használhat, például az alábbi gépi tanulási keretrendszereket:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Felügyelt környezetben végezheti el kísérleteit, például a Docker-tárolókban vagy Spark-fürtökben. Ezenkívül speciális hardvereket is használhat a kísérletek elvégzésének felgyorsítására, ilyenek például a [GPU-kompatibilis virtuális gépek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

Az Azure Machine Learning az alábbi nyílt forráskódú technológiákra épül:

- [Jupyter Notebook](http://jupyter.org/)
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
- [Adatok előkészítésének használati útmutatója](../desktop-workbench/data-prep-user-guide.md)
- [Git használata az Azure Machine Learninggel](../desktop-workbench/using-git-ml-project.md)
- [Jupyter Notebook használata az Azure Machine Learningben](../desktop-workbench/how-to-use-jupyter-notebooks.md)
- Barangolás és megosztás
- [Futtatási előzmények útmutatója](../desktop-workbench/how-to-use-run-history-model-metrics.md)
- [IDE-integráció](../desktop-workbench/how-to-configure-your-ide.md)

## <a name="azure-machine-learning-experimentation-service"></a>Azure Machine Learning-kísérletezés szolgáltatás
A kísérletezési szolgáltatás kezeli a gépi tanulási kísérletek elvégzését. Emellett a projektkezelés, Git-integráció, hozzáférés-vezérlés, barangolás és megosztás biztosításával támogatja a Workbench használatát. 

Az egyszerű konfigurációval kísérleteket végezhet el számos számítási környezetben:

- Helyi natív
- Helyi Docker-tároló
- Docker-tároló egy távoli virtuális gépen
- Spark-fürt horizontális felskálázása az Azure-ban

A kísérletezési szolgáltatás virtuális környezeteket hoz létre annak érdekében, hogy biztosítsa a szkriptek elkülönített, reprodukálható eredményekkel szolgáló végrehajtását. Futtatási előzményekről szóló információkat rögzít, és gondoskodik az előzmények vizuális megjelenítéséről. Egyszerűen kiválaszthatja a legjobb modellt a kísérletezési futtatások közül. 

További információkért tekintse meg a [kísérletezési szolgáltatás konfigurációjával kapcsolatos](../desktop-workbench/experimentation-service-configuration.md) cikket.

## <a name="azure-machine-learning-model-management-service"></a>Azure Machine Learning Modellkezelés szolgáltatás

A modellkezelési szolgáltatás lehetővé teszi az adatszakértők és a fejlesztő-operátor csapatok számára a prediktív modellek különböző környezetekben történő üzembe helyezését. Nyomon követi a modellverziókat és a leszármaztatást a betanítási futtatástól az üzembe helyezésig. A modellek a felhőben vannak tárolva, regisztrálva és kezelve. 

Egyszerű parancssori felületi parancsokkal tárolhatja a modelleket, a pontozó szkripteket és a függőségeket a Docker-rendszerképekben. Ezek a rendszerképek a saját, az Azure-ban (Azure Container Registryben) futtatott Docker-beállításjegyzékében vannak regisztrálva. Megbízhatóan üzembe helyezhetők az alábbi célokhoz:

- Helyi gépek
- Helyszíni kiszolgálók
- A felhő
- IoT Edge-eszközök

Az Azure Container Service-ben (ACS) futó Kubernetes felhőbeli horizontálisan felskálázott telepítésre használható. A modellvégrehajtás telemetriája az AppInsightsban lesz rögzítve a vizuális elemzéshez. 

További információkat a modellkezelési szolgáltatásról a [Modellkezelés áttekintésében](../desktop-workbench/model-management-overview.md) találhat.


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

További információkért tekintse meg [az MMLSpark az Azure Machine Learningben történő használatát](../desktop-workbench/how-to-use-mmlspark.md) ismertető cikket.

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
A Visual Studio Code Tools for AI a Visual Studio Code egy bővítménye, amely mélytanulási és AI-megoldások összeállítását, tesztelését és üzembe helyezését teszi lehetővé. Számos integrációs pontot tartalmaz az Azure Machine Learninggel, például a következőket:
- A betanítási futtatás és a naplózott mérőszámok teljesítményét megjelenítő futtatásielőzmény-nézet.
- Az új projektek Microsoft Cognitive Toolkittel, TensorFlow-val és számos egyéb mélytanulási keretrendszerrel történő tallózását és elindítását lehetővé tévő katalógusnézet. 
- Tallózó nézet a végrehajtandó szkriptek számítási céljainak kiválasztásához.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Milyen gépi tanulási lehetőségeket kínál a Microsoft?
Az Azure Machine Learning mellett a lehetőségek széles köre áll rendelkezésre az Azure-ban gépi tanulási modellek összeállítására, üzembe helyezésre és kezelésére. [Ezeket itt ismerheti meg.](../desktop-workbench/overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>További lépések
* [Az Azure Machine Learning telepítése és létrehozása](quickstart-installation.md)
