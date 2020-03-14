---
title: 'Gyors útmutató: Windows Data Science Virtual Machine létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurálja és a egy adatelemző virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 1fdf8eae5d19a2d43499d1984f4dea834d8a61d8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241107"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Rövid útmutató: a Windows Data Science Virtual Machine beállítása

Ismerkedjen meg a Windows Server 2019 Data Science Virtual Machine rendszerrel.

## <a name="prerequisite"></a>Előfeltétel

Windows Data Science Virtual Machine létrehozásához Azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyenesen az Azure](https://azure.com/free)-t.
Vegye figyelembe, hogy az ingyenes Azure-fiókok nem támogatják a GPU-t használó virtuális gépekhez tartozó SKU-ket.

## <a name="create-your-dsvm"></a>A DSVM létrehozása

DSVM-példány létrehozása:

1. Ha még nem jelentkezett be, lépjen a [Azure Portalba](https://portal.azure.com) , ahol a rendszer kérni fogja, hogy jelentkezzen be az Azure-fiókjába.
1. Keresse meg a virtuális gép listáját az "adatelemzési virtuális gép" beírásával, és válassza a "Data Science Virtual Machine-Win 2019 (előzetes verzió)" lehetőséget.

1. Válassza a **Létrehozás** gombot alul.

1. A rendszer átirányítja a virtuális gép létrehozása panelre.

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
* Ismerje meg a Azure Machine Learningt, ha elolvasta [, mi az Azure Machine learning?](../overview-what-is-azure-ml.md) , és próbálja ki az [oktatóanyagokat](../index.yml).
* Olvassa el a [Data Science Virtual Machineon](https://aka.ms/dsvmtenthings)elvégezhető tíz dolgot.
* Látogasson el az Azure-ban Azure Machine Learning és kapcsolódó adatszolgáltatásokat használó gépi tanulási és adatelemzési minták [Azure AI Gallery](https://gallery.cortanaintelligence.com) . A katalógushoz a **Start** menüben és a virtuális gép asztalán is elérhető egy ikon.

