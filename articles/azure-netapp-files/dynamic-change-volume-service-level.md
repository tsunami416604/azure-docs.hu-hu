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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: bd28f949d35d38c9e64af7ff4196aa1754fbc37a
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172606"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Kötet szolgáltatási szintjének dinamikus módosítása

Megváltoztathatja egy meglévő kötet szolgáltatási szintjét úgy, hogy áthelyezi a kötetet egy másik, a kötethez használni kívánt [szolgáltatási szintre](azure-netapp-files-service-levels.md) . Ennek a kötetnek a helyi szolgáltatási szintjének módosítása nem igényli az adatáttelepítést. Emellett nem befolyásolja a kötethez való hozzáférést is.  

> [!IMPORTANT] 
> Ennek a funkciónak a használata a Whitelisting szolgáltatást igényli. E-mail küldése az anffeedback@microsoft.com előfizetés-azonosítóval a szolgáltatás igényléséhez.

Ez a funkció lehetővé teszi, hogy igény szerint megfeleljen a számítási feladatok igényeinek.  Egy meglévő kötetet úgy módosíthat, hogy magasabb szolgáltatási szintet használjon a jobb teljesítmény érdekében, vagy alacsonyabb szolgáltatási szintet használjon a költség optimalizálásához. Ha például a kötet jelenleg a *standard* szintű szolgáltatási szintet használja, és azt szeretné, hogy a kötet a *prémium* szintű szolgáltatási szintet használja, dinamikusan áthelyezheti a kötetet a *prémium* szintű szolgáltatási szintet használó kapacitási készletre.  

Az a kapacitási készlet, amelyre át szeretné helyezni a kötetet, már léteznie kell. A kapacitási készlet tartalmazhat más köteteket is.  Ha a kötetet egy teljesen új kapacitási készletbe szeretné áthelyezni, a kötet áthelyezése előtt [létre kell hoznia a kapacitás-készletet](azure-netapp-files-set-up-capacity-pool.md) .  

## <a name="considerations"></a>Megfontolandó szempontok

* Miután a kötetet áthelyezte egy másik kapacitási készletbe, már nem fog tudni hozzáférni az előző mennyiségi tevékenység naplóihoz és a mennyiségi metrikához. A kötet új tevékenység-naplókkal és metrikákkal fog kezdődni az új kapacitási készlet alatt.

* Ha egy kötetet magasabb szolgáltatási szint (például a *standard* és a *prémium* vagy *Ultra* szolgáltatási szint) kapacitási készletére helyez át, akkor legalább hét nappal azelőtt meg kell várnia, hogy a kötetet egy alacsonyabb szolgáltatási szint kapacitási készletére helyezze át (például az *Ultra* -ról a *prémium* vagy a *standard*szintre).  
Ez a várakozási időszak nem érvényes, ha a kötetet egy olyan kapacitási készletbe helyezi, amely azonos szolgáltatási szinttel vagy alacsonyabb szolgáltatási szinttel rendelkezik.

## <a name="steps"></a>Lépések

1.  A kötetek lapon kattintson a jobb gombbal arra a kötetre, amelynek szolgáltatási szintjét módosítani szeretné. Válassza a **készlet módosítása**lehetőséget.

    ![Kattintson a jobb gombbal a kötetre](../media/azure-netapp-files/right-click-volume.png)

2. A készlet módosítása ablakban válassza ki azt a kapacitási készletet, amelyre át szeretné helyezni a kötetet. 

    ![Készlet módosítása](../media/azure-netapp-files/change-pool.png)

3.  Kattintson az **OK** gombra.


## <a name="next-steps"></a>További lépések  

* [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
