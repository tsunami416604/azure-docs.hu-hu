---
title: Hozzon létre a Machine Learning Studio munkaterületének |} Microsoft Docs
description: Egy munkaterület létrehozása az Azure Machine Learning Studióban
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 94502cbb0946ad1568cf33716480406b17fd57ac
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833954"
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterület létrehozása és megosztása
Ez a menüben a témakörök, amelyek bemutatják, hogyan állíthatja be a különböző adatok tudományos környezetekben a Cortana Analytics folyamat (nagybetűs) által használt mutató hivatkozásokat tartalmaz.

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Azure Machine Learning Studio használatához meg kell rendelkeznie a Machine Learning munkaterülettel. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Munkaterület létrehozása
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/)

    > [!NOTE]
    > Jelentkezzen be, és hozzon létre egy munkaterület, kell lennie az Azure-előfizetési rendszergazda. 
    >
    > 

2. Kattintson a **+ új**

3. Írja be a keresőmezőbe, **Machine Learning Studio munkaterületének** , és válassza ki a megfelelő elemet. Ezután kattintson a kijelölés **létrehozása** az oldal alján.

4. Adja meg a munkaterület adatokat:

    - A *munkaterületnevet* nem adható meg a befejezési legfeljebb 260 karakter lehet. A neve nem tartalmazhatja a következő karaktereket: `< > * % & : \ ? + /`
    - A *web service-csomag* akkor válasszon (vagy hozzon létre), valamint a társított *tarifacsomag* válassza ki, akkor használatos, ha a munkaterület webszolgáltatások telepítése.

    ![Új munkaterület létrehozása](./media/create-workspace/create-new-workspace.png)

5. Kattintson a **Create** (Létrehozás) gombra.

Ha a munkaterületet van telepítve, a Machine Learning Studióban indíthatja el.

1. Tallózás a Machine Learning Studióba [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. A munkaterület kiválasztása a felső – jobb sarkában található.

    ![Munkaterület kiválasztása](./media/create-workspace/open-workspace.png)

3. Kattintson a **saját kísérletek opcióra**.

    ![Nyissa meg benne](./media/create-workspace/my-experiments.png)

A munkaterület kezelésével kapcsolatos információkért lásd: [kezelése az Azure Machine Learning-munkaterület](manage-workspace.md).
Ha a munkaterület létrehozását probléma adódik, tekintse meg [hibaelhárítási útmutatója: hozzon létre, és kapcsolódjon a Machine Learning-munkaterület](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Egy Azure Machine Learning munkaterülettel megosztása
Egyszer a Machine Learning munkaterület jön létre, felajánlhatja a felhasználóknak a munkaterülethez való fájlmegosztás elérését a munkaterület és minden a kísérletek adatkészletek, jegyzetfüzeteket, stb. Hozzáadhat felhasználókat két szerepkör egyikében:

* **Felhasználói** -munkaterület felhasználó létrehozása, megnyithat, módosíthatja, és kísérleteket, adatkészleteket, stb. a munkaterület törlése.
* **Tulajdonos** - kérhetnek fel egy olyan tulajdonost, és távolítsa el a munkaterületen milyen felhasználói mellett végezhető műveletek.

> [!NOTE]
> A rendszergazdai fiók, amely létrehozza a munkaterület tulajdonos munkaterületként automatikusan hozzáadódik a munkaterületen. Azonban más rendszergazdák vagy a felhasználók az adott előfizetés nem automatikusan hozzáférést kapnak a munkaterület - kell explicit módon hívhat meg.
> 
> 

### <a name="to-share-a-workspace"></a>A munkaterület megosztása

1. Jelentkezzen be a Machine Learning Studióba [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. A bal oldali panelen kattintson a **beállítások**

3. Kattintson a **felhasználók** lap

4. Kattintson a **több felhasználók MEGHÍVÁSA** a lap alján

    ![Studio beállításai](./media/create-workspace/settings.png)

5. Adjon meg egy vagy több e-mail címet. A felhasználók egy érvényes Microsoft-fiókkal vagy (az Azure Active Directory) szervezeti fiók szükséges.

6. Válassza ki, hogy a felhasználók hozzáadása a tulajdonos vagy a felhasználó.

7. Kattintson a **OK** pipa gombra.

Minden felhasználóhoz hozzá jelentkezzen be a megosztott munkaterület kapcsolatos utasításokat tartalmazó e-mailt fog kapni.

> [!NOTE]
> A felhasználók tudnak telepíteni, vagy a munkaterület webszolgáltatások kezelése fel kell egy közreműködő vagy az Azure-előfizetés rendszergazdája. 



