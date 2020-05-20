---
title: Jupyter-jegyzetfüzetek futtatása a munkaterületen
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan futtathat egy Jupyter Notebook a munkaterület Azure Machine Learning Studióban való elhagyása nélkül.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 96e37afd8bf7d59eef4a4c0c831f535faa36d34d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681440"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Jupyter-jegyzetfüzetek futtatása a munkaterületen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan futtathat Jupyter-jegyzetfüzeteket közvetlenül a munkaterületen a Azure Machine Learning Studióban. A [Jupyter](https://jupyter.org/) vagy a [JupyterLab](https://jupyterlab.readthedocs.io)elindításához lehetősége van a jegyzetfüzetek szerkesztésére és futtatására is a munkaterület elhagyása nélkül.

Ismerje meg, hogyan teheti meg:

* Jupyter-jegyzetfüzetek létrehozása a munkaterületen
* Kísérlet futtatása jegyzetfüzetből
* A notebook-környezet módosítása
* A jegyzetfüzetek futtatásához használt számítási példányok részleteinek megkeresése

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a>Jegyzetfüzetek létrehozása

A Azure Machine Learning munkaterületen hozzon létre egy új Jupyter-jegyzetfüzetet, és kezdjen el dolgozni. Az újonnan létrehozott jegyzetfüzetet az alapértelmezett munkaterület-tárolóban tárolja a rendszer. Ez a jegyzetfüzet a munkaterülethez hozzáféréssel rendelkező bárkivel megosztható. 

Új jegyzetfüzet létrehozása: 

1. Nyissa meg a munkaterületét [Azure Machine learning Studióban](https://ml.azure.com).
1. A bal oldalon válassza a **jegyzetfüzetek**lehetőséget. 
1. Válassza az **új fájl létrehozása** ikont a **saját fájlok** szakaszban lévő **felhasználói fájlok** listájának megadásához.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Új fájl létrehozása":::

1. Nevezze el a fájlt. 
1. Jupyter Notebook fájlok esetében válassza a **Python notebook** lehetőséget a fájl típusaként.
1. Válassza ki a kívánt könyvtárat.
1. Kattintson a **Létrehozás** gombra.

> [!TIP]
> Szövegfájlokat is létrehozhat.  Válassza ki a kívánt **szöveget** , és adja hozzá a kiterjesztést a névhez (például myfile.py vagy sajat. txt).  

A notebookok oldal tetején található eszközökkel mappákat és fájlokat is feltölthet, beleértve a jegyzetfüzeteket is.  A jegyzetfüzetek és a legtöbb szöveges fájltípus az előnézet szakaszban látható.  A legtöbb fájltípushoz nem érhető el előzetes verzió.

### <a name="clone-samples"></a>Minták klónozása

A munkaterület tartalmaz egy jegyzetfüzeteket tartalmazó **minta** mappát, amely segít az SDK megismerésében, és példaként szolgál a saját gépi tanulási projektjeihez.  Ezeket a jegyzetfüzeteket a munkaterület Storage-tárolójában lévő saját mappájába is klónozott.  

Példaként tekintse [meg az első ml-kísérlet létrehozását ismertető oktatóanyagot](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a>Fájlok használata a git és a saját fájlok verziójában

Az összes git-műveletet egy terminál-ablak használatával érheti el. A rendszer az összes git-fájlt és-mappát a munkaterület fájlrendszerében fogja tárolni.

> [!NOTE]
> A **~/cloudfiles/Code/Users** mappában bárhol hozzáadhatja fájljait és mappáit, így azok az összes Jupyter-környezetben láthatók lesznek.

A terminál elérése:

1. Nyissa meg a munkaterületét [Azure Machine learning Studióban](https://ml.azure.com).
1. A bal oldalon válassza a **jegyzetfüzetek**lehetőséget.
1. Válassza ki a bal oldalon a **felhasználói fájlok** szakaszban található bármelyik jegyzetfüzetet.  Ha nincs jegyzetfüzete, először [hozzon létre egy jegyzetfüzetet](#create)
1. Válasszon ki egy **számítási** célt, vagy hozzon létre egy újat, és várjon, amíg fut.
1. Válassza a **nyitott terminál** ikont.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Terminál megnyitása":::

1. Ha nem látja az ikont, válassza a **...** lehetőséget a számítási cél jobb oldalán, majd válassza a **terminál megnyitása** lehetőséget.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Terminál megnyitása a következőből:...":::


További információ a [git-adattáraknak a munkaterület-fájlrendszerbe való klónozásáról](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Jegyzetfüzetek és egyéb fájlok megosztása

Másolja és illessze be az URL-címet egy jegyzetfüzet vagy fájl megosztásához.  Ehhez az URL-címhez csak a munkaterület más felhasználói férhetnek hozzá.  További információ [a munkaterület hozzáférésének biztosításáról](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Jegyzetfüzet szerkesztése

A jegyzetfüzetek szerkesztéséhez nyissa meg a munkaterület **felhasználói fájlok** szakaszában található bármelyik jegyzetfüzetet. Kattintson a szerkeszteni kívánt cellára. 

Ha egy rendszert futtató számítási példány fut, akkor a kód befejezését is használhatja az [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)használatával, bármely Python-jegyzetfüzetben.

A Jupyter vagy a JupyterLab a jegyzetfüzet eszköztáráról is elindíthatja.  A Azure Machine Learning nem biztosít frissítéseket, és nem javítja ki a Jupyter vagy a JupyterLab hibáit, mivel a Microsoft ügyfélszolgálata határán kívül nyílt forráskódú termékek.

### <a name="useful-keyboard-shortcuts"></a>Hasznos billentyűparancsok

|Billentyűzet  |Művelet  |
|---------|---------|
|SHIFT + ENTER     |  Cella futtatása       |
|CTRL + M (Windows)     |  A lapfülek alátöltésének engedélyezése/letiltása a jegyzetfüzetben.       |
|CTRL + SHIFT + M (Mac & Linux)     |    A lapfülek alátöltésének engedélyezése/letiltása a jegyzetfüzetben.     |
|Tab (ha engedélyezve van a TAB trap) | "\T" karakter hozzáadása (behúzás)
|Tab (ha a TAB trap le van tiltva) | Fókusz módosítása a következő fókuszálható elemre (cella törlése gomb, Futtatás gomb stb.)

## <a name="delete-a-notebook"></a>Jegyzetfüzet törlése

A **mintákat** tartalmazó jegyzetfüzetek *nem* törölhetők.  Ezek a jegyzetfüzetek a stúdió részét képezik, és minden alkalommal frissülnek, amikor új SDK kerül közzétételre.  

A következő módokon *törölheti* a **felhasználói fájlok** jegyzetfüzeteit:

* A Studióban válassza a **...** elemet a mappa vagy fájl végén.  Győződjön meg arról, hogy támogatott böngészőt használ (Microsoft Edge, Chrome vagy Firefox).
* Bármely jegyzetfüzet eszköztárból válassza a [**terminál megnyitása**](#terminal) lehetőséget a számítási példányhoz tartozó terminál ablak eléréséhez.
* A Jupyter vagy a JupyterLab eszközzel.

## <a name="run-an-experiment"></a>Kísérlet futtatása

Egy kísérlet jegyzetfüzetből való futtatásához először egy futó [számítási példányhoz](concept-compute-instance.md)kell csatlakoznia. Ha nem rendelkezik számítási példánnyal, az alábbi lépésekkel hozhat létre egyet: 

1. Válassza ki **+** a jegyzetfüzet eszköztárát. 
2. Nevezze el a számítást, és válassza ki a **virtuális gép méretét**. 
3. Kattintson a **Létrehozás** gombra.
4. A számítási példány automatikusan csatlakozik a jegyzetfüzethez, és most már futtathatja a cellákat.

Csak az Ön által létrehozott számítási példányok láthatók és használhatók.  A **felhasználói fájlokat** a rendszer külön tárolja a virtuális gépen, és a munkaterület összes számítási példánya között meg van osztva.

### <a name="view-logs-and-output"></a>Naplók és kimenet megtekintése

A Futtatás és a naplók előrehaladásának megtekintéséhez [Jegyzetfüzet-minialkalmazások](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) használhatók. A widgetek aszinkron módon frissülnek, és frissítéseket biztosítanak, amíg befejeződik a képzés. A Azure Machine Learning widgeteket a Jupyter és a JupterLab is támogatja.

## <a name="change-the-notebook-environment"></a>A notebook-környezet módosítása

A notebook eszköztár lehetővé teszi, hogy megváltoztassa a környezetet, amelyen a jegyzetfüzet fut.  

Ezek a műveletek nem változtatják meg a jegyzetfüzet állapotát vagy a jegyzetfüzetben lévő változók értékeit:

|Művelet  |Eredmény  |
|---------|---------| --------|
|A kernel leállítása     |  Leállítja a futó cellákat. A cella futtatása automatikusan újraindítja a rendszermagot. |
|Navigáljon egy másik munkaterület szakaszhoz     |     A futó cellák le vannak állítva. |

Ezek a műveletek alaphelyzetbe állítják a jegyzetfüzet állapotát, és a jegyzetfüzetben lévő összes változót visszaállítják.

|Művelet  |Eredmény  |
|---------|---------| --------|
| A kernel módosítása | A jegyzetfüzet új kernelt használ |
| Számítás váltása    |     A jegyzetfüzet automatikusan az új számítást használja. |
| Számítás visszaállítása | Újraindul, amikor egy cellát próbál futtatni |
| Számítás leállítása     |    Egyetlen cella sem fog futni  |
| Jegyzetfüzet megnyitása a Jupyter vagy a JupyterLab     |    A jegyzetfüzet új lapon lett megnyitva.  |

### <a name="add-new-kernels"></a>Új kernelek hozzáadása

A jegyzetfüzet automatikusan megkeresi a csatlakoztatott számítási példányra telepített összes Jupyter-kernelt.  Kernel hozzáadása a számítási példányhoz:

1. A jegyzetfüzet eszköztárán válassza a [**terminál megnyitása**](#terminal) lehetőséget.
1. Hozzon létre egy új környezetet a terminálablak használatával.
1. Aktiválja a környezetet.  Például a létrehozása után `newenv` :

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

A [rendelkezésre álló Jupyter-kernelek](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) bármelyike telepíthető.

### <a name="status-indicators"></a>Állapotjelzők

A **számítási** legördülő lista melletti kijelző megjeleníti az állapotát.  Az állapot a legördülő menüben is látható.  

|Szín |Számítási állapot |
|---------|---------| 
| Zöld | A számítás fut |
| Vörös |A számítás nem sikerült | 
| Fekete | Számítás leállítva |
|  Világoskék |Számítás létrehozása, indítása, újraindítása, beállítás |
|  Szürke |Számítási törlés, Leállítás |

A **kernel** legördülő lista melletti kijelző megjeleníti az állapotát.

|Szín |Kernel állapota |
|---------|---------|
|  Zöld |Kernel csatlakoztatva, tétlen, foglalt|
|  Szürke |A kernel nincs csatlakoztatva |

## <a name="find-compute-details"></a>Számítási részletek keresése 

A számítási példányok részleteit a [Studióban](https://ml.azure.com), a **számítási** oldalon találhatja meg.

## <a name="next-steps"></a>További lépések

* [Az első kísérlet futtatása](tutorial-1st-experiment-sdk-train.md)
* [A file Storage biztonsági mentése pillanatképekkel](../storage/files/storage-snapshots-files.md)
