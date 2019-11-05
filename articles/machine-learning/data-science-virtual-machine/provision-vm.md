---
title: 'Gyors útmutató: Windows DSVM létrehozása'
description: Azure-beli Data Science Virtual Machine konfigurálása és létrehozása az elemzéshez és a gépi tanuláshoz.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: afc026ccb602538b0749fa294cee763efa3d27d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73483235"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Rövid útmutató: a Windows Data Science Virtual Machine beállítása

Megkezdheti a Windows Data Science Virtual Machineának futtatását.

## <a name="prerequisite"></a>Előfeltétel

Windows Data Science Virtual Machine létrehozásához Azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyenesen az Azure](https://azure.com/free)-t.
Vegye figyelembe, hogy az ingyenes Azure-fiókok nem támogatják a GPU-t használó virtuális gépekhez tartozó SKU-ket.

## <a name="create-your-dsvm"></a>A DSVM létrehozása

DSVM-példány létrehozása:

1. Ha még nem jelentkezett be, lépjen a [Azure Portalba](https://portal.azure.com) , ahol a rendszer kérni fogja, hogy jelentkezzen be az Azure-fiókjába.
1. Keresse meg a virtuális gép listáját az "adatelemzési virtuális gép" beírásával, és válassza a "Data Science Virtual Machine-Windows 2016" lehetőséget.

    ![Windows rendszerű virtuális gépek listája](./media/provision-vm/search-windows.png)

1. Válassza a **Létrehozás** gombot alul.

    [![](media/provision-vm/create-windows.png "Button to create a Windows machine")](media/provision-vm/create-windows-expanded.png#lightbox)

1. A rendszer átirányítja a virtuális gép létrehozása panelre.
   a Windows rendszerű virtuális géphez tartozó ![alapjai lap](./media/provision-vm/review-create-windows.png)

1. Adja meg az **alapok** lapot:
      * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki azt a számítógépet, amelyet a gép létre fog hozni és számláz. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
      * **Erőforráscsoport**: hozzon létre egy új csoportot, vagy használjon egy meglévőt.
      * **Virtuális gép neve**: adja meg a virtuális gép nevét. Így fog megjelenni a Azure Portalban.
      * **Hely**: válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáféréshez ez az adatközpont, amely a legtöbb adattal rendelkezik, vagy a legközelebb áll a fizikai helyhez. További információ az [Azure-régiókról](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Rendszerkép**: hagyja meg az alapértelmezett értéket.
      * **Méret**: az általános számítási feladatokhoz megfelelő méretű automatikus feltöltést kell megadni. További információ a [Windowsos virtuális gépek méretéről az Azure-ban](../../virtual-machines/windows/sizes.md).
      * **Felhasználónév**: adja meg a rendszergazda felhasználónevét. Ezt a felhasználónevet fogja használni a virtuális gépre való bejelentkezéshez, és nem kell megegyeznie az Azure-felhasználónévvel.
      * **Password (jelszó**): adja meg a virtuális gépre való bejelentkezéshez használni kívánt jelszót.    
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. **Felülvizsgálat + létrehozás**
   * Győződjön meg arról, hogy a megadott összes adat helyes. 
   * Kattintson a **Létrehozás** gombra.


> [!NOTE]
> * A virtuális gépen előre betöltött szoftverek esetében nem kell fizetnie a licencelési díjakat. A számítási költségeket a **méret** lépésben kiválasztott kiszolgáló méretéhez kell fizetnie.
> * A kiépítés 10 – 20 percet vesz igénybe. A virtuális gép állapotát a Azure Portal tekintheti meg.

## <a name="access-the-dsvm"></a>A DSVM elérése

A virtuális gép létrehozása és üzembe helyezése után kövesse a felsorolt lépéseket az [Azure-alapú virtuális géphez való kapcsolódáshoz](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Használja a virtuális gép létrehozásának **alapjai** lépésében konfigurált rendszergazdai fiók hitelesítő adatait. 

Készen áll a virtuális gépen telepített és konfigurált eszközök használatának megkezdésére. A **Start** menü csempéi és asztali ikonjai számos eszközt is elérhet.

Emellett DSVM is csatolhat Azure Notebooks a Jupyter notebookok futtatásához a virtuális gépen, és megkerülheti az ingyenes szolgáltatási szintek korlátozásait. További információ: [notebook-projektek kezelése és konfigurálása](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>További lépések

* A **Start** menü megnyitásával tárja fel a DSVM található eszközöket.
* Ismerje meg a Azure Machine Learningt, ha elolvasta [, mi az Azure Machine learning?](../service/overview-what-is-azure-ml.md) , és próbálja ki az [oktatóanyagokat](../index.yml).
* A Fájlkezelőben keresse meg a C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts azokat a mintákat, amelyek az R RevoScaleR-könyvtárat használják, és nagyvállalati szintű adatelemzést támogatnak. 
* Olvassa el a [Data Science Virtual Machineon](https://aka.ms/dsvmtenthings)elvégezhető tíz dolgot.
* Ismerje meg, hogyan hozhat létre rendszeresen teljes körű analitikai megoldásokat a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml)használatával.
* Látogasson el az Azure-ban Azure Machine Learning és kapcsolódó adatszolgáltatásokat használó gépi tanulási és adatelemzési minták [Azure AI Gallery](https://gallery.cortanaintelligence.com) . A katalógushoz a **Start** menüben és a virtuális gép asztalán is elérhető egy ikon.

