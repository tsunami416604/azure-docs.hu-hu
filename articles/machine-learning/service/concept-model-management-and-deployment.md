---
title: 'MLOps: Kezelheti, üzembe helyezheti és figyelheti a gépi Tanulási modelleket'
titleSuffix: Azure Machine Learning service
description: 'Ismerje meg, hogyan használható az Azure Machine Learning szolgáltatás MLOps: telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető. A helyi gépen, vagy egyéb forrásokból az Azure Machine Learning szolgáltatás a betanított modellek is telepítheti.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 0eaf48f57c3011222b71a63d703e1ccec7aca001
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692833"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Kezelheti, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás

Ebből a cikkből megtudhatja, hogyan felügyelheti a modellek Azure Machine Learning szolgáltatás használatával. A Machine Learning-műveletek (MLOps) módszer, amely javítja a minőség és a konzisztencia, a gépi tanulási megoldások az Azure Machine Learninget használja. Az Azure Machine Learning szolgáltatás a következő MLOps képességeket biztosítja:

* Az Azure-folyamatok integrációja. Folyamatos integrációs és üzembe helyezési munkafolyamatokat a modellek meghatározásához.
* A modell adatait, amely fenntartja a betanított modellek különböző verzióinak.
* Modell érvényesítése. Automatikusan ellenőrzése a betanított modellek, és válassza ki az optimális konfigurációt éles környezetben történő üzembe helyezést.
* Modellek üzembe helyezése a felhőben, webszolgáltatásként helyileg vagy az IoT Edge-eszközökön.
* Az üzembe helyezett modell teljesítményét, figyeléséhez, így fejlesztései ösztönözheti a modell új verziója.

Több felvesszük Önnel a MLOps, és hogyan alkalmazhatók az Azure Machine Learning szolgáltatáshoz fogalmakat, tekintse meg a következő videót.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>Integráció az Azure-folyamatok

Az Azure-folyamatok használatával hozzon létre egy folyamatos integrációs folyamat, amely a betanítja a modellt. Egy tipikus forgatókönyv Adatszakértő ellenőrzi a projekt Git-tárház be módosítása az Azure-folyamat megkezdi a betanítási Futtatás. A Futtatás eredményeit is ellenőrizni kell a teljesítményjellemzők a betanított modell megtekintéséhez. Létrehozhat egy folyamatot, amely a modellt webszolgáltatásként helyezi üzembe.

A [Azure Machine Learning-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) megkönnyíti az Azure-folyamatok használata. A következő fejlesztéseket biztosít az Azure-folyamatok:

* Munkaterület kiválasztása lehetővé teszi a szolgáltatáskapcsolódási meghatározásakor.
* Lehetővé teszi, hogy kibocsátási folyamatok által létrehozott a betanítási folyamat betanított modellek.

Az Azure Machine Learning Azure folyamatok használatával további információkért lásd: a [folyamatos integrációs és üzembe helyezési rendelkező Azure Machine Learning modellek](/azure/devops/pipelines/targets/azure-machine-learning) cikkben és a [Azure Machine Learning szolgáltatás MLOps](https://aka.ms/mlops) tárház.

## <a name="convert-and-optimize-models"></a>Átalakítás és a modellek optimalizálása

A modell konvertálása [nyílt Neurális hálózat Exchange](https://onnx.ai) (ONNX) javíthatja a teljesítményt. Átlagosan ONNX konvertálása partíciónként akár egy 2 x teljesítmény növekedését.

Az Azure Machine Learning szolgáltatás a ONNX további információkért lásd: a [létrehozás és gépi Tanulási modelleket](concept-onnx.md) cikk.

## <a name="register-models"></a>Modellek regisztrálása

Modell regisztrálását segítségével tárolja és verzió a modellek az Azure-felhőben, a munkaterületén. A modell beállításjegyzék megkönnyíti a betanított modellek rendszerezéséhez és nyomon követésére.

> [!TIP]
> Az Azure Machine Learning szolgáltatás kívül betanított modellek is rögzítheti.
 
A regisztrált modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék növeli a verziót. Használható kereséskor a modellek regisztrálása során is megadható további metaadat-címkéket. Az Azure Machine Learning szolgáltatásban minden olyan modell, amely lehet betölteni a Python 3.5.2-es verzióját használja, vagy magasabb támogatja.

Egy aktív központi telepítés használt modellek nem törölhető.

További információkért lásd: a register-modell szakasz, [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel).

Regisztrálás a modell pickle formátumban tárolja egy példa: [oktatóanyag: Egy rendszerkép osztályozási modell betanításához](tutorial-deploy-models-with-aml.md).

## <a name="package-and-debug-models"></a>Csomag- és hibakeresési minták

Éles környezetben a modell üzembe helyezése előtt, kerül csomagokként egy Docker-rendszerképet. A legtöbb esetben lemezképkészítés automatikusan történik a háttérben üzembe helyezés során. A speciális alkalmazási manuálisan megadhatja a lemezképet.

Ha hibákba ütközne való üzembe helyezéséhez, a hibaelhárítás és hibakeresés a helyi fejlesztési környezetet is telepítheti.

További információkért lásd: [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel) és [üzemelő példányainak hibaelhárítása](how-to-troubleshoot-deployment.md).

## <a name="validate-and-profile-models"></a>Ellenőrizze és modellek profil

Az Azure Machine Learning szolgáltatás segítségével a profilkészítés a modell üzembe helyezésekor használandó ideális CPU és memória beállításainak megállapítása. Modell érvényesítési ezt a folyamatot, felhasználó által megadott adatok segítségével a profilkészítési folyamat részeként történik.

## <a name="use-models"></a>Modellek használata

Betanított gépi tanulási modelleket webszolgáltatásként a felhőben vagy helyileg, a fejlesztési környezet is telepíthető. Modellek az Azure IoT Edge-eszközökön is telepítheti. Központi telepítések használhatja CPU, a GPU-n és a mező-programmable gate arrays (FPGA) következtetési. A Power bi-BÓL modellek is használhatja.

A modell egy webszolgáltatás vagy az IoT Edge-eszköz használatakor meg kell adnia a következő elemek:

* A modellek, amelyek a szolgáltatás/eszközre elküldött adatok pontozása.
* Egy bejegyzés szkriptet. Ez a szkript kéréseket fogad el, használja a modellek pontozása az adatokat, és adott vissza választ.
* Conda környezet fájl, amely leírja a függőségeket, a modellek és bejegyzés parancsfájlhoz szükséges.
* Minden további tartalmaktól, például szöveg, adatok, stb. a modellek és bejegyzés parancsprogram szükséges.

Ezek az eszközök Wizard egy Docker-rendszerképet, és a webszolgáltatás vagy az IoT Edge-modul üzembe helyezve.

A következő paraméterek segítségével igény szerint további hangolás az üzemelő példány:

* GPU engedélyezése: Használja a Docker-rendszerkép a GPU-támogatás engedélyezése. A lemezkép a Microsoft Azure-szolgáltatásokra például az Azure Container Instances, az Azure Kubernetes Service, Azure Machine Learning Compute vagy Azure virtuális gépek kell használni.
* Extra docker-fájl lépéseket: Egy fájl, amely tartalmazza a Docker további lépéseket a Docker-rendszerkép létrehozásához futtassa.
* Alaplemezkép: Egyéni kép az alap rendszerképet használja. Ha egyéni lemezkép nem használja, az alaprendszerképet az Azure Machine Learning szolgáltatás által biztosított.

Emellett adja meg a célplatformot telepítési konfigurációját. Például a virtuális gép címcsalád-típus, rendelkezésre álló memória és magok Azure Kubernetes Service-ben való üzembe helyezés esetén.

Ha a kép jön létre, az Azure Machine Learning szolgáltatás szükséges összetevőket is bekerülnek. Például eszközök szükségesek a webszolgáltatások futtatásához és az IoT Edge segítségével kezelheti.

> [!NOTE]
> Nem lehet módosítani, vagy változtassa meg a webalkalmazás-kiszolgáló vagy a Docker-rendszerképet használja az IoT Edge-összetevők. Az Azure Machine Learning szolgáltatás egy webes konfigurációját és a Microsoft által támogatott és tesztelt IoT Edge-összetevőket használ.

### <a name="web-service"></a>Webszolgáltatás

Használhatja a modellek **webszolgáltatások** számítási célok a következők:

* Azure Container Instance
* Azure Kubernetes Service
* Helyi fejlesztési környezetbe

A modellt webszolgáltatásként üzembe helyezéséhez meg kell adnia a következő elemek:

* A modell vagy ensemble modellek.
* A modell használatához szükséges függőségeket. Például egy parancsfájlt, amely elfogadja a kérelmeket, és hívja meg a modellt, a conda-függőségeket stb.
* Központi telepítés konfigurálása, amely azt ismerteti, hogyan és hol a modell rendszerbe.

További információkért lásd: [modellek üzembe helyezése](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>IoT Edge-eszközök


Az IoT-eszközök segítségével is használhatja a modellek **Azure IoT Edge-modulok**. IoT Edge-modulok hardvereszköz, amely lehetővé teszi a következtetésekhez, vagy a modell pontozása, az eszközön telepített.

További információkért lásd: [modellek üzembe helyezése](how-to-deploy-and-where.md).

### <a name="analytics"></a>Elemzés

A Microsoft Power BI támogatja a machine learning-modellek data Analytics. További információkért lásd: [Azure Machine Learning-integráció a Power bi-ban (előzetes verzió)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="monitor-and-collect-data"></a>Adatainak figyelésére és gyűjtésére

Figyelési lehetővé teszi, hogy jobban megismerhesse, milyen adatokat küld a rendszer a modell és az előrejelzések, amely azt adja vissza.

Ezek az információk segítségével megismerheti, hogyan a modellt használják. A bemeneti adatokat gyűjti össze a modell betanítási jövőbeli verzióinak hasznos lehet.

További információkért lásd: [a modelladatok gyűjtésének engedélyezése](how-to-enable-data-collection.md).

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hol és hogyan telepítheti a modellek](how-to-deploy-and-where.md) az Azure Machine Learning szolgáltatással. A központi telepítés egy példa: [oktatóanyag: Egy kép besorolási modell az Azure Container Instancesben üzembe](tutorial-deploy-models-with-aml.md).

Ismerje meg, hogyan hozhat létre [folyamatos integrációs és üzembe helyezési rendelkező Azure Machine Learning modellek](/azure/devops/pipelines/targets/azure-machine-learning). 

Ismerje meg, hogyan hozhat létre ügyfél alkalmazások és szolgáltatások, amelyek [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md).
