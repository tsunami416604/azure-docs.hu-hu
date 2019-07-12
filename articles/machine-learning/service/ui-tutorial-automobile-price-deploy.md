---
title: 'Oktatóanyag: Machine learning-modell vizuális felhasználói felülettel üzembe helyezése'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre prediktív elemzési megoldások az Azure Machine Learning szolgáltatás vizuális felületen. Betanításához, pontszám, és üzembe helyezése egy gépi tanulási modell használatával húzza, és dobja el a modulok. Ez az oktatóanyag második része egy kétrészes sorozat harmadik része a lineáris regressziós autó árának előrejelzése.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: dd28fb51a4fc3fbf3dfc893f2f5f159ccafdb4b3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839307"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Oktatóanyag: Machine learning-modell vizuális felhasználói felülettel üzembe helyezése

Biztosíthat mások és a prediktív modellel fejlesztett [az oktatóanyag részében](ui-tutorial-automobile-price-train-score.md), telepíthet egy Azure-webszolgáltatásként. Az eddigi, hogy már kísérleteztek az a modell tanítása. Most, ideje létrehozni a felhasználói bemenet alapján új előrejelzéseket. Az oktatóanyag ezen részében meg:

> [!div class="checklist"]
> * A modell telepítésének előkészítése
> * Webszolgáltatás üzembe helyezése
> * Webes szolgáltatás tesztelése
> * Webszolgáltatások kezelése
> * A webszolgáltatás használata

## <a name="prerequisites"></a>Előfeltételek

Teljes [az oktatóanyag részében](ui-tutorial-automobile-price-train-score.md) hogyan betanítása és a egy machine learning-modellek pontozása a vizuális felületen.

## <a name="prepare-for-deployment"></a>Üzembe helyezés előkészítése

A kísérlethez webszolgáltatásként, üzembe helyezése előtt először azt kell konvertálni a *betanítási kísérlet* be egy *prediktív kísérletté*.

1. Válassza ki **prediktív kísérletezhet létrehozása*** a kísérlet vászon alján.

    ![Animált gif egy prediktív kísérletet, automatikus betanítási kísérlet átalakítása megjelenítése](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Ha bejelöli **létrehozása prediktív Kísérletté**, több dolog történik:
    
    * A betanított modell tárolódik, egy **betanított modell** a modulpaletta modul. Annak a **betanított modellek**.
    * A betanításhoz használt modulok törlődnek; pontosabban:
      * Modell betanítása
      * Adatok felosztása
      * Modell értékelése
    * A mentett betanított modell adnak vissza a kísérletbe.
    * **Webalkalmazás-bemenet** és **webes szolgáltatás kimeneti** modulok hozzáadásakor. Ezeket a modulokat azonosítása, ahol adja meg a felhasználói adatokat a modell, és ahol adatokat adja vissza.

    A **betanítási kísérlet** van továbbra is menti az új lap felső részén a kísérletvászonra.

1. **Futtassa** a kísérletet.

1. Válassza ki a kimenetét a **Score Model** modul, és válassza ki **eredmények megtekintése** , ellenőrizze, hogy a modell továbbra is működik. Láthatja, hogy az eredeti adatok jelenik meg, valamint a becsült ár ("pontozott címkék").

Is futtathatja a kísérletet most így kell kinéznie:  

![Üzembe helyezés előkészítése után a kísérlet várható konfigurációját ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése

1. Válassza ki **webszolgáltatás üzembe helyezése** a vászon alatti.

1. Válassza ki a **cél számítási** , hogy szeretné-e a webszolgáltatások futtatásához.

    Jelenleg a vizuális felhasználói felületet csak az üzembe helyezés az Azure Kubernetes Service (AKS) számítási célnak támogatja. A machine learning-munkaterület szolgáltatási a rendelkezésre álló AKS számítási célnak közül választhat, vagy egy új AKS-környezet a lépéseket a megjelenő párbeszédpanelen konfigurálja.

    ![Egy új számítási célnak lehetséges konfigurációját ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Válassza ki **Web Service szolgáltatásának telepítése**. Üzembe helyezés befejezése után megjelenik a következő értesítés. Üzembe helyezés eltarthat néhány percig.

    ![Képernyőfelvétel: a sikeres telepítés a megerősítést kérő üzenet.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez

Tesztelheti, és nyissa meg a vizuális felhasználói felületet webszolgáltatások kezelése a **webszolgáltatások** fülre.

1. Nyissa meg a web Service szolgáltatásról szóló szakasz. Látni fogja a webszolgáltatás üzembe helyezett nevű **oktatóanyag – Automobile Price előrejelzése [prediktív Exp]** .

     ![Képernyőfelvétel: a web service lapon a legutóbb létrehozott webes szolgáltatással kiemelésével](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Jelölje ki a web service további részletek megtekintéséhez.

     ![Képernyőfelvétel: az a web service-ben elérhető további részletek megtekintéséhez](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Válassza ki **teszt**.

    ![A web service a tesztelési lapot ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Bemeneti adatok tesztelés vagy autofilled mintaadatok használatával, és válassza a **teszt**.

    A tesztelési kérelem elküldésekor a web Service, és az eredmények lapon látható. Bár az ár értékét a bemeneti adatok jön létre, nem használatos az előrejelzési érték létrehozásához.

## <a name="consume-the-web-service"></a>A webszolgáltatás használata

Felhasználók most már API-kérések küldése az Azure web Service és eredményeket előre, hogy új autók árát.

**Kérés/válasz** – a felhasználó egy vagy több sor az autókat tartalmazó adatok küld a szolgáltatás egy HTTP-protokoll használatával. A szolgáltatás egy vagy több csoportot, az eredmények válaszként.

Példa REST-hívások annak a **felhasználás** weblap szolgáltatás részletei lapon.

   ![Képernyőfelvétel: a minta REST hívja, hogy a felhasználás lapon találhatja meg a felhasználók](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Keresse meg a **API Doc** lap az API további részleteket talál.

  ![Képernyőfelvétel: az API további részletek, hogy az API Doc lapon találhatja meg a felhasználók](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments"></a>Modellek és központi telepítések kezelése

A modellek és a webszolgáltatások üzembe helyezéséhez hoz létre a vizuális felületen is kezelhetők az Azure Machine Learning szolgáltatás munkaterületről.

1. Nyissa meg a munkaterület a [az Azure portal](https://portal.azure.com/).  

1. A munkaterületen válassza ki a **modellek**. Ezután válassza ki a létrehozott kísérlet.

    ![Képernyőfelvétel: hogyan navigálhat a kísérleteket az Azure Portalon](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Ezen az oldalon látni fogja a modellel kapcsolatos további részleteket.

    ![Kísérlet statisztikák az Azure Portal képernyőképe ábrázoló áttekintése](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Válassza ki **központi telepítések**, azt a modellt használó webes szolgáltatások listázza. Válassza ki a webes szolgáltatás nevét, fog megérkezni webes szolgáltatás részletei lapon. Ezen a lapon a web service kapcsolatos részletesebb információkat kaphat.

    ![Képernyőkép – részletes futtassa a jelentést](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, a kulcs létrehozásához, üzembe helyezéséhez, és a egy gépi tanulási modellt a vizuális felületen felhasználása lépéseit. Hogyan használhatja a vizuális felhasználói felületet más típusú kapcsolatos problémák megoldásához kapcsolatos további információkért lásd: meg, hogy más nyelven íródott mintakísérleteket.

> [!div class="nextstepaction"]
> [Kredit kockázati besorolást minta](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
