---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165387"
---
Ebben a lépésben a rendelkezésre állási csoport figyelőjét, amelyek ugyanazon a hálózaton fut-e ügyfél-alkalmazás használatával tesztelheti.

Ügyfélkapcsolat követelményei a következők:

* A figyelővel ügyfélkapcsolatok egy másik felhőalapú szolgáltatás fut, a futtató Always On rendelkezésre állási replikán található gépek kell származnia.
* Ha az AlwaysOn-replikákat külön alhálózatokon vannak, az ügyfelek meg kell adnia *MultisubnetFailover = True* a kapcsolati karakterláncban. Ezt az állapotot eredményez a párhuzamos kapcsolódási kísérletek, a replikákat a különböző alhálózatokon. Ez az eset tartalmazza a régiók közötti Always On rendelkezésre állási csoport központi telepítése.

Egy példa arra, hogy a figyelő csatlakozhat a virtuális gépeket ugyanabban az Azure virtuális hálózatban (de nem tartozik, amelyen replika) az egyik. Ez a teszt elvégzéséhez egyszerűen az SQL Server Management Studióval csatlakozhat a rendelkezésre állási csoport figyelőjének próbál. Egyszerű, hogy futtassa [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), az alábbiak szerint:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Ha a EndpointPort érték *1433-as*, nem kell megadnia azt a hívás. Az előző hívás is feltételezi, hogy az ügyfélszámítógép csatlakozik, ugyanabban a tartományban, és, hogy a hívó rendelkezik engedéllyel az adatbázis Windows-hitelesítés használatával.
> 
> 

Ha a figyelő, mindenképpen győződjön meg arról, hogy az ügyfelek képes csatlakozni a figyelő között lévő feladatátvételi teszteket, hogy a rendelkezésre állási csoport feladatátvételét.

