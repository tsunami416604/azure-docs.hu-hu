---
title: (ELAVULT) DC/OS az Azure container alkalmazás hozzáférésének engedélyezése
description: Tudnivalók az Azure Container Service DC/OS-tárolók nyilvános hozzáférésének engedélyezése.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996602"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(ELAVULT) Egy Azure Container Service-alkalmazás nyilvános hozzáférésének engedélyezése

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Az ACS DC/OS tárolója sem [nyilvános ügynökkészlet](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automatikusan közvetlenül csatlakozik az internethez. Alapértelmezés szerint portok **80-as**, **443-as**, **8080-as** nyílnak meg, és ezeket a portokat figyeli (nyilvános) tárolója sem érhetők el. Ez a cikk bemutatja, hogyan az alkalmazások további portok megnyitásához az Azure Container Service-ben.

## <a name="open-a-port-portal"></a>Nyisson meg egy portot (portál)
Először létre kell a kívánt port megnyitásához.

1. Jelentkezzen be a portálra.
2. Keresse meg az erőforráscsoport, az Azure Container Service a központilag telepített.
3. Válassza ki az ügynök terheléselosztó (amely hasonló nevű **XXXX-ügynök-lb-XXXX**).
   
    ![Az Azure container service-terheléselosztó](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kattintson a **mintavételek** , majd **hozzáadása**.
   
    ![Az Azure container service a load balancer vizsgálatok](./media/container-service-enable-public-access/add-probe.png)
5. Töltse ki a mintavétel az űrlapot, és kattintson a **OK**.
   
   | Mező | Leírás |
   | --- | --- |
   | Name (Név) |A mintavétel egy leíró nevet. |
   | Port |A portja a tároló tesztelése. |
   | Útvonal |(Ha a HTTP-módban) A webhely relatív elérési útja mintavételi. A HTTPS nem támogatott. |
   | Intervallum |Mintavétel közötti idő másodpercben megkísérli. |
   | Nem kifogástalan állapot küszöbértéke |Egymást követő számát megkísérli, mielőtt a tároló nem megfelelő állapotú. |
6. Vissza az ügynök terheléselosztó tulajdonságait, kattintson **terheléselosztási szabályok** , majd **Hozzáadás**.
   
    ![Az Azure container service load balancer-szabályok](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Töltse ki a load balancer űrlapot, és kattintson a **OK**.
   
   | Mező | Leírás |
   | --- | --- |
   | Name (Név) |A terheléselosztó egy leíró nevet. |
   | Port |Nyilvános bejövő port. |
   | Háttérport |A tároló irányíthatja a forgalmat a belső nyilvános port. |
   | Háttérkészlet |A tárolók a készlet lesz a cél ehhez a terheléselosztóhoz. |
   | Mintavétel |A mintavétel határozza meg, ha a cél a **háttérkészlet** állapota kifogástalan. |
   | Munkamenet megőrzését |Meghatározza, hogy egy ügyféltől érkező forgalmat a munkamenet időtartamának kezelésének módját.<br><br>**Nincs**: egyetlen tárolója sem tudja kezelni az ugyanazon ügyféltől érkező későbbi kérelmeket.<br>**Ügyfél IP**: az azonos ügyfél IP-címről egymást követő kéréseket kezeli ugyanazt a tárolót.<br>**Ügyfél IP protokoll és**: az azonos ügyfél IP-protokoll kombináció az egymást követő kéréseket kezeli ugyanazt a tárolót. |
   | Üresjárat időkorlátja |(Csak a TCP) Percek alatt az idő, így egy TCP/HTTP-alapú nyissa meg a anélkül, hogy az *életben tartási* üzeneteket. |

## <a name="add-a-security-rule-portal"></a>Adjon meg egy biztonsági szabályt (portál)
Következő lépésként hozzá kell adnunk a biztonsági szabály, amely a megnyitott port a tűzfalon keresztül irányítja a forgalmat.

1. Jelentkezzen be a portálra.
2. Keresse meg az erőforráscsoport, az Azure Container Service a központilag telepített.
3. Válassza ki a **nyilvános** ügynök hálózati biztonsági csoport (amely hasonló nevű **XXXX-ügynök-public-nsg-XXXX**).
   
    ![Az Azure container service a hálózati biztonsági csoport](./media/container-service-enable-public-access/agent-nsg.png)
4. Válassza ki **bejövő biztonsági szabályok** , majd **Hozzáadás**.
   
    ![Az Azure container service a hálózati biztonsági csoport szabályai](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Töltse ki a nyilvános port engedélyezéséhez, és kattintson a tűzfalszabály **OK**.
   
   | Mező | Leírás |
   | --- | --- |
   | Name (Név) |A tűzfalszabály leíró nevével. |
   | Prioritás |A szabály prioritását rang. Minél kisebb számra annál magasabb a prioritás. |
   | Forrás |A bejövő IP-címtartományt engedélyez vagy tilt Ez a szabály a korlátozása. Használat **bármely** korlátozás megadása. |
   | Szolgáltatás |Válassza ki a biztonsági szabály előre meghatározott szolgáltatások. Ellenkező esetben használjon **egyéni** hozhat létre saját. |
   | Protokoll |Korlátozza a forgalmat a alapján **TCP** vagy **UDP**. Használat **bármely** korlátozás megadása. |
   | Porttartomány |Amikor **szolgáltatás** van **egyéni**, érinti ez a szabály-portok tartományát határozza meg. Használhatja például egy portot **80-as**, vagy egy tartományt, például **1024 – 1500**. |
   | Műveletek |Adatforgalom engedélyezéséhez vagy letiltásához, amely megfelel a feltételeknek. |

## <a name="next-steps"></a>További lépések
További információ a különbség a között [nyilvános és privát DC/OS-ügynökök](container-service-dcos-agents.md).

További információ [a DC/OS-tárolók kezelése](container-service-mesos-marathon-ui.md).

