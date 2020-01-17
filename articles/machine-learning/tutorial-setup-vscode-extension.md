---
title: 'Oktatóanyag: Azure Machine Learning Visual Studio Code-bővítmény beállítása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állíthatja be a Visual Studio Code Azure Machine Learning bővítményt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157611"
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
- Telepítse a [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)-ot, amely egy egyszerű, platformfüggetlen Kódszerkesztő. 

## <a name="install-the-extension"></a>A bővítmény telepítése

1. Nyissa meg a Visual Studio Code-ot.
1. Válassza a **bővítmények** ikont a **tevékenység sávján** a bővítmények nézet megnyitásához.
1. A bővítmények nézetben keressen rá a "Azure Machine Learning" kifejezésre.
1. Válassza az **Install** (Telepítés) lehetőséget.

> [!NOTE]
> Azt is megteheti, hogy a Visual Studio Marketplace-en keresztül telepíti a Azure Machine Learning bővítményt, ha [közvetlenül letölti a telepítőt](https://aka.ms/vscodetoolsforai). 

Az oktatóanyag további lépései a bővítmény **verziójának 0.6.8** lettek tesztelve.

## <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Az erőforrások kiépítéséhez és az Azure-beli számítási feladatok futtatásához be kell jelentkeznie az Azure-fiók hitelesítő adataival. A fiókkezelés támogatásához Azure Machine Learning automatikusan telepíti az Azure-fiók bővítményét. Az alábbi webhelyen [További információt talál az Azure-fiók bővítményéről](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Nyissa meg a parancssort úgy, hogy kijelöli a menüsávban látható **> a parancs-palettát** . 
1. A bejelentkezési folyamat elindításához írja be az "Azure: Sign in" parancsot a szövegmezőbe.

## <a name="run-a-script-in-azure"></a>Parancsfájl futtatása az Azure-ban

Most, hogy bejelentkezett az Azure-ba a fiókja hitelesítő adataival, kövesse az ebben a szakaszban ismertetett lépéseket, amelyből megtudhatja, hogyan használható a bővítmény a Machine learning-modellek betanításához.

1. Töltse le és csomagolja [ki a vs Code-eszközöket a AI-tárházba](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) bárhol a számítógépen.
1. Nyissa meg a `mnist-vscode-docs-sample` könyvtárat a Visual Studio Code-ban.
1. Válassza ki az **Azure** ikont a tevékenység sávján.
1. Válassza a **kísérlet futtatása** ikont a Azure Machine learning nézet tetején.

    > [!div class="mx-imgBorder"]
    > ![kísérlet futtatása](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. A parancs-paletta kibontásakor kövesse az utasításokat.

    1. Válassza ki az Azure-előfizetését.
    1. Válassza **az új Azure ml-munkaterület létrehozása** lehetőséget.
    1. Válassza ki a **TensorFlow egycsomópontos betanítási** feladattípust.
    1. Adja meg `train.py`ként a betanításhoz használandó parancsfájlt. Ez a fájl tartalmazza a gépi tanulási modell kódját, amely a kézzel írt számjegyek képeit kategorizálja.
    1. A futtatáshoz adja meg a következő csomagokat.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. Ezen a ponton az alábbihoz hasonló konfigurációs fájl jelenik meg a szövegszerkesztőben. A konfiguráció tartalmazza a betanítási feladatok futtatásához szükséges adatokat, például a kódot tartalmazó fájlt, amely a modell betanítását és az előző lépésben megadott Python-függőségeket tartalmazza.

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Válassza a **kísérlet küldése** lehetőséget a kísérlet Azure-beli futtatásához. Ez elküldi a `train.py` és a konfigurációs fájlt a Azure Machine Learning munkaterületre. Ezután a betanítási feladatot egy Azure-beli számítási erőforráson indítja el.
1. Néhány perc elteltével a rendszer egy `output` nevű könyvtárat hoz létre helyileg, amely egy betanított TensorFlow modellt tartalmaz.

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: lemezkép besorolása TensorFlow-modell betanítása és üzembe helyezése a Visual Studio Code Azure Machine learning használatával](tutorial-train-deploy-image-classification-model-vscode.md).
