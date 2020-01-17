---
title: 'Oktatóanyag: rendszerképek besorolása TensorFlow-modell betanítása és üzembe helyezése a Visual Studio Code Azure Machine Learning használatával'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet betanítani és üzembe helyezni a lemezkép-besorolási modellt a TensorFlow és a Azure Machine Learning Visual Studio Code bővítmény használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 899681f2bb9c3ef2a0368015a58db30a843738f5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157555"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>Képbesorolási TensorFlow modell betanítása és üzembe helyezése a Visual Studio Code Azure Machine Learning használatával

Megtudhatja, hogyan taníthat a képbesorolási modellt a kézzel írt számok TensorFlow és a Visual Studio Code Azure Machine Learning használatával való felismeréséhez.

Eben az oktatóanyagban az alábbi feladatokkal fog megismerkedni:

> [!div class="checklist"]
> * A kód értelmezése
> * Munkaterületek létrehozása
> * Kísérlet létrehozása
> * Számítógép-tárolók konfigurálása
> * Konfigurációs fájl futtatása
> * Modell betanítása
> * Modell regisztrálása
> * Modell üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés. Ha még nem rendelkezik ilyennel, regisztráljon a [Azure Machine learning ingyenes vagy fizetős verziójának](https://aka.ms/AMLFree)kipróbálásához.
- Telepítse a [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)-ot, amely egy egyszerű, platformfüggetlen Kódszerkesztő.
- Azure Machine Learning Studio Visual Studio Code-bővítményt. A telepítési utasításokért lásd a [telepítő Azure Machine learning a Visual Studio Code bővítmény oktatóanyaga](./tutorial-setup-vscode-extension.md) című témakört.

## <a name="understand-the-code"></a>A kód értelmezése

Ennek az oktatóanyagnak a kódja a TensorFlow-t használja a képbesorolás Machine learning-modell betanításához, amely kategorizálja a kézzel írt számokat a 0-9-ból. Ezt egy olyan neurális hálózat létrehozásával végzi, amely a 28 px x 28 px képponthoz tartozó képpont-értékeket bemenetként veszi át, és egy 10 valószínűségből álló listát ad eredményül, amely a besorolt számjegyek mindegyike. Alább látható egy példa arra, hogy az adatnézet milyen módon néz ki.  

![MNIST számjegyei](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Az oktatóanyag kódjának beszerzéséhez töltse le és a dezipping a [vs Code-eszközöket az AI-tárházhoz](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) bárhol a számítógépen.

## <a name="create-a-workspace"></a>Munkaterületek létrehozása

Ahhoz, hogy egy alkalmazást Azure Machine Learning hozzon létre, egy munkaterületet kell létrehoznia. A munkaterület a modellek betanításához, valamint a betanított modellekhez is tartalmaz erőforrásokat. További információ: [Mi az a munkaterület](./concept-workspace.md). 

1. A Visual Studio Code tevékenység sávján válassza az **Azure** ikont a Azure Machine learning nézet megnyitásához.
1. Kattintson a jobb gombbal az Azure-előfizetésre, és válassza a **Munkaterület létrehozása**lehetőséget. 
    
    > [!div class="mx-imgBorder"]
    > ![Munkaterület létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Alapértelmezés szerint a létrehozás dátumát és időpontját tartalmazó név jön létre. A parancs palettáján módosítsa a nevet "TeamWorkspace" értékre, majd nyomja le az **ENTER**billentyűt.
1. Válassza az **Új erőforráscsoport létrehozása** elemet a parancs palettáján. 
1. A Command paletta szövegmezőbe írja be a "TeamWorkspace-RG" kifejezést, majd nyomja le az **ENTER**billentyűt. 
1. A parancs palettáján válassza ki a munkaterület helyét. Ajánlott olyan helyet választani, amely a modell üzembe helyezéséhez legközelebb eső helyen található. Ebben az esetben válassza az **USA 2. nyugati**régióját.
1. Amikor a rendszer rákérdez a munkaterület SKU kiválasztására, válassza az **alapszintű** lehetőséget egy alapszintű munkaterület létrehozásához. A különböző munkaterület-ajánlatokkal kapcsolatos további információkért lásd: [Azure Machine learning áttekintése](./overview-what-is-azure-ml.md#sku).

Ezen a ponton a rendszer egy új munkaterületet hoz létre a fiókjában. Néhány perc elteltével az új munkaterület megjelenik az előfizetési csomópontban. 

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Egy vagy több kísérlet hozható létre a munkaterületen, és nyomon követheti és elemezheti az egyes modellek betanítási futtatásait. A futtatások az Azure-felhőben vagy a helyi gépen is elvégezhető.

1. A Visual Studio Code tevékenység sávján válassza az **Azure** ikont. Megjelenik a Azure Machine Learning nézet.
1. Bontsa ki az előfizetési csomópontot.
1. Bontsa ki a **TeamWorkspace** csomópontot. 
1. Kattintson a jobb gombbal a **kísérletek** csomópontra.
1. Válassza a **kísérlet létrehozása** lehetőséget a helyi menüből.

    > [!div class="mx-imgBorder"]
    > ![Kísérlet létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. A parancssorba írja be a "MNIST" nevű kísérletet, majd nyomja le az **ENTER** billentyűt az új kísérlet létrehozásához. 

A munkaterületekhez hasonlóan a rendszer küld egy kérelmet az Azure-nak, hogy hozzon létre egy kísérletet a megadott konfigurációkkal. Néhány perc elteltével az új kísérlet a munkaterület *kísérletek* csomópontjában jelenik meg. 

## <a name="configure-compute-targets"></a>Számítási célok konfigurálása

A számítási cél az a számítási erőforrás vagy környezet, amelyben a parancsfájlok futtatása és a betanított modellek üzembe helyezése történik. További információ: [Azure Machine learning számítási célok dokumentációja](./concept-compute-target.md).

Számítási cél létrehozása:

1. A Visual Studio Code tevékenység sávján válassza az **Azure** ikont. Megjelenik a Azure Machine Learning nézet. 
1. Bontsa ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace** csomópontot. 
1. A munkaterület csomópontban kattintson a jobb gombbal a **számítási** csomópontra, majd válassza a **számítás létrehozása**lehetőséget. 

    > [!div class="mx-imgBorder"]
    > ![számítási cél létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Válassza ki **Azure Machine learning számítást (AmlCompute)** . Azure Machine Learning a számítás egy felügyelt számítási infrastruktúra, amely lehetővé teszi a felhasználó számára, hogy egyszerűen hozzon létre egy vagy több csomópontos számítást, amelyet a munkaterület más felhasználóival is használhatnak.
1. Válassza ki a virtuális gép méretét. A parancssorban válassza a **Standard_F2s_v2**lehetőséget. A virtuális gép mérete hatással van a modellek betanításához szükséges idő mennyiségére. A virtuális gépek méretével kapcsolatos további információkért lásd: [a Linux rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
1. A parancssorba írja be a "TeamWkspc-com" számítási nevet, majd nyomja le az **ENTER** billentyűt a számítás létrehozásához.

Néhány perc elteltével megjelenik az új számítási cél a munkaterület *számítási* csomópontján.

## <a name="create-a-run-configuration"></a>Futtatási konfiguráció létrehozása

Ha a betanítást egy számítási célra küldi el, a betanítási feladatok futtatásához szükséges konfigurációt is elküldheti. Például a betanítási kódot és a futtatásához szükséges Python-függőségeket tartalmazó parancsfájl.

Futtatási konfiguráció létrehozása:

1. A Visual Studio Code tevékenység sávján válassza az **Azure** ikont. Megjelenik a Azure Machine Learning nézet. 
1. Bontsa ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace** csomópontot. 
1. A munkaterület csomópontban kattintson a jobb gombbal a **TeamWkspc-com** számítási csomópontra, majd válassza a **futtatási konfiguráció létrehozása**parancsot.

    > [!div class="mx-imgBorder"]
    > ![futtatási konfiguráció létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. A parancssorban adja meg a Run konfiguráció "MNIST-RC" nevét, majd nyomja le az **ENTER** billentyűt a számítás létrehozásához.
1. Ezután válassza a **TensorFlow egycsomópontos képzés** lehetőséget a betanítási feladattípusként.
1. Nyomja le az **ENTER** billentyűt, és tallózással keresse meg a parancsfájlt a számításhoz. Ebben az esetben a modell betanítására szolgáló parancsfájl a `train.py` fájl a `vscode-tools-for-ai/mnist-vscode-docs-sample` könyvtárban.
1. A szükséges csomagok megadásához írja be a következőt a parancssorba.
    
    ```text
    pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
    ```
    
    Egy `MNIST-rc.runconfig` nevű fájl a VS Code-ban az alábbihoz hasonló tartalommal jelenik meg:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "python": {
                "userManagedDependencies": false,
                "condaDependencies": {
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                }
            },
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:0.2.4",
                "enabled": true,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                }
            }
        },
        "nodeCount": 1,
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Ha elégedett a konfigurációval, nyissa meg a parancssort a **> parancs-paletta megtekintése**lehetőség kiválasztásával.
1. A futtatási konfigurációs fájl mentéséhez írja be a következő parancsot a parancssorba.

    ```text
    Azure ML: Save and Continue
    ```

A `MNIST-rc` futtatási konfigurációt a *TeamWkspc-com* számítási csomópont alatt adja hozzá a rendszer.

## <a name="train-the-model"></a>A modell tanítása

A betanítási folyamat során a rendszer létrehoz egy TensorFlow-modellt a besorolt betanítási és a hozzájuk tartozó tanulási minták feldolgozásával. 

Azure Machine Learning kísérlet futtatása:

1. A Visual Studio Code tevékenység sávján válassza az **Azure** ikont. Megjelenik a Azure Machine Learning nézet. 
1. Bontsa ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace > kísérletek** csomópontot. 
1. Kattintson a jobb gombbal a **MNIST** kísérletre.
1. Válassza a **kísérlet futtatása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![kísérlet futtatása](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. A parancs palettáján válassza ki a **TeamWkspc-com** számítási célt.
1. Ezután válassza ki a **MNIST-RC** futtatási konfigurációt.
1. Ezen a ponton a rendszer elküld egy kérést az Azure-nak, hogy futtassa a kísérletet a munkaterület kiválasztott számítási célján. Ez a folyamat több percig is eltarthat. A betanítási feladatok futtatásának időtartamát számos tényező befolyásolja, például a számítási típus és a betanítási adatok mérete. A kísérlet előrehaladásának nyomon követéséhez kattintson a jobb gombbal a jelenlegi Futtatás csomópontra, és válassza a **futtatás Azure Portalban**lehetőséget.
1. Amikor megjelenik a külső webhely megnyitását kérő párbeszédpanel, válassza a **Megnyitás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![a kísérlet előrehaladásának nyomon követése](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Ha a modell elkészült, a Futtatás csomópont frissítései mellett a "befejezett" állapot felirat látható.

## <a name="register-the-model"></a>A modell regisztrálása

Most, hogy betanítta a modellt, regisztrálhatja azt a munkaterületen. 

A modell regisztrálása:

1. A Visual Studio Code tevékenység sávján válassza az **Azure** ikont. Megjelenik a Azure Machine Learning nézet.
1. Bontsa ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace > kísérletek > MNIST** csomópontot.
1. Szerezze be a modell betanításával generált modell kimeneteit. Kattintson a jobb gombbal a **Futtatás 1** Futtatás csomópontra, majd válassza a **kimenetek letöltése**lehetőséget. 

    > [!div class="mx-imgBorder"]
    > ![letöltési modell kimenetei](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Válassza ki azt a könyvtárat, ahová menteni szeretné a letöltött kimeneteket. Alapértelmezés szerint a kimenetek a Visual Studio Code-ban jelenleg megnyitott könyvtárba kerülnek.
1. Kattintson a jobb gombbal a **modellek** csomópontra, és válassza a **modell regisztrálása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![modell regisztrálása](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. A parancssorban nevezze el a "MNIST-TensorFlow-Model" modellt, majd nyomja le az **ENTER**billentyűt.
1. A TensorFlow modellek több fájlból állnak. Válassza ki a **modell mappáját** a modell elérési útja formátumban a parancs palettáján. 
1. Válassza ki a `azureml_outputs/Run_1/outputs/Run_1/outputs/outputs/model` könyvtárat.

    A modell konfigurációit tartalmazó fájl a Visual Studio Code-ban jelenik meg, az alábbihoz hasonló tartalommal:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Ha elégedett a konfigurációval, mentse a parancsot a parancssor megnyitásával, és írja be a következő parancsot:

    ```text
    Azure ML: Save and Continue
    ```

Néhány perc elteltével a modell a *modellek* csomópont alatt jelenik meg.

## <a name="deploy-the-model"></a>A modell rendszerbe állítása

A Visual Studio Code-ban a modellt webszolgáltatásként helyezheti üzembe a következőhöz:

+ Azure Container Instances (ACI).
+ Azure Kubernetes szolgáltatás (ak).

A teszteléshez nem kell ACI-tárolót létrehoznia, mert az ACI-tárolók szükség szerint jönnek létre. Azonban az AK-fürtöket előre kell konfigurálnia. További információ az üzembe helyezési lehetőségekről: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md) .

Webszolgáltatás üzembe helyezése ACI-ként:

1. A Visual Studio Code tevékenység sávján válassza az **Azure** ikont. Megjelenik a Azure Machine Learning nézet.
1. Bontsa ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace > modellek** csomópontot. 
1. Kattintson a jobb gombbal a **MNIST-TensorFlow-Model** elemre, majd válassza a **szolgáltatás telepítése a regisztrált modellből**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A modell üzembe helyezése](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. A parancs palettáján válassza a **Azure Container instances**lehetőséget.
1. Nevezze el a szolgáltatást "mnist-tensorflow-SVC" néven, majd nyomja le az **ENTER billentyűt** a parancs palettáján.
1. Válassza ki a tárolóban futtatni kívánt parancsfájlt. Ehhez nyomja le az **ENTER billentyűt** a parancssorban, és tallózással keresse meg a `score.py` fájlt a `mnist-vscode-docs-sample` könyvtárban.
1. Adja meg a parancsfájl futtatásához szükséges függőségeket, ha lenyomja az **ENTER billentyűt** a parancssorban, és megkeresi a `env.yml` fájlt a `mnist-vscode-docs-sample` könyvtárban.

    A modell konfigurációit tartalmazó fájl a Visual Studio Code-ban jelenik meg, az alábbihoz hasonló tartalommal:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```
1. Ha elégedett a konfigurációval, mentse a parancsot a parancssor megnyitásával, és írja be a következő parancsot:

    ```text
    Azure ML: Save and Continue
    ```

Ezen a ponton egy kérést küldünk az Azure-nak a webszolgáltatás üzembe helyezéséhez. Ez a folyamat több percig is eltarthat. A telepítés után az új szolgáltatás megjelenik a *végpontok* csomópont alatt.

## <a name="next-steps"></a>Következő lépések

* A Visual Studio code-on kívüli Azure Machine Learning betanításával kapcsolatos útmutatóért lásd [: oktatóanyag: modellek Betanítása Azure Machine learning](tutorial-train-models-with-aml.md)használatával.
* A kódok helyi szerkesztéséről, futtatásáról és hibakereséséről a [Python Hello-World oktatóanyagban](https://code.visualstudio.com/docs/Python/Python-tutorial)talál további információt.

