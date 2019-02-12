---
title: 'A Machine learning-a felhőben: Feltételek és architektúra'
titleSuffix: Azure Machine Learning service
description: Ismerje meg az architektúra, a terminológiáról és az Azure Machine Learning szolgáltatás alkotó fogalmak. Emellett megismerjük a szolgáltatást, és az Azure-szolgáltatások, Azure Machine Learning szolgáltatás által használt általános munkafolyamata kapcsolatos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a6f558fd97dc13044d1ea4da63ff5879e6599f9e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100705"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Az Azure Machine Learning szolgáltatás működése: Architektúra és fogalmak

Ez a cikk ismerteti az architektúra és az Azure Machine Learning szolgáltatás fogalmak. A szolgáltatás és az általános munkafolyamat a szolgáltatás fő összetevőit az alábbi ábrán láthatók: 

[![Munkafolyamat és az Azure Machine Learning szolgáltatás architektúrája](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

A munkafolyamat általában ez a sorozat következő:

1. Fejlesztés a gépi tanulási parancsfájlok képzési **Python**.
1. Létrehozhat és konfigurálhat egy **számítási célt**.
1. **Küldje el a parancsfájlok** a konfigurált számítási célnak az adott környezetben való futtatásához. Során képzés, a parancsfájlok olvasni vagy írni **adattárolója**. Végrehajtás a rekordokat a rendszer menti és **fut** a a **munkaterület** és szerint csoportosított **kísérletek**.
1. **A kísérlet lekérdezése** a naplózott mérőszámok az aktuális és korábbi fut a. A metrikák nem szükséges teljesítendő jelzik, ha 1. lépés, és a parancsfájlok iterálása ikonjához.
1. Miután megtalálta a megfelelő futtató, regisztrálja a megőrzött modellel a **modell beállításjegyzék**.
1. A pontozó szkript fejleszthet.
1. **Hozzon létre egy rendszerképet** , és regisztrálja a a **regisztrációs adatbázisba**. 
1. **A lemezkép telepítése** , egy **webszolgáltatás** az Azure-ban.


> [!NOTE]
> Bár ez a cikk az Azure Machine Learning szolgáltatás által használt kifejezések és fogalmak meghatározása, kifejezések és fogalmak az Azure platform nem definiál. Az Azure platform terminológia kapcsolatos további információkért lásd: a [Microsoft Azure szószedet](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Munkaterület

A munkaterületet, hogy az Azure Machine Learning szolgáltatás legfelső szintű erőforrás. Biztosít egy központi helyen hoz létre az Azure Machine Learning szolgáltatás használatakor minden összetevő dolgozhat.

A munkaterület tartja a számítási célokhoz, amely segítségével a modell betanítását listáját. Azt is előzményeket a betanítási futtatás, beleértve a naplók, metrikák, kimeneti és a parancsfájlok pillanatképét. Ez az információ segítségével meghatározhatja, melyik betanítási Futtatás a legjobb modellt hoz létre.

A munkaterület regisztrált modellek. Pontozó szkripteket és a egy regisztrált modell használatával hozzon létre egy rendszerképet. Telepítheti a lemezképet az Azure Container Instances Azure Kubernetes Service-ben vagy egy mező-programmable gate array (FPGA) REST-alapú HTTP-végpontként. A lemezképet az Azure IoT Edge-eszköz modulként is telepítheti.

Több munkaterületet is létrehozhat, és minden munkaterülethez több személy közösen használhat. Ha megoszt egy munkaterületet, szabályozhatja a hozzáférést a következő szerepkörök hozzárendelésével a felhasználók számára:

* Tulajdonos
* Közreműködő
* Olvasó

Amikor létrehoz egy új munkaterületet, automatikusan létrehoz több Azure-a munkaterület által használt erőforrások:

* [Az Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Regisztrálja a docker-tárolókat, hogy a betanítás során, és a egy modell központi telepítésekor használ.
* [Az Azure storage-fiók](https://azure.microsoft.com/services/storage/): Az alapértelmezett adattároló mint szolgál a munkaterületen.
* [Az Azure Application Insights](https://azure.microsoft.com/services/application-insights/): A tároló figyelése a modellek kapcsolatos információkat.
* [Az Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Tárolók titkos kulcsok által használt számítási célozza meg, és egyéb bizalmas információkat, amelyeket a munkaterületen.

> [!NOTE]
> Mellett hoz létre új verziókat, meglévő Azure-szolgáltatásokat is használhatja. 

A besorolás, a munkaterület az alábbi ábra mutatja be:

[![Munkaterület besorolás](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="model"></a>Modell

A legegyszerűbb esetben egy modell olyan kódot, amely veszi a bemenetnek és kimenetet. Egy gépi tanulási modellek létrehozásával magában foglalja a algoritmusok kiválasztása, megadják a data és a hangolási hiperparaméterek. Képzési, amely a betanított modell, amely magában foglalja, mi a modell a betanítási folyamat során megtanult iteratív folyamat.

Futtatás az Azure Machine Learning-modell elő. Egy modellt, amely be van tanítva, az Azure Machine Learning-en kívül is használhatja. Az Azure Machine Learning szolgáltatás munkaterületen egy modell regisztrálhat.

Az Azure Machine Learning szolgáltatás keretrendszer független. Amikor létrehoz egy modellt, minden olyan közkedvelt machine learning-keretrendszerek, például a Scikit-ismerje meg, a XGBoost, a PyTorch, a TensorFlow, a Chainer és a Microsoft Cognitive Toolkittel (korábban cntk) is használhatja.

A modell tanítása példát talál [a rövid útmutató: Hozzon létre egy Machine Learning szolgáltatás munkaterületet](quickstart-get-started.md).

### <a name="model-registry"></a>Modell beállításjegyzék

A modell beállításjegyzék nyomon követi az összes a modellek az Azure Machine Learning szolgáltatás munkaterületen. 

Modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék azt feltételezi, hogy-e egy új verziója. A verzió értéke akkor nő, és az új modell ugyanazzal a névvel van regisztrálva.

Ha regisztrálja a modellt, akkor is további metaadat-címkéket adja meg, majd a címkék modellek keresésekor.

Lemezkép által használt modellek nem törölhető.

A modellek regisztrálása egy példa: [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="image"></a>Kép

Képek hardvermódosításainak megbízhatóan helyezhet üzembe modelleket, együtt kell használni a modell az összes összetevő. Kép a következő elemeket tartalmazza:

* A modell.
* A pontozó szkript vagy alkalmazás. A szkript használatával a modell bemenetet átadni, és a modell a hibaüzenettel reagál.
* A modell vagy a pontozó szkript vagy alkalmazás számára szükséges függőségek. Például előfordulhat, hogy közé tartozik egy Conda környezet fájlt, amely felsorolja a Python csomagfüggőségek.

Az Azure Machine Learning két típusú lemezképek hozhatók létre:

* **FPGA-lemezkép**: Amikor üzembe helyezi a egy mezőben-programmable gate array az Azure-ban használni.
* **Docker-rendszerkép**: Amikor üzembe helyezi a számítási céljainak eltérő FPGA használja. Példák a következők: Azure Container Instances szolgáltatásban és az Azure Kubernetes Service-ben.

Lemezkép létrehozása a példát talál [egy kép besorolási modell az Azure Container Instancesben üzembe](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Regisztrációs adatbázisba

A regisztrációs adatbázisba nyomon követi, hogy a modellek a létrehozott rendszerképeket. A rendszerkép létrehozásakor megadhat további metaadat-címkéket. Metaadat-címkéket tárolt által a regisztrációs adatbázisba, és keresse meg a lemezkép lekérdezhetők.

## <a name="deployment"></a>Környezet

Központi telepítés rendszer vagy egy webszolgáltatás, amelyet a felhőben üzemeltethető, vagy egy IoT-modul az integrált eszközök központi telepítéséhez a lemezkép által okozott. 

### <a name="web-service"></a>Webszolgáltatás

Egy már üzembe helyezett webszolgáltatás az Azure Container Instances, az Azure Kubernetes Service-ben vagy az FPGA-kban. A szolgáltatás-lemezképről, amely magában foglalja a modell, a parancsfájl és a kapcsolódó fájlokat hoz létre. A rendszerkép egy elosztott terhelésű, HTTP-végpontot, amely megkapja a pontozási a web Service küldött kérések rendelkezik.

Az Azure megkönnyíti a webszolgáltatás üzembe figyelemmel gyűjtése az Application Insights telemetria- vagy modell telemetriai adatokat, ha úgy döntött, ez a funkció engedélyezéséhez. A telemetriai adatok csak Ön számára elérhető, és az Application Insights és a tárolási fiók példányait tárolja.

Ha engedélyezte az automatikus méretezés, az Azure automatikusan méretezi a központi telepítés.

Helyezi üzembe a modellt webszolgáltatásként, amely egy példa: [egy kép besorolási modell az Azure Container Instancesben üzembe](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>IoT-modul

Egy üzembe helyezett IoT-modul Docker-tároló, amely tartalmazza a modellt és a társított szkript vagy alkalmazás és további függőségek. A peremhálózati eszközökön az Azure IoT Edge segítségével telepítheti ezeket a modulokat. 

Ha engedélyezte a figyelés, az Azure az Azure IoT Edge-modul lévő modell gyűjt telemetrikus adatokat. A telemetriai adatok csak Ön számára elérhető, és a storage-fiók példány található.

Az Azure IoT Edge biztosítja, hogy a modul fut, és az azt futtató eszköz figyeli.

## <a name="datastore"></a>Adattár

Egy adattár egy tárolási absztrakciós Azure storage-fiók felett. Az adattár egy Azure blob-tárolóba vagy Azure-fájlmegosztások használhat a háttér-tárolóként. Az egyes munkaterületeken rendelkezik alapértelmezett adattárolót, és további adattárainak regisztrálhatja. 

A Python SDK API-t vagy az Azure Machine Learning parancssori felület használatával fájlok tárolásához és lekéréséhez, az adattárral. 

## <a name="run"></a>Futtassa a következőt:

Futtatás a következő egy rekordot, amely a következő információkat tartalmazza:

* A Futtatás (timestamp, időtartama és így tovább) kapcsolatos metaadatok
* A parancsfájl által naplózott mérőszámok
* Kimeneti fájlok, amelyek autocollected által a kísérletet, vagy explicit módon feltöltött Ön
* A könyvtár, amely tartalmazza a parancsprogramok, a Futtatás előtt pillanatképét

Futtatás a modell betanításához parancsfájl elküldésekor hozhat létre. Futtató rendelkezhet, nulla vagy több alárendelt futtatások. Például a legfelső szintű futtatás rendelkezhet két gyermek futtatásakor a szolgáltatás, amelyek mindegyike előfordulhat, hogy a saját gyermek futtatni.

Egy modell által készített futtatások nem csupán egy példa: [a rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).

## <a name="experiment"></a>Kísérlet

Egy kísérlet megadott parancsfájl több futtatások csoportja. Mindig tartozik egy munkaterülethez. Ha elküldi egy futtatás, meg kell adnia egy kísérlet neve. A kísérlet keretében a Futtatás adatait tárolja. Ha futtató nyújt, és adja meg, egy kísérlet neve, amely nem létezik, a rendszer automatikusan létrehoz egy új kísérlet a újonnan megadott névvel.

Egy kísérlet használatának példájáért lásd [a rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).

## <a name="pipeline"></a>Folyamat

Machine learning segítségével folyamatokat hozhat létre és kezelhet a munkafolyamatok, amelyek összefűzheti a machine learning fázisait. Például egy folyamatot tartalmazhat adat-előkészítés, modell betanítása, modell-üzembehelyezés és következtetési fázisait. Az egyes fázisok is magában foglalja a több lépésből, amelyek mindegyike a különböző számítási célnak beavatkozás nélkül futtatható.

Machine learning-folyamatokat ezzel a szolgáltatással kapcsolatos további információkért lásd: [folyamatok és az Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Számítási célt

Egy számítási célnak, hogy az a tanítási szkriptet futtatni, vagy a szolgáltatás üzembe helyezésének üzemeltetéséhez használt számítási erőforrás. A támogatott számítási célnak a következők: 

| Számítási célt | Képzés | Környezet |
| ---- |:----:|:----:|
| A helyi számítógépen | ✓ | &nbsp; |
| Az Azure Machine Learning compute | ✓ | &nbsp; |
| Linux rendszerű virtuális gép az Azure-ban</br>(például a Data Science virtuális gép) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Az Apache Spark for HDInsight | ✓ | &nbsp; |
| Azure Container Instances | &nbsp; | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Field-programmable gate array) | &nbsp; | ✓ |

Számítási célnak csatolt munkaterület. Számítási, a munkaterület felhasználók által megosztott célok nem a helyi gépen.

### <a name="managed-and-unmanaged-compute-targets"></a>Felügyelt és nem felügyelt számítási célnak

* **Felügyelt**: Tárolók létrehozása és felügyelete az Azure Machine Learning szolgáltatás számítási. Ezek a számítási célok machine learning feladatokra lettek optimalizálva. Az Azure Machine Learning compute az egyetlen felügyelt számítási célnak 2018. December 4. A jövőben további felügyelt számítási célnak vehetők fel. 

    Létrehozhatja a machine learning számítási példányok a munkaterület segítségével közvetlenül az Azure Portalon, az Azure Machine Learning-SDK vagy az Azure CLI használatával. Minden egyéb számítási célnak a munkaterület alkalmazáson kívül létrehozott legyen, és ezután csatlakozik.

* **Nem felügyelt**: Számítási célnak, amelyek *nem* Azure Machine Learning szolgáltatás kezeli. Szüksége lehet kívül az Azure Machine Learning létrehozása őket, és csatolja őket a munkaterület használata előtt. Nem felügyelt számítási célnak karbantartásához, illetve a machine learning számítási feladatokhoz a teljesítmény javítása szükséges további lépéseket is szükséges.

Egy számítási célnak képzéshez kiválasztásával kapcsolatos információkért lásd: [kiválasztása és használata egy számítási célnak a modell betanításához](how-to-set-up-training-targets.md).

Központi telepítés egy számítási célnak kiválasztásával kapcsolatos információkért lásd: a [modellek Azure Machine Learning szolgáltatás üzembe helyezése](how-to-deploy-and-where.md).

## <a name="run-configuration"></a>Futtatási konfigurációt

A futtatási konfigurációs utasításokat, amely meghatározza a parancsfájl a megadott számítási célnak futtatásának módját. A konfiguráció viselkedésének definíciókat, például hogy egy meglévő Python-környezettel vagy egy Conda-környezet, amely egy specifikációt használata széles készletét tartalmazza.

Egy futtatási konfigurációt megőrizhetők a tanítási szkriptet tartalmazó könyvtárra belül egy fájlba, vagy is kell kialakítani, memórián belüli objektumot és egy Futtatás küldéséhez használt.

Például futtatási konfigurációkat, lásd: [kiválasztása és használata egy számítási célnak a modell betanításához](how-to-set-up-training-targets.md).

## <a name="training-script"></a>Tanítási szkriptet

A modell betanítását, meg kell adni a tanítási szkriptet és a kapcsolódó fájlokat tartalmazó könyvtárra. Is megad, egy kísérlet neve, amely a betanítás során gyűjtött adatok tárolására szolgál. Során képzés, a teljes címtárra kerül a képzési környezet (számítási célt), és a parancsfájl a futtatási konfiguráció által meghatározott elindult. A könyvtár pillanatképet is tárolja a munkaterületen a kísérlet keretében.

Egy vonatkozó példáért lásd: [munkaterület létrehozása pythonnal](quickstart-get-started.md).

## <a name="logging"></a>Naplózás

Amikor a megoldás fejlesztése, akkor a Python-szkript használata az Azure Machine Learning Python SDK tetszőleges metrikák jelentkezzen. A Futtatás után határozza meg, hogy a Futtatás által előállított számára telepíteni kívánja a modell a metrikák lekérdezése. 

## <a name="snapshot"></a>Pillanatkép

Futtatás elküldésekor az Azure Machine Learning tömöríti a könyvtárat, amely tartalmazza a parancsfájl egy zip-fájlba, és elküldi azokat a számítási célnak. A zip-fájl majd ki kell olvasni, és a szkript futása hiba. Az Azure Machine Learning a zip-fájl is tárolja a futtatási rekord részeként pillanatképet készít. A munkaterület segítségével bárki futtatási rekord kereshetnek és a pillanatkép letöltése.

## <a name="activity"></a>Tevékenység

Egy tevékenység egy hosszú ideig futó művelet jelöli. A következő műveleteket a példák a tevékenységeket:

* Létrehozása vagy törlése egy számítási célnak
* Egy parancsfájlt futtat egy számítási célnak

Tevékenységek biztosíthat az SDK-t vagy a webes felhasználói felületen keresztül értesítéseket, hogy ezek a műveletek állapotát egyszerűen figyelheti.

## <a name="next-steps"></a>További lépések

Ismerkedés az Azure Machine Learning szolgáltatással, tekintse meg:

* [Mi az Azure Machine Learning szolgáltatás?](overview-what-is-azure-ml.md)
* [Rövid útmutató: Hozzon létre egy munkaterületet a Pythonnal](quickstart-get-started.md)
* [Oktatóanyag: A modell tanítása](tutorial-train-models-with-aml.md)
* [Munkaterület létrehozása egy resource manager-sablonnal](how-to-create-workspace-template.md)