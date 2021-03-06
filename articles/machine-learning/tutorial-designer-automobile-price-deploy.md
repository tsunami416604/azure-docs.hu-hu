---
title: 'Oktatóanyag: ML modellek üzembe helyezése a tervezővel'
titleSuffix: Azure Machine Learning
description: Hozzon létre egy prediktív elemzési megoldást Azure Machine Learning Designerben. A gépi tanulási modellek betanítása, pontszáma és üzembe helyezése drag-and-drop modulok használatával.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: designer
ms.openlocfilehash: 14be695f2f58b9738af11a3d2ca3f06592a1cc6e
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575958"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Oktatóanyag: gépi tanulási modell üzembe helyezése a tervezővel


Az [oktatóanyag első részében](tutorial-designer-automobile-price-train-score.md) létrehozott prediktív modell üzembe helyezésével mások számára is lehetővé teheti a használatát. Az első részben betanított egy modellt. Most itt az ideje, hogy a felhasználói bevitel alapján új előrejelzéseket hozzon. Az oktatóanyag ezen részében a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Valós idejű következtetési folyamat létrehozása.
> * Hozzon létre egy következtetésben lévő fürtöt.
> * A valós idejű végpont üzembe helyezése.
> * Tesztelje a valós idejű végpontot.

## <a name="prerequisites"></a>Előfeltételek

[Az oktatóanyag első részében](tutorial-designer-automobile-price-train-score.md) megismerheti, hogyan végezheti el a gépi tanulási modellek betanítását és kiértékelését a tervezőben.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

A folyamat üzembe helyezéséhez először át kell alakítania a betanítási folyamatot egy valós idejű következtetési folyamatba. Ez a folyamat eltávolítja a betanítási modulokat, és hozzáadja a webszolgáltatás bemeneteit és kimeneteit a kérelmek kezeléséhez.

### <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

1. A folyamat fölötti vászonnál válassza a **következtetés létrehozása folyamat**  >  **valós idejű következtetési folyamat** lehetőséget.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Képernyőfelvétel a folyamat létrehozása gombról":::

    A folyamatnak most így kell kinéznie: 

   ![Képernyőfelvétel a folyamat várható konfigurációjának megjelenítéséről az üzembe helyezés előkészítése után](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Amikor kiválasztja a **következtetési folyamat létrehozása** lehetőséget, több dolog történik:
    
    * A betanított modellt a modul palettáján **adatkészlet** -modulként tárolja a rendszer. **Az adatkészletek** szakaszban találja.
    * A képzési modulok, például a **betanítási modell** és a **felosztott adategységek** törlődnek.
    * A rendszer visszaadja a mentett betanított modellt a folyamatba.
    * A **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** moduljai hozzáadódnak a szolgáltatáshoz. Ezek a modulok azt mutatják be, hogy a felhasználói adatcsatorna hol adja meg a folyamatot

    > [!NOTE]
    > Alapértelmezés szerint a **webszolgáltatás bemenete** ugyanazt az adatsémát fogja várni, mint a prediktív folyamat létrehozásához használt betanítási adatok. Ebben az esetben a séma tartalmazza az árat. Az előrejelzés során azonban az ár nem használható faktorként.
    >

1. Válassza a **Submit (Küldés**) lehetőséget, és használja ugyanazt a számítási célt és kísérletet, amelyet az első részben használt.

    Ha ez az első futtatás, akár 20 percet is igénybe vehet, amíg a folyamat befejezi a futását. Az alapértelmezett számítási beállításokhoz a csomópont minimális mérete 0, ami azt jelenti, hogy a tervezőnek üresjárat után le kell foglalnia az erőforrásokat. Az ismétlődő folyamat-futtatások kevesebb időt vesznek igénybe, mivel a számítási erőforrások már le vannak foglalva. Emellett a tervező az egyes modulok gyorsítótárazott eredményeit használja a hatékonyság növelése érdekében.

1. Válassza az **Üzembe helyezés** lehetőséget.

## <a name="create-an-inferencing-cluster"></a>Következtetési fürt létrehozása

A megjelenő párbeszédpanelen bármelyik meglévő Azure Kubernetes Service-(ak-) fürtből kiválaszthatja a modell üzembe helyezését. Ha nem rendelkezik AK-fürttel, a következő lépésekkel hozhat létre egyet.

1. A **számítási** lapon megjelenő párbeszédpanelen kattintson a **számítás** elemre.

1. A navigációs menüszalagon válassza a **következtetési fürtök**  >  **+ új** lehetőséget.

    ![Az új következtetési fürt ablaktábla beszerzését bemutató képernyőkép](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. A következtetési fürt ablaktáblán konfigurálja az új Kubernetes szolgáltatást.

1. Írja be a következőt: *Kaba-számítás* a **számítási névhez**.
    
1. Válasszon egy közeli régiót, amely elérhető a **régió** számára.

1. Kattintson a **Létrehozás** gombra.

    > [!NOTE]
    > Egy új AK-szolgáltatás létrehozása körülbelül 15 percet vesz igénybe. A kiépítési állapotot megtekintheti a **következtetési fürtök** oldalon.
    >

## <a name="deploy-the-real-time-endpoint"></a>A valós idejű végpont üzembe helyezése

Miután az AK-szolgáltatás befejezte a kiépítést, térjen vissza a valós idejű következtetési folyamatra az üzembe helyezés befejezéséhez.

1. Válassza a vászon fölötti **üzembe helyezés** lehetőséget.

1. Válassza az **új valós idejű végpont telepítése** lehetőséget. 

1. Válassza ki a létrehozott AK-fürtöt.

1. Válassza az **Üzembe helyezés** lehetőséget.
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Az új valós idejű végpont beállítását bemutató képernyőkép":::

    Az üzembe helyezés befejeződése után a vászon fölötti sikeres értesítés jelenik meg. Néhány percet is igénybe vehet.

## <a name="view-the-real-time-endpoint"></a>Valós idejű végpont megtekintése

Az üzembe helyezés befejeződése után megtekintheti a valós idejű végpontot a **végpontok** lapon.

1. A **végpontok** lapon válassza ki a telepített végpontot.

1. A **részletek** lapon további információkat tekinthet meg, például a REST URI-t, az állapotot és a címkéket.

1. A felhasználás **lapon** megtalálhatja a biztonsági kulcsokat, és megadhatja a hitelesítési módszereket.

1. A **központi telepítési naplók** lapon megtekintheti a valós idejű végpont részletes üzembe helyezési naplóit. 

További információ a webszolgáltatás használatáról: a [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre, helyezhet üzembe és használhat fel gépi tanulási modellt a tervezőben. Ha többet szeretne megtudni a tervező használatáról, tekintse meg az alábbi hivatkozásokat:

+ [Tervezői minták](samples-designer.md): megtudhatja, hogyan használhatja a tervezőt más típusú problémák megoldására.
+ [Azure Machine learning Studio használata Azure-beli virtuális hálózaton](how-to-enable-studio-virtual-network.md).