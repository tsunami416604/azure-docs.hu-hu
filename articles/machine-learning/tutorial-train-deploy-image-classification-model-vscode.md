---
title: 'Oktatóanyag: Modell betanítása és üzembe helyezése a Visual Studio-kódbővítmény használatával'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthat be és helyezhet üzembe lemezképbesorolási modellt a TensorFlow és az Azure Machine Learning Visual Studio-kódbővítmény használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: f793f8c4cb84f821c098cc5ce98e693d272e725f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272793"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension"></a>A TensorFlow-modell betanítása és üzembe helyezése az Azure Machine Learning Visual Studio kódbővítmény használatával

Ismerje meg, hogyan taníthat be és helyezhet üzembe egy lemezkép-besorolási modellt a Hand-written flow és az Azure Machine Learning Visual Studio code extension használatával a kézzel írott számok felismeréséhez.

Eben az oktatóanyagban az alábbi feladatokkal fog megismerkedni:

> [!div class="checklist"]
> * A kód értelmezése
> * Munkaterület létrehozása
> * Kísérlet létrehozása
> * Számítógép-célok konfigurálása
> * Konfigurációs fájl futtatása
> * Modell betanítása
> * Modell regisztrálása
> * Modell üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés. Ha még nem rendelkezik ilyen, regisztráljon az [Azure Machine Learning ingyenes vagy fizetős verziójának kipróbálására.](https://aka.ms/AMLFree)
- Telepítse [a Visual Studio Code-ot,](https://code.visualstudio.com/docs/setup/setup-overview)egy könnyű, platformfüggetlen kódszerkesztőt.
- Az Azure Machine Learning Studio Visual Studio Kód bővítmény. A telepítési útmutatót a [Setup Azure Machine Learning Visual Studio Code bővítmény oktatóanyaga ismerteti.](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>A kód értelmezése

Az oktatóanyag kódja a TensorFlow-t használja egy képbesorolási gépi tanulási modell betanításához, amely 0-9-től kategorizálja a kézzel írt számjegyeket. Ezt úgy éri el, hogy létrehoz egy neurális hálózatot, amely a 28 px x 28 px kép képpontértékeit bemenetként veszi fel, és 10 valószínűségből álló listát ad ki, egyet-egyet a besorolt számjegyek mindegyikéhez. Az alábbiakban egy példa, hogy az adatok hogyan néz ki.  

![MNIST-számjegyek](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Az oktatóanyag kódját a vs code [tools for AI repository](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) letöltésével és kioldásával, bárhol a számítógépen.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az első dolog, amit meg kell tennie, hogy hozzon létre egy alkalmazást az Azure Machine Learning egy munkaterület létrehozása. A munkaterület tartalmazza a modellek betanításához szükséges erőforrásokat, valamint magukat a betanított modelleket. További információt a munkaterület című [témakörben talál.](./concept-workspace.md) 

1. A Visual Studio-kód tevékenységsávján válassza az **Azure** ikont az Azure Machine Learning nézet megnyitásához.
1. Kattintson a jobb gombbal az Azure-előfizetésére, és válassza **a Munkaterület létrehozása parancsot.** 
    
    > [!div class="mx-imgBorder"]
    > ![Munkaterület létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Alapértelmezés szerint létrejön egy név, amely tartalmazza a létrehozás dátumát és időpontját. A szövegbeviteli mezőben módosítsa a nevet "TeamWorkspace" névre, és nyomja le az **Enter billentyűt.**
1. Válassza **az Új erőforráscsoport létrehozása**lehetőséget. 
1. Nevezze el az erőforráscsoportot "TeamWorkspace-rg" néven, és nyomja **le az Enter billentyűt.** 
1. Adja meg a munkaterület helyét. Javasoljuk, hogy olyan helyet válasszon, amely a legközelebb áll a modell üzembe helyezéséhez. Például "US2 nyugat-amerikai 2".
1. Amikor a rendszer kéri a munkaterület típusának kiválasztását, válassza az **Alapszintű** lehetőséget az alapmunkaterület létrehozásához. A különböző munkaterületi ajánlatokról az [Azure Machine Learning áttekintése című témakörben olvashat bővebben.](./overview-what-is-azure-ml.md#sku)

Ezen a ponton az Azure-hoz egy kérést, hogy hozzon létre egy új munkaterületet a fiókjában. Néhány perc múlva az új munkaterület megjelenik az előfizetési csomóponton. 

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Egy vagy több kísérlet hozható létre a munkaterületen az egyes modellbetanítási futtatások nyomon követésére és elemzésére. A futtatások az Azure-felhőben vagy a helyi számítógépen végezhetők el.

1. A Visual Studio-kód tevékenységsávján válassza az **Azure** ikont. Megjelenik az Azure Machine Learning nézet.
1. Bővítse ki az előfizetési csomópontot.
1. Bontsa ki a **TeamWorkspace** csomópontot. 
1. Kattintson a jobb gombbal a **Kísérletek** csomópontra.
1. Válassza a helyi menü **Kísérlet létrehozása parancsát.**

    > [!div class="mx-imgBorder"]
    > ![Kísérlet létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Nevezze el a kísérletet "MNIST"-nek, és nyomja le az **Enter billentyűt** az új kísérlet létrehozásához. 

A munkaterületekhez hasonlóan a rendszer kérést küld az Azure-nak, hogy hozzon létre egy kísérletet a megadott konfigurációkkal. Néhány perc múlva az új kísérlet megjelenik a munkaterület *Kísérletek* csomópontjában. 

## <a name="configure-compute-targets"></a>Számítási célok konfigurálása

A számítási cél az a számítási erőforrás vagy környezet, ahol parancsfájlokat futtat, és telepített modelleket telepít. További információt az [Azure Machine Learning számítási célok dokumentációjában](./concept-compute-target.md)talál.

Számítási cél létrehozása:

1. A Visual Studio-kód tevékenységsávján válassza az **Azure** ikont. Megjelenik az Azure Machine Learning nézet. 
1. Bővítse ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace** csomópontot. 
1. A munkaterület-csomópont alatt kattintson a jobb gombbal a **Számítási** csomópontra, és válassza **a Számítási létrehozása parancsot.** 

    > [!div class="mx-imgBorder"]
    > ![Számítási cél létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Válassza az **Azure Machine Learning Compute (AmlCompute) lehetőséget.** Az Azure Machine Learning Compute egy felügyelt számítási infrastruktúra, amely lehetővé teszi a felhasználó számára, hogy egyszerűen hozzon létre egy vagy többcsomópontos számítási, amely használható más felhasználókkal a munkaterületen.
1. Válassza ki a virtuális gép méretét. Válassza ki **Standard_F2s_v2** a beállítások listájából. A virtuális gép mérete hatással van a modellek betanításához szükséges időre. A virtuális gépek méretéről további információt az [Azure-beli Linux-virtuális gépek méretei című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)
1. Nevezze el a számítási "TeamWkspc-com" nevet, és nyomja le az **Enter** billentyűt a számítás létrehozásához.

    A VS Code fájltartalma az alábbihoz hasonló tartalommal jelenik meg:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": 120
                },
                "userAccountCredentials": {
                    "adminUserName": "",
                    "adminUserPassword": "",
                    "adminUserSshPublicKey": ""
                },
                "subnetName": "",
                "vnetName": "",
                "vnetResourceGroupName": "",
                "remoteLoginPortPublicAccess": ""
            }
        }
    }
    ```

1. Ha elégedett a konfigurációval, nyissa meg a parancspalettát a **Nézet > parancspaletta**lehetőség kiválasztásával.
1. A futtatási konfigurációs fájl mentéséhez írja be a következő parancsot a parancspalettába.

    ```text
    Azure ML: Save and Continue
    ```

Néhány perc múlva az új számítási cél megjelenik a munkaterület *számítási* csomópontján.

## <a name="create-a-run-configuration"></a>Futtatási konfiguráció létrehozása

Amikor beküld egy betanítási futtatást egy számítási célnak, akkor a betanítási feladat futtatásához szükséges konfigurációt is elküldi. Például a parancsfájlt, amely tartalmazza a betanítási kódot, és a Python-függőségek futtatásához szükséges.

Futtatási konfiguráció létrehozása:

1. A Visual Studio-kód tevékenységsávján válassza az **Azure** ikont. Megjelenik az Azure Machine Learning nézet. 
1. Bővítse ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace > számítási** csomópontot. 
1. A számítási csomópont alatt kattintson a jobb gombbal a **TeamWkspc-com** számítási csomópontjára, és válassza **a Futtatási konfiguráció létrehozása parancsot.**

    > [!div class="mx-imgBorder"]
    > ![Futtatási konfiguráció létrehozása](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Nevezze el a futtatási konfigurációt "MNIST-rc" néven, és nyomja le az **Enter billentyűt** a futtatási konfiguráció létrehozásához.
1. Ezután válassza **az Új Azure ML-környezet létrehozása**lehetőséget. A környezetek határozzák meg a parancsfájlok futtatásához szükséges függőségeket.
1. Nevezze el a környezetet "MNIST-env" néven, és nyomja **le az Enter billentyűt.**
1. Válassza **a Conda függőségek fájlját** a listából.
1. Az **Enter** billentyű lenyomásával tallózhat a Conda-függőségfájlban. Ebben az esetben a függőségi `env.yml` fájl `vscode-tools-for-ai/mnist-vscode-docs-sample` a könyvtárban lévő fájl.

    A VS Code fájltartalma az alábbihoz hasonló tartalommal jelenik meg:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. Miután elégedett a konfigurációval, mentse el a parancspaletta megnyitásával és a következő parancs beírásával:

    ```text
    Azure ML: Save and Continue
    ```

1. Az **Enter** billentyű lenyomásához tallózzon a parancsfájlban a számítási számítógépen való futtatáshoz. Ebben az esetben a modell betanításához szükséges `vscode-tools-for-ai/mnist-vscode-docs-sample` parancsfájl a `train.py` könyvtárban lévő fájl.

    A VS `MNIST-rc.runconfig` Code-ban megjelenik egy hívott fájl, amelynek tartalma hasonló az alábbihoz:

    ```json
    {
        "script": "train.py",
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Miután elégedett a konfigurációval, mentse el a parancspaletta megnyitásával és a következő parancs beírásával:

    ```text
    Azure ML: Save and Continue
    ```

A `MNIST-rc` futtatási konfiguráció a *TeamWkspc-com* számítási csomópont `MNIST-env` alatt, a környezeti konfiguráció pedig a *Környezetek* csomópont ban kerül hozzáadásra.

## <a name="train-the-model"></a>A modell betanítása

A betanítási folyamat során a TensorFlow modell jön létre a betanítási adatok és a benne beágyazott tanulási minták feldolgozásával minden egyes megfelelő számjegyek besorolása. 

Azure Machine Learning-kísérlet futtatása:

1. A Visual Studio-kód tevékenységsávján válassza az **Azure** ikont. Megjelenik az Azure Machine Learning nézet. 
1. Bővítse ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace > kísérletek** csomópontot. 
1. Kattintson a jobb gombbal az **MNIST-kísérletre.**
1. Válassza **a Kísérlet futtatása**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Kísérlet futtatása](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. A számítási célbeállítások listájában válassza ki a **TeamWkspc-com** számítási célt.
1. Ezután válassza ki az **MNIST-rc** futtatási konfigurációt.
1. Ezen a ponton egy kérést küld az Azure-nak, hogy futtassa a kísérletet a munkaterület kiválasztott számítási cél. Ez a folyamat több percig is eltarthat. A betanítási feladat futtatásához szükséges időt számos tényező befolyásolja, például a számítási típus és a betanítási adatok mérete. A kísérlet előrehaladásának nyomon követéséhez kattintson a jobb gombbal az aktuális futtatási csomópontra, és válassza a Futtatás megtekintése az **Azure Portalon parancsot.**
1. Amikor megjelenik egy külső webhely megnyitását kérő párbeszédpanel, válassza a **Megnyitás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A kísérlet előrehaladásának nyomon követése](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Amikor a modell betanítása befejeződött, a futtatási csomópont melletti állapotfelirat "Befejezett" lesz.

## <a name="register-the-model"></a>Regisztrálja a modellt

Most, hogy betanította a modellt, regisztrálhatja azt a munkaterületen. 

A modell regisztrálása:

1. A Visual Studio-kód tevékenységsávján válassza az **Azure** ikont. Megjelenik az Azure Machine Learning nézet.
1. Bővítse ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace > kísérletek > MNIST** csomópontot.
1. A modell betanítása a modell betanítása generált modell kimenetek. Kattintson a jobb gombbal az **1-es futtatási** csomópontra, és válassza **a Kimenetek letöltése parancsot.** 

    > [!div class="mx-imgBorder"]
    > ![Modellkimenetek letöltése](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Válassza ki azt a könyvtárat, amelybe a letöltött kimeneteket menteni szeretné. Alapértelmezés szerint a kimenetek a Visual Studio Code programban jelenleg megnyitott könyvtárba kerülnek.
1. Kattintson a jobb gombbal a **Modellek** csomópontra, és válassza **a Modell regisztrálása parancsot.**

    > [!div class="mx-imgBorder"]
    > ![Modell regisztrálása](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Nevezze el a modellt "MNIST-TensorFlow-model" néven, és nyomja **le az Enter billentyűt.**
1. A TensorFlow modell több fájlból áll. A beállítások listájából válassza a **Modell mappát** modellelérési útformátumként. 
1. Jelölje `azureml_outputs/Run_1/outputs/outputs/model` ki a könyvtárat.

    A modellkonfigurációkat tartalmazó fájl a Visual Studio-kódban az alábbihoz hasonló tartalommal jelenik meg:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Miután elégedett a konfigurációval, mentse el a parancspaletta megnyitásával és a következő parancs beírásával:

    ```text
    Azure ML: Save and Continue
    ```

Néhány perc múlva a modell megjelenik a *Modellek* csomópont alatt.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A Visual Studio-kódban a modellt webszolgáltatásként telepítheti a következőkre:

+ Azure Container instances (ACI).
+ Azure Kubernetes szolgáltatás (AKS).

Nem kell létrehozni a ACI-tároló t előzetes tesztelése, mert ACI-tárolók jönnek létre, ha szükséges. Az AKS-fürtöket azonban előre be kell állítania. A telepítési lehetőségekről további információt az Azure Machine Learning használatával rendelkező modellek üzembe helyezése című témakörben [talál.](how-to-deploy-and-where.md)

Webszolgáltatás telepítése ACI-ként:

1. A Visual Studio-kód tevékenységsávján válassza az **Azure** ikont. Megjelenik az Azure Machine Learning nézet.
1. Bővítse ki az előfizetési csomópontot. 
1. Bontsa ki a **TeamWorkspace > modellek** csomópontját. 
1. Kattintson a jobb gombbal az **MNIST-TensorFlow-modellre,** és válassza **a Szolgáltatás telepítése a regisztrált modellből parancsot.**

    > [!div class="mx-imgBorder"]
    > ![A modell üzembe helyezése](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Válassza az **Azure Container Instances lehetőséget.**
1. Nevezze el a szolgáltatást "mnist-tensorflow-svc" néven, és nyomja **le az Enter billentyűt.**
1. Válassza ki a tárolóban futtatni kívánt parancsfájlt az **Enter** `score.py` elemre a `mnist-vscode-docs-sample` beviteli mezőbe, és keresse meg a fájlt a könyvtárban.
1. Adja meg a parancsfájl futtatásához szükséges függőségeket az **Enter** elemre `env.yml` a `mnist-vscode-docs-sample` beviteli mezőbe, és keresse meg a fájlt a könyvtárban.

    A modellkonfigurációkat tartalmazó fájl a Visual Studio-kódban az alábbihoz hasonló tartalommal jelenik meg:

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

1. Miután elégedett a konfigurációval, mentse el a parancspaletta megnyitásával és a következő parancs beírásával:

    ```text
    Azure ML: Save and Continue
    ```

Ezen a ponton egy kérést küld az Azure-nak a webszolgáltatás üzembe helyezéséhez. Ez a folyamat több percig is eltarthat. Üzembe helyezés után az új szolgáltatás megjelenik a *végpontok* csomópont alatt.

## <a name="next-steps"></a>További lépések

* Az Azure Machine Learning visual studio-kódon kívüli betanításának forgatókönyvét az [Oktatóanyag: Modellek betanítása az Azure Machine Learning szolgáltatással](tutorial-train-models-with-aml.md)ismerteti.
* A helyi szerkesztési, futtatási és hibakeresési kód ismertetése a [Python hello-world oktatóanyagában látható.](https://code.visualstudio.com/docs/Python/Python-tutorial)

