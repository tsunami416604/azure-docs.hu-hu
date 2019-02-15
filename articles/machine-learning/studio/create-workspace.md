---
title: A Machine Learning Studio-munkaterület létrehozása
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio használatához kell rendelkeznie egy Machine Learning Studio-munkaterület. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: bcd5b377f00ad43ff727c581471aad3ac651bdbb
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270099"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Létrehozhat és megoszthat egy Azure Machine Learning Studio-munkaterület

Azure Machine Learning Studio használatához kell rendelkeznie egy Machine Learning Studio-munkaterület. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.

## <a name="create-a-studio-workspace"></a>Studio-munkaterület létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

    > [!NOTE]
    > Jelentkezzen be, és a Studio-munkaterület létrehozásához szüksége lehet az Azure-előfizetési rendszergazda. 
    >
    > 

2. Kattintson a **+ új**

3. A Keresés mezőbe írja be a **Machine Learning Studio-munkaterület** , és válassza ki a megfelelő elemet. Ezután kattintson a kijelölés **létrehozás** az oldal alján.

4. Adja meg a munkaterület-információk:

    - A *munkaterületnév* lehet, legfeljebb 260 karakternél, nem záró szóközzel. A neve nem tartalmazhatja a következő karaktereket: `< > * % & : \ ? + /`
    - A *webszolgáltatási csomag* , válasszon (vagy hozzon létre), valamint a társított *tarifacsomag* , válassza ki, akkor használatos, ha telepít webszolgáltatások erről a munkaterületről.

    ![Hozzon létre egy új Studio-munkaterülethez](./media/create-workspace/create-new-workspace.png)

5. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> A Machine Learning Studio menti a köztes adatokat, a munkafolyamat végrehajtása során biztosító Azure storage-fiókot használ. Után a munkaterület létrehozását, ha a tárfiókot törlik vagy módosítják a hívóbetűket, a munkaterület fog működni, és a munkaterület összes kísérletek sikertelenek lesznek.
Ha véletlenül törli a storage-fiók, hozza létre újból a tárfiókot, a törölt tárfiókkal megegyező régióban ugyanazzal a névvel, és szinkronizálja újra a hívóbetűt. Ha módosította a tárfiók hozzáférési kulcsát, az Azure Portal használatával szinkronizálja újra a hozzáférési kulcsot a munkaterületen.

A munkaterület üzembe helyezését követően nyissa meg azt a Machine Learning Studióban.

1. Tallózással keresse meg a Machine Learning Studióba [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Válassza ki a munkaterületet a felső – jobb-oldali sarokban.

    ![Munkaterület kiválasztása](./media/create-workspace/open-workspace.png)

3. Kattintson a **kísérleteim**.

    ![Nyissa meg kísérletek](./media/create-workspace/my-experiments.png)

Studio munkaterületét kezelésével kapcsolatos információkért lásd: [egy Azure Machine Learning Studio-munkaterület kezelése](manage-workspace.md).
Ha a munkaterület létrehozása során hibát tapasztal, tekintse meg [hibaelhárítási útmutatója: Létrehozás és csatlakozás egy Machine Learning Studio-munkaterülethez](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Egy Azure Machine Learning Studio-munkaterület megosztása
Miután egy Machine Learning Studio munkaterület jön létre, felajánlhatja a felhasználóknak a munkaterületéhez megosztani a hozzáférést a munkaterület, és minden a kísérletek, adatkészletek, jegyzetfüzeteket stb. Hozzáadhat felhasználókat két szerepkör egyikében:

* **Felhasználói** -munkaterület felhasználó is létrehozni, nyissa meg a, módosításához és törléséhez kísérletek, adatkészletek, stb. a munkaterületen.
* **Tulajdonos** – meghívhatja a tulajdonosa, és felhasználók eltávolítása a felhasználó milyen mellett a munkaterületen teheti meg.

> [!NOTE]
> A rendszergazdai fiók, amely a munkaterületet hoz létre a munkaterületet automatikusan hozzáadja a munkaterület tulajdonosa szerint. Azonban más rendszergazdák vagy a felhasználók, az adott előfizetésben vannak nem automatikusan biztosít hozzáférést a munkaterület - felkínáljuk számukra az explicit módon kell.
> 
> 

### <a name="to-share-a-studio-workspace"></a>Studio-munkaterület megosztása

1. Jelentkezzen be a Machine Learning Studióba [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Kattintson a bal oldali panel **beállításai**

3. Kattintson a **felhasználók** lap

4. Kattintson a **több felhasználó MEGHÍVÁSA** a lap alján

    ![Studio-beállítások](./media/create-workspace/settings.png)

5. Adjon meg egy vagy több e-mail címet. A felhasználók kell egy érvényes Microsoft-fiókjával vagy szervezeti fiókkal (az Azure Active Directory).

6. Válassza ki, hogy a felhasználók hozzáadása tulajdonosa vagy a felhasználó.

7. Kattintson a **OK** pipa gombra.

Minden felhasználó hozzáadhat való jelentkezzen be a megosztott munkaterület-mailt fog kapni.

> [!NOTE]
> Felhasználók tudják, telepítenie és felügyelnie a munkaterület webszolgáltatások hogy kell közreműködőjének vagy rendszergazdájának az Azure-előfizetésében. 



