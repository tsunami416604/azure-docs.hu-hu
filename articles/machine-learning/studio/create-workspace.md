---
title: Munkaterület létrehozása
titleSuffix: ML Studio (classic) - Azure
description: Az Azure Machine Learning Studio (klasszikus) használatához machine learning-stúdió (klasszikus) munkaterülettel kell rendelkeznie. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204460"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasszikus) munkaterület létrehozása és megosztása

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning Studio (klasszikus) használatához machine learning-stúdió (klasszikus) munkaterülettel kell rendelkeznie. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.

## <a name="create-a-studio-classic-workspace"></a>Stúdió (klasszikus) munkaterület létrehozása

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com/)

    > [!NOTE]
    > A bejelentkezéshez és a Studio (klasszikus) munkaterület létrehozásához Azure-előfizetés-rendszergazdának kell lennie. 
    >
    > 

2. Kattintson **a +Új gombra**

3. A keresőmezőbe írja be a **Machine Learning Studio (klasszikus) munkaterületet,** és jelölje ki a megfelelő elemet. Ezután kattintson a lap alján található **Létrehozás** gombra.

4. Adja meg a munkaterület adatait:

   - A *munkaterület neve* legfeljebb 260 karakter lehet, és nem ér véget szóközben. A név nem tartalmazhat ilyen karaktereket:`< > * % & : \ ? + /`
   - A kiválasztott (vagy létrehozott) *webszolgáltatási csomag,* valamint a kiválasztott társított *tarifacsomag,* akkor használatos, ha webszolgáltatásokat telepít erről a munkaterületről.

     ![Új Studio (klasszikus) munkaterület létrehozása](./media/create-workspace/create-new-workspace.png)

5. Kattintson **a Létrehozás gombra.**

> [!NOTE]
> Machine Learning Studio (klasszikus) támaszkodik egy Azure storage-fiók, amely biztosítja a köztes adatok mentése során a munkafolyamat végrehajtása. A munkaterület létrehozása után, ha a tárfiókot törlik, vagy ha a hozzáférési kulcsok módosulnak, a munkaterület leáll, és a munkaterületen lévő összes kísérlet sikertelen lesz.
Ha véletlenül törli a tárfiókot, hozza létre újra a tárfiókot ugyanazzal a névvel, mint a törölt tárfiókot, és szinkronizálja újra a hozzáférési kulcsot. Ha módosította a tárfiók hozzáférési kulcsát, az Azure Portal használatával szinkronizálja újra a hozzáférési kulcsot a munkaterületen.

A munkaterület üzembe helyezése után megnyithatja a Machine Learning Studio (klasszikus).

1. Keresse meg a Machine Learning [https://studio.azureml.net/](https://studio.azureml.net/)Studio (klasszikus) a .

2. Válassza ki munkaterületét a jobb felső sarokban.

    ![Munkaterület kiválasztása](./media/create-workspace/open-workspace.png)

3. Kattintson **a kísérleteimre.**

    ![Kísérletek megnyitása](./media/create-workspace/my-experiments.png)

A Studio (klasszikus) munkaterület kezeléséről az [Azure Machine Learning Studio (klasszikus) munkaterület kezelése](manage-workspace.md)című témakörben talál további információt.
Ha problémát tapasztal a munkaterület létrehozásakor, olvassa el a [Hibaelhárítási útmutató: Gépi tanulási stúdió (klasszikus) munkaterület létrehozása és csatlakoztatása című témakört.](troubleshooting-creating-ml-workspace.md)


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasszikus) munkaterület megosztása
A Machine Learning Studio (klasszikus) munkaterület létrehozása után meghívhat felhasználókat a munkaterületre, hogy megosszák a munkaterülethez és annak minden kísérletéhez, adatkészletéhez, jegyzetfüzetéhez stb. Két szerepkör egyikében adhat hozzá felhasználókat:

* **Felhasználó** – A munkaterület-felhasználók kísérleteket, adatkészleteket stb.
* **Tulajdonos** – A tulajdonos meghívhat és eltávolíthat felhasználókat a munkaterületen, a felhasználó által megtehető adatokon kívül.

> [!NOTE]
> A munkaterületet létrehozó rendszergazdai fiók automatikusan hozzáadódik a munkaterülethez munkaterület-tulajdonosként. Az előfizetés más rendszergazdái vagy felhasználói azonban nem kapnak automatikusan hozzáférést a munkaterülethez – explicit módon meg kell hívnia őket.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Stúdió (klasszikus) munkaterület megosztása

1. Bejelentkezés a Machine Learning Studio-ba (klasszikus)[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. A bal oldali panelen kattintson a **BEÁLLÍTÁSOK**

3. Kattintson a **FELHASZNÁLÓK** fülre

4. Kattintson **a További felhasználók meghívása** gombra az oldal alján

    ![Stúdió beállításai](./media/create-workspace/settings.png)

5. Adjon meg egy vagy több e-mail címet. A felhasználóknak érvényes Microsoft-fiókra vagy szervezeti fiókra van szükségük (az Azure Active Directoryból).

6. Válassza ki, hogy a felhasználókat tulajdonosként vagy felhasználóként kívánja-e hozzáadni.

7. Kattintson az **OK** pipa gombra.

Minden hozzáadott felhasználó kap egy e-mailt a megosztott munkaterületre való bejelentkezéssel kapcsolatos utasításokkal.

> [!NOTE]
> Ahhoz, hogy a felhasználók ezen a munkaterületen telepíthessék vagy kezelhessék a webszolgáltatásokat, közreműködőknek vagy rendszergazdának kell lenniük az Azure-előfizetésben. 



