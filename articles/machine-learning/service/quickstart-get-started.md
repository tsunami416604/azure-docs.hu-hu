---
title: 'Gyors útmutató: Használja az Azure portal indítása'
titleSuffix: Azure Machine Learning service
description: Ismerkedés az Azure Machine Learning szolgáltatásban. Az Azure portal használatával hozzon létre egy munkaterületet, amely az alapvető blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 00ad774caa0fe0dcfb7f41614ec25e6cc802da10
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103098"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatással az Azure Portalon

Ebben a rövid útmutatóban egy Azure Machine Learning-munkaterületet fog létrehozni az Azure Portalon. A Machine Learning használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanításuk és üzembe helyezésük alapjául a felhőben. Ez a rövid útmutató felhőerőforrásokat használ, és nem igényel telepítést. Ha ehelyett saját Jupyter-notebookkiszolgálót szeretne konfigurálni, tekintse meg az [Azure Machine Learning Pythonnal való használatának első lépéseit bemutató rövid útmutatót](quickstart-create-workspace-with-python.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

Ebben a rövid útmutatóban a következőket hajtja végre:

* Munkaterület létrehozása az Azure-előfizetésben.
* A munkaterület kipróbálása a Pythonnal egy Azure-notebookban és az értékek naplózása több iterációban.
* A naplózott értékek megtekintése a munkaterületen.

A következő Azure-erőforrásokat a rendszer automatikusan hozzáadja a munkaterületéhez, ha az Ön régiójában rendelkezésre állnak:

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Az Ön által létrehozott erőforrások előfeltételként is használhatók a Machine Learning más szolgáltatási oktatóanyagaiban vagy útmutatóiban. Mivel Azure-szolgáltatásokat, nincs korlátok bizonyos erőforrások, például a Machine Learning, társított számítási fürt mérete. Tudjon meg többet [az alapértelmezett korlátok és a kvóta növelése](how-to-manage-quotas.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLfree) a virtuális gép létrehozásának megkezdése előtt.


## <a name="create-a-workspace"></a>Munkaterület létrehozása 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

A munkaterületen válassza az `Explore your Azure Machine Learning service workspace` lehetőséget.

 ![Ismerkedés a munkaterülettel](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>A munkaterület használata

Most tekintse meg, hogyan segít a munkaterület a gépi tanulási szkriptek felügyeletében. Ebben a szakaszban:

* Megnyit egy notebookot az Azure Notebooksban.
* Naplózott értékeket létrehozó kódot futtat.
* A naplózott értékek megtekintése a munkaterületen.

Ez a példa bemutatja, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

### <a name="open-a-notebook"></a>Notebook megnyitása 

Az Azure Notebooks ingyenes felhőalapú platformot nyújt olyan Jupyter-notebookokhoz, amelyek előre konfigurálva vannak a Machine Learning futtatásához szükséges összes beállítással.  

Kattintson az `Open Azure Notebooks` gombra az első kísérlet kipróbálásához.

 ![Az Azure Notebooks megnyitása](./media/quickstart-get-started/explore_ws.png)

A cég vagy az intézmény a bejelentkezéshez [rendszergazdai hozzájárulást](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) írhat elő.

A bejelentkezés után megnyílik egy új lap, és megjelenik egy `Clone Library` üzenet. Válassza a(z) `Clone` lehetőséget.


### <a name="run-the-notebook"></a>A notebook futtatása

Két notebook mellett egy `config.json` fájlt is látni fog. Ez a konfigurációs fájl az imént létrehozott munkaterülettel kapcsolatban tartalmaz adatokat.  

Egy notebook megnyitásához válassza a `01.run-experiment.ipynb` elemet.

A cellákat egyenként is futtathatja a `Shift`+`Enter` billentyűkombinációval. A teljes notebook futtatásához válassza a `Cells` > `Run All` menüpontot. A cellák mellett megjelenő csillag [*] azt jelzi, hogy a cella fut. A cella kódjának lezárulása után megjelenik egy szám. 

A notebookban lévő összes cella futtatásának befejezése után megtekintheti a naplózott értékeket a munkaterületen.

## <a name="view-logged-values"></a>Naplózott értékek megtekintése

A notebook összes cellájának futtatása után lépjen vissza a portál oldalára.  

Válassza a(z) `View Experiments` lehetőséget.

![Kísérletek megtekintése](./media/quickstart-get-started/view_exp.png)

Zárja be a `Reports` előugró ablakot.

Válassza a(z) `my-first-experiment` lehetőséget.

Tekintse meg az imént elvégzett futtatás információit. Görgessen le az oldalon a futtatások táblájának megkereséséhez. Kattintson a futtatás számának hivatkozására.

 ![Futtatási előzmények hivatkozása](./media/quickstart-get-started/report.png)

Láthatja a naplózott értékekhez automatikusan létrehozott grafikonokat. Mindig automatikusan létrejön egy diagram, amikor több értéket naplóz ugyanazzal a névparaméterrel.

   ![Előzmények megtekintése](./media/quickstart-get-started/plots.png)

Mivel a pí hozzávetőleges értékének meghatározására szolgáló kód véletlenszerű értékeket használ, a grafikonokon eltérő értékek jelennek meg.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Azt is teheti, hogy megtartja az erőforráscsoportot, és csak egyetlen munkaterületet töröl. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Létrehozta a modellekkel való kísérletezéshez és azok üzembe helyezéséhez szükséges erőforrásokat. Kódot is futtatott egy notebookban, és áttekintette a kód futtatási előzményeit a felhőben lévő munkaterületen.

A munkafolyamattal kapcsolatos mélyebb betekintés érdekében kövesse a Machine Learning-modellek betanításával és üzembe helyezésével kapcsolatos oktatóanyagokat.  

> [!div class="nextstepaction"]
> [Oktatóanyag: Képbesorolási modell betanítása](tutorial-train-models-with-aml.md)
