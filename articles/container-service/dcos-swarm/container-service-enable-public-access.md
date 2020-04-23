---
title: ELAVULT Az Azure DC/OS Container app elérésének engedélyezése
description: A DC/OS-tárolók nyilvános hozzáférésének engedélyezése Azure Container Serviceban.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61457328"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>ELAVULT Azure Container Service alkalmazáshoz való nyilvános hozzáférés engedélyezése

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Az ACS [nyilvános ügynök készletében](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) lévő összes DC/os-tároló automatikusan elérhetővé válnak az interneten. Alapértelmezés szerint a **80**-es, **443**-as és **8080** -as portok meg vannak nyitva, és az ilyen portokat figyelő bármely (nyilvános) tároló elérhető. Ez a cikk bemutatja, hogyan nyithat meg több portot az alkalmazásaihoz Azure Container Serviceban.

## <a name="open-a-port-portal"></a>Port megnyitása (portál)
Először meg kell nyitnia a kívánt portot.

1. Jelentkezzen be a portálra.
2. Keresse meg azt az erőforráscsoportot, amelyet központilag telepített a Azure Container Service.
3. Válassza ki az ügynök Load balancert (amely a következőhöz hasonló: **XXXX-Agent-LB-XXXX**).
   
    ![Azure Container Service terheléselosztó](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kattintson a mintavételek **, majd a** **Hozzáadás**elemre.
   
    ![Azure Container Service terheléselosztó-tesztek](./media/container-service-enable-public-access/add-probe.png)
5. Töltse ki a mintavételi űrlapot, és kattintson **az OK**gombra.
   
   | Mező | Leírás |
   | --- | --- |
   | Name (Név) |A mintavétel leíró neve. |
   | Port |A vizsgálandó tároló portja. |
   | Útvonal |(HTTP-módban) A mintavételhez a relatív webhely elérési útja. A HTTPS nem támogatott. |
   | Intervallum |A mintavételi kísérletek közötti időtartam másodpercben kifejezve. |
   | Nem kifogástalan állapot küszöbértéke |Az egymást követő mintavételi kísérletek száma, mielőtt a tárolót nem megfelelő állapotba venné. |
6. Vissza az ügynök terheléselosztó tulajdonságainál kattintson a terheléselosztási **szabályok** elemre, majd a **Hozzáadás**gombra.
   
    ![Az Azure Container Service terheléselosztó szabályai](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Töltse ki a terheléselosztó űrlapot, és kattintson **az OK**gombra.
   
   | Mező | Leírás |
   | --- | --- |
   | Name (Név) |A terheléselosztó leíró neve. |
   | Port |A nyilvános bejövő port. |
   | Háttér-port |A tároló belső nyilvános portja, amelybe irányítja a forgalmat. |
   | A háttérkészlet |A készletben található tárolók a terheléselosztó célját fogják használni. |
   | Mintavétel |Az a mintavétel, amellyel megállapítható, hogy a **háttér-készletben** lévő cél kifogástalan állapotú-e. |
   | Munkamenet-állandóság |Meghatározza, hogy a rendszer hogyan kezelje az ügyfelek forgalmát a munkamenet időtartama alatt.<br><br>**Nincs**: az azonos ügyféltől származó egymást követő kérelmek bármelyik tárolóban kezelhetők.<br>**Ügyfél IP-** címe: ugyanazon ügyfél IP-címéről érkező egymást követő kérelmeket ugyanaz a tároló kezeli.<br>**Ügyfél IP-címe és protokollja**: ugyanazon ügyfél IP-címéről és protokoll-kombinációból származó egymást követő kérelmeket ugyanaz a tároló kezeli. |
   | Üresjárat időkorlátja |(Csak TCP) Percek alatt a TCP/HTTP-ügyfél nyitva tartása a *Keep-Alive* üzenetekre való támaszkodás nélkül. |

## <a name="add-a-security-rule-portal"></a>Biztonsági szabály hozzáadása (portál)
Ezután hozzá kell adnia egy biztonsági szabályt, amely átirányítja a forgalmat a megnyitott portról a tűzfalon keresztül.

1. Jelentkezzen be a portálra.
2. Keresse meg azt az erőforráscsoportot, amelyet központilag telepített a Azure Container Service.
3. Válassza ki a **nyilvános** ügynök hálózati biztonsági csoportot (amely a következőhöz hasonló: **XXXX-Agent-Public-NSG-XXXX**).
   
    ![Azure Container Service hálózati biztonsági csoport](./media/container-service-enable-public-access/agent-nsg.png)
4. Válassza a **bejövő biztonsági szabályok** lehetőséget, majd **adja hozzá**a elemet.
   
    ![Azure Container Service hálózati biztonsági csoport szabályai](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Töltse ki a tűzfalszabály a nyilvános port engedélyezéséhez, majd kattintson **az OK**gombra.
   
   | Mező | Leírás |
   | --- | --- |
   | Name (Név) |A tűzfalszabály leíró neve. |
   | Prioritás |A szabály prioritási rangsora. Minél alacsonyabb a szám, annál magasabb a prioritás. |
   | Forrás |Korlátozza a bejövő IP-címtartományt a szabály által engedélyezett vagy letiltva. Ha **nem** szeretne korlátozást megadni, használja a következőt:. |
   | Szolgáltatás |Válassza ki az előre definiált szolgáltatások készletét, amelyre ez a biztonsági szabály vonatkozik. Más esetben **Egyéni** használatával hozza létre a sajátját. |
   | Protocol (Protokoll) |A forgalom korlátozása **TCP** vagy **UDP**alapján. Ha **nem** szeretne korlátozást megadni, használja a következőt:. |
   | Porttartomány |Ha **Service** a szolgáltatás **Egyéni**, a szabály által érintett portok tartományát adja meg. Használhat egyetlen portot, például az **80**-et vagy egy olyan tartományt, mint a **1024-1500**. |
   | Műveletek |Engedélyezheti vagy megtagadhatja a feltételeket teljesítő forgalmat. |

## <a name="next-steps"></a>További lépések
Ismerje meg a [nyilvános és a privát DC/os-ügynökök](container-service-dcos-agents.md)közötti különbséget.

További információ a [DC/os-tárolók kezeléséről](container-service-mesos-marathon-ui.md).

