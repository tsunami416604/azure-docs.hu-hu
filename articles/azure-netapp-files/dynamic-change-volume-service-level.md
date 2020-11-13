---
title: A kötetek szolgáltatási szintjének dinamikus módosítása Azure NetApp Files esetén | Microsoft Docs
description: Leírja, hogyan lehet dinamikusan módosítani egy kötet szolgáltatási szintjét.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: e5219e1c87221ade8da68c21209f41b4d6139be2
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579079"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Kötetek szolgáltatásszintjének dinamikus módosítása

> [!IMPORTANT] 
> * A szolgáltatás nyilvános előzetes regisztrációja továbbra is érvényben marad. 
> * A replikációs cél kötet szolgáltatási szintjének dinamikus módosítása jelenleg nem támogatott.

Megváltoztathatja egy meglévő kötet szolgáltatási szintjét úgy, hogy áthelyezi a kötetet egy másik, a kötethez használni kívánt [szolgáltatási szintre](azure-netapp-files-service-levels.md) . Ennek a kötetnek a helyi szolgáltatási szintjének módosítása nem igényli az adatáttelepítést. Emellett nem befolyásolja a kötethez való hozzáférést is.  

Ez a funkció lehetővé teszi, hogy igény szerint megfeleljen a számítási feladatok igényeinek.  Egy meglévő kötetet úgy módosíthat, hogy magasabb szolgáltatási szintet használjon a jobb teljesítmény érdekében, vagy alacsonyabb szolgáltatási szintet használjon a költség optimalizálásához. Ha például a kötet jelenleg a *standard* szintű szolgáltatási szintet használja, és azt szeretné, hogy a kötet a *prémium* szintű szolgáltatási szintet használja, dinamikusan áthelyezheti a kötetet a *prémium* szintű szolgáltatási szintet használó kapacitási készletre.  

Az a kapacitási készlet, amelyre át szeretné helyezni a kötetet, már léteznie kell. A kapacitási készlet tartalmazhat más köteteket is.  Ha a kötetet egy teljesen új kapacitási készletbe szeretné áthelyezni, a kötet áthelyezése előtt [létre kell hoznia a kapacitás-készletet](azure-netapp-files-set-up-capacity-pool.md) .  

## <a name="considerations"></a>Megfontolandó szempontok

* Miután a kötetet áthelyezte egy másik kapacitási készletbe, már nem fog tudni hozzáférni az előző mennyiségi tevékenység naplóihoz és a mennyiségi metrikához. A kötet új tevékenység-naplókkal és metrikákkal fog kezdődni az új kapacitási készlet alatt.

* Ha egy kötetet magasabb szolgáltatási szint (például a *standard* és a *prémium* vagy *Ultra* szolgáltatási szint) kapacitási készletére helyez át, akkor legalább hét nappal azelőtt meg kell várnia *a kötetet, hogy az* alacsonyabb szolgáltatási szint (például az *Ultra* – *prémium* vagy a *standard* ) kapacitási készletére váltson.  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Kötet áthelyezése másik kapacitási készletbe

1.  A kötetek lapon kattintson a jobb gombbal arra a kötetre, amelynek szolgáltatási szintjét módosítani szeretné. Válassza a **készlet módosítása** lehetőséget.

    ![Kattintson a jobb gombbal a kötetre](../media/azure-netapp-files/right-click-volume.png)

2. A készlet módosítása ablakban válassza ki azt a kapacitási készletet, amelyre át szeretné helyezni a kötetet. 

    ![Készlet módosítása](../media/azure-netapp-files/change-pool.png)

3.  Kattintson az **OK** gombra.


## <a name="next-steps"></a>Következő lépések  

* [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
