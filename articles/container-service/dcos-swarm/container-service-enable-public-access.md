---
title: (ELAVULT) Hozzáférés engedélyezése az Azure DC/OS tárolóalkalmazáshoz
description: Hogyan engedélyezheti a nyilvános hozzáférést a DC/os tárolókhoz az Azure Container Service-ben.
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
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(ELAVULT) Nyilvános hozzáférés engedélyezése egy Azure Container Service-alkalmazáshoz

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Az ACS [nyilvános ügynökkészletében](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) lévő tartományvezérlő/operációs rendszer tárolói automatikusan elérhetővé lesznek téve az internetnek. Alapértelmezés szerint a **80,** **443**, **8080** portok megnyílnak, és az adott portokon figyelő (nyilvános) tárolók elérhetők. Ez a cikk bemutatja, hogyan nyithat meg további portokat az alkalmazásokhoz az Azure Container Service-ben.

## <a name="open-a-port-portal"></a>Port megnyitása (portál)
Először is meg kell nyitnunk a kikötőt, amit akarunk.

1. Jelentkezzen be a portálra.
2. Keresse meg azt az erőforráscsoportot, amelybe az Azure Container Service-t telepítette.
3. Válassza ki az ügynök terheléselosztóját (amely az **XXXX-agent-lb-XXXX-hez**hasonló).
   
    ![Az Azure container szolgáltatás terheléselosztója](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kattintson **a Mintavételek,** majd a **Hozzáadás gombra.**
   
    ![Az Azure container service load balancer mintavételei](./media/container-service-enable-public-access/add-probe.png)
5. Töltse ki a mintavételi űrlapot, és kattintson **az OK**gombra.
   
   | Mező | Leírás |
   | --- | --- |
   | Név |A szonda leíró neve. |
   | Port |A vizsgálandó tartály portja. |
   | Útvonal |(HTTP módban) A mintavételhez vezető relatív webhely elérési útja. A HTTPS nem támogatott. |
   | Intervallum |A mintavételi kísérletek közötti idő másodpercben. |
   | Nem kifogástalan állapot küszöbértéke |Egymást követő mintavételi kísérletek száma, mielőtt a tároló tanuskodik. |
6. Az ügynök terheléselosztótulajdonságainál kattintson a **Terheléselosztási szabályok** elemre, majd **a Hozzáadás gombra.**
   
    ![Az Azure container szolgáltatás terheléselosztószabályai](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Töltse ki a terheléselosztó űrlapot, és kattintson az **OK**gombra.
   
   | Mező | Leírás |
   | --- | --- |
   | Név |A terheléselosztó leíró neve. |
   | Port |A nyilvános bejövő port. |
   | Háttérablak |A konténer belső-nyilvános portja, ahhoz, hogy a forgalmat irányítsa. |
   | A háttérkészlet |A készletben lévő tárolók lesznek a terheléselosztó célja. |
   | Mintavétel |A mintavétel, amely annak megállapítására, hogy a cél a **háttérkészletben** kifogástalan.The probe used to determine if a target in the Backend pool is healthy. |
   | Munkamenet-állandóság |Meghatározza, hogy az ügyféltől érkező forgalmat hogyan kell kezelni a munkamenet időtartama alatt.<br><br>**Nincs**: Az ugyanazon ügyféltől érkező egymást követő kéréseket bármely tároló kezelheti.<br>**Ügyfél IP-címe**: Az azonos ügyfélIP-címről érkező egymást követő kérelmeket ugyanaz a tároló kezeli.<br>**Ügyfél IP-címe és protokollja:** Az azonos ügyfél IP- és protokollkombinációból származó egymást követő kérelmeket ugyanaz a tároló kezeli. |
   | Tétlen időelés |(csak TCP esetén) Percek alatt a TCP/HTTP-ügyfél nyitva tartásának ideje az *életben tartást eredményező* üzenetekre való támaszkodás nélkül. |

## <a name="add-a-security-rule-portal"></a>Biztonsági szabály hozzáadása (portál)
Ezután hozzá kell adnunk egy biztonsági szabályt, amely a megnyitott portról a tűzfalon keresztül irányítja a forgalmat.

1. Jelentkezzen be a portálra.
2. Keresse meg azt az erőforráscsoportot, amelybe az Azure Container Service-t telepítette.
3. Válassza ki a **nyilvános** ügynök hálózati biztonsági csoportját (amely az **XXXX-agent-public-nsg-XXXX-hez**hasonló).
   
    ![Az Azure container service hálózati biztonsági csoportja](./media/container-service-enable-public-access/agent-nsg.png)
4. Válassza **a Bejövő biztonsági szabályok,** majd a **Hozzáadás lehetőséget.**
   
    ![Az Azure container service hálózati biztonsági csoport szabályai](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Töltse ki a tűzfalszabályt a nyilvános port engedélyezéséhez, majd kattintson az **OK**gombra.
   
   | Mező | Leírás |
   | --- | --- |
   | Név |A tűzfalszabály leíró neve. |
   | Prioritás |A szabály elsőbbségi rangja. Minél alacsonyabb a szám, annál magasabb a prioritás. |
   | Forrás |Korlátozza a beérkező IP-címtartományt, amelyet ez a szabály engedélyez vagy meg fog tagadni. A **Bármely** használatával ne adjon meg korlátozást. |
   | Szolgáltatás |Válassza ki azokat az előre definiált szolgáltatásokat, amelyekhez a biztonsági szabály szól. Ellenkező esetben az **Egyéni** használatával hozzon létre sajátot. |
   | Protocol (Protokoll) |A forgalom korlátozása **TCP** vagy **UDP**alapján. A **Bármely** használatával ne adjon meg korlátozást. |
   | Porttartomány |Ha a **Szolgáltatás** **egyéni**, megadja a szabály által hatással lévő portok tartományát. Használhatja egyetlen portot, például **80**, vagy egy tartományt, például **1024-1500**. |
   | Műveletek |A feltételeknek megfelelő forgalom engedélyezése vagy megtagadása. |

## <a name="next-steps"></a>További lépések
További információ a [nyilvános és a privát DC/OS ügynökök](container-service-dcos-agents.md)közötti különbségről.

További információ [a DC/OS tárolók kezeléséről.](container-service-mesos-marathon-ui.md)

