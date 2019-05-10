---
title: 'Oktatóanyag: Machine learning-modell vizuális felhasználói felülettel üzembe helyezése'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre prediktív elemzési megoldások az Azure Machine Learning szolgáltatás vizuális felületen. Betanításához, pontszám, és üzembe helyezése egy gépi tanulási modell használatával húzza, és dobja el a modulok. Ez az oktatóanyag második része egy kétrészes sorozat harmadik része a lineáris regressziós autó árának előrejelzése.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 5f29e3820416686b42167fa278c4b7d0f9a58f1f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190919"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Oktatóanyag: Machine learning-modell vizuális felhasználói felülettel üzembe helyezése

Ebben az oktatóanyagban azt tekintjük át az Azure Machine Learning szolgáltatás vizuális felületen prediktív megoldás fejlesztése igénybe vehet. Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. A [az oktatóanyag részében](ui-tutorial-automobile-price-train-score.md), betanított, pontozott, és az autó árának előrejelzése modell kiértékelése. Az oktatóanyag ezen részében meg:

> [!div class="checklist"]
> * A modell telepítésének előkészítése
> * Webszolgáltatás üzembe helyezése
> * Webes szolgáltatás tesztelése
> * Webszolgáltatások kezelése
> * A webszolgáltatás használata

## <a name="prerequisites"></a>Előfeltételek

Teljes [az oktatóanyag részében](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Üzembe helyezés előkészítése

Biztosíthat mások a fejlett ebben az oktatóanyagban a prediktív modellben használandó alkalma, telepítheti az Azure-webszolgáltatásként.

Az eddigi, hogy már kísérleteztek az a modell tanítása. Most, ideje létrehozni a felhasználói bemenet alapján új előrejelzéseket.

Felkészülés az üzembe helyezés két lépésből áll:  

1. Konvertálja a *betanítási kísérlet* létrehozott egy *prediktív kísérletté*
1. A prediktív kísérletté üzembe webszolgáltatásként

Érdemes először készítsen róla egy másolatot a kísérlet kiválasztásával **Mentés másként** a kísérlet vászon alján.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletté

Felkészülés a Ez a modell a központi telepítés, a betanítási kísérlet átalakítása egy prediktív kísérletet. Ez általában három lépésből áll:

1. Mentse a módosítást az adataink, és cserélje le a képzési modulok
1. Trim a kísérletet, amely csak volt szükség a betanításhoz modulok eltávolítása
1. Ahol a webszolgáltatás bemeneti adatokat fogad, és ahol állít elő a kimeneti megadása

Ezeket a lépéseket megteheti manuálisan, vagy bejelölheti **webszolgáltatás beállítása** ezek automatikusan végbemennek a kísérlet vászon alján.

![Animált gif egy prediktív kísérletet, automatikus betanítási kísérlet átalakítása megjelenítése](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Ha bejelöli **webszolgáltatás beállítása**, több dolog történik:

* Egyetlen alakítja át a betanított modell **betanított modell** modul. A modulpaletta a kísérlet vászon bal oldalán találhatók. Annak a **betanított modellek**.
* A betanításhoz használt modulok törlődnek; pontosabban:
  * Modell betanítása
  * Adatok felosztása
  * Modell értékelése
* A mentett betanított modell adnak vissza a kísérletbe
* **Webalkalmazás-bemenet** és **webes szolgáltatás kimeneti** modulok hozzáadásakor. Ezek a modulok azonosítása, adja meg a felhasználói adatokat a modell, és ahol adatokat adja vissza.

Láthatja, hogy a kísérletben menti az új lap felső részén a kísérleti vászonra a két részből áll. A lap alatt áll az eredeti betanítási kísérlet **betanítási kísérlet**, és alatt áll az újonnan létrehozott prediktív kísérletté **prediktív kísérletté**. A prediktív kísérletté lesz webszolgáltatásként fogja központilag telepíteni.

Is futtathatja a kísérletet most így kell kinéznie:  

![Üzembe helyezés előkészítése után a kísérlet várható konfigurációját ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Futtassa a kísérletet, egyszer utoljára (válasszon **futtatása**). Válassza ki a számítási célnak azt szeretné, hogy a kísérlet futtatásához előugró párbeszédpanelen. Ellenőrizze, hogy a modell továbbra is működik, válassza ki a kimenet a Score Model-modul, és válassza ki **eredmények megtekintése**. Láthatja, hogy az eredeti adatok jelenik meg, valamint a becsült ár ("pontozott címkék").

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése

Egy új webszolgáltatás üzembe helyezéséhez származó a kísérlet során:

1. Válassza ki **webszolgáltatás üzembe helyezése** a vászon alatti.
1. Válassza ki a **cél számítási** , hogy szeretné-e a webszolgáltatások futtatásához.

    Jelenleg a vizuális felhasználói felületet csak az üzembe helyezés az Azure Kubernetes Service (AKS) számítási célnak támogatja. A machine learning-munkaterület szolgáltatási a rendelkezésre álló AKS számítási célnak közül választhat, vagy egy új AKS-környezet a lépéseket a megjelenő párbeszédpanelen konfigurálja.

    ![Egy új számítási célnak lehetséges konfigurációját ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Válassza ki **Web Service szolgáltatásának telepítése**. Üzembe helyezés befejezése után megjelenik a következő értesítés. Üzembe helyezés eltarthat néhány percig.

    ![Képernyőfelvétel: a sikeres telepítés a megerősítést kérő üzenet.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>A webszolgáltatás teszteléséhez

Felhasználó által bevitt adatok kerül, az üzembe helyezett modellt a **bemenet webes** modul. A bemeneti majd számlálása az a **Score Model** modul. Beállította a prediktív kísérletté módja, a modellt vár adatok ugyanazt a formátumot az eredeti adatkészletként autó árát. Végül, mely visszaadja az eredményeket a felhasználó leállította a **webes szolgáltatás kimeneti** modul.

Webszolgáltatás tesztelheti a vizuális felhasználói felületet a web service lapján.

1. Nyissa meg a web Service szolgáltatásról szóló szakasz. Látni fogja a webszolgáltatás üzembe helyezett nevű **oktatóanyag – Automobile Price előrejelzése [prediktív Exp]**.

     ![Képernyőfelvétel: a web service lapon a legutóbb létrehozott webes szolgáltatással kiemelésével](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Jelölje ki a web service további részletek megtekintéséhez.

     ![Képernyőfelvétel: az a web service-ben elérhető további részletek megtekintéséhez](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Válassza ki **teszt**.

    ![A web service a tesztelési lapot ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Bemeneti adatok tesztelés vagy autofilled mintaadatok használatával, és válassza a **teszt** alján. A tesztelési kérelem elküldésekor a web Service, és az eredmények lapon látható.

## <a name="manage-the-web-service"></a>A webszolgáltatás kezelése

A web service telepítése után, és kezelhető a **webszolgáltatások** lapján a vizuális felhasználói felületet.

Webszolgáltatás kiválasztásával törölheti **törlése** a webes szolgáltatás részletei lapon.

   ![A törlés webes szolgáltatás gombra az ablak alján helyét ábrázoló képernyőfelvétel](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>A webszolgáltatás használata

Ez az oktatóanyag előző lépésben üzembe helyezett egy autó előrejelzési modell egy Azure-webszolgáltatásként. Felhasználók mostantól adatokat küldeni, és a REST API-n keresztül eredményeket kapni.

**Kérés/válasz** – a felhasználó egy vagy több sor az autókat tartalmazó adatok küld a szolgáltatás egy HTTP-protokoll használatával. A szolgáltatás egy vagy több csoportot, az eredmények válaszként.

Példa REST-hívások annak a **felhasználás** weblap szolgáltatás részletei lapon.

   ![Képernyőfelvétel: a minta REST hívja, hogy a felhasználás lapon találhatja meg a felhasználók](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Keresse meg a **API Doc** lap az API további részleteket talál.

  ![Képernyőfelvétel: az API további részletek, hogy az API Doc lapon találhatja meg a felhasználók](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Modellek és az Azure Machine Learning szolgáltatás munkaterület központi telepítések kezelése

A modellek és a webszolgáltatások üzembe helyezéséhez a vizuális felületen hoz létre az Azure Machine Learning szolgáltatás munkaterületről kezelhetők.

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

Ebben az oktatóanyagban megtudhatta, a kulcs létrehozásához, üzembe helyezéséhez, és a egy gépi tanulási modellt a vizuális felületen felhasználása lépéseit. Hogyan használhatja a vizuális felhasználói felületet más típusú kapcsolatos problémák megoldásához kapcsolatos további információkért tekintse meg a mintakísérleteket.

> [!div class="nextstepaction"]
> [Kredit kockázati besorolást minta](ui-sample-classification-predict-credit-risk-basic.md)
