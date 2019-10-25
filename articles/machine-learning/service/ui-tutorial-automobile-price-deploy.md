---
title: 'Oktatóanyag: gépi tanulási modell üzembe helyezése a vizualizációs felületen'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre prediktív elemzési megoldást a Azure Machine Learning Visual Interface-ben. Gépi tanulási modell betanítása, pontszáma és üzembe helyezése a drag and drop modulok használatával.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792677"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Oktatóanyag: gépi tanulási modell üzembe helyezése a vizualizációs felületen

Annak érdekében, hogy az [oktatóanyag első részében](ui-tutorial-automobile-price-train-score.md)kifejlesztett prediktív modellt is használhassa, valós idejű végpontként telepítheti. Az 1. részben betanítja a modellt. Most itt az ideje, hogy a felhasználói bevitel alapján új előrejelzéseket hozzon. Az oktatóanyag ezen részében a következőket végezheti el:

> [!div class="checklist"]
> * Valós idejű végpont üzembe helyezése
> * Következtetési fürt létrehozása
> * Valós idejű végpont tesztelése

## <a name="prerequisites"></a>Előfeltételek

Az [oktatóanyag első részében](ui-tutorial-automobile-price-train-score.md) megismerheti, hogyan végezheti el a gépi tanulási modellek betanítását és kiértékelését a vizuális felületen.

## <a name="deploy-a-real-time-endpoint"></a>Valós idejű végpont üzembe helyezése

A folyamat üzembe helyezéséhez a következőket kell tennie:

1. Alakítsa át a betanítási folyamatot egy valós idejű következtetési folyamatba, amely eltávolítja a betanítási modulokat, és bemeneteket és kimeneteket ad a következtetésekhez.
1. A következtetési folyamat üzembe helyezése.

### <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

1. A folyamat vászon tetején válassza a **következtetési folyamat létrehozása**  > **valós idejű következtetési folyamat** lehetőséget.

    Amikor kiválasztja a **következtetési folyamat létrehozása**lehetőséget, több dolog történik:
    
    * A betanított modellt a modul palettáján **adatkészlet** -modulként tárolja a rendszer. **Az adatkészletek**szakaszban találja.
    * A képzéshez használt modulok, például a betanítási **modell** és a **felosztott adategységek**el lesznek távolítva.
    * A rendszer visszaadja a mentett betanított modellt a folyamatba.
    * A **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** moduljai hozzáadódnak a szolgáltatáshoz. Ezek a modulok határozzák meg, hogy a felhasználói adattípusok hol lépnek be a modellbe, és hol lesznek visszaadva.

    > [!Note]
    > A **betanítási folyamat** a folyamat vászonjának tetején található új lapon lesz mentve. A vizuális felületen közzétett folyamatként is megtalálhatók.
    >

    A folyamatnak most így kell kinéznie:  

   ![Képernyőfelvétel a folyamat várható konfigurációjának megjelenítéséről az üzembe helyezés előkészítése után](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. Válassza a **Futtatás** lehetőséget, és használja ugyanazt a számítási célt, és kísérletezzen, amelyet az 1. részben használt.

1. Válassza ki a **pontszám modell** modult.

1. A Tulajdonságok ablaktáblán válassza a **kimenetek**  > **Megjelenítés** elemet annak ellenőrzéséhez, hogy a modell továbbra is működik-e. Láthatja, hogy az eredeti adatértékek az előre jelzett árral ("pontozásos címkék") együtt jelennek meg.

1. Válassza az **Üzembe helyezés** lehetőséget.

### <a name="create-an-inferencing-cluster"></a>Következtetési fürt létrehozása

A megjelenő párbeszédpanelen kiválaszthatja a munkaterületen meglévő Azure Kubernetes Service (ak) fürtöket a modell üzembe helyezéséhez. Ha nem rendelkezik AK-fürttel, a következő lépésekkel hozhat létre egyet.

1. Válassza a **számítás** lehetőséget a párbeszédpanelen a **számítási** oldal megnyitásához.

1. A navigációs menüszalagon válassza a **következtetési fürtök**  >  **+ új**lehetőséget.

    ![Az új következtetési fürt panel megnyitását bemutató képernyőkép](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. A következtetési fürt ablaktáblán konfigurálja az új Kubernetes szolgáltatást.

1. Adja meg a **számítási név**"AK-számítás" oszlopát.
    
1. Válasszon egy közeli elérhető **régiót**.

1. Kattintson a **Létrehozás** gombra.

    > [!Note]
    > Egy új AK-szolgáltatás létrehozása körülbelül 15 percet vesz igénybe. A kiépítési állapotot a **következtetési fürtök** oldalon tekintheti meg
    >

### <a name="deploy-the-real-time-endpoint"></a>A valós idejű végpont üzembe helyezése

Miután az AK-szolgáltatás befejezte a kiépítést, térjen vissza a valós idejű következtetési folyamatra az üzembe helyezés befejezéséhez.

1. Válassza a vászon fölötti **üzembe helyezés** lehetőséget.

1. Válassza az **új valós idejű végpont telepítése**lehetőséget. 

1. Válassza ki a létrehozott AK-fürtöt.

1. Válassza az **Üzembe helyezés** lehetőséget.

    ![Az új valós idejű végpont beállítását bemutató képernyőkép](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Ha az üzembe helyezés befejeződik, a rendszer a vászon fölötti sikeres értesítést jeleníti meg, néhány percet is igénybe vehet.

## <a name="test-the-real-time-endpoint"></a>A valós idejű végpont tesztelése

A valós idejű végpontot a bal oldali munkaterület navigációs paneljének **végpontok** lapjára kattintva ellenőrizheti.

1. A **végpontok** lapon válassza ki a telepített végpontot.

    ![Képernyőfelvétel: a valós idejű végpontok lap, amely a legutóbb létrehozott végpontot mutatja](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Válassza a **teszt**lehetőséget.

1. Adja meg a bemeneti tesztelési adatokat, vagy használja az kitöltött mintaadatok használatát, és válassza a **teszt**lehetőséget.

    A rendszer elküldte a tesztelési kérelmet a végpontnak, és az eredmények megjelennek a lapon. Bár a bemeneti adatokhoz érték jön létre, a rendszer nem használja fel az előrejelzési érték generálására.

    ![Képernyőfelvétel: a valós idejű végpont és az emelt szintű címke tesztelése](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre, helyezhet üzembe és vehet fel gépi tanulási modellt a vizualizációs felületen. Ha többet szeretne megtudni arról, hogyan használhatja a vizuális felületet más típusú problémák megoldására, tekintse meg a többi minta folyamatát.

> [!div class="nextstepaction"]
> [Hitelkockázat besorolási minta](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
