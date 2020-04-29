---
title: 'Oktatóanyag: a Visual Studio Code bővítmény beállítása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állíthatja be a Visual Studio Code Azure Machine Learning bővítményt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: 731ab18346ac9f100862174312c2c9950026f1eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272914"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>A Visual Studio Code-bővítmény Azure Machine Learning beállítása

Megtudhatja, hogyan telepíthet és futtathat parancsfájlokat a Azure Machine Learning Visual Studio Code bővítmény használatával.

Eben az oktatóanyagban az alábbi feladatokkal fog megismerkedni:

> [!div class="checklist"]
> * A Azure Machine Learning Visual Studio Code bővítmény telepítése
> * Jelentkezzen be az Azure-fiókjába a Visual Studio Code-ból
> * Minta parancsfájl futtatása a Azure Machine Learning bővítmény használatával

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés. Ha még nem rendelkezik ilyennel, regisztráljon a [Azure Machine learning ingyenes vagy fizetős verziójának](https://aka.ms/AMLFree)kipróbálásához.
- Visual Studio Code. Ha nincs telepítve, [telepítse](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>A bővítmény telepítése

1. Nyissa meg a Visual Studio Code-ot.
1. Válassza a **bővítmények** ikont a **tevékenység sávján** a bővítmények nézet megnyitásához.
1. A bővítmények nézetben keressen rá a "Azure Machine Learning" kifejezésre.
1. Válassza az **Install** (Telepítés) lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A Azure Machine Learning VS Code bővítmény telepítése](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Azt is megteheti, hogy a Visual Studio Marketplace-en keresztül telepíti a Azure Machine Learning bővítményt, ha [közvetlenül letölti a telepítőt](https://aka.ms/vscodetoolsforai). 

Az oktatóanyag további lépései a bővítmény **verziójának 0.6.8** lettek tesztelve.

## <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Az erőforrások kiépítéséhez és az Azure-beli számítási feladatok futtatásához be kell jelentkeznie az Azure-fiók hitelesítő adataival. A fiókkezelés támogatásához Azure Machine Learning automatikusan telepíti az Azure-fiók bővítményét. Az alábbi webhelyen [További információt talál az Azure-fiók bővítményéről](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Nyissa meg a parancssort úgy, hogy kijelöli a menüsávban látható **> a parancs-palettát** . 
1. A bejelentkezési folyamat elindításához írja be az "Azure: bejelentkezés" parancsot a parancssorba.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Gépi tanulási modell betanítási szkript futtatása az Azure-ban

Most, hogy bejelentkezett az Azure-ba a fiókja hitelesítő adataival, kövesse az ebben a szakaszban ismertetett lépéseket, amelyből megtudhatja, hogyan használható a bővítmény a Machine learning-modellek betanításához.

1. Töltse le és csomagolja [ki a vs Code-eszközöket a AI-tárházba](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) bárhol a számítógépen.
1. Nyissa `mnist-vscode-docs-sample` meg a könyvtárat a Visual Studio Code-ban.
1. Válassza ki az **Azure** ikont a tevékenység sávján.
1. Válassza a **kísérlet futtatása** ikont a Azure Machine learning nézet tetején.

    > [!div class="mx-imgBorder"]
    > ![Kísérlet futtatása](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. A parancs-paletta kibontásakor kövesse az utasításokat.

    1. Válassza ki az Azure-előfizetését.
    1. A környezetek listájából válassza az **Conda-függőségek fájl**elemet.
    1. Nyomja le az **ENTER** billentyűt a Conda-függőségek fájl tallózásához. Ez a fájl tartalmazza a parancsfájl futtatásához szükséges függőségeket. Ebben az esetben a függőségek fájl a `env.yml` `mnist-vscode-docs-sample` könyvtárban található fájl.
    1. Nyomja le az **ENTER** billentyűt a betanítási parancsfájl tallózásához. Ez a fájl tartalmazza a gépi tanulási modell kódját, amely a kézzel írt számjegyek képeit kategorizálja. Ebben az esetben a modell betanítására szolgáló parancsfájl a `train.py` `mnist-vscode-docs-sample` könyvtáron belül található fájl.

1. Ezen a ponton az alábbihoz hasonló konfigurációs fájl jelenik meg a szövegszerkesztőben. A konfiguráció tartalmazza a betanítási feladatok futtatásához szükséges adatokat, például a kódot tartalmazó fájlt, amely a modell betanítását és az előző lépésben megadott Python-függőségeket tartalmazza.

    ```json
    {
        "workspace": "WS04131142",
        "resourceGroup": "WS04131142-rg1",
        "location": "South Central US",
        "experiment": "WS04131142-exp1",
        "compute": {
            "name": "WS04131142-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS04131142-com1-rc1",
            "environment": {
                "name": "WS04131142-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. Ha elégedett a konfigurációval, küldje el a kísérletet a Command paletta megnyitásával, és írja be a következő parancsot:

    ```text
    Azure ML: Submit Experiment
    ```

    Ez elküldi a `train.py` és a konfigurációs fájlt a Azure Machine learning munkaterületre. Ezután a betanítási feladatot egy Azure-beli számítási erőforráson indítja el.

### <a name="track-the-progress-of-the-training-script"></a>A betanítási szkript előrehaladásának nyomon követése

A szkript futtatása több percet is igénybe vehet. A folyamat nyomon követése:

1. Válassza ki az **Azure** ikont a tevékenység sávjából.
1. Bontsa ki az előfizetési csomópontot.
1. Bontsa ki a jelenleg futó kísérlet csomópontját. Ez a `{workspace}/Experiments/{experiment}` csomóponton belül található, ahol a munkaterület és a kísérlet értékei megegyeznek a konfigurációs fájlban megadott tulajdonságokkal.
1. A kísérlet összes futtatása megjelenik, valamint az állapotuk. A legutóbbi állapot beszerzéséhez kattintson a Azure Machine Learning nézet tetején található frissítés ikonra.

    > [!div class="mx-imgBorder"]
    > ![A kísérlet előrehaladásának nyomon követése](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>A betanított modell letöltése

A kísérlet futtatása után a kimenet egy betanított modell. A kimenetek helyi letöltése:

1. Kattintson a jobb gombbal a legutóbbi futtatásra, és válassza a **Letöltés kimenetek**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Betanított modell letöltése](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Válassza ki azt a helyet, ahová a kimeneteket menteni szeretné.
1. A rendszer helyileg tölti le a Futtatás nevét tartalmazó mappát. Keresse meg.
1. A modell fájljai a `outputs/outputs/model` könyvtárban vannak.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: lemezkép besorolása TensorFlow-modell betanítása és üzembe helyezése a Visual Studio Code Azure Machine learning használatával](tutorial-train-deploy-image-classification-model-vscode.md).
