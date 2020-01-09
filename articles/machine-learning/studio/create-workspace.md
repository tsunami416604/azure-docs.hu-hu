---
title: Munkaterületek létrehozása
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasszikus) használatához Machine Learning Studio (klasszikus) munkaterületre van szükség. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 91ba4d1f7d32071cce0de1de528abf02982ce7be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427620"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasszikus) munkaterület létrehozása és megosztása

Azure Machine Learning Studio (klasszikus) használatához Machine Learning Studio (klasszikus) munkaterületre van szükség. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.

## <a name="create-a-studio-classic-workspace"></a>Studio (klasszikus) munkaterület létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

    > [!NOTE]
    > A bejelentkezéshez és a Studio (klasszikus) munkaterület létrehozásához Azure-előfizetés rendszergazdájának kell lennie. 
    >
    > 

2. Kattintson az **+ új** elemre

3. A keresőmezőbe írja be a **Machine learning Studio (klasszikus) munkaterületet** , és válassza ki a megfelelő elemet. Ezután válassza a lap alján található **Létrehozás** elemet.

4. Adja meg a munkaterület adatait:

   - A *munkaterület neve* legfeljebb 260 karakter hosszú lehet. A név nem tartalmazhatja a következő karaktereket: `< > * % & : \ ? + /`
   - A *webszolgáltatások* ezen a munkaterületen való üzembe helyezéséhez a kiválasztott (vagy létrehozás) webszolgáltatás-csomagot, valamint a társított *díjszabási szintet* kell használni.

     ![Új Studio (klasszikus) munkaterület létrehozása](./media/create-workspace/create-new-workspace.png)

5. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> A Machine Learning Studio (klasszikus) egy Azure Storage-fiókra támaszkodik, amelyet Ön a munkafolyamatok végrehajtásakor a köztes adattároláshoz biztosít. A munkaterület létrehozása után, ha a Storage-fiókot törölték, vagy ha a hozzáférési kulcsok módosulnak, a munkaterület működése leáll, és a munkaterület összes kísérlete sikertelen lesz.
Ha véletlenül törölte a Storage-fiókot, hozza létre újra a Storage-fiókot ugyanabban a régióban, mint a törölt Storage-fiókot, és szinkronizálja újra a hozzáférési kulcsot. Ha módosította a tárfiók hozzáférési kulcsát, az Azure Portal használatával szinkronizálja újra a hozzáférési kulcsot a munkaterületen.

Miután telepítette a munkaterületet, megnyithatja azt Machine Learning Studio (klasszikus).

1. Keresse meg Machine Learning Studio (klasszikus) at [https://studio.azureml.net/](https://studio.azureml.net/)címen.

2. Válassza ki munkaterületét a jobb felső sarokban.

    ![Munkaterület kiválasztása](./media/create-workspace/open-workspace.png)

3. Kattintson **a saját kísérletek**elemre.

    ![Kísérletek megnyitása](./media/create-workspace/my-experiments.png)

A Studio (klasszikus) munkaterület kezelésével kapcsolatos információkért lásd: [Azure Machine learning Studio (klasszikus) munkaterületek kezelése](manage-workspace.md).
Ha probléma merül fel a munkaterület létrehozásakor, olvassa el a [hibaelhárítási útmutató: Machine learning Studio (klasszikus) munkaterület létrehozása és kapcsolódása](troubleshooting-creating-ml-workspace.md)című témakört.


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasszikus) munkaterület megosztása
Miután létrehozta a Machine Learning Studio (klasszikus) munkaterületet, meghívhatja a felhasználókat a munkaterületre, hogy megosszák a hozzáférést a munkaterülethez, valamint az összes kísérletet, adatkészletet, jegyzetfüzetet stb. A felhasználókat a két szerepkör egyikében adhatja hozzá:

* **Felhasználó** – a munkaterület felhasználója létrehozhat, megnyithat, módosíthat és törölhet kísérleteket, adatkészleteket stb. a munkaterületen.
* **Tulajdonos** – a felhasználók meghívhatják és eltávolíthatják a munkaterületen lévő felhasználókat, a felhasználó által elvégezhető műveletek mellett.

> [!NOTE]
> A munkaterületet létrehozó rendszergazdai fiók automatikusan hozzá lesz adva a munkaterülethez a munkaterület tulajdonosaként. Azonban az adott előfizetéshez tartozó más rendszergazdák vagy felhasználók nem kapnak automatikusan hozzáférést a munkaterülethez – ezeket explicit módon meg kell hívnia.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Studio (klasszikus) munkaterület megosztása

1. Jelentkezzen be Machine Learning Studio (klasszikus) [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. A bal oldali panelen kattintson a **Beállítások** elemre.

3. Kattintson a **felhasználók** lapra

4. Kattintson a lap alján található **további felhasználók meghívása** elemre.

    ![Studio-beállítások](./media/create-workspace/settings.png)

5. Írjon be egy vagy több e-mail-címet. A felhasználóknak érvényes Microsoft-fiók vagy szervezeti fiókra van szükségük (Azure Active Directory).

6. Válassza ki, hogy tulajdonosként vagy felhasználóként kívánja-e hozzáadni a felhasználókat.

7. Kattintson az **OK** pipa gombra.

Minden hozzáadott felhasználó kap egy e-mailt, amely útmutatást ad a megosztott munkaterületre való bejelentkezéshez.

> [!NOTE]
> Ahhoz, hogy a felhasználók képesek legyenek a webszolgáltatások üzembe helyezésére vagy kezelésére ezen a munkaterületen, az Azure-előfizetésben közreműködőnek vagy rendszergazdának kell lennie. 



