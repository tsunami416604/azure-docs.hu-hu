---
title: 'MLOps: ML-modell kezelése'
titleSuffix: Azure Machine Learning
description: 'További információ a Azure Machine Learning (MLOps) modellel történő kezeléséről. A modellek üzembe helyezése, kezelése és monitorozása folyamatosan fejleszthető. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9cde171f1c031b4b02a71fe0dac1a689e3ec0722
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772584"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: modellek kezelése, üzembe helyezés és monitorozás Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learning a modellek életciklusának kezeléséhez. A Azure Machine Learning a Machine Learning Operations (MLOps) módszert használja. A MLOps javítja a gépi tanulási megoldások minőségét és egységességét. 

A Azure Machine Learning a következő MLOps képességeket biztosítja:

- **Reprodukálható ml-folyamatokat hozhat létre**. A folyamatok lehetővé teszik a megismételhető és újrafelhasználható lépések megadását az adatelőkészítési, a képzési és a pontozási folyamatokhoz.
- **Modellek regisztrálása, becsomagolása és üzembe helyezése bárhonnan** , valamint a modell használatához szükséges metaadatok nyomon követése.
- **Rögzítse a végpontok közötti ml életciklus rögzítéséhez szükséges irányítási adatmennyiséget**, beleértve a modellek közzétételi modelljeit, a módosítások elvégzésének okát, valamint a modellek üzembe helyezését vagy éles környezetben való használatát.
- **Értesítés és riasztás a ml-életciklus eseményeiről** , mint például a kísérletezés befejezése, a modell regisztrálása, a modell üzembe helyezése és az adateltolódások észlelése.
- **Ml-alkalmazások figyelése operatív és ml-vel kapcsolatos problémák esetén**. Hasonlítsa össze a modell bemeneteit a képzés és a következtetések között, Fedezze fel a modellre jellemző mérőszámokat, és figyelje a ML-infrastruktúráját.
- **Automatizálja a teljes körű ml-életciklust a Azure Machine learning és az Azure DevOps** a modellek gyakori frissítéséhez, az új modellek teszteléséhez, valamint az új ml-modellek folyamatos üzembe helyezéséhez más alkalmazások és szolgáltatások mellett.

## <a name="create-reproducible-ml-pipelines"></a>Reprodukálható ML-folyamatok létrehozása

A modell betanítási folyamatában szereplő összes lépés összefűzéséhez használjon Azure Machine Learning ML-es folyamatokat.

A ML-folyamatok az adatok előkészítésének lépéseit is tartalmazhatják, hogy hiperparaméter a hangolást a modell kiértékeléséhez. További információ: [ml folyamatok](concept-ml-pipelines.md).

Ha a [Designer](concept-designer.md) használatával hozza létre az ml-folyamatokat, akkor a tervező oldal jobb felső sarkában található **"..."** gombra kattintva bármikor kiválaszthatja a **klónozás**elemet. A folyamat klónozása lehetővé teszi, hogy a régi verziók elvesztése nélkül megismételje a folyamat kialakítását.  

## <a name="register-package-and-deploy-models-from-anywhere"></a>Modellek regisztrálása, becsomagolása és üzembe helyezése bárhonnan

### <a name="register-and-track-ml-models"></a>ML-modellek regisztrálása és nyomon követése

A modell regisztrációja lehetővé teszi, hogy a saját munkaterületén tárolja és megtekintse a modelljeit az Azure-felhőben. A modell beállításjegyzéke megkönnyíti a betanított modellek rendszerezését és nyomon követését.

> [!TIP]
> A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a Azure Machine Learning munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

A regisztrált modelleket név és verzió alapján azonosítjuk. Minden alkalommal, amikor ugyanazzal a névvel regisztrál egy modellt, a beállításjegyzék megnöveli a verziót. A regisztráció során további metaadat-címkéket is megadhat. Ezeket a címkéket a rendszer a modell keresésekor használja. A Azure Machine Learning a Python 3.5.2-es vagy újabb verziójával tölthető modelleket támogat.

> [!TIP]
> A Azure Machine Learningon kívül betanított modelleket is regisztrálhat.

Aktív központi telepítésben használt regisztrált modell nem törölhető.
További információkért lásd a [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel)című szakaszt a modell regisztrálása szakaszban.

### <a name="package-and-debug-models"></a>Csomagok és hibakeresési modellek

A modell éles környezetben történő üzembe helyezése előtt egy Docker-rendszerképbe van csomagolva. A legtöbb esetben a rendszerkép létrehozása a háttérben automatikusan megtörténik a telepítés során. Manuálisan is megadhatja a rendszerképet.

Ha a telepítés során problémákba ütközne, a hibaelhárításhoz és a hibakereséshez telepítheti a helyi fejlesztési környezetét.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel) és [hibaelhárítása](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Ellenőrzés és profil modelljei

A Azure Machine Learning használhatja a profilkészítést a modell üzembe helyezéséhez használt ideális CPU-és memória-beállítások meghatározásához. A modell ellenőrzése a folyamat részeként történik a profilkészítési folyamathoz megadott adatmennyiség használatával.

### <a name="convert-and-optimize-models"></a>Modellek konvertálása és optimalizálása

A modell átalakítása a [neurális hálózati Exchange](https://onnx.ai) (ONNX) megnyitásával növelheti a teljesítményt. Átlagosan a ONNX-re való konvertálás a kétszeri teljesítmény növelését eredményezheti.

A Azure Machine Learningrel való ONNX kapcsolatos további információkért tekintse meg a [ml-modellek létrehozása és felgyorsítása](concept-onnx.md) című cikket.

### <a name="use-models"></a>Modellek használata

A betanított gépi tanulási modellek a felhőben vagy helyileg webszolgáltatásként vannak üzembe helyezve. A modelleket Azure IoT Edge-eszközökre is üzembe helyezheti. A központi telepítések CPU-, GPU-vagy mező-programozható Gate-tömböket (FPGA) használnak a következtetésekhez. Power BI modelleket is használhat.

Ha webszolgáltatásként vagy IoT Edge eszközként használ modellt, a következő elemeket kell megadnia:

* A szolgáltatásnak/eszköznek beküldött adatértékek kiértékeléséhez használt modell (ek).
* Egy bejegyzési parancsfájl. Ez a szkript fogadja a kérelmeket, a modell (ek) et használja az adatpontszámok kiértékelésére és a válasz visszaküldésére.
* Egy Conda-környezeti fájl, amely leírja a modell (ek) és a bejegyzési parancsfájl által igényelt függőségeket.
* A modell (ek) és a bejegyzési parancsfájl által igényelt további eszközök, például szöveg, adatok stb.

Megadhatja a cél telepítési platform konfigurációját is. Például a virtuálisgép-család típusa, a rendelkezésre álló memória és a magok száma az Azure Kubernetes szolgáltatásban való üzembe helyezéskor.

A rendszerkép létrehozásakor a Azure Machine Learning által igényelt összetevőket is hozzáadja a rendszer. Például a webszolgáltatás futtatásához és a IoT Edgehoz való interakcióhoz szükséges eszközök.

#### <a name="batch-scoring"></a>Kötegelt kiértékelés
A Batch pontozása ML-folyamatok esetében támogatott. További információ: batch- [előrejelzések Big Data](how-to-run-batch-predictions.md).

#### <a name="real-time-web-services"></a>Valós idejű webszolgáltatások

A **webszolgáltatások** modelljei a következő számítási célokkal használhatók:

* Azure Container Instance
* Azure Kubernetes Service
* Helyi fejlesztési környezet

A modell webszolgáltatásként való üzembe helyezéséhez a következő elemeket kell megadnia:

* A modellek modellje vagy együttese.
* A modell használatához szükséges függőségek. Például egy olyan parancsfájl, amely fogadja a kérelmeket, és meghívja a modellt, a Conda függőségeit stb.
* Telepítési konfiguráció, amely leírja, hogyan és hol kell telepíteni a modellt.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Eszközök IoT Edge

A modelleket IoT eszközökkel **Azure IoT Edge modulokon**keresztül is használhatja. IoT Edge modulok üzembe helyezése egy hardvereszközön történik, amely lehetővé teszi a következtetések kiértékelését vagy a modellek pontozását az eszközön.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md).

### <a name="analytics"></a>Elemzés

A Microsoft Power BI támogatja a gépi tanulási modellek használatát az adatelemzéshez. További információ: [Azure Machine learning Integration in Power bi (előzetes verzió)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Rögzítse a végpontok közötti ML életciklus rögzítéséhez szükséges irányítási adatmennyiséget

Az Azure ML lehetővé teszi az összes ML-eszköz végpontok közötti naplózásának nyomon követését. Konkrétan:

- Az Azure ML a git szolgáltatással [integrálva](how-to-set-up-training-targets.md#gitintegration) követheti azokat az információkat, amelyeken a tárház/ág/a kód véglegesítve származik.
- Az [Azure ml-adatkészletek](how-to-create-register-datasets.md) segítenek a nyomon követésben, a profilban és a verziószámozásban. 
- Az Azure ML futtatási előzményei a modell betanításához használt kód, adatok és számítások pillanatképét tárolják.
- Az Azure ML-modell beállításjegyzéke rögzíti a modellhez társított összes metaadatot (amely kitanítja azt, hogy hol van üzembe helyezve, ha a központi telepítése kifogástalan).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>A ML-életciklus eseményeinek értesítése, automatizálása és riasztása
Az Azure ML kulcsfontosságú eseményeket tesz közzé az Azure EventGrid, amely a ML-életciklus eseményeinek értesítésére és automatizálására használható. További információkért tekintse meg [ezt a dokumentumot](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Az Operational & ML-vel kapcsolatos problémák monitorozása

A figyelés lehetővé teszi, hogy megtudja, milyen adatok kerülnek a modellbe, valamint a visszaadott előrejelzések.

Ez az információ segít megérteni a modell felhasználásának módját. A begyűjtött bemeneti adatok is hasznosak lehetnek a modell jövőbeli verzióinak betanításához.

További információ: [a Model-adatok gyűjtésének engedélyezése](how-to-enable-data-collection.md).

## <a name="automate-the-ml-lifecycle"></a>A ML életciklus automatizálása 

A GitHub és az Azure-folyamatok segítségével folyamatos integrációs folyamatot hozhat létre, amely egy modellt hajt. Egy tipikus forgatókönyv esetén, amikor egy adattudós ellenőrzi a projekt git-tárházában bekövetkező változást, az Azure-folyamat elkezdi a képzés futtatását. A Futtatás eredményei megtekinthetők a betanított modell teljesítmény-jellemzőinek megtekintéséhez. Létrehozhat egy olyan folyamatot is, amely webszolgáltatásként telepíti a modellt.

A [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) megkönnyíti az Azure-folyamatokkal való munkát. A következő fejlesztéseket biztosítja az Azure-folyamatok számára:

* Engedélyezi a munkaterület kijelölését a szolgáltatás kapcsolatának meghatározásakor.
* Lehetővé teszi, hogy a kiadási folyamatokat a betanítási folyamat során létrehozott betanított modellek aktiválja.

További információ az Azure-folyamatok Azure Machine Learning használatával történő használatáról: [ml-modellek folyamatos integrációja és üzembe helyezése az Azure-folyamatokkal](/azure/devops/pipelines/targets/azure-machine-learning) és a [Azure Machine learning MLOps](https://aka.ms/mlops) adattárral.

## <a name="next-steps"></a>Következő lépések

További információ: a következő erőforrások olvasása és feltárása:

+ [Hogyan & a modellek üzembe helyezését](how-to-deploy-and-where.md) Azure Machine learning

+ [Oktatóanyag: lemezkép-besorolási modell üzembe helyezése az ACI-ban](tutorial-deploy-models-with-aml.md).

+ [A végpontok közötti MLOps-példák tárháza](https://github.com/microsoft/MLOps)

+ [CI/CD/ML modellek Azure-folyamatokkal](/azure/devops/pipelines/targets/azure-machine-learning)

+ [Üzembe helyezett modellt](how-to-consume-web-service.md) használó ügyfelek létrehozása

+ [Gépi tanulás nagy léptékben](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI-referenciák architektúrái & ajánlott eljárások rep](https://github.com/microsoft/AI)
