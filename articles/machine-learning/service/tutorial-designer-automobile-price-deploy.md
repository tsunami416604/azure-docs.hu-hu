---
title: 'Oktatóanyag: gépi tanulási modell üzembe helyezése a tervezővel'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre prediktív elemzési megoldást Azure Machine Learning Designerben (előzetes verzió). A gépi tanulási modellek betanítása, pontszáma és üzembe helyezése drag-and-drop modulok használatával.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 724a38cb516e5689f817e9ddeaa867b17274971b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932042"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Oktatóanyag: gépi tanulási modell üzembe helyezése a Designerben (előzetes verzió)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Az [oktatóanyag első részében](tutorial-designer-automobile-price-train-score.md) létrehozott prediktív modell üzembe helyezésével mások számára is lehetővé teheti a használatát. Az első részben betanított egy modellt. Most itt az ideje, hogy a felhasználói bevitel alapján új előrejelzéseket hozzon. Az oktatóanyag ezen részében a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Valós idejű következtetési folyamat létrehozása.
> * Hozzon létre egy következtetésben lévő fürtöt.
> * A valós idejű végpont üzembe helyezése.
> * Tesztelje a valós idejű végpontot.

## <a name="prerequisites"></a>Előfeltételek

[Az oktatóanyag első részében](tutorial-designer-automobile-price-train-score.md) megismerheti, hogyan végezheti el a gépi tanulási modellek betanítását és kiértékelését a tervezőben.

## <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

A folyamat üzembe helyezéséhez először át kell alakítania a betanítási folyamatot egy valós idejű következtetési folyamatba. Ez a folyamat eltávolítja a betanítási modulokat, és bemeneteket és kimeneteket ad a következtetésekhez.

### <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

1. A csővezeték fölött válassza a **következtetési folyamat létrehozása** > a **valós idejű következtetések folyamata**lehetőséget.

    A folyamatnak most így kell kinéznie: 

   ![Képernyőfelvétel a folyamat várható konfigurációjának megjelenítéséről az üzembe helyezés előkészítése után](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

    Amikor kiválasztja a **következtetési folyamat létrehozása**lehetőséget, több dolog történik:
    
    * A betanított modellt a modul palettáján **adatkészlet** -modulként tárolja a rendszer. **Az adatkészletek**szakaszban találja.
    * A képzési modulok, például a **betanítási modell** és a **felosztott adategységek** törlődnek.
    * A rendszer visszaadja a mentett betanított modellt a folyamatba.
    * A **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** moduljai hozzáadódnak a szolgáltatáshoz. Ezek a modulok azt mutatják be, hogy a felhasználói adattípusok hol kerülnek be a modellbe, és hol adja vissza az

    > [!NOTE]
    > A *betanítási folyamat* a folyamat vászonjának tetején található új lapon lesz mentve. A tervezőben közzétett folyamatként is megtalálhatók.
    >

1. Válassza a **Futtatás**lehetőséget, és használja ugyanazt a számítási célt és kísérletet, amelyet az első részben használt.

1. Válassza ki a **pontszám modell** modult.

1. A Tulajdonságok ablaktáblán válassza a **kimenetek** > **Megjelenítés** elemet annak ellenőrzéséhez, hogy a modell továbbra is működik-e. Láthatja, hogy az eredeti adatértékek az előre jelzett árral ("pontozásos címkék") együtt jelennek meg.

1. Válassza az **Üzembe helyezés** lehetőséget.

## <a name="create-an-inferencing-cluster"></a>Következtetési fürt létrehozása

A megjelenő párbeszédpanelen bármelyik meglévő Azure Kubernetes Service-(ak-) fürtből kiválaszthatja a modell üzembe helyezését. Ha nem rendelkezik AK-fürttel, a következő lépésekkel hozhat létre egyet.

1. A **számítási** lapon megjelenő párbeszédpanelen kattintson a **számítás** elemre.

1. A navigációs menüszalagon válassza a **következtetési fürtök** >  **+ új**lehetőséget.

    ![Az új következtetési fürt ablaktábla beszerzését bemutató képernyőkép](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. A következtetési fürt ablaktáblán konfigurálja az új Kubernetes szolgáltatást.

1. Írja be a következőt: *Kaba-számítás* a **számítási névhez**.
    
1. Válasszon egy közeli régiót, amely elérhető a **régió**számára.

1. Kattintson a **Létrehozás** gombra.

    > [!NOTE]
    > Egy új AK-szolgáltatás létrehozása körülbelül 15 percet vesz igénybe. A kiépítési állapotot megtekintheti a **következtetési fürtök** oldalon.
    >

## <a name="deploy-the-real-time-endpoint"></a>A valós idejű végpont üzembe helyezése

Miután az AK-szolgáltatás befejezte a kiépítést, térjen vissza a valós idejű következtetési folyamatra az üzembe helyezés befejezéséhez.

1. Válassza a vászon fölötti **üzembe helyezés** lehetőséget.

1. Válassza az **új valós idejű végpont telepítése**lehetőséget. 

1. Válassza ki a létrehozott AK-fürtöt.

1. Válassza az **Üzembe helyezés** lehetőséget.

    ![Az új valós idejű végpont beállítását bemutató képernyőkép](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Az üzembe helyezés befejeződése után a vászon fölötti sikeres értesítés jelenik meg. Néhány percet is igénybe vehet.

## <a name="test-the-real-time-endpoint"></a>A valós idejű végpont tesztelése

Az üzembe helyezés befejeződése után a **végpontok** lapon ellenőrizheti a valós idejű végpontot.

1. A **végpontok** lapon válassza ki a telepített végpontot.

    ![Képernyőfelvétel: a valós idejű végpontok lap, amely a legutóbb létrehozott végpontot mutatja](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Válassza a **teszt**lehetőséget.

1. Manuálisan is beírhatja a tesztelési adatokat, vagy használhatja az autofilled Sample-adatokat, és kiválaszthatja a **teszt**lehetőséget.

    A portál egy teszt kérelmet küld a végpontnak, és megjeleníti az eredményeket. Bár a bemeneti adatokhoz érték jön létre, a rendszer nem használja fel az előrejelzési érték generálására.

    ![Képernyőfelvétel: a valós idejű végpont és az emelt szintű címke tesztelése](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre, helyezhet üzembe és használhat fel gépi tanulási modellt a tervezőben. Ha többet szeretne megtudni arról, hogyan használhatja a tervezőt más típusú problémák megoldására, tekintse meg a többi minta folyamatát.

> [!div class="nextstepaction"]
> [Hitelkockázat besorolási minta](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
