---
title: 'Rövid útmutató: Gépi tanulási szolgáltatás létrehozása az Azure portálon – az Azure Machine Learning szolgáltatás'
description: Az Azure Portalon létrehozhat egy Azure Machine Learning-munkaterületet. Az Azure Machine Learning használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanításuk és üzembe helyezésük alapjául a felhőben.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: 14bd85a23e2630a1cf2a8b5621d669c4c6748168
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376618"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>Rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatással az Azure Portalon

Ebben a rövid útmutatóban egy Azure Machine Learning-munkaterületet fog létrehozni az Azure Portalon. A Machine Learning használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanításuk és üzembe helyezésük alapjául a felhőben. 

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Munkaterület létrehozása az Azure-előfizetésben.
* A munkaterület kipróbálása a Pythonnal egy Azure-notebookban és az értékek naplózása több iterációban.
* A naplózott értékek megtekintése a munkaterületen.

A következő Azure-erőforrásokat a rendszer automatikusan hozzáadja a munkaterületéhez, ha az Ön régiójában rendelkezésre állnak:

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Az Ön által létrehozott erőforrások előfeltételként is használhatók a Machine Learning más szolgáltatási oktatóanyagaiban vagy útmutatóiban. A többi Azure-szolgáltatáshoz hasonlóan a Machine Learninghez társított egyes erőforrások korlátozva vannak. Ilyen például az Azure Batch AI fürtmérete. Az alapértelmezett korlátokról és a kvóta emelésének módjáról [ebben a cikkben](how-to-manage-quotas.md) tájékozódhat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


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

A bejelentkezés után megnyílik egy új lap, és megjelenik egy `Clone Library` üzenet. A következők szerint válasszon: `Clone`


### <a name="run-the-notebook"></a>A notebook futtatása

Két notebook mellett egy `config.json` fájlt is látni fog. Ez a konfigurációs fájl az imént létrehozott munkaterülettel kapcsolatban tartalmaz adatokat.  

Egy notebook megnyitásához válassza a `01.run-experiment.ipynb` elemet.

A cellákat egyenként is futtathatja a `Shift`+`Enter` billentyűkombinációval. A teljes notebook futtatásához válassza a `Cells` > `Run All` menüpontot. A cellák mellett megjelenő csillag [*] azt jelzi, hogy a cella fut. A cella kódjának lezárulása után megjelenik egy szám.

Előfordulhat, hogy a rendszer felszólítja, hogy jelentkezzen be. Másolja ki az üzenetben szereplő kódot. Ezután kattintson a hivatkozásra, és illessze a kódot az új ablakba. Ügyeljen rá, hogy ne másolja a kód előtt vagy után lévő szóközt. Jelentkezzen be ugyanazzal a fiókkal, amelyet az Azure Portalon használt.

 ![Bejelentkezés](./media/quickstart-get-started/login.png)

A notebookban a rendszer beolvassa a `config.json` fájlból a második cellát, hogy csatlakozzon a munkaterülethez.
```
ws = Workspace.from_config()
```

A kód harmadik cellája elkezdi a „my-first-experiment” nevű kísérletet. Ezt a nevet használhatja a futtatással kapcsolatos információk megkereséséhez a munkaterületen.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

A notebook utolsó cellájában figyelje meg a naplófájlba írt értékeket.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Ezeket az értékeket a kód futtatása után tekintheti meg a munkaterületen.

## <a name="view-logged-values"></a>Naplózott értékek megtekintése

A notebook összes cellájának futtatása után lépjen vissza a portál oldalára.  

Válassza a `View Experiments` lehetőséget.

![Kísérletek megtekintése](./media/quickstart-get-started/view_exp.png)

Zárja be a `Reports` előugró ablakot.

Válassza a `my-first-experiment` lehetőséget.

Tekintse meg az imént elvégzett futtatás információit. Görgessen le az oldalon a futtatások táblájának megkereséséhez. Kattintson a futtatás számának hivatkozására.

 ![Futtatási előzmények hivatkozása](./media/quickstart-get-started/report.png)

Láthatja a naplózott értékekhez automatikusan létrehozott grafikonokat.  

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
