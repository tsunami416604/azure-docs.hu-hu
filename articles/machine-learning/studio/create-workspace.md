---
title: Egy munkaterület létrehozása – Azure Machine Learning Studióban |} A Microsoft Docs
description: Azure Machine Learning Studio használatához kell rendelkeznie egy Machine Learning Studio-munkaterület. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.
services: machine-learning
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: fc201b91ba3e2e7ba0f880445542333e58ed4a8c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079823"
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterület létrehozása és megosztása

Azure Machine Learning Studio használatához kell rendelkeznie egy Machine Learning Studio-munkaterület. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Munkaterület létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

    > [!NOTE]
    > Jelentkezzen be, és hozzon létre egy munkaterületet, szüksége lehet az Azure-előfizetési rendszergazda. 
    >
    > 

2. Kattintson a **+ új**

3. A Keresés mezőbe írja be a **Machine Learning Studio-munkaterület** , és válassza ki a megfelelő elemet. Ezután kattintson a kijelölés **létrehozás** az oldal alján.

4. Adja meg a munkaterület-információk:

    - A *munkaterületnév* lehet, legfeljebb 260 karakternél, nem záró szóközzel. A neve nem tartalmazhatja a következő karaktereket: `< > * % & : \ ? + /`
    - A *webszolgáltatási csomag* , válasszon (vagy hozzon létre), valamint a társított *tarifacsomag* , válassza ki, akkor használatos, ha telepít webszolgáltatások erről a munkaterületről.

    ![Új munkaterület létrehozása](./media/create-workspace/create-new-workspace.png)

5. Kattintson a **Create** (Létrehozás) gombra.

A munkaterület üzembe helyezését követően nyissa meg azt a Machine Learning Studióban.

1. Tallózással keresse meg a Machine Learning Studióba [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Válassza ki a munkaterületet a felső – jobb-oldali sarokban.

    ![Munkaterület kiválasztása](./media/create-workspace/open-workspace.png)

3. Kattintson a **kísérleteim**.

    ![Nyissa meg kísérletek](./media/create-workspace/my-experiments.png)

A munkaterület kezelésével kapcsolatos információkért lásd: [kezelése az Azure Machine Learning-munkaterület](manage-workspace.md).
Ha a munkaterület létrehozása során hibát tapasztal, tekintse meg [hibaelhárítási útmutatója: létrehozása és csatlakozás egy Machine Learning-munkaterületet](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Az Azure Machine Learning-munkaterület megosztása
Miután egy Machine Learning munkaterület létrehozása, felajánlhatja a felhasználóknak a munkaterületéhez megosztani a hozzáférést a munkaterület, és minden a kísérletek, adatkészletek, jegyzetfüzeteket stb. Hozzáadhat felhasználókat két szerepkör egyikében:

* **Felhasználói** -munkaterület felhasználó is létrehozni, nyissa meg a, módosításához és törléséhez kísérletek, adatkészletek, stb. a munkaterületen.
* **Tulajdonos** – meghívhatja a tulajdonosa, és felhasználók eltávolítása a felhasználó milyen mellett a munkaterületen teheti meg.

> [!NOTE]
> A rendszergazdai fiók, amely a munkaterületet hoz létre a munkaterületet automatikusan hozzáadja a munkaterület tulajdonosa szerint. Azonban más rendszergazdák vagy a felhasználók, az adott előfizetésben vannak nem automatikusan biztosít hozzáférést a munkaterület - felkínáljuk számukra az explicit módon kell.
> 
> 

### <a name="to-share-a-workspace"></a>A munkaterület megosztása

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



