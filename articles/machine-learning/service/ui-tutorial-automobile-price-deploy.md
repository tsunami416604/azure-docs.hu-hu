---
title: 'Oktatóanyag: Gépi tanulási modell üzembe helyezése a vizualizációs felületen'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre prediktív elemzési megoldást Azure Machine Learning vizuális felületén. Gépi tanulási modell betanítása, pontszáma és üzembe helyezése a drag and drop modulok használatával. Ez az oktatóanyag egy kétrészes sorozat második része, amely az autók árának lineáris regresszió használatával történő előrejelzését ismerteti.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 22d5c41e8b815fd99450962cb63d11e9560c787f
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997008"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Oktatóanyag: Gépi tanulási modell üzembe helyezése a vizualizációs felületen

Annak érdekében, hogy az [oktatóanyag első részében](ui-tutorial-automobile-price-train-score.md)kifejlesztett prediktív modellt használják mások számára, üzembe helyezhető Azure-webszolgáltatásként. Eddig a modell tanításával kísérletezett. Most itt az ideje, hogy a felhasználói bevitel alapján új előrejelzéseket hozzon. Az oktatóanyag ezen részében a következőket végezheti el:

> [!div class="checklist"]
> * Modell előkészítése üzembe helyezéshez
> * Webszolgáltatás üzembe helyezése
> * Webszolgáltatás tesztelése
> * Webszolgáltatás kezelése
> * A webszolgáltatás felhasználása

## <a name="prerequisites"></a>Előfeltételek

Az [oktatóanyag első részében](ui-tutorial-automobile-price-train-score.md) megismerheti, hogyan végezheti el a gépi tanulási modellek betanítását és kiértékelését a vizuális felületen.

## <a name="prepare-for-deployment"></a>Üzembe helyezés előkészítése

A kísérlet webszolgáltatásként való üzembe helyezése előtt először alakítsa át a *betanítási kísérletet* egy *prediktív kísérletbe*.

1. Válassza a **prediktív kísérlet létrehozása*** elemet a kísérlet vászon alján.

    ![Animált GIF – a betanítási kísérlet automatikus átalakítása prediktív kísérletre](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Ha a **prediktív kísérlet létrehozása**lehetőséget választja, több dolog történik:
    
    * A betanított modellt a modul palettáján a **betanított modell** modulként tárolja a rendszer. Megtalálhatja a **betanított modellekben**.
    * A betanításhoz használt modulok törlődnek; pontosabban:
      * Modell betanítása
      * Adatok felosztása
      * Modell értékelése
    * A rendszer visszaadja a mentett betanított modellt a kísérlethez.
    * A **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** moduljai hozzáadódnak a szolgáltatáshoz. Ezek a modulok határozzák meg, hogy a felhasználói adattípusok hol fognak belépni a modellbe.

    A **betanítási kísérlet** továbbra is a kísérlet vászon felső részén található új lapok alatt lesz mentve.

1. **Futtassa** a kísérletet.

1. Válassza ki a **pontszám modell** modul kimenetét, és válassza az **eredmények megtekintése** lehetőséget annak ellenőrzéséhez, hogy a modell továbbra is működik-e. Láthatja az eredeti adatmegjelenítést, valamint az előre jelzett árat ("pontozásos címkék").

A kísérletnek most így kell kinéznie:  

![A kísérlet várható konfigurációját bemutató képernyőkép a telepítés előkészítése után](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése

1. Válassza a **webszolgáltatás üzembe helyezése** a vászon alatt lehetőséget.

1. Válassza ki a webszolgáltatás futtatásához használni kívánt **számítási célt** .

    A vizualizációs felület jelenleg csak az Azure Kubernetes Service (ak) számítási célokhoz való üzembe helyezést támogatja. Az elérhető AK számítási célok közül választhat a Machine learning szolgáltatás munkaterületén, vagy konfigurálhat egy új AK-környezetet a megjelenő párbeszéd lépéseinek használatával.

    ![Az új számítási cél lehetséges konfigurációját ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Válassza a **webszolgáltatás telepítése**lehetőséget. Az üzembe helyezés befejezésekor a következő értesítés jelenik meg. Az üzembe helyezés néhány percet is igénybe vehet.

    ![A sikeres telepítés megerősítő üzenetét bemutató képernyőkép.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez

A **webszolgáltatások** lapon navigálva tesztelheti és kezelheti a Visual Interface Web Services szolgáltatást.

1. Nyissa meg a webszolgáltatás szakaszt. Ekkor megjelenik a webszolgáltatást, amelyet a Name (név) **oktatóanyaggal telepített – a személygépkocsi árának előrejelzése [prediktív exp]** .

     ![Képernyőfelvétel: a webszolgáltatás lap, amely a közelmúltban létrehozott webszolgáltatás Kiemelt](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. További részletek megtekintéséhez válassza ki a webszolgáltatás nevét.

1. Válassza a **teszt**lehetőséget.

    [![A webszolgáltatás tesztelési lapját ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Adja meg a bemeneti tesztelési adatokat, vagy használja az kitöltött mintaadatok használatát, és válassza a **teszt**lehetőséget.

    A tesztelési kérelem a webszolgáltatásnak van elküldve, és az eredmények a lapon jelennek meg. Bár a bemeneti adatokhoz érték jön létre, a rendszer nem használja fel az előrejelzési érték generálására.

## <a name="consume-the-web-service"></a>A webszolgáltatás felhasználása

A felhasználók mostantól API-kérelmeket küldhetnek az Azure-webszolgáltatásnak, és eredményeket kaphatnak az új személygépkocsik árának előrejelzéséhez.

**Kérelem/válasz** – a felhasználó egy vagy több sornyi mobil-adatokat küld a szolgáltatásnak HTTP protokoll használatával. A szolgáltatás egy vagy több eredménnyel válaszol.

A REST-hívásokat a webszolgáltatás részletei lap **felhasználás lapján** találhatja meg.

   ![Képernyőkép: a felhasználók által a felhasználás lapon megtalálható minta REST-hívás](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

További API-részletekért keresse fel az **API doc** lapot.

## <a name="manage-models-and-deployments"></a>Modellek és központi telepítések kezelése

A vizuális felületen létrehozott modellek és webszolgáltatás-telepítések a Azure Machine Learning munkaterületről is kezelhetők.

1. Nyissa meg a munkaterületet a [Azure Portalban](https://portal.azure.com/).  

1. A munkaterületen válassza a **modellek**elemet. Ezután válassza ki a létrehozott kísérletet.

    ![A Azure Portalban található kísérletek elérését bemutató képernyőkép](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Ezen az oldalon további részleteket láthat a modellről.

1. Válassza a **központi telepítések**lehetőséget, majd listázza a modellt használó webszolgáltatásokat. Válassza ki a webszolgáltatás nevét, majd nyissa meg a webszolgáltatások részletei lapot. Ezen az oldalon részletesebb információkhoz juthat a webszolgáltatásról.

    [![Képernyőkép – részletes futtatási jelentés](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

Ezeket a modelleket és központi telepítéseket a munkaterület kezdőlapjának **modellek** és **végpontok** szakaszában [(előzetes verzió)](https://ml.azure.com)is megtalálhatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre, helyezhet üzembe és vehet fel gépi tanulási modellt a vizualizációs felületen. Ha többet szeretne megtudni arról, hogyan használhatja a vizuális felületet más típusú problémák megoldására, tekintse meg az egyéb példákat.

> [!div class="nextstepaction"]
> [Hitelkockázat besorolási minta](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
