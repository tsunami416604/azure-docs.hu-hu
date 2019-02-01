---
title: Munkaterület létrehozása
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
ms.openlocfilehash: 16c67c217c8ef33a360fd479a45317d6c42af494
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486317"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Létrehozhat és megoszthat egy Azure Machine Learning Studio-munkaterület

Azure Machine Learning Studio használatához kell rendelkeznie egy Machine Learning Studio-munkaterület. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.



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
Ha a munkaterület létrehozása során hibát tapasztal, tekintse meg [hibaelhárítási útmutatója: Létrehozás és csatlakozás a Machine Learning-munkaterület](troubleshooting-creating-ml-workspace.md).


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



