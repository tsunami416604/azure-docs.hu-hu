---
title: 'MLOps: ML-modell kezelése'
titleSuffix: Azure Machine Learning
description: 'Ismerje meg a modellkezelést az Azure Machine Learning (MLOps) használatával. A modellek üzembe helyezése, kezelése és figyelése folyamatos fejlesztésük érdekében. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 40e335ee0377c560b15a588269cbdb39cdebca82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477357"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Modellkezelés, -telepítés és figyelés az Azure Machine Learning segítségével

Ebből a cikkből megtudhatja, hogyan használhatja az Azure Machine Learning et a modellek életciklusának kezeléséhez. Az Azure Machine Learning egy Machine Learning-műveletek (MLOps) megközelítést használ. Az MLOps javítja a gépi tanulási megoldások minőségét és konzisztenciáját. 

## <a name="what-is-mlops"></a>Mi az az MLOps?

A Machine Learning Operations (MLOps) [devops-alapelveken](https://azure.microsoft.com/overview/what-is-devops/) és eljárásokon alapul, amelyek növelik a munkafolyamatok hatékonyságát. Például folyamatos integráció, kézbesítés és üzembe helyezés. Az MLOps ezeket az elveket alkalmazza a gépi tanulási folyamatra, a következő céllal:

* A modellek gyorsabb kísérletezése és fejlesztése
* A modellek gyorsabb üzembe helyezése az éles környezetben
* Minőségbiztosítás

Az Azure Machine Learning a következő MLOps-képességeket biztosítja:

- **Reprodukálható ML-folyamatok létrehozása**. A Machine Learning-folyamatok lehetővé teszik az adatelőkészítési, betanítási és pontozási folyamatok megismételhető és újrafelhasználható lépéseimeghatározását.
- **Újrafelhasználható szoftverkörnyezeteket hozhat létre** a modellek betanításához és üzembe helyezéséhez.
- **Regisztráljon, csomagolja és telepítse a modelleket bárhonnan.** A modell használatához szükséges társított metaadatokat is nyomon követheti.
- **Rögzítse a végpontok**között életciklus irányítási adatait. A naplózott adatok közé tartozhat, hogy ki teszi közzé a modelleket, miért történt módosítások, és mikor telepítettek vagy használtak modelleket éles környezetben.
- **Értesítés és riasztás az ml életciklusban lévő eseményekről.** Például a kísérlet befejezése, a modell regisztrációja, a modell üzembe helyezése és az adateltolódás észlelése.
- **A pénzmosással kapcsolatos alkalmazások figyelése a működéssel és a pénzmosással kapcsolatos problémák miatt**. Modellbemenetek összehasonlítása a betanítás és a következtetés között, modellspecifikus metrikák feltárása, valamint figyelés és riasztások biztosítása a dél-ellenőrzési infrastruktúra.
- **Automatizálja a végpontok között az ml-életciklust az Azure Machine Learning és az Azure Pipelines segítségével.** A folyamatok használatával gyakran frissítheti a modelleket, tesztelheti az új modelleket, és folyamatosan új rendszermeghatározási modelleket is kiállíthat a többi alkalmazás és szolgáltatás mellett.

## <a name="create-reproducible-ml-pipelines"></a>Reprodukálható ML-folyamatok létrehozása

Az Azure Machine Learning ml-folyamatai segítségével összefűzheti a modell betanítási folyamatában részt vevő összes lépést.

A rendszervíz-konfigurációs folyamat tartalmazhat lépéseket az adatelőkészítéstől a szolgáltatáskinyerésig a modell kiértékeléséig történő hiperparaméter-hangolásig. További információ: [ML-folyamatok](concept-ml-pipelines.md).

Ha a [Tervező](concept-designer.md) segítségével hozza létre a pénzmosási folyamatokat, bármikor kattinthat a Tervező lap jobb felső részén található **"..."** gombra, majd a **Klónozás parancsra**kattintva. A folyamat klónozása lehetővé teszi a csővezeték tervezésének iterate-je a régi verziók elvesztése nélkül.  

## <a name="create-reusable-software-environments"></a>Újrafelhasználható szoftverkörnyezetek létrehozása

Az Azure Machine Learning-környezetek lehetővé teszik a projektek szoftverfüggőségének nyomon követését és reprodukálását azok fejlődésével. A környezetek lehetővé teszik annak biztosítását, hogy a buildek manuális szoftverkonfigurációk nélkül reprodukálhatók legyenek.

A környezetek leírják a projektek pip és Conda függőségeit, és a modellek betanításához és telepítéséhez is használhatók. További információ: [Mik azok az Azure Machine Learning-környezetek.](concept-environments.md)

## <a name="register-package-and-deploy-models-from-anywhere"></a>Modellek regisztrálása, csomagolása és üzembe helyezése bárhonnan

### <a name="register-and-track-ml-models"></a>Ml-modellek regisztrálása és nyomon követése

A modellregisztráció lehetővé teszi a modellek tárolását és verziózását az Azure-felhőben, a munkaterületen. A modell beállításjegyzék megkönnyíti a betanított modellek rendszerezését és nyomon követését.

> [!TIP]
> A regisztrált modell a modellt egy vagy több fájlt tartalmazó logikai tároló. Ha például egy modell, amely több fájlban van tárolva, regisztrálhatja őket egyetlen modellként az Azure Machine Learning-munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkaphatja a regisztrált összes fájlt.

A regisztrált modelleket a név és a verziószám azonosítja. Ha egy modellt egy már létező néven regisztrál, a beállításjegyzék megnöveli annak verziószámát. A regisztráció során további metaadat-címkék is megadhatók. Ezek a címkék ezután a modell keresésekor használatosak. Az Azure Machine Learning minden olyan modellt támogat, amely a Python 3.5.2-es vagy újabb verzióval tölthető be.

> [!TIP]
> Az Azure Machine Learningen kívül betanított modelleket is regisztrálhat.

Egy aktív központi telepítésben használt regisztrált modell nem törölhető.
További információt a Telepítési modellek regisztermodell szakaszában [talál.](how-to-deploy-and-where.md#registermodel)

### <a name="profile-models"></a>Profilmodellek

Az Azure Machine Learning segítségével megismerheti a modell üzembe helyezésekor létrejön szolgáltatás processzor- és memóriaigényeit. A profilkészítés teszteli a modellt futtató szolgáltatást, és olyan információkat ad vissza, mint a PROCESSZOR-használat, a memóriahasználat és a válaszkés. Azt is előírja, hogy a CPU és a memória ajánlása alapján az erőforrás-használat.
További információt a [Telepítési modellek](how-to-deploy-and-where.md#profilemodel)profilkészítési szakaszában talál.

### <a name="package-and-debug-models"></a>Csomagok és hibakeresési modellek

Mielőtt üzembe helyezne egy modellt éles környezetben, egy Docker-rendszerképbe van csomagolva. A legtöbb esetben a lemezkép létrehozása automatikusan történik a háttérben a központi telepítés során. Manuálisan is megadhatja a képet.

Ha problémákba ütközik a központi telepítés, telepítheti a helyi fejlesztési környezetben a hibaelhárítás és a hibakeresés.

További információt a [Modellek telepítése](how-to-deploy-and-where.md#registermodel) és a központi [telepítések elhárítása című témakörben](how-to-troubleshoot-deployment.md)talál.

### <a name="convert-and-optimize-models"></a>Modellek konvertálása és optimalizálása

A modell [Open Neural Network Exchange](https://onnx.ai) (ONNX) rendszerre történő konvertálása javíthatja a teljesítményt. Az ONNX-re való áttérés átlagosan 2-szeres teljesítménynövekedést eredményezhet.

Az ONNX-ről az Azure Machine Learning használatával kapcsolatos további információkért tekintse meg az [ML-modellek létrehozása és felgyorsítása](concept-onnx.md) című cikket.

### <a name="use-models"></a>Modellek használata

A betanított gépi tanulási modellek webszolgáltatásként vannak telepítve a felhőben vagy helyileg. Modelleket is üzembe helyezhet az Azure IoT Edge-eszközökre. A központi telepítések CPU-, GPU- vagy mezőprogramozható kaputömböket (FPGA) használnak a következtetéshez. A Power BI-ból származó modelleket is használhat.

Ha egy modellt webszolgáltatásként vagy IoT Edge-eszközként használ, a következő elemeket adja meg:

* A szolgáltatásnak/eszköznek küldött adatok pontozására használt modell(ek).
* Egy belépési szkript. Ez a parancsfájl elfogadja a kérelmeket, a modell(eke)t használja az adatok pontozására, és választ ad vissza.
* Egy Azure Machine Learning-környezet, amely leírja a pip és conda függőségek által igényelt modell(ek) és a belépési parancsfájl.
* A modell(ek) és a bejegyzési parancsfájl által megkövetelt további eszközök, például szöveg, adatok stb.

Azt is biztosítja a konfiguráció a cél üzembe helyezési platform. Például a virtuális gép család típusa, a rendelkezésre álló memória és a magok száma az Azure Kubernetes szolgáltatás üzembe helyezésekor.

A rendszerkép létrehozásakor az Azure Machine Learning által igényelt összetevők is hozzáadódnak. Például a webszolgáltatás futtatásához és az IoT Edge-hez szükséges eszközök.

#### <a name="batch-scoring"></a>Batch-pontozás
A kötegelt pontozás t ml-folyamatokon keresztül támogatja. További információ: [Batch-előrejelzések a big data-adatokról.](how-to-use-parallel-run-step.md)

#### <a name="real-time-web-services"></a>Valós idejű webes szolgáltatások

A modellek a **webszolgáltatások** ban a következő számítási célokat használhatja:

* Azure Container Instance
* Azure Kubernetes Service
* Helyi fejlesztési környezet

A modell webszolgáltatásként való üzembe helyezéséhez a következő elemeket kell biztosítania:

* A modell vagy együttese modellek.
* A modell használatához szükséges függőségek. Például egy parancsfájl, amely elfogadja a kérelmeket, és meghívja a modellt, conda függőségeket stb.
* Üzembe helyezési konfiguráció, amely leírja, hogyan és hol kell telepíteni a modellt.

További információ: [Modellek telepítése.](how-to-deploy-and-where.md)

#### <a name="iot-edge-devices"></a>IoT Edge-eszközök

Az **Azure IoT Edge-modulokon**keresztül iot-eszközökkel rendelkező modelleket is használhat. Az IoT Edge-modulok egy hardvereszközre vannak telepítve, amely lehetővé teszi a következtetések levonását vagy a modell pontozását az eszközön.

További információ: [Modellek telepítése.](how-to-deploy-and-where.md)

### <a name="analytics"></a>Elemzés

A Microsoft Power BI támogatja a gépi tanulási modellek használatát az adatelemzéshez. További információ: [Azure Machine Learning integration in Power BI (előzetes verzió)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>A végpontok között a végpontok életciklusának rögzítéséhez szükséges cégirányítási adatok rögzítése

Az Azure ML lehetővé teszi az összes ml-es eszközösszes rendszer-ellenőrzési nyomvonal nyomon követését. Ezek a következők:

- Az Azure ML [integrálja a Git-et,](how-to-set-up-training-targets.md#gitintegration) hogy nyomon követhesse, hogy melyik tárház / ág / véglegesítés i kód származik.
- [Az Azure ML-adatkészletek](how-to-create-register-datasets.md) segítségével nyomon követheti, profil- és verzióadatokat. 
- Az Azure ML-futtatási előzmények egy pillanatképet tárol a modell betanításához használt kódról, adatokról és számítási feladatokról.
- Az Azure ML-modell beállításjegyzék rögzíti a modellhez társított összes metaadatot (melyik kísérlet betanítása, ahol üzembe helyezése alatt áll, ha a központi telepítések kifogástalanok).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Értesítés, automatizálás és riasztás az ml-életciklus eseményeiről
Az Azure ML közzéteszi a legfontosabb eseményeket az Azure EventGridben, amely a gépidő-életciklus eseményeinek értesítésére és automatizálására használható. További információkért tekintse meg [ezt a dokumentumot](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>A pénzmosással kapcsolatos & működési problémák figyelése

Figyelés lehetővé teszi, hogy megértsék, milyen adatokat küld a modell, és az előrejelzések, hogy visszatér.

Ez az információ segít megérteni, hogyan használja a modellt. Az összegyűjtött bemeneti adatok is hasznos lehet a modell jövőbeli verzióinak betanítása.

További információ: [A modelladatgyűjtés engedélyezése.](how-to-enable-data-collection.md)

## <a name="retrain-your-model-on-new-data"></a>A modell újratanítása új adatokra

Gyakran érdemes frissíteni a modellt, vagy akár újrabetanítását a semmiből, ahogy új információkat kap. Néha az új adatok fogadása a tartomány várható része. Máskor, ahogy azt az [adateltolódás (előzetes verzió) észlelése az adatkészleteken](how-to-monitor-datasets.md)című részében tárgyaljuk, a modell teljesítménye romolhat olyan dolgokkal szemben, mint egy adott érzékelő módosítása, a természetes adatok változásai, például a szezonális hatások, vagy a más funkciókhoz való viszonyulva változó funkciók. 

Nincs általános válasz a "Honnan tudom, ha kell átképezni?" de az Azure ML-esemény- és figyelési eszközök, amelyekről korábban tárgyaltak, jó kiindulópontot jelentenek az automatizáláshoz. Miután úgy döntött, hogy átképezi, akkor: 

- Az adatok előfeldolgozása megismételhető, automatizált folyamattal
- Az új modell betanítása
- Hasonlítsa össze az új modell kimeneteit a régi modell kimeneteivel
- Előre definiált feltételek használatával eldöntheti, hogy lecseréli-e a régi modellt 

A fenti lépések egyik témája, hogy az átképzést automatizálni kell, nem pedig ad hoc jellegűnek. [Az Azure Machine Learning-folyamatok](concept-ml-pipelines.md) jó választ jelentenek az adatok előkészítésével, betanításával, ellenőrzésével és üzembe helyezésével kapcsolatos munkafolyamatok létrehozására. Olvassa [el a modellek újratanítását az Azure Machine Learning tervezőjével (előzetes verzió),](how-to-retrain-designer.md) hogy lássa, hogyan illeszkednek a folyamatok és az Azure Machine Learning-tervező egy átképzési forgatókönyvbe. 

## <a name="automate-the-ml-lifecycle"></a>Az ml-életciklus automatizálása 

A GitHub és az Azure-folyamatok segítségével hozhat létre egy folyamatos integrációs folyamatot, amely beiktat egy modellt. Egy tipikus forgatókönyv, amikor egy adattudós ellenőrzi a változás a Git-tárházban egy projekt, az Azure Pipeline elindítja a betanítási futtatás. A futtatás eredményeit ezután meg lehet vizsgálni, hogy a betanított modell teljesítményjellemzői. Létrehozhat egy folyamatot is, amely webszolgáltatásként telepíti a modellt.

Az [Azure Machine Learning-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) megkönnyíti az Azure-folyamatok kal való munkát. A következő fejlesztéseket biztosítja az Azure-folyamatokhoz:

* Szolgáltatáskapcsolat definiálásakor engedélyezi a munkaterület kiválasztását.
* Lehetővé teszi, hogy a kiadási folyamatokat egy betanítási folyamatban létrehozott betanított modellek váltsák ki.

Az Azure Pipelines Azure Machine Learning használatával kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

* [A ml-modellek folyamatos integrációja és üzembe helyezése az Azure Pipelines-szal](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning MLOps-tárház.](https://aka.ms/mlops)
* [Azure Machine Learning MLOpsPython-tárház.](https://github.com/Microsoft/MLOpspython)

Az Azure Data Factory segítségével is létrehozhat egy adatbetöltési folyamatot, amely előkészíti az adatokat a betanítással való használatra. További információ: [Data beestiton pipeline](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>További lépések

További információ a következő források elolvasásával és feltárásával:

+ [Hogyan &, hogy hol üzembe helyezheti a modelleket az](how-to-deploy-and-where.md) Azure Machine Learning segítségével?

+ [Oktatóanyag: Lemezkép-besorolási modell üzembe helyezése az ACI-ben.](tutorial-deploy-models-with-aml.md)

+ [Végpontok között az MLOps-példák tártára](https://github.com/microsoft/MLOps)

+ [AZ Azure-folyamatokat is lemásó ML-modellek CI/CD-je](/azure/devops/pipelines/targets/azure-machine-learning)

+ [Üzembe helyezett modellt használó](how-to-consume-web-service.md) ügyfelek létrehozása

+ [Gépi tanulás nagy léptékben](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Az Azure AI referenciaarchitektúrái & ajánlott eljárások attetszője](https://github.com/microsoft/AI)
