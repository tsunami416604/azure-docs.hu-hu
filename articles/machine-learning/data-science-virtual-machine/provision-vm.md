---
title: 'Rövid útmutató: Windows adatelemzési virtuális gép létrehozása'
titleSuffix: Azure Data Science Virtual Machine
description: Állítson be és hozzon létre egy adatelemzési és gépi tanulási adatelemzési és gépi tanulási adatelemzési virtuális gépet az Azure-ban.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: afcb676f68e7be9d3ebef11ea2c6876a86bbd062
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281782"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Rövid útmutató: A Windows adatelemzési virtuális gépének beállítása

A Windows Server 2019 Data Science virtuális gépe imitálása.

## <a name="prerequisite"></a>Előfeltétel

Windows data science virtuális gép létrehozásához Azure-előfizetéssel kell rendelkeznie. [Próbálja ki ingyen az Azure-t.](https://azure.com/free)
Kérjük, vegye figyelembe, hogy az ingyenes Azure-fiókok nem támogatják a GPU-kompatibilis virtuálisgép-skus-okat.

## <a name="create-your-dsvm"></a>A DSVM létrehozása

DSVM-példány létrehozása:

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com) ha még nincs bejelentkezve, a rendszer kérheti, hogy jelentkezzen be az Azure-fiókjába.
1. Keresse meg a virtuális gép listáját az "adatelemzési virtuális gép" beírásával és az "Adatelemzési virtuális gép - Windows 2019" lehetőség kiválasztásával.

1. Válassza a **Létrehozás** gombot az alján.

1. Át kell irányítani a "Virtuális gép létrehozása" panelre.

1. Töltse ki az **Alapok** lapot:
      * **Előfizetés:** Ha egynél több előfizetéssel rendelkezik, válassza ki azt, amelyen a gép létrejön és kiszámlázásra kerül. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
      * **Erőforráscsoport**: Hozzon létre egy új csoportot, vagy használjon egy meglévőt.
      * **Virtuális gép neve**: Adja meg a virtuális gép nevét. Így fog megjelenni az Azure Portalon.
      * **Hely**: Válassza ki a legmegfelelőbb adatközpontot. A leggyorsabb hálózati hozzáférés érdekében ez az adatközpont, amely a legtöbb adatot, vagy a legközelebb a fizikai helyét. További információ az [Azure-régiókról.](https://azure.microsoft.com/global-infrastructure/regions/)
      * **Kép**: Hagyja meg az alapértelmezett értéket.
      * **Méret**: Ez automatikusan feltölti az általános számítási feladatoknak megfelelő méretet. További információ a [Windows virtuális gépek azure-beli méreteiről.](../../virtual-machines/windows/sizes.md)
      * **Felhasználónév**: Adja meg a rendszergazda felhasználónevét. Ez az a felhasználónév, amelyet a virtuális gépbe való bejelentkezéshez használ, és nem kell megegyeznie az Azure-felhasználónevével.
      * **Jelszó**: Adja meg a virtuális gépre való bejelentkezéshez használt jelszót.    
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. **Véleményezés+létrehozás**
   * Ellenőrizze, hogy minden megadott információ helyes-e. 
   * Kattintson a **Létrehozás** gombra.


> [!NOTE]
> * Nem kell licencdíjat fizetni a virtuális gépen előre betöltött szoftverért. A Méret lépésben kiválasztott kiszolgálóméret számítási költségét kell **fizetnie.**
> * A kiépítés 10-20 percet vesz igénybe. Megtekintheti a virtuális gép állapotát az Azure Portalon.

## <a name="access-the-dsvm"></a>A DSVM elérése

A virtuális gép létrehozása és kiépítése után kövesse a felsorolt lépéseket az [Azure-alapú virtuális géphez való csatlakozáshoz.](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md) Használja a felügyeleti fiók hitelesítő adatait, hogy a konfigurált **az Alapokok** lépés a virtuális gép létrehozása. 

Készen áll a virtuális gépen telepített és konfigurált eszközök használatára. Számos eszköz elérhető a **Start** menü csempéin és asztali ikonokon keresztül.

DSVM-et is csatolhat az Azure Notebooks-hoz a Jupyter-jegyzetfüzetek futtatásához a virtuális gépen, és megkerülheti az ingyenes szolgáltatási szint korlátait. További információt a [Jegyzetfüzet-projektek kezelése és konfigurálása című témakörben](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects)talál.

<a name="tools"></a>


## <a name="next-steps"></a>További lépések

* Fedezze fel a DSVM eszközeit a **Start** menü megnyitásával.
* Ismerje meg az Azure Machine Learninget a [Mi az Azure Machine Learning](../overview-what-is-azure-ml.md) című program elolvasásával és az [oktatóanyagok kipróbálásával.](../index.yml)
* Olvassa el a [cikket Tíz dolog, amit tehetünk az adattudomány virtuális gép](https://aka.ms/dsvmtenthings).
* Látogasson el az [Azure AI-galériába,](https://gallery.cortanaintelligence.com) ahol az Azure Machine Learninget és az Azure-ban kapcsolódó adatszolgáltatásokat használó gépi tanulási és adatelemzési mintákat. A Galéria ikonját is biztosítottuk a **Start** menüben és a virtuális gép asztalán.

