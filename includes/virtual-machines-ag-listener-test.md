---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2689e81b089147dfc913fb119e0a499d60574b60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050216"
---
Ebben a lépésben a rendelkezésre állási csoport figyelőjét egy ugyanazon a hálózaton futó ügyfélalkalmazás használatával teszteli.

Az Ügyfélkapcsolat az alábbi követelményekkel rendelkezik:

* A figyelőhöz tartozó ügyfélkapcsolatoknak olyan gépekről kell származniuk, amelyek egy másik felhőalapú szolgáltatásban találhatók, mint az Always On rendelkezésre állási replikák.
* Ha az Always On replikák különböző alhálózatokban találhatók, az ügyfeleknek a *MultisubnetFailover = True* értéket kell megadniuk a kapcsolatok karakterláncában. Ez az állapot párhuzamos kapcsolódási kísérletet eredményez a különböző alhálózatokban lévő replikák esetében. Ez a forgatókönyv több régióra kiterjedő always on rendelkezésre állási csoport központi telepítését is magában foglalja.

Az egyik példa a figyelőhöz való kapcsolódás egy ugyanazon Azure-beli virtuális hálózatban lévő egyik virtuális gépről (de nem egy replikát működtető). A teszt elvégzésének egyszerű módja, ha megpróbál csatlakozni SQL Server Management Studio a rendelkezésre állási csoport figyelőhöz. Egy másik egyszerű módszer a [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)futtatása, a következőképpen:

```console
sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15
```

> [!NOTE]
> Ha a EndpointPort értéke *1433*, akkor nem kell megadnia azt a hívásban. Az előző hívás azt is feltételezi, hogy az ügyfélszámítógép ugyanahhoz a tartományhoz csatlakozik, és a hívó a Windows-hitelesítés használatával engedélyt kapott az adatbázisra.
> 
> 

A figyelő tesztelésekor ügyeljen arra, hogy a rendelkezésre állási csoport feladatátvétele után ellenőrizze, hogy az ügyfelek csatlakozhatnak-e a figyelőhöz a feladatátvételek között.

