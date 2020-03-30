---
title: Azure Machine Learning-munkaterületek létrehozása a portálon
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre, tekinthet meg és törölhet Azure Machine Learning-munkaterületeket az Azure Portalon.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: f38b0895b0d6eddcf63c082d3df205f4d9de9d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297067"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Machine Learning-munkaterületek létrehozása és kezelése az Azure Portalon
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben azure [**Machine Learning-munkaterületeket**](concept-workspace.md) hozhat létre, tekinthet meg és törölhet az Azure Machine Learning hez létrehozott Azure [Portalon.](overview-what-is-azure-ml.md)  A portál a legegyszerűbb módja a munkaterületek használatának megkezdésének, de ahogy az igények változnak vagy az automatizálási követelmények növekednek, a [CLI használatával](reference-azure-machine-learning-cli.md), [Python kóddal](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) vagy [a VS Code kiterjesztéssel](tutorial-setup-vscode-extension.md)is létrehozhat és törölhet munkaterületeket.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Egy munkaterület létrehozásához szüksége van egy Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt elkezdené. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetés hitelesítő adatainak használatával. 

1. Az Azure Portal bal felső sarkában válassza a **+ Erőforrás létrehozása**lehetőséget.

      ![Új erőforrás létrehozása](./media/how-to-manage-workspace/create-workspace.gif)

1. A keresősáv segítségével keresse meg a **Machine Learning et.**

1. Válassza a **Gépi tanulás lehetőséget.**

1. A **Machine Learning** ablaktáblán válassza a **Létrehozás** gombot a kezdéshez.

1. Adja meg a következő információkat az új munkaterület konfigurálásához:

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában **docs-ws**. A neveknek egyedinek kell lenniük az erőforráscsoportban. Használjon könnyen visszahívható nevet, és különböztetse meg a mások által létrehozott munkaterületeket. A munkaterület neve nem i.
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Az előfizetés valamelyik meglévő erőforráscsoportját használja, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Egy erőforráscsoport egy Azure-megoldás kapcsolódó erőforrásait tartalmazza. Ebben a példában **docs-aml**. 
   Hely | Válassza ki a felhasználókhoz legközelebb eső helyet és a munkaterület létrehozásához szükséges adaterőforrásokat.
   Munkaterületi kiadás | Válassza **az Alapszintű** vagy **a Vállalati lehetőséget.**  Ez a munkaterületi kiadás határozza meg azokat a funkciókat, amelyekhez hozzáféréssel és díjszabással rendelkezik. További információ az [alapszintű és nagyvállalati kiadásajánlatokról.](overview-what-is-azure-ml.md#sku) 

    ![A munkaterület konfigurálása](./media/how-to-manage-workspace/select-edition.png)

1. Ha befejezte a munkaterület konfigurálását, válassza a **Véleményezés + Létrehozás**lehetőséget.
2. Tekintse át a beállításokat, és hajtson végre további módosításokat vagy javításokat. Ha elégedett a beállításokkal, válassza a **Létrehozás gombot.**

   > [!Warning] 
   > A munkaterület felhőbeli létrehozása több percig is eltarthat.

   Amikor a folyamat befejeződött, megjelenik egy sikeres telepítési üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás az erőforrásra**lehetőséget.

### <a name="download-a-configuration-file"></a>Konfigurációs fájl letöltése

1. Ha [számítási példányt](tutorial-1st-experiment-sdk-setup.md#azure)fog létrehozni, hagyja ki ezt a lépést.

1. Ha olyan kódot kíván használni a helyi környezetben, amely erre a munkaterületre hivatkozik, válassza a **config.json letöltése lehetőséget** a munkaterület **Áttekintés szakaszában.**  

   ![Config.json letöltése](./media/how-to-manage-workspace/configure.png)
   
   Helyezze a fájlt a könyvtárstruktúrába a Python-parancsfájlokkal vagy a Jupyter-jegyzetfüzetekkel. Lehet ugyanabban a könyvtárban, egy *.azureml*nevű alkönyvtárban vagy szülőkönyvtárban. Amikor létrehoz egy számítási példányt, ez a fájl hozzáadódik a megfelelő könyvtárba a virtuális gép az Ön számára.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Frissítés az Enterprise edition verzióra

A munkaterületet Basic kiadásról Enterprise kiadásra frissítheti, hogy kihasználja a továbbfejlesztett funkciókat, például az alacsony kódú szolgáltatásokat és a továbbfejlesztett biztonsági funkciókat.

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com)

1. Válassza ki a frissíteni kívánt munkaterületet.

1. Válassza a Lap jobb felső részén található **További információ** lehetőséget.

   [![Munkaterület](./media/how-to-manage-workspace/upgrade.png) frissítése](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. A megjelenő ablakban válassza a **Frissítés** lehetőséget.


> [!IMPORTANT]
> Az Enterprise edition munkaterület nem minősíthető alapszintű kiadási munkaterületre. 

## <a name="find-a-workspace"></a><a name="view"></a>Munkaterület keresése

1. A felső keresőmezőbe írja be a **Machine Learning (Gépi tanulás)** kifejezést.  

1. Válassza a **Gépi tanulás lehetőséget.**

   ![Azure Machine Learning-munkaterület keresése](./media/how-to-manage-workspace/find-workspaces.png)

1. Tekintse át a talált munkaterületek listáját. Szűrhet előfizetés, erőforráscsoportok és helyek alapján.  

1. Jelöljön ki egy munkaterületet a tulajdonságainak megjelenítéséhez.

## <a name="delete-a-workspace"></a>Munkaterület törlése

Használja a törölni kívánt munkaterület tetején található Törlés gombot.

  ![Törlés gomb](./media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Az Azure Machine Learning-munkaterület áthelyezése egy másik előfizetésre, vagy a tulajdonában lévő előfizetés áthelyezése egy új bérlőre, nem támogatott. Ez hibákat okozhat.

### <a name="deleting-the-azure-container-registry"></a>Az Azure Container-beállításjegyzék törlése

Az Azure Machine Learning-munkaterület bizonyos műveletekhez az Azure Container Registry (ACR) szolgáltatást használja. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége van rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>További lépések

A teljes hosszúságú oktatóanyagból megtudhatja, hogyan hozhat létre, taníthat be és helyezhet üzembe modelleket az Azure Machine Learning használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: Vonatmodellek](tutorial-train-models-with-aml.md)
