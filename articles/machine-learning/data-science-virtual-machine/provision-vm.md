---
title: 'Gyors útmutató: Windows rendszerű DSVM létrehozása'
description: Konfigurálja és a egy adatelemző virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: f543db0240d59cf99f3bd793e059b7985f1ffe22
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170665"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Gyors útmutató: A Windows Data Science Virtual Machine beállítása

Megkezdheti a Windows Data Science Virtual Machineának futtatását.

## <a name="prerequisite"></a>Előfeltétel

Windows Data Science Virtual Machine létrehozásához Azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyenesen az Azure](https://azure.com/free)-t.

## <a name="create-your-dsvm"></a>A DSVM létrehozása

DSVM-példány létrehozása:

1. Ha még nem jelentkezett be, lépjen a [Azure Portalba](https://portal.azure.com) , ahol a rendszer kérni fogja, hogy jelentkezzen be az Azure-fiókjába.
1. Keresse meg a virtuális gép listáját az "adatelemzési virtuális gép" beírásával, és válassza a "Data Science Virtual Machine-Windows 2016" lehetőséget.
    ![Windows rendszerű virtuális gépek listája](./media/provision-vm/search-windows.png)
1. Válassza a **Létrehozás** gombot alul.

   ![Virtuális gépek listázása a Azure Portal a létrehozás gombbal](./media/provision-vm/create-windows.png)

1. A rendszer átirányítja a virtuális gép létrehozása panelre.
   ![A Windows rendszerű virtuális géphez tartozó alapismeretek lap](./media/provision-vm/review-create-windows.png)

1. Adja meg az **alapok** lapot:
      * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki azt a számítógépet, amelyet a gép létrehoz és számláz. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
      * **Erőforráscsoport**: Hozzon létre egy új csoportot, vagy használjon egy meglévőt.
      * **Virtuális gép neve**: Adja meg a virtuális gép nevét. Így fog megjelenni a Azure Portalban.
      * **Hely**: Válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáféréshez ez az adatközpont, amely a legtöbb adattal rendelkezik, vagy a legközelebb áll a fizikai helyhez. További információ az [Azure-régiókról](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Rendszerkép**: Ne módosítsa az alapértelmezett értéket.
      * **Méret**: Az automatikus feltöltésnek az általános számítási feladatokhoz megfelelő méretűnek kell lennie. További információ a [Windowsos virtuális gépek méretéről az Azure-ban](../../virtual-machines/windows/sizes.md).
      * **Felhasználónév**: Adja meg a rendszergazda felhasználónevét. Ezt a felhasználónevet fogja használni a virtuális gépre való bejelentkezéshez, és nem kell megegyeznie az Azure-felhasználónévvel.
      * **Jelszó**: Adja meg azt a jelszót, amelyet a virtuális gépre való bejelentkezéshez használni fog.    
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. **Felülvizsgálat + létrehozás**
   * Győződjön meg arról, hogy helyesen szerepel-e a megadott összes információt. 
   * Kattintson a **Létrehozás** gombra.


> [!NOTE]
> * A virtuális gépen előre betöltött szoftverek esetében nem kell fizetnie a licencelési díjakat. A számítási költségeket a **méret** lépésben kiválasztott kiszolgáló méretéhez kell fizetnie.
> * A kiépítés 10 – 20 percet vesz igénybe. A virtuális gép állapotát a Azure Portal tekintheti meg.

## <a name="access-the-dsvm"></a>A DSVM elérése

A virtuális gép létrehozása és üzembe helyezése után kövesse a felsorolt lépéseket az [Azure-alapú virtuális géphez való kapcsolódáshoz](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Használja a virtuális gép létrehozásának **alapjai** lépésében konfigurált rendszergazdai fiók hitelesítő adatait. 

Készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. A **Start** menü csempéi és asztali ikonjai számos eszközt is elérhet.

Emellett DSVM is csatolhat Azure Notebooks a Jupyter notebookok futtatásához a virtuális gépen, és megkerülheti az ingyenes szolgáltatási szintek korlátozásait. További információ: [notebook-projektek kezelése és konfigurálása](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>További lépések

* A **Start** menü megnyitásával tárja fel a DSVM található eszközöket.
* Ismerkedjen meg a Azure Machine Learning szolgáltatással, olvassa el a [Mi az Azure Machine learning Service?](../service/overview-what-is-azure-ml.md) című témakört, és próbálja ki az [oktatóanyagokat](../index.yml).
* A Fájlkezelőben keresse meg a C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts azokat a mintákat, amelyek az R RevoScaleR-könyvtárat használják, és nagyvállalati szintű adatelemzést támogatnak. 
* Olvassa el a [Data Science Virtual Machineon](https://aka.ms/dsvmtenthings)elvégezhető tíz dolgot.
* Megtudhatja, hogyan teljes körű elemzési megoldásokat rendszeresen használatával hozhat létre a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml).
* Látogasson el a [Azure AI-katalógusban](https://gallery.cortanaintelligence.com) machine learning és a data analytics minták az Azure Machine Learning és a kapcsolódó adatokat használó szolgáltatások az Azure-ban. A katalógushoz a **Start** menüben és a virtuális gép asztalán is elérhető egy ikon.
* Olvassa el a virtuális gép megfelelő [dokumentációját](./reference-windows-vm.md) .

