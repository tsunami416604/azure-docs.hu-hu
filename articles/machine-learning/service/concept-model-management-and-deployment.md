---
title: 'MLOps: ML-modellek kezelése, üzembe helyezése & monitorozása'
titleSuffix: Azure Machine Learning service
description: 'Ismerje meg, hogyan használhatja a Azure Machine Learning szolgáltatást a MLOps: a modellek üzembe helyezése, kezelése és monitorozása a folyamatos fejlesztés érdekében. A helyi gépen, vagy egyéb forrásokból az Azure Machine Learning szolgáltatás a betanított modellek is telepítheti.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 270ebb6247e812d42ac84698d0073b41c639cb9a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011588"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Kezelheti, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatást a modellek életciklusának kezeléséhez. A Azure Machine Learning egy Machine Learning Operations (MLOps) megközelítést használ, amely javítja a gépi tanulási megoldások minőségét és egységességét. 

Azure Machine Learning szolgáltatás a következő MLOps funkciókat biztosítja:

- **ML-projektek üzembe helyezése bárhonnan**
- **Ml-alkalmazások monitorozása működési és ml-vel kapcsolatos problémák esetén** – a modell bemenetének összehasonlítása a képzés és a következtetések között, a modellre vonatkozó mérőszámok megismerése, valamint monitorozás és riasztások biztosítása a ml-infrastruktúrán.
- Rögzítse azokat az adatmennyiségeket, amelyek szükségesek a (z) **ml-életciklus végpontok közötti naplózásának létrehozásához**, beleértve a modellek közzétételét, a módosítások végrehajtását, valamint a modellek üzembe helyezését vagy éles környezetben való használatát.
- **Automatizálja a végpontok közötti ml-életciklust a Azure Machine learning és az Azure DevOps** , hogy gyakran frissítse a modelleket, tesztelje az új modelleket, és folyamatosan új ml-modelleket a többi alkalmazással és szolgáltatással együtt.

Ha többet szeretne megtudni a MLOps mögötti fogalmakról, valamint arról, hogy azok hogyan vonatkoznak a Azure Machine Learning szolgáltatásra, tekintse meg az alábbi videót.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>ML-projektek üzembe helyezése bárhonnan

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>A betanítási folyamat bekapcsolása reprodukálható folyamatba
Az Azure Machine Learning ML-es folyamatait a modell betanítási folyamatának összes lépését összegyűjtheti az adatok előkészítése és a szolgáltatások kinyerése hiperparaméter a modell kiértékeléséhez.

További információ: [ml folyamatok](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>ML-modellek regisztrálása és nyomon követése

A modell regisztrációja lehetővé teszi, hogy a saját munkaterületén tárolja és megtekintse a modelljeit az Azure-felhőben. A modell beállításjegyzéke megkönnyíti a betanított modellek rendszerezését és nyomon követését.

> [!TIP]
> A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a Azure Machine Learning munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.
 
A regisztrált modelleket név és verzió alapján azonosítjuk. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék növeli a verziót. Használható kereséskor a modellek regisztrálása során is megadható további metaadat-címkéket. A Azure Machine Learning szolgáltatás minden olyan modellt támogat, amelyet a Python 3.5.2-es vagy újabb verziójával lehet betölteni.

> [!TIP]
> Emellett a Azure Machine Learning szolgáltatáson kívül betanított modelleket is regisztrálhat.

Aktív központi telepítésben használt regisztrált modell nem törölhető.
További információkért lásd a [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel)című szakaszt a modell regisztrálása szakaszban.

### <a name="package-and-debug-models"></a>Csomagok és hibakeresési modellek

A modell éles környezetben történő üzembe helyezése előtt egy Docker-rendszerképbe van csomagolva. A legtöbb esetben a rendszerkép létrehozása a háttérben automatikusan megtörténik a telepítés során. A speciális forgatókönyvek esetében manuálisan is megadhatja a rendszerképet.

Ha a telepítés során problémákba ütközne, a hibaelhárításhoz és a hibakereséshez telepítheti a helyi fejlesztési környezetét.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel) és [hibaelhárítása](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Ellenőrzés és profil modelljei

A Azure Machine Learning-szolgáltatás profilkészítés használatával határozza meg a modell telepítésekor használandó ideális CPU-és memória-beállításokat. A modell ellenőrzése a folyamat részeként történik a profilkészítési folyamathoz megadott adatmennyiség használatával.

### <a name="convert-and-optimize-models"></a>Modellek konvertálása és optimalizálása

A modell átalakítása a [neurális hálózati Exchange](https://onnx.ai) (ONNX) megnyitásával növelheti a teljesítményt. Átlagosan a ONNX-re való konvertálás a kétszeri teljesítmény növelését eredményezheti.

A Azure Machine Learning szolgáltatással történő ONNX kapcsolatos további információkért lásd a [ml-modellek létrehozása és](concept-onnx.md) felgyorsítása című cikket.

### <a name="use-models"></a>Modellek használata

A betanított gépi tanulási modellek a Felhőbeli webszolgáltatásként vagy helyileg, a fejlesztési környezetben is üzembe helyezhetők. A modelleket Azure IoT Edge-eszközökre is üzembe helyezheti. A központi telepítések CPU-, GPU-vagy mező-programozható Gate tömböket (FPGA) használhatnak a következtetésekhez. Power BI modelleket is használhat.

Ha webszolgáltatásként vagy IoT Edge eszközként használ modellt, a következő elemeket kell megadnia:

* A szolgáltatásnak/eszköznek beküldött adatértékek kiértékeléséhez használt modell (ek).
* Egy bejegyzési parancsfájl. Ez a szkript fogadja a kérelmeket, a modell (ek) et használja az adatpontszámok kiértékelésére és a válasz visszaküldésére.
* Egy Conda-környezeti fájl, amely leírja a modell (ek) és a bejegyzési parancsfájl által igényelt függőségeket.
* A modell (ek) és a bejegyzési parancsfájl által igényelt további eszközök, például szöveg, adatok stb.

Ezek az eszközök egy Docker-rendszerképbe vannak csomagolva, és webszolgáltatásként vagy IoT Edge modulként vannak telepítve.

Igény szerint a következő paramétereket használhatja az üzemelő példány további finomhangolásához:

* GPU engedélyezése: A GPU-támogatás engedélyezésére szolgál a Docker-rendszerképben. A rendszerképet olyan Microsoft Azure-szolgáltatásokhoz kell használni, mint például a Azure Container Instances, az Azure Kubernetes Service, a Azure Machine Learning számítás vagy az Azure Virtual Machines.
* További Docker-fájl lépései: A Docker-rendszerkép létrehozásakor futtatandó további Docker-lépéseket tartalmazó fájl.
* Alaprendszerkép: Az alaprendszerképként használandó egyéni rendszerkép. Ha nem használ egyéni rendszerképet, az alapképet a Azure Machine Learning szolgáltatás kapja meg.

Megadhatja a cél telepítési platform konfigurációját is. Például a virtuálisgép-család típusa, a rendelkezésre álló memória és a magok száma az Azure Kubernetes szolgáltatásban való üzembe helyezéskor.

A rendszerkép létrehozásakor a rendszer a Azure Machine Learning szolgáltatáshoz szükséges összetevőket is hozzáadja. Például a webszolgáltatás futtatásához és a IoT Edgehoz való interakcióhoz szükséges eszközök.

> [!NOTE]
> A Docker-rendszerképben használt webkiszolgáló vagy IoT Edge-összetevők nem módosíthatók vagy módosíthatók. A Azure Machine Learning szolgáltatás egy webkiszolgáló-konfigurációt és a Microsoft által tesztelt és támogatott IoT Edge összetevőket használ.

#### <a name="web-service"></a>Webszolgáltatás

A webszolgáltatások modelljei a következő számítási célokkal használhatók:

* Azure Container Instance
* Azure Kubernetes Service
* Helyi fejlesztési környezet

A modell webszolgáltatásként való üzembe helyezéséhez a következő elemeket kell megadnia:

* A modellek modellje vagy együttese.
* A modell használatához szükséges függőségek. Például egy olyan parancsfájl, amely fogadja a kérelmeket, és meghívja a modellt, a Conda függőségeit stb.
* Telepítési konfiguráció, amely leírja, hogyan és hol kell telepíteni a modellt.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>IoT Edge-eszközök

A modelleket IoT eszközökkel **Azure IoT Edge modulokon**keresztül is használhatja. IoT Edge modulok üzembe helyezése egy hardvereszközön történik, amely lehetővé teszi a következtetések kiértékelését vagy a modellek pontozását az eszközön.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md).

### <a name="analytics"></a>Elemzés

A Microsoft Power BI támogatja a gépi tanulási modellek használatát az adatelemzéshez. További információ: [Azure Machine learning Integration in Power bi (előzetes verzió)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>ML-alkalmazások figyelése működési és ML-vel kapcsolatos problémák esetén

A figyelés lehetővé teszi, hogy megtudja, milyen adatok kerülnek a modellbe, valamint a visszaadott előrejelzések.

Ez az információ segít megérteni a modell felhasználásának módját. A begyűjtött bemeneti adatok is hasznosak lehetnek a modell jövőbeli verzióinak betanításához.

További információ: [a Model-adatok gyűjtésének engedélyezése](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>A ML életciklus végpontok közötti naplózási nyomvonalának rögzítése

Az Azure ML lehetővé teszi, hogy nyomon követhesse az összes ML-eszköz végpontok közötti naplózását. Konkrétan:

- Az Azure ML a [git-vel integrálva](how-to-set-up-training-targets.md#gitintegration) követheti azokat az információkat, amelyek tárháza/ág/a kód véglegesítve származik.
- Az [Azure ml](how-to-create-register-datasets.md) -adatkészletek segítenek a nyomon követésben és a verziókban.
- Az Azure ML-futtatási előzményei a modell betanításához használt kód, adatok és számítások pillanatképét tárolják.
- Az Azure ML-modell beállításjegyzéke rögzíti a modellhez társított összes metaadatot (amely kitanítja azt, hogy hol van üzembe helyezve, ha a központi telepítése kifogástalan).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>A végpontok közötti ML életciklus automatizálása 

A GitHub és az Azure-folyamatok segítségével folyamatos integrációs folyamatot hozhat létre, amely egy modellt hajt. Egy tipikus forgatókönyv esetén, amikor egy adattudós ellenőrzi a projekt git-tárházában bekövetkező változást, az Azure-folyamat elkezdi a képzés futtatását. A Futtatás eredményei megtekinthetők a betanított modell teljesítmény-jellemzőinek megtekintéséhez. Létrehozhat egy olyan folyamatot is, amely webszolgáltatásként telepíti a modellt.

A [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) megkönnyíti az Azure-folyamatokkal való munkát. A következő fejlesztéseket biztosítja az Azure-folyamatok számára:

* Engedélyezi a munkaterület kijelölését a szolgáltatás kapcsolatának meghatározásakor.
* Lehetővé teszi, hogy a kiadási folyamatokat a betanítási folyamat során létrehozott betanított modellek aktiválja.

További információ az Azure-folyamatok Azure Machine Learning használatával történő használatáról: [ml-modellek folyamatos integrációja és üzembe helyezése az Azure](/azure/devops/pipelines/targets/azure-machine-learning) -folyamatokkal és a [Azure Machine learning Service MLOps](https://aka.ms/mlops) adattárával.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hol és hogyan telepítheti a modellek](how-to-deploy-and-where.md) az Azure Machine Learning szolgáltatással. Az üzembe helyezés példáját lásd [: oktatóanyag: Lemezkép besorolási modell üzembe helyezése](tutorial-deploy-models-with-aml.md)Azure Container Instancesban.

Ismerje meg, hogyan hozhat létre [folyamatos integrációt és üzembe helyezést a ml-modellek Azure-folyamatokkal való](/azure/devops/pipelines/targets/azure-machine-learning)használatával. 

Megtudhatja, hogyan hozhat létre webszolgáltatásként [üzembe helyezett modellt](how-to-consume-web-service.md)használó ügyfélalkalmazások és szolgáltatások.
