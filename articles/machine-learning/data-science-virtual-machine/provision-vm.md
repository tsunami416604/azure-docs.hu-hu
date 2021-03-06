---
title: 'Gyors útmutató: Windows Data Science Virtual Machine létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Azure-beli Data Science Virtual Machine konfigurálása és létrehozása az elemzéshez és a gépi tanuláshoz.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: e876ddc9b4df32cad5bbf15215da5ce2f912bba1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109725"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Rövid útmutató: a Windows Data Science Virtual Machine beállítása

Ismerkedjen meg a Windows Server 2019 Data Science Virtual Machine rendszerrel.

## <a name="prerequisite"></a>Előfeltétel

Windows Data Science Virtual Machine létrehozásához Azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyenesen az Azure](https://azure.com/free)-t.
Lényeges, hogy az ingyenes Azure-fiókok nem támogatják a GPU-alapú virtuálisgép-termékváltozatokat.

## <a name="create-your-dsvm"></a>A DSVM létrehozása

DSVM-példány létrehozása:

1. Ha még nem jelentkezett be, lépjen a [Azure Portalba](https://portal.azure.com) , ahol a rendszer kérni fogja, hogy jelentkezzen be az Azure-fiókjába.
1. Keresse meg a virtuális gép listáját az "adatelemzési virtuális gép" beírásával, és válassza a "Data Science Virtual Machine-Windows 2019" lehetőséget.

1. Válassza a **Létrehozás** gombot alul.

1. A rendszer átirányítja a virtuális gép létrehozása panelre.

1. Adja meg az **alapok** lapot:
      * **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki azt a számítógépet, amelyet a gép létre fog hozni és számláz. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
      * **Erőforráscsoport**: hozzon létre egy új csoportot, vagy használjon egy meglévőt.
      * **Virtuális gép neve**: adja meg a virtuális gép nevét. Így fog megjelenni a Azure Portalban.
      * **Hely**: válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáféréshez ez az adatközpont, amely a legtöbb adattal rendelkezik, vagy a legközelebb áll a fizikai helyhez. További információ az [Azure-régiókról](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Rendszerkép**: hagyja meg az alapértelmezett értéket.
      * **Méret**: az általános számítási feladatokhoz megfelelő méretű automatikus feltöltést kell megadni. További információ a [Windowsos virtuális gépek méretéről az Azure-ban](../../virtual-machines/sizes.md).
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

A virtuális gép létrehozása és üzembe helyezése után kövesse a felsorolt lépéseket az [Azure-alapú virtuális géphez való kapcsolódáshoz](../../marketplace/azure-vm-create-using-approved-base.md). Használja a virtuális gép létrehozásának **alapjai** lépésében konfigurált rendszergazdai fiók hitelesítő adatait. 

Készen áll a virtuális gépen telepített és konfigurált eszközök használatának megkezdésére. A **Start** menü csempéi és asztali ikonjai számos eszközt is elérhet.

<a name="tools"></a>


## <a name="next-steps"></a>Következő lépések

* A **Start** menü megnyitásával tárja fel a DSVM található eszközöket.
* Ismerje meg a Azure Machine Learningt, ha elolvasta [, mi az Azure Machine learning?](../overview-what-is-azure-ml.md) , és próbálja ki az [oktatóanyagokat](../index.yml).
* Olvassa el az [adatelemzési cikk a Windows Data Science Virtual Machine az Azure-ban](./vm-do-ten-things.md) című cikket
