---
title: 'Oktatóanyag: A Visual Studio-kódbővítmény beállítása'
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
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272914"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Az Azure Machine Learning Visual Studio-kódbővítmény beállítása

Ismerje meg, hogyan telepítheti és futtathatja a parancsfájlokat az Azure Machine Learning Visual Studio Code bővítmény használatával.

Eben az oktatóanyagban az alábbi feladatokkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure Machine Learning Visual Studio-kódbővítmény telepítése
> * Jelentkezzen be Azure-fiókjába a Visual Studio-kódból
> * Mintaparancsfájl futtatásához használja az Azure Machine Learning-bővítményt

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés. Ha még nem rendelkezik ilyen, regisztráljon az [Azure Machine Learning ingyenes vagy fizetős verziójának kipróbálására.](https://aka.ms/AMLFree)
- Visual Studio Code. Ha nincs meg, [telepítse.](https://code.visualstudio.com/docs/setup/setup-overview)
- [Piton 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>A bővítmény telepítése

1. Nyissa meg a Visual Studio Code-ot.
1. A Bővítmények nézet megnyitásához válassza a **Bővítmények** ikont a **Tevékenységsávon.**
1. A bővítmények nézetben keressen rá az "Azure Machine Learning" kifejezésre.
1. Válassza az **Install** (Telepítés) lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Az Azure Machine Learning VS-kódbővítmény telepítése](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Azt is megteheti, hogy telepíti az Azure Machine Learning bővítményt a Visual Studio Piactéren [keresztül, ha közvetlenül letölti a telepítőt.](https://aka.ms/vscodetoolsforai) 

Az oktatóanyag további lépéseit a bővítmény **0.6.8-as verziójával** tesztelték.

## <a name="sign-in-to-your-azure-account"></a>Bejelentkezés Azure-fiókjába

Az erőforrások kiépítéséhez és a számítási feladatok azure-beli futtatásához az Azure-fiók hitelesítő adataival kell bejelentkeznie. A fiókkezelés segítése érdekében az Azure Machine Learning automatikusan telepíti az Azure-fiókbővítményt. Az [Azure-fiók bővítményről az](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)alábbi webhelyen olvashat bővebben.

1. Nyissa meg a parancspalettát a menüsor **Nézet > parancspalettája parancsával.** 
1. Írja be az "Azure: Bejelentkezés" parancsot a parancspalettába a bejelentkezési folyamat elindításához.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Gépi tanulási modell betanítási parancsfájljának futtatása az Azure-ban

Most, hogy bejelentkezett az Azure-ba a fiók hitelesítő adataival, az ebben a szakaszban ismertetett lépésekkel megtudhatja, hogyan használhatja a bővítményt egy gépi tanulási modell betanításához.

1. Töltse le és csomagolja ki a [VS Code Tools for AI repository](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) bárhol a számítógépen.
1. Nyissa `mnist-vscode-docs-sample` meg a könyvtárat a Visual Studio-kódban.
1. Válassza ki az **Azure** ikont a tevékenységsávon.
1. Válassza a **Kísérlet futtatása** ikont az Azure Machine Learning nézet tetején.

    > [!div class="mx-imgBorder"]
    > ![Kísérlet futtatása](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Amikor a parancspaletta kitágul, kövesse az utasításokat.

    1. Válassza ki az Azure-előfizetését.
    1. A környezetek listájában válassza a **Conda-függőségek fájl lehetőséget.**
    1. Az **Enter** billentyű lenyomásával tallózhat a Conda-függőségfájlban. Ez a fájl a parancsfájl futtatásához szükséges függőségeket tartalmazza. Ebben az esetben a függőségi `env.yml` fájl `mnist-vscode-docs-sample` a könyvtárban lévő fájl.
    1. Az **Enter** billentyű lenyomása a betanítási parancsfájl ban való böngészéshez. Ez az a fájl, amely egy gépi tanulási modell kódját tartalmazza, amely a kézzel írt számjegyek képeit kategorizálja. Ebben az esetben a modell betanításához szükséges `mnist-vscode-docs-sample` parancsfájl a `train.py` könyvtárban lévő fájl.

1. Ezen a ponton az alábbihoz hasonló konfigurációs fájl jelenik meg a szövegszerkesztőben. A konfiguráció tartalmazza a betanítási feladat futtatásához szükséges információkat, például a modell betanításához szükséges kódot és az előző lépésben megadott Python-függőségeket tartalmazó fájlt.

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

1. Miután elégedett a konfigurációval, küldje el a kísérletet a parancspaletta megnyitásával és a következő parancs beírásával:

    ```text
    Azure ML: Submit Experiment
    ```

    Ez elküldi `train.py` a konfigurációs fájlt az Azure Machine Learning-munkaterületre. A betanítási feladat ezután elindul egy számítási erőforrás az Azure-ban.

### <a name="track-the-progress-of-the-training-script"></a>A betanítási parancsfájl előrehaladásának nyomon követése

A parancsfájl futtatása több percet is igénybe vehet. A folyamat nyomon követése:

1. Válassza ki az **Azure** ikont a tevékenységsávon.
1. Bővítse ki az előfizetési csomópontot.
1. Bontsa ki az éppen futó kísérlet csomópontját. Ez a `{workspace}/Experiments/{experiment}` csomóponton belül található, ahol a munkaterület és a kísérlet értékei megegyeznek a konfigurációs fájlban megadott tulajdonságokkal.
1. A kísérlet összes futtatása fel van sorolva, valamint azok állapota. A legutóbbi állapot lefelvételéhez kattintson a frissítésikonra az Azure Machine Learning nézet tetején.

    > [!div class="mx-imgBorder"]
    > ![A kísérlet előrehaladásának nyomon követése](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>A betanított modell letöltése

Amikor a kísérlet futtatása befejeződött, a kimenet egy betanított modell. A kimenetek helyi letöltése:

1. Kattintson a jobb gombbal a legutóbbi futtatásra, és válassza **a Kimenetek letöltése parancsot.**

    > [!div class="mx-imgBorder"]
    > ![Betanított modell letöltése](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Válassza ki azt a helyet, ahová a kimeneteket menteni szeretné.
1. A program helyileg letölt egy mappát a futtatás nevével. Keresse meg.
1. A modellfájlok a `outputs/outputs/model` könyvtárban vannak.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Betanítson és telepítsen egy HypersorFlow-modellrendszer-besorolást az Azure Machine Learning Visual Studio kódbővítmény használatával.](tutorial-train-deploy-image-classification-model-vscode.md)
