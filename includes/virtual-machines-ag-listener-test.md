---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179064"
---
Ebben a lépésben tesztelheti a rendelkezésre állási csoport figyelője egy ügyfélalkalmazás, amely ugyanazon a hálózaton fut.

Az ügyfélkapcsolat követelményei a következőkövetelményekkel rendelkeznek:

* A figyelővel létesített ügyfélkapcsolatoknak olyan gépekről kell származniuk, amelyek más felhőszolgáltatásban rendelkeznek, mint az Always On rendelkezésre állási replikák.
* Ha a Mindig bekapcsolva replikák különböző alhálózatokban találhatók, az ügyfeleknek meg kell adniuk a *MultisubnetFailover=True* értéket a kapcsolati karakterláncban. Ez a feltétel párhuzamos kapcsolódási kísérleteket eredményez a különböző alhálózatok replikáira. Ebben a forgatókönyvben egy régióközi Mindig a rendelkezésre állási csoport központi telepítése.

Egy példa az, hogy csatlakozzon a figyelő az egyik virtuális gép ugyanabban az Azure virtuális hálózat (de nem az egyik, amely egy replika található). A teszt végrehajtásának egyszerű módja, ha megpróbálja csatlakoztatni az SQL Server Management Studio-t a rendelkezésre állási csoport figyelőjéhez. Egy másik egyszerű módszer az [SQLCMD.exe futtatása](https://technet.microsoft.com/library/ms162773.aspx)az alábbiak szerint:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Ha az EndpointPort értéke *1433,* akkor nem kell megadnia azt a hívásban. Az előző hívás azt is feltételezi, hogy az ügyfélszámítógép ugyanahhoz a tartományhoz csatlakozik, és hogy a hívó windowsos hitelesítéssel kapott engedélyeket az adatbázishoz.
> 
> 

A figyelő tesztelése során győződjön meg arról, hogy az ügyfelek csatlakozhatnak a figyelő feladatátvételi.

