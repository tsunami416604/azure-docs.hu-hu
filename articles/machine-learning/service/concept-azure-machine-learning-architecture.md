---
title: Architektúra és alapfogalmak
titleSuffix: Azure Machine Learning service
description: Ismerje meg az architektúra, használati, fogalmak és Azure Machine Learning szolgáltatás alkotó munkafolyamat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: 294b376665ba6b62f79f826520bc933543b38bda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059282"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Az Azure Machine Learning szolgáltatás működése: Architektúra és fogalmak

Ismerje meg az architektúra, fogalmak és a munkafolyamat az Azure Machine Learning szolgáltatás. A szolgáltatás és az általános munkafolyamat a szolgáltatás fő összetevőit az alábbi ábrán láthatók:

[![Munkafolyamat és az Azure Machine Learning szolgáltatás architektúrája](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>Munkafolyamat

A machine learning munkafolyamat általában ez a sorozat a következőképpen:

1. Fejlesztés a gépi tanulási parancsfájlok képzési **Python** vagy vizuális felhasználói felülettel.
1. Létrehozhat és konfigurálhat egy **számítási célt**.
1. **Küldje el a parancsfájlok** a konfigurált számítási célnak az adott környezetben való futtatásához. Során képzés, a parancsfájlok olvasni vagy írni **adattárolója**. Végrehajtás a rekordokat a rendszer menti és **fut** a a **munkaterület** és szerint csoportosított **kísérletek**.
1. **A kísérlet lekérdezése** a naplózott mérőszámok az aktuális és korábbi fut a. A metrikák nem szükséges teljesítendő jelzik, ha 1. lépés, és a parancsfájlok iterálása ikonjához.
1. Miután megtalálta a megfelelő futtató, regisztrálja a megőrzött modellel a **modell beállításjegyzék**.
1. A pontozó szkript, amely a modell fejlesztése és **a modell üzembe helyezése** , egy **webszolgáltatás** az Azure-ban, vagy a- **IoT Edge-eszköz**.

Ezek az az alábbi lépéseket fogja végrehajtani:
+ [Az Azure Machine Learning SDK a Pythonhoz](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Az Azure Machine Learning parancssori felület](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Az Azure Machine Learning a VS Code-bővítmény](how-to-vscode-tools.md)
+  A [vizuális felhasználói felületet (előzetes verzió) az Azure Machine Learning szolgáltatás](ui-concept-visual-interface.md)


## <a name="glossary-of-concepts"></a>Szószedet fogalmak

+ <a href="#workspaces">Workspace</a>
+ <a href="#experiments">Kísérletek</a>
+ <a href="#models">modellek</a>
+ <a href="#run-configurations">Futtatási konfigurációt</a>
+ <a href="#datasets-and-datastores">Adatkészlet és adattárolók</a>
+ <a href="#compute-targets">Számítási céljainak</a>
+ <a href="#training-scripts">Tanítási szkriptet</a>
+ <a href="#runs">Futtatás</a>
+ <a href="#github-tracking-and-integration">Git-követés</a>
+ <a href="#snapshots">Snapshot</a>
+ <a href="#activities">Tevékenység</a>
+ <a href="#images">Rendszerkép</a>
+ <a href="#deployment">Üzembe helyezés</a>
+ <a href="#web-service-deployments">Webszolgáltatások</a>
+ <a href="#iot-module-deployments">IoT-modulok</a>
+ <a href="#ml-pipelines">Gépi Tanulási folyamatok</a>
+ <a href="#logging">Logging</a>

> [!NOTE]
> Bár ez a cikk az Azure Machine Learning szolgáltatás által használt kifejezések és fogalmak meghatározása, kifejezések és fogalmak az Azure platform nem definiál. Az Azure platform terminológia kapcsolatos további információkért lásd: a [Microsoft Azure szószedet](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).


### <a name="workspaces"></a>Munkaterületek

[A munkaterület](concept-workspace.md) , hogy az Azure Machine Learning szolgáltatás legfelső szintű erőforrás. Biztosít egy központi helyen hoz létre az Azure Machine Learning szolgáltatás használatakor minden összetevő dolgozhat.

A besorolás, a munkaterület az alábbi ábra mutatja be:

[![Munkaterület besorolás](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Munkaterületek kapcsolatos további információkért lásd: [Mi az Azure Machine Learning-munkaterület?](concept-workspace.md).

### <a name="experiments"></a>Kísérletek

Egy kísérlet megadott parancsfájl több futtatások csoportja. Mindig tartozik egy munkaterülethez. Ha elküldi egy futtatás, meg kell adnia egy kísérlet neve. A kísérlet keretében a Futtatás adatait tárolja. Ha futtató nyújt, és adja meg, egy kísérlet neve, amely nem létezik, a rendszer automatikusan létrehoz egy új kísérlet a újonnan megadott névvel.

Egy kísérlet használatának példájáért lásd [a rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-run-cloud-notebook.md).

### <a name="models"></a>Modellek

A legegyszerűbb esetben egy modell olyan kódot, amely veszi a bemenetnek és kimenetet. Egy gépi tanulási modellek létrehozásával magában foglalja a algoritmusok kiválasztása, megadják a data és a hangolási hiperparaméterek. Képzési, amely a betanított modell, amely magában foglalja, mi a modell a betanítási folyamat során megtanult iteratív folyamat.

Futtatás az Azure Machine Learning-modell elő. Egy modellt, amely be van tanítva, az Azure Machine Learning-en kívül is használhatja. Az Azure Machine Learning szolgáltatás munkaterületen egy modell regisztrálhat.

Az Azure Machine Learning szolgáltatás keretrendszer független. Amikor létrehoz egy modellt, minden olyan közkedvelt machine learning-keretrendszerek, például a Scikit-ismerje meg, XGBoost, PyTorch, tensorflow-hoz és Chainer is használhatja.

A modell tanítása példát talál [oktatóanyag: Egy rendszerkép osztályozási modell Azure Machine Learning szolgáltatással betanításához](tutorial-train-models-with-aml.md).

A **modell beállításjegyzék** nyomon követi az összes a modellek az Azure Machine Learning szolgáltatás munkaterületen.

Modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék azt feltételezi, hogy-e egy új verziója. A verzió értéke akkor nő, és az új modell ugyanazzal a névvel van regisztrálva.

Ha regisztrálja a modellt, akkor is további metaadat-címkéket adja meg, majd a címkék modellek keresésekor.

Egy aktív központi telepítés által használt modellek nem törölhető.

A modellek regisztrálása egy példa: [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Futtatási beállítások

A futtatási konfigurációs utasításokat, amely meghatározza a parancsfájl a megadott számítási célnak futtatásának módját. A konfiguráció viselkedésének definíciókat, például hogy egy meglévő Python-környezettel vagy egy Conda-környezet, amely egy specifikációt használata széles készletét tartalmazza.

Egy futtatási konfigurációt megőrizhetők a tanítási szkriptet tartalmazó könyvtárra belül egy fájlba, vagy is kell kialakítani, memórián belüli objektumot és egy Futtatás küldéséhez használt.

Például futtatási konfigurációkat, lásd: [kiválasztása és használata egy számítási célnak a modell betanításához](how-to-set-up-training-targets.md).

### <a name="datasets-and-datastores"></a>Az adatkészletek és adattárolók

**Az Azure Machine Learning adatkészletek** könnyebb hozzáférés (előzetes verzió), és dolgozhat az adatokkal. Adatkészletek kezelése az adatok különböző alkalmazási helyzetekben, például a modell betanítása, folyamat-létrehozás. Az Azure Machine Learning SDK, segítségével alapjául szolgáló tároló eléréséhez, Fedezze fel és előkészíti az adatokat, különböző az adatkészlet-definíciók életciklusának kezelése, és képzési és éles környezetben használt Adatkészletekre közötti összehasonlítása.

Az adatkészletek adatok legnépszerűbb formátumokhoz, például az módszert biztosít `from_delimited_files()` vagy `to_pandas_dataframe()`.

További információkért lásd: [létrehozása és regisztrálása az Azure Machine Learning adatkészletek](how-to-create-register-datasets.md).  Adatkészleteket használó további példákért lásd a [notebookok minta](https://aka.ms/dataset-tutorial).

A **adattárolója** egy tárolási absztrakciós van egy Azure storage-fiókot. Az adattár egy Azure blob-tárolóba vagy Azure-fájlmegosztások használhat a háttér-tárolóként. Az egyes munkaterületeken rendelkezik alapértelmezett adattárolót, és további adattárainak regisztrálhatja. A Python SDK API-t vagy az Azure Machine Learning parancssori felület használatával fájlok tárolásához és lekéréséhez, az adattárral.

### <a name="compute-targets"></a>Számítási céljainak

A [számítási célt](concept-compute-target.md) lehetővé teszi, hogy adja meg, amelyen futtatja a tanítási szkriptet vagy a gazdagép helyezheti üzembe számítási erőforrásokat. Ezen a helyen a helyi számítógépre vagy egy olyan felhőalapú számítási erőforrásra lehet. Számítási célnak megkönnyítik a számítási környezetben módosítsa a kód módosítása nélkül. 

Tudjon meg többet a [képzés és a központi telepítés rendelkezésre álló számítási célnak](concept-compute-target.md). 

### <a name="training-scripts"></a>Betanítási szkriptekhez

A modell betanítását, meg kell adni a tanítási szkriptet és a kapcsolódó fájlokat tartalmazó könyvtárra. Is megad, egy kísérlet neve, amely a betanítás során gyűjtött adatok tárolására szolgál. Során képzés, a teljes címtárra kerül a képzési környezet (számítási célt), és a parancsfájl a futtatási konfiguráció által meghatározott elindult. A könyvtár pillanatképet is tárolja a munkaterületen a kísérlet keretében.

Egy vonatkozó példáért lásd: [oktatóanyag: Egy rendszerkép osztályozási modell Azure Machine Learning szolgáltatással betanításához](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Futtatások

Futtatás a következő egy rekordot, amely a következő információkat tartalmazza:

* A Futtatás (timestamp, időtartama és így tovább) kapcsolatos metaadatok
* A parancsfájl által naplózott mérőszámok
* Kimeneti fájlok, amelyek autocollected által a kísérletet, vagy explicit módon feltöltött Ön
* A könyvtár, amely tartalmazza a parancsprogramok, a Futtatás előtt pillanatképét

Futtatás a modell betanításához parancsfájl elküldésekor hozhat létre. Futtató rendelkezhet, nulla vagy több alárendelt futtatások. Például a legfelső szintű futtatás rendelkezhet két gyermek futtatásakor a szolgáltatás, amelyek mindegyike előfordulhat, hogy a saját gyermek futtatni.

Egy modell által készített futtatások nem csupán egy példa: [a rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-run-cloud-notebook.md).

### <a name="github-tracking-and-integration"></a>GitHub nyomon követését és integráció

Amikor egy futtassa, amelyben a forráskönyvtár helyi Git-tárház képzés, a tárház kapcsolatos információkat a futtatási előzmények tárolódik. Például a tárház aktuális véglegesítési Azonosítóját kerül az előzmények részeként. Ez egy estimator, gépi Tanulási folyamatot vagy parancsfájlt futtassa használatával elküldött futtatások együttműködik. Is működik, az SDK-t vagy a Machine Learning parancssori elküldött futtatások.

### <a name="snapshots"></a>Pillanatképek

Futtatás elküldésekor az Azure Machine Learning tömöríti a könyvtárat, amely tartalmazza a parancsfájl egy zip-fájlba, és elküldi azokat a számítási célnak. A zip-fájl majd ki kell olvasni, és a szkript futása hiba. Az Azure Machine Learning a zip-fájl is tárolja a futtatási rekord részeként pillanatképet készít. A munkaterület segítségével bárki futtatási rekord kereshetnek és a pillanatkép letöltése.

> [!NOTE]
> Bekerüljenek a pillanatkép a szükségtelen fájlok megakadályozása érdekében, hogy egy figyelmen kívül hagyása fájl (.gitignore vagy .amlignore). Helyezze a fájlt a pillanatkép-címtárban, és adja hozzá a fájlnevek, figyelmen kívül hagyni. A .amlignore fájlja azonos [szintaxist és a .gitignore fájlt, amelyek az](https://git-scm.com/docs/gitignore). Ha mindkét fájl létezik, a .amlignore fájl élvez elsőbbséget.

### <a name="activities"></a>Tevékenységek

Egy tevékenység egy hosszú ideig futó művelet jelöli. A következő műveleteket a példák a tevékenységeket:

* Létrehozása vagy törlése egy számítási célnak
* Egy parancsfájlt futtat egy számítási célnak

Tevékenységek biztosíthat az SDK-t vagy a webes felhasználói felületen keresztül értesítéseket, hogy ezek a műveletek állapotát egyszerűen figyelheti.

### <a name="images"></a>Képek

Képek hardvermódosításainak megbízhatóan helyezhet üzembe modelleket, együtt kell használni a modell az összes összetevő. Kép a következő elemeket tartalmazza:

* A modell.
* A pontozó szkript vagy alkalmazás. A szkript használatával a modell bemenetet átadni, és a modell a hibaüzenettel reagál.
* A modell vagy a pontozó szkript vagy alkalmazás számára szükséges függőségek. Például előfordulhat, hogy közé tartozik egy Conda környezet fájlt, amely felsorolja a Python csomagfüggőségek.

Az Azure Machine Learning két típusú lemezképek hozhatók létre:

* **FPGA-lemezkép**: Amikor üzembe helyezi a egy mezőben-programmable gate array az Azure-ban használni.
* **Docker-rendszerkép**: Amikor üzembe helyezi a számítási céljainak eltérő FPGA használja. Példák a következők: Azure Container Instances szolgáltatásban és az Azure Kubernetes Service-ben.

Az Azure Machine Learning szolgáltatáshoz biztosít alapképet, amely alapértelmezés szerint használt. A saját egyéni rendszerképeit is megadhatja.

### <a name="image-registry"></a>Regisztrációs adatbázisba

Képek a rendszer katalogizált a **regisztrációs adatbázisba** a munkaterületén. Megadhat további metaadat-címkéket a lemezkép létrehozásakor, hogy később keresse meg a lemezkép lekérdezhetők.

Lemezkép létrehozása a példát talál [egy kép besorolási modell az Azure Container Instancesben üzembe](tutorial-deploy-models-with-aml.md).

Egyéni lemezkép alapján üzembe helyezéséhez egy példa: [modell üzembe helyezése egy egyéni Docker-rendszerkép használatával hogyan](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Környezet

Központi telepítés rendszer vagy egy webszolgáltatás, amelyet a felhőben üzemeltethető, vagy egy IoT-modul az integrált eszközök központi telepítéséhez a modell által okozott.

#### <a name="web-service-deployments"></a>Webszolgáltatások üzembe helyezéséhez

Egy már üzembe helyezett webszolgáltatás az Azure Container Instances, az Azure Kubernetes Service-ben vagy az FPGA-kban. A szolgáltatás létrehozása a modell, a parancsfájl és a kapcsolódó fájlokat. Ezek a képet, amely a web service a futási idő környezetet biztosít, vannak beágyazva. A rendszerkép egy elosztott terhelésű, HTTP-végpontot, amely megkapja a pontozási a web Service küldött kérések rendelkezik.

Az Azure megkönnyíti a webszolgáltatás üzembe figyelemmel gyűjtése az Application Insights telemetria- vagy modell telemetriai adatokat, ha úgy döntött, ez a funkció engedélyezéséhez. A telemetriai adatok csak Ön számára elérhető, és az Application Insights és a tárolási fiók példányait tárolja.

Ha engedélyezte az automatikus méretezés, az Azure automatikusan méretezi a központi telepítés.

Helyezi üzembe a modellt webszolgáltatásként, amely egy példa: [egy kép besorolási modell az Azure Container Instancesben üzembe](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>IoT-modul központi telepítések

Egy üzembe helyezett IoT-modul Docker-tároló, amely tartalmazza a modellt és a társított szkript vagy alkalmazás és további függőségek. A peremhálózati eszközökön az Azure IoT Edge segítségével telepítheti ezeket a modulokat.

Ha engedélyezte a figyelés, az Azure az Azure IoT Edge-modul lévő modell gyűjt telemetrikus adatokat. A telemetriai adatok csak Ön számára elérhető, és a storage-fiók példány található.

Az Azure IoT Edge biztosítja, hogy a modul fut, és az azt futtató eszköz figyeli.

### <a name="ml-pipelines"></a>Gépi Tanulási folyamatok

Machine learning segítségével folyamatokat hozhat létre és kezelhet a munkafolyamatok, amelyek összefűzheti a machine learning fázisait. Például egy folyamatot tartalmazhat adat-előkészítés, modell betanítása, modell-üzembehelyezés és következtetésekhez/pontozási fázisait. Az egyes fázisok is magában foglalja a több lépésből, amelyek mindegyike a különböző számítási célnak beavatkozás nélkül futtatható.

Machine learning-folyamatokat ezzel a szolgáltatással kapcsolatos további információkért lásd: [folyamatok és az Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Naplózás

Amikor a megoldás fejlesztése, akkor a Python-szkript használata az Azure Machine Learning Python SDK tetszőleges metrikák jelentkezzen. A Futtatás után határozza meg, hogy a Futtatás által előállított számára telepíteni kívánja a modell a metrikák lekérdezése.

### <a name="next-steps"></a>További lépések

Ismerkedés az Azure Machine Learning szolgáltatással, tekintse meg:

* [Mi az Azure Machine Learning szolgáltatás?](overview-what-is-azure-ml.md)
* [Az Azure Machine Learning szolgáltatás munkaterület létrehozása](setup-create-workspace.md)
* [Oktatóanyag: (1. rész): A modell tanítása](tutorial-train-models-with-aml.md)
