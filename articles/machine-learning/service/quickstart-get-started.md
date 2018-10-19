---
title: 'Rövid útmutató: Gépi tanulási szolgáltatás létrehozása az Azure portálon – az Azure Machine Learning szolgáltatás'
description: Az Azure Portalon létre fog hozni egy Azure Machine Learning szolgáltatási munkaterületet. Az Azure Machine Learning szolgáltatás használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanítás és üzembe helyezés alapjául a felhőben.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b6f0201a36a676e7647b9f5e60bc2df3415b9594
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831330"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning-service"></a>Rövid útmutató: Ismerkedés az Azure Machine Learning szolgáltatással az Azure Portal használatával

Ebben a rövid útmutatóban egy Azure Machine Learning szolgáltatási munkaterületet hoz létre az Azure Portalon. Az Azure Machine Learning szolgáltatás használata során ez a munkaterület szolgál a gépi tanulási modellekkel való kísérletezés, valamint a betanítás és üzembe helyezés alapjául a felhőben. 

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Munkaterület létrehozása az Azure-előfizetésben
* A munkaterület kipróbálása a Python használatával egy Azure Notebookban és az értékek naplózása több iterációban
* A naplózott értékek megtekintése a munkaterületen

Az Ön kényelme érdekében a következő Azure-erőforrások automatikusan hozzá lesznek adva a munkaterülethez, ha elérhetők a régióban: [tárolóregisztrációs adatbázis](https://azure.microsoft.com/services/container-registry/), [tároló](https://azure.microsoft.com/services/storage/), [application insights](https://azure.microsoft.com/services/application-insights/) és [key vault](https://azure.microsoft.com/services/key-vault/).

Az Ön által létrehozott erőforrások előfeltételként is használhatók más Azure Machine Learning szolgáltatási oktatóanyagokban vagy útmutatókban. A többi Azure-szolgáltatáshoz hasonlóan bizonyos erőforrások (pl. A BatchAI fürtmérete), amelyek az Azure Machine Learning szolgáltatással vannak társítva, korlátozva vannak. Olvassa el [ezt](how-to-manage-quotas.md) a cikket az alapértelmezett korlátokról és a kvóta emelésének igényléséről.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="create-a-workspace"></a>Munkaterület létrehozása 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

A munkaterületen kattintson az `Explore your Azure Machine Learning service workspace` parancsra

 ![a munkaterület vizsgálata](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>A munkaterület használata

Most tekintse meg, hogyan segít a munkaterület a gépi tanulási szkriptek felügyeletében. Ebben a szakaszban a következőket végzi el:

* Egy notebook megnyitása az Azure Notebooksban
* Naplózott értékeket létrehozó kód futtatása
* A naplózott értékek megtekintése a munkaterületen

Ez egy példa arra, hogyan segíthet a munkaterület a szkriptben létrehozott információk nyomon követésében. 

### <a name="open-a-notebook"></a>Notebook megnyitása 

Az Azure Notebooks ingyenes felhőalapú platformot nyújt a Jupyter-notebookokhoz, amely előre konfigurálva van az Azure Machine Learning szolgáltatás futtatásához szükséges összes beállítással.  

Kattintson az `Open Azure Notebooks` gombra az első kísérlet kipróbálásához.

 ![Az Azure-notebook elindítása](./media/quickstart-get-started/explore_ws.png)

A bejelentkezés után megnyílik egy új lap, és megjelenik egy `Clone Library` üzenet.  Kattintson a `Clone` gombra.


### <a name="run-the-notebook"></a>A notebook futtatása

Két notebook mellett egy `config.json` fájlt is látni fog.  Ez a konfigurációs fájl az imént létrehozott munkaterülettel kapcsolatban tartalmaz adatokat.  

Kattintson a `01.run-experiment.ipynb` fájlra a notebook megnyitásához.

A cellákat egyenként is futtathatja a `Shift`+`Enter` billentyűkombinációval.  Vagy a `Cells` > `Run All` menüben futtathatja a teljes jegyzetfüzetet is.  Amikor egy cella mellett [*] látható, az fut.  Ha a cella kódja lefutott, egy szám jelenik meg.

Előfordulhat, hogy a rendszer arra kéri, hogy jelentkezzen be.  Másolja az üzenetben lévő kódot, majd kattintson a hivatkozásra, és illessze a kódot az új ablakba.  Ügyeljen rá, hogy ne másolja a kód előtt vagy után lévő szóközt.  Jelentkezzen be ugyanazzal a fiókkal, amelyet az Azure Portalon használt.

 ![bejelentkezés](./media/quickstart-get-started/login.png)

A notebookban a rendszer beolvassa a `config.json` fájlból a második cellát, hogy csatlakozzon a munkaterülethez.
```
ws = Workspace.from_config()
```

A kód harmadik cellája elkezdi a „my-first-experiment” nevű kísérletet.  Ezt a nevet fogja használni a futtatással kapcsolatos információk megkereséséhez a munkaterületen.

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

Figyelje meg a jegyzetfüzet utolsó cellájában a naplófájlba írt értékeket.

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

Ezeket az értékeket a kód futtatása után tekintheti meg a munkaterületen.

## <a name="view-logged-values"></a>Naplózott értékek megtekintése

A notebook összes cellájának befejezése után lépjen vissza a portál oldalára.  

Kattintson a `View Experiments` gombra.

![kísérletek megtekintése](./media/quickstart-get-started/view_exp.png)

Zárja be a `Reports` előugró ablakot.

Kattintson a `my-first-experiment` kísérletre.

Tekintse meg az imént elvégzett futtatás információit.  Görgessen le az oldalon a futtatások táblájának megkereséséhez, és kattintson a futtatás számának hivatkozására.

 ![futtatási előzmények hivatkozása](./media/quickstart-get-started/report.png)

Láthatja a naplózott értékekhez automatikusan létrehozott grafikonokat:

   ![előzmények megtekintése](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Az erőforráscsoportot meg is tarthatja, de egyetlen munkaterületet annak tulajdonságait megjelenítve, majd a Törlés gombra kattintva törölhet.

## <a name="next-steps"></a>További lépések

Létrehozta a modellekkel való kísérletezéshez és azok üzembe helyezéséhez szükséges erőforrásokat. Kódot is futtatott egy notebookban, és megvizsgálta a kód futtatási előzményeit a felhőben lévő munkaterületen.

A munkafolyamattal kapcsolatos mélyebb betekintés érdekében kövesse az Azure Machine Learning modellek betanításával és üzembe helyezésével kapcsolatos oktatóanyagait.  

> [!div class="nextstepaction"]
> [Oktatóanyag: Képbesorolási modell betanítása](tutorial-train-models-with-aml.md)
