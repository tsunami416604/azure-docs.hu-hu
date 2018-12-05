---
title: Hogyan működik az Azure Machine Learning szolgáltatás?
description: Ismerje meg az architektúra, a terminológiáról és az Azure Machine Learning szolgáltatás alkotó fogalmak. Emellett megismerjük a szolgáltatást, és az Azure-szolgáltatások az Azure Machine Learning szolgáltatás által használt általános munkafolyamata kapcsolatos.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 12/04/2018
ms.openlocfilehash: 054fba06ea4a372fe59fb1ef0c7aa51e72558210
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877394"
---
# <a name="how-the-azure-machine-learning-service-works-architecture-and-concepts"></a>Az Azure Machine Learning szolgáltatás működése: architektúra és fogalmak

Ez a dokumentum architektúra és az Azure Machine Learning szolgáltatás fogalmakat ismerteti. Az alábbi ábrán látható a szolgáltatás fő összetevőit, és általános folyamatát mutatja be, a szolgáltatás használata esetén: 

[![Az Azure Machine Learning szolgáltatás architektúrája és munkafolyamat](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

A munkafolyamat általában kövesse az alábbi lépéseket:

1. Fejlesztés a gépi tanulási parancsfájlok képzési __Python__.
1. Létrehozhat és konfigurálhat egy __számítási célt__.
1. __Küldje el a parancsfájlok__ a konfigurált számítási célnak az adott környezetben való futtatásához. Során képzés, a számítási célnak futtatási rekordok tárolja egy __adattárolója__. Hiba a rekordok kerülnek egy __kísérletezhet__.
1. __A kísérlet lekérdezése__ a naplózott mérőszámok az aktuális és korábbi fut a. A metrikák nem szükséges teljesítendő jelzik, ha 1. lépés, és a parancsfájlok iterálása ikonjához.
1. Egy megfelelő futtató található, ha regisztrálja a megőrzött modellt a a __modell beállításjegyzék__.
1. A pontozó szkript fejleszthet.
1. __Hozzon létre egy rendszerképet__ , és regisztrálja a a __regisztrációs adatbázisba__. 
1. __A lemezkép telepítése__ , egy __webszolgáltatás__ az Azure-ban.


> [!NOTE]
> Amíg ez a dokumentum az Azure Machine Learning által használt kifejezések és fogalmak meghatározása, kifejezések és fogalmak az Azure platform nem definiál. További információ az Azure-platform-terminológia, tekintse meg a [Microsoft Azure szószedet](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Munkaterület

A munkaterület az Azure Machine Learning szolgáltatás a legfelső szintű erőforrás. Biztosít egy központi helyen hoz létre az Azure Machine Learning szolgáltatás használatakor minden összetevő dolgozhat.

A munkaterület biztosítja, hogy a modell betanításához felhasználható számítási célnak listáját. Azt is előzményeket a betanítási futtatás, beleértve a naplók, metrikák, kimeneti és a parancsfájlok pillanatképét. Ezek az információk segítségével határozza meg, melyik betanítási Futtatás a legjobb modellt hoz létre.

A munkaterület regisztrált modellek. Pontozó szkripteket és a egy regisztrált modell segítségével hozzon létre egy rendszerképet. A lemezkép ezután központilag telepítheti az Azure Container Instances Azure Kubernetes Service-ben vagy egy mező-programmable gate array (FPGA) REST-alapú HTTP-végpontként. Azt is telepíthetők az Azure IoT Edge-eszköz modulként.

Több munkaterületet is létrehozhat, és minden munkaterülethez több személy közösen használhat. Munkaterület megosztásakor a munkaterülethez való hozzáférésének szabályozása a következő szerepkörök hozzárendelése a felhasználókhoz:

* Tulajdonos
* Közreműködő
* Olvasó

Amikor létrehoz egy új munkaterületet, automatikusan létrehoz több Azure-a munkaterület által használt erőforrások:

* [Az Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -regisztrálja a docker-tárolók használt betanítás során, és üzembe helyezéséhez.
* [Az Azure Storage](https://azure.microsoft.com/services/storage/) –, a munkaterület az alapértelmezett adattárhoz használatos.
* [Az Azure Application Insights](https://azure.microsoft.com/services/application-insights/) – figyelés a modellek kapcsolatos információkat tárolja.
* [Az Azure Key Vault](https://azure.microsoft.com/services/key-vault/) - tárolók titkos kulcsok által használt számítási céljainak, és egyéb bizalmas adatokat a munkaterület számára szükséges.

> [!NOTE]
> Ahelyett, hogy létrehozná az új verziók, meglévő Azure-szolgáltatásokat is használhatja. 

Az alábbi ábrán a besorolás, a munkaterület:

[![Munkaterület besorolás](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="model"></a>Modell

A legegyszerűbb esetben egy modell olyan kódot, amely veszi a bemenetnek és kimenetet. Egy gépi tanulási modellek létrehozásával magában foglalja a algoritmusok kiválasztása, megadják a data és a hangolási hiperparaméterek. Képzési, amely a betanított modell, amely magában foglalja, mi a modell a betanítási folyamat során megtanult iteratív folyamat.

Futtatás az Azure Machine Learning-modell elő. Egy kívül az Azure Machine Learning betanított modellt is használható. A modell a az Azure Machine Learning szolgáltatás munkaterülete lehet regisztrálni.

Az Azure Machine Learning szolgáltatás keretrendszer független. Egy modellt, például a scikit létrehozásakor használhatja bármely közkedvelt machine learning keretrendszer-xgboost, a PyTorch, a TensorFlow, a Chainer és a CNTK megismeréséhez.

A modell tanítása példát talál a [a rövid útmutató: hozzon létre a machine learning-munkaterület szolgáltatási](quickstart-get-started.md) dokumentumot.

### <a name="model-registry"></a>Modell beállításjegyzék

A modell beállításjegyzék nyomon követi az összes a modellek az Azure Machine Learning szolgáltatás munkaterületen. 

Modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék azt feltételezi, hogy-e egy új verziója. A verziószáma növekszik, és az új modell neve van regisztrálva.

Megadhat további metaadat-címkéket, amikor regisztrálja a modellt, és ezekkel a címkékkel majd használni a modellek keresésekor.

Lemezkép által használt modellek nem törölhető.

Regisztrálja a modellt egy példát, tekintse meg a [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentumot.

## <a name="image"></a>Kép

Képek hardvermódosításainak megbízhatóan helyezhet üzembe modelleket, a modell használatához szükséges összes összetevő együtt. Kép a következő elemeket tartalmazza:

* A modell.
* A pontozó szkript vagy alkalmazás. Ez a szkript segítségével adja át a bemenetet a modell és a modell a hibaüzenettel reagál.
* A modell vagy a pontozó szkript vagy alkalmazás számára szükséges függőségeket.  Például előfordulhat, hogy közé tartozik egy Conda környezet fájlt, amely felsorolja a Python csomagfüggőségek.

Amellyel az Azure Machine Learning hozható létre két típusa van:

* FPGA-kép: a mező-programmable gate array az Azure-felhőben való üzembe helyezése során használt.
* Docker-rendszerkép: célok FPGA eltérő számítási üzembe helyezése során használt. Ha például az Azure Container Instances és az Azure Kubernetes Service-ben.

Lemezkép létrehozása a példát talál a [üzembe helyezése egy kép osztályozási modell az Azure-Tárolópéldányon](tutorial-deploy-models-with-aml.md) dokumentum.

### <a name="image-registry"></a>Regisztrációs adatbázisba

A regisztrációs adatbázisba nyomon követi az lemezképeit a modellből. A rendszerkép létrehozásakor megadhat további metaadat-címkéket. Metaadat-címkéket által a regisztrációs adatbázisba vannak tárolva, és a rendszerkép keresése kérdezhetők le.

## <a name="deployment"></a>Környezet

Központi telepítés rendszer vagy egy webszolgáltatás, amelyet a felhőben üzemeltethető, vagy egy IoT-modul az integrált eszközök központi telepítéséhez a lemezkép által okozott. 

### <a name="web-service"></a>Webszolgáltatás

Egy már üzembe helyezett webszolgáltatás az Azure Container Instances, az Azure Kubernetes Service-ben vagy az a mező-programmable gate arrays (FPGA).
A szolgáltatás, amely magában foglalja a modell, a parancsfájl és a kapcsolódó fájlokat egy lemezkép alapján jön létre. A rendszerképre egy elosztott terhelésű, HTTP-végpontot, amely a web Service pontozási kéréseket fogad.

Az Azure megkönnyíti a webszolgáltatás üzembe figyelni által az Application Insights telemetria és/vagy a modell telemetriai adatok gyűjtése, ha Ön úgy döntött, hogy engedélyezze ezt a funkciót. A telemetriai adatok csak Ön számára elérhető, és az Application Insights és a tárolási fiók példányait tárolja.

Ha engedélyezte az automatikus méretezés, az Azure automatikusan skálázza az üzemelő példány.

Helyezi üzembe a modellt webszolgáltatásként, amely egy példát, tekintse meg a [üzembe helyezése egy kép osztályozási modell az Azure-Tárolópéldányon](tutorial-deploy-models-with-aml.md) dokumentumot.

### <a name="iot-module"></a>IoT-modul

Egy üzembe helyezett IoT-modul Docker-tároló, amely tartalmazza a modellt és a társított szkript vagy alkalmazás és további függőségek. Ezeket a modulokat az Azure IoT Edge segítségével peremhálózati eszközökön vannak üzembe helyezve. 

Ha engedélyezte a figyelés, az Azure az Azure IoT Edge-modul lévő modell gyűjt telemetrikus adatokat. A telemetriai adatok csak Ön számára elérhető, és a tárolási fiók egyik példányában tárolt.

Az Azure IoT Edge biztosítja, hogy a modul fut-e, és figyelheti az azt futtató eszköz.

## <a name="datastore"></a>Adattár

Egy adattár egy tárolási absztrakciós egy Azure Storage-fiók felett. Az adattárhoz használhat egy Azure blob-tároló vagy Azure-fájlmegosztások a háttér-tárolóként. Az egyes munkaterületeken rendelkezik alapértelmezett adattárolót, és előfordulhat, hogy további adattárainak regisztrálja. 

A Python SDK API-t vagy az Azure Machine Learning parancssori felület használatával fájlok tárolásához és lekéréséhez, az adattárral. 

## <a name="run"></a>Futtassa a következőt:

Futtatás a következő egy rekordot, amely a következő információkat tartalmazza:

* Metaadatait, a Futtatás (timestamp, időtartam stb.)
* A parancsfájl által naplózott mérőszámok
* Kimeneti fájlok autocollected által a kísérletet, vagy explicit módon Ön által feltöltött.
* A könyvtár, amely tartalmazza a parancsprogramok, a Futtatás előtt pillanatképét

Futtatás a modell betanításához parancsfájl elküldésekor jön létre. Futtató rendelkezhet, nulla vagy több alárendelt futtatások. Így előfordulhat, hogy a legfelső szintű futtatás két gyermek futtatásakor a rendelkezhetnek, amelyek mindegyike saját gyermek futtatja.

Példa megtekintése a modell által előállított fut, lásd: a [a rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md) dokumentumot.

## <a name="experiment"></a>Kísérlet

Kísérlet egy adott parancsfájlból származó számos futtatások csoportja. Mindig tartozik egy munkaterülethez. Ha elküldi egy futtatás, meg kell adnia egy kísérlet neve. A kísérlet keretében a Futtatás adatait tárolja. Ha futtató nyújt, és adja meg, egy kísérlet neve, amely nem létezik, a rendszer automatikusan létrehoz egy ilyen nevű új kísérlet.

Egy kísérlet használatának példájáért lásd a [a rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md) dokumentum.

## <a name="pipeline"></a>Folyamat

Machine learning-folyamatokat hozhat létre és kezelhet a munkafolyamatok, amelyek összefűzheti a használt gépi tanulási fázisait. Például egy folyamatot tartalmazhat adat-előkészítés, modell betanítása, modell-üzembehelyezés és következtetési fázisait. Az egyes fázisok is magában foglalja a több lépésből, amelyek mindegyike a különböző számítási célnak beavatkozás nélkül futtatható.

Machine learning-folyamatokat ezzel a szolgáltatással kapcsolatban tekintse meg a cikket [folyamatok és az Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Számítási célt

Egy számítási célnak, hogy az a tanítási szkriptet futtatni, vagy a szolgáltatás üzembe helyezésének üzemeltetéséhez használt számítási erőforrás. A támogatott számítási célnak a következők: 

| Számítási célt | Képzés | Környezet |
| ---- |:----:|:----:|
| A helyi számítógépen | ✓ | &nbsp; |
| Az Azure Machine Learning Compute | ✓ | &nbsp; |
| Linux rendszerű virtuális gép az Azure-ban</br>(például a Data Science virtuális gép) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Az Apache Spark for HDInsight | ✓ | &nbsp; |
| Azure Container Instance | &nbsp; | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Field-programmable gate array) | &nbsp; | ✓ |

Számítási célnak csatolt munkaterület. Számítási, a munkaterület felhasználók által megosztott célok nem a helyi gépen.

### <a name="managed-and-unmanaged-compute-targets"></a>Felügyelt és nem felügyelt számítási célnak

**Felügyelt** számítási tárolók létrehozása és felügyelete az Azure Machine Learning szolgáltatás által. Ezek a számítási célok ML feladatokra lettek optimalizálva. __Az Azure Machine Learning Compute__ az egyetlen felügyelt számítási célnak jelenleg (2018. December 4.). A jövőben további felügyelt számítási célnak vehetők fel. Ml-es számítási példányok az Azure Portalon, az Azure Machine Learning SDK vagy az Azure CLI használatával közvetlenül a munkaterület hozható létre. Minden egyéb számítási célokhoz kell létrehozni a munkaterületen kívül, és ezután csatlakozik.

**Nem felügyelt** nincsenek tárolók számítási kezeli az Azure Machine Learning szolgáltatás. Szükség lehet kívül az Azure Machine Learning létrehozása őket, és csatolja őket a munkaterület használata előtt. Ezek a számítási célok karbantartása további lépések szükségesek, vagy a Machine Learning számítási feladatokhoz a teljesítmény javítása.

A képzési számítási céljának kiválasztásakor információkért lásd: a [kiválasztása és használata egy számítási célnak a modell betanításához](how-to-set-up-training-targets.md) dokumentumot.

A központi telepítési számítási céljának kiválasztásakor információkért lásd: a [helyezhet üzembe modelleket az Azure Machine Learning szolgáltatással](how-to-deploy-and-where.md) dokumentumot.

## <a name="run-configuration"></a>Futtatási konfigurációt

A futtatási konfigurációs utasításokat, amely meghatározza a parancsfájl egy adott számítási célnak futtatásának módját. Egy széles definíciókészletet viselkedését, például hogy egy meglévő Python-környezetet használja, vagy egy specifikáció alapján összeállított Conda-környezetet használja-e benne.

Egy futtatási konfigurációt is őrizhető meg, amely tartalmazza a tanítási szkriptet vagy egy memórián belüli objektumként kialakítani és futtató küldéséhez használt a könyvtárán belül található egy fájlba.

Például futtatási konfigurációkat, tekintse meg a [kiválasztása és használata egy számítási célnak a modell betanításához](how-to-set-up-training-targets.md) dokumentumot.

## <a name="training-script"></a>Tanítási szkriptet

A modell betanítását, meg kell adni a tanítási szkriptet és a kapcsolódó fájlokat tartalmazó könyvtárra. Is megad, egy kísérlet neve, amely a betanítás során gyűjtött adatok tárolására szolgál. Során képzés, a teljes címtárra kerül a képzési környezet (számítási célt), és a parancsfájl a futtatási konfiguráció által meghatározott elindult. A könyvtár pillanatképet is tárolja a munkaterületen a kísérlet keretében.

Egy vonatkozó példáért lásd: [hozzon létre egy munkaterületet a Pythonnal](quickstart-get-started.md)

## <a name="logging"></a>Naplózás

A megoldás fejlesztése során, akkor a Python-szkript használata az Azure Machine Learning Python SDK tetszőleges metrikák naplózása. A Futtatás után határozza meg, ha a Futtatás elő a modell számára telepíteni kívánja a metrikák lekérdezése. 

## <a name="snapshot"></a>Pillanatkép

Küldjön el egy futtatás, az Azure Machine Learning tömöríti a könyvtárat, amely tartalmazza a parancsfájl egy zip-fájlba, és elküldi azokat a számítási célnak. A zip-fájl majd ki van bontva, és a szkript futása hiba. Az Azure Machine Learning a zip-fájl is tárolja a futtatási rekord részeként pillanatképet készít. A munkaterület segítségével bárki futtatási rekord kereshetnek és a pillanatkép letöltése.

## <a name="activity"></a>Tevékenység

Egy tevékenység egy hosszú ideig futó művelet jelöli. A következő műveleteket a példák a tevékenységeket:

* Létrehozása vagy törlése egy számítási célnak
* Egy parancsfájlt futtat egy számítási célnak

Tevékenységek is biztosítanak értesítéseket az SDK-t vagy a webes felhasználói Felületet, így könnyedén figyelheti ezek a műveletek állapotát.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozások segítségével az Azure Machine Learning használatának első lépései:

* [Az Azure Machine Learning szolgáltatás ismertetése](overview-what-is-azure-ml.md)
* [Rövid útmutató: Munkaterület létrehozása Python használatával](quickstart-get-started.md)
* [Oktatóanyag: Modell betanítása](tutorial-train-models-with-aml.md)
