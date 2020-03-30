---
title: 'Oktatóanyag: Gépi tanulási modell üzembe helyezése a tervezővel'
titleSuffix: Azure Machine Learning
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre prediktív elemzési megoldást az Azure Machine Learning designerben (előzetes verzió). Betanítson, pontozást és üzembe helyezzen egy gépi tanulási modellt húzási modulok használatával.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c3ca37fd47b6551a95f9a491053ec7863acd1eeb
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389392"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Oktatóanyag: Gépi tanulási modell üzembe helyezése a tervezővel (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Az [oktatóanyag első részében](tutorial-designer-automobile-price-train-score.md) kifejlesztett prediktív modellt telepítheti, hogy másoknak is lehetőséget adjon a használatára. Az első részben kiképezte a modelljét. Most itt az ideje, hogy új előrejelzéseket hozzon létre a felhasználói bevitel alapján. Ebben a részben a bemutató, akkor:

> [!div class="checklist"]
> * Hozzon létre egy valós idejű következtetési folyamatot.
> * Hozzon létre egy következtetési fürtöt.
> * Telepítse a valós idejű végpontot.
> * Tesztelje a valós idejű végpontot.

## <a name="prerequisites"></a>Előfeltételek

Töltse ki [az oktatóanyag első részét,](tutorial-designer-automobile-price-train-score.md) amelyből megtudhatja, hogyan taníthat be és szerezhet meg egy gépi tanulási modellt a tervezőben.

## <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

A folyamat üzembe helyezéséhez először át kell alakítania a betanítási folyamatot egy valós idejű következtetési folyamattá. Ez a folyamat eltávolítja a betanítási modulokat, és webszolgáltatás-bemeneteket és kimeneteket ad hozzá a kérelmek kezeléséhez.

### <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

1. A folyamatvászon felett válassza a **Következtetési folyamat** > létrehozása**valós idejű következtetési folyamat lehetőséget.**

    ![Képernyőkép, amelyen a folyamat létrehozása gomb található.](./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png)

    A folyamatnak így kell kinéznie: 

   ![Képernyőkép a folyamat várható konfigurációjáról a telepítésre való előkészítés után](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Ha a **Következtetéslétrehozása folyamat lehetőséget választja,** több dolog is történhet:
    
    * A betanított modell **adatkészletmodulként** van tárolva a modulpalettán. Az **Adatkészletek**csoportban található.
    * A betanítási modulok, például **a betanítási modell** és **a felosztási adatok** törlődnek.
    * A mentett betanított modell újra hozzáadódik a folyamathoz.
    * **A webszolgáltatás bemeneti** és **webszolgáltatás kimeneti** moduljai hozzáadódnak. Ezek a modulok azt mutatják, hogy a felhasználói adatok hol kerülnek be a folyamatba, és hol adják vissza az adatokat.

    > [!NOTE]
    > Alapértelmezés szerint a **webszolgáltatás bemeneti** elvárja ugyanazt az adatsémát, mint a betanítási adatok létrehozásához használt prediktív folyamat. Ebben a forgatókönyvben az ár szerepel a sémában. Az előrejelzés során azonban az ár nem számít tényezőnek.
    >

1. Válassza **a Küldés**lehetőséget, és használja ugyanazt a számítási célt és kísérletet, amelyet az első részben használt.

    Ha az első futtatás, akár 20 percet is igénybe vehet, amíg a folyamat futása befejeződik. Az alapértelmezett számítási beállítások minimális csomópontmérete 0, ami azt jelenti, hogy a tervező nek erőforrásokat kell lefoglalnia, miután tétlen. Az ismétlődő folyamatfuttatások kevesebb időt vesznek igénybe, mivel a számítási erőforrások már le vannak foglalva. Emellett a tervező az egyes modulok gyorsítótárazott eredményeit használja a hatékonyság további növelése érdekében.

1. Válassza az **Üzembe helyezés** lehetőséget.

## <a name="create-an-inferencing-cluster"></a>Következtetési fürt létrehozása

A megjelenő párbeszédpanelen választhat bármely meglévő Azure Kubernetes-szolgáltatás (AKS) fürtközül, ahol a modellüzembe helyezéséhez. Ha nem rendelkezik AKS-fürttel, az alábbi lépésekkel hozzon létre egyet.

1. Válassza **a Számítási lehetőséget** a számítási lapra megjelenő párbeszédpanelen. **Compute**

1. A navigációs menüszalagján válassza **a Fürtök levonása** > **+ Új**lehetőséget.

    ![Képernyőkép, amely bemutatja, hogyan juthat el az új következtetésfürtablaktáblához](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. A következtetés fürtpanelen konfiguráljon egy új Kubernetes-szolgáltatást.

1. Adja meg az *aks-compute nevet* a **Számítási névhez.**
    
1. Válassza ki a régióhoz elérhető közeli **régiót.**

1. Kattintson a **Létrehozás** gombra.

    > [!NOTE]
    > Az új AKS-szolgáltatás létrehozása körülbelül 15 percet vesz igénybe. A létesítési állapot a **Következtetés fürtök** lapon ellenőrizheti.
    >

## <a name="deploy-the-real-time-endpoint"></a>A valós idejű végpont üzembe helyezése

Miután az AKS-szolgáltatás befejezte a kiépítést, térjen vissza a valós idejű következtetési folyamathoz az üzembe helyezés befejezéséhez.

1. Válassza a Vászon feletti **üzembe helyezés** lehetőséget.

1. Válassza **az Új valós idejű végpont telepítése**lehetőséget. 

1. Jelölje ki a létrehozott AKS-fürtöt.

1. Válassza az **Üzembe helyezés** lehetőséget.

    ![Képernyőkép az új valós idejű végpont beállításáról](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    A telepítés befejezése után megjelenik a vászon feletti sikerességi értesítés. Beletelhet pár percbe.

## <a name="test-the-real-time-endpoint"></a>A valós idejű végpont tesztelése

Az üzembe helyezés befejezése után tesztelheti a valós idejű végpontot a **Végpontok** lapra való kivizsgáláskor.

1. A **Végpontok** lapon jelölje ki a telepített végpontot.

    ![Képernyőkép a valós idejű végpontok fülről, amelyen a legutóbb létrehozott végpont van kiemelve](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Válassza a **Tesztelés** lehetőséget.

1. Manuálisan is beviheti a tesztelési adatokat, vagy használhatja az automatikus kitöltésű mintaadatokat, és válassza a **Teszt**lehetőséget.

    A portál egy tesztkérelmet küld a végpontnak, és megjeleníti az eredményeket. Bár a bemeneti adatok hoz létre árértéket, nem használják az előrejelzési érték létrehozásához.

    ![Képernyőkép a valós idejű végpont teszteléséről a kiemelt ár pontozott címkéjével](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta a legfontosabb lépéseket, hogyan hozhat létre, üzembe helyezhet és használható fel egy gépi tanulási modella tervezőben. Ha többet szeretne megtudni arról, hogyan használhatja a tervezőt más típusú problémák megoldására, tekintse meg a többi mintafolyamatokat.

> [!div class="nextstepaction"]
> [Tervezői minták](samples-designer.md)
