---
title: "Azure DC/OS tároló alkalmazás hozzáférésének engedélyezése |} Microsoft Docs"
description: "How Azure Tárolószolgáltatási DC/OS tárolók elérésének engedélyezéséhez."
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Mesos, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: c9ef5913859cf3a55a2de2107a9304f1d28a4829
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Azure Tárolószolgáltatás alkalmazáshoz való nyilvános hozzáférés engedélyezésére
A DC/OS-tároló az ACS [nyilvános ügynök készlet](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) automatikusan kommunikál az internettel. Alapértelmezés szerint portok **80-as**, **443-as**, **8080** nyitva vannak, és bármilyen (nyilvános) tároló, ezeket a portokat figyeli érhetők el. Ez a cikk bemutatja, hogyan az alkalmazások több portok megnyitásához az Azure Tárolószolgáltatásban.

## <a name="open-a-port-portal"></a>Nyisson meg egy portot (portál)
Először azt kell megnyitni azt szeretnénk, ha a portot.

1. Jelentkezzen be a portálra.
2. Az erőforráscsoport az Azure Tárolószolgáltatás számára központilag telepített található.
3. Válassza ki az ügynök terheléselosztó (amely hasonló nevű **XXXX-ügynök-lb-XXXX**).
   
    ![Azure-tárolót szolgáltatás terheléselosztó](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kattintson a **vizsgálat** , majd **hozzáadása**.
   
    ![Azure-tárolót szolgáltatás terheléselosztó-mintavétel](./media/container-service-enable-public-access/add-probe.png)
5. Töltse ki a mintavétel az űrlapot, és kattintson a **OK**.
   
   | Mező | Leírás |
   | --- | --- |
   | Név |A mintavétel egy leíró nevet. |
   | Port |A port, a tároló teszteléséhez. |
   | Útvonal |(Ha HTTP módban) Mintavételi relatív webhely elérési útja A HTTPS nem támogatott. |
   | időköz |Mintavételi közötti idő próbál, másodpercben. |
   | Sérült küszöbérték |Próbálkozások száma egymást követő mintavétel az előtt annak eldöntéséhez, hogy a tároló nem kifogástalan. |
6. Eközben a az ügynök terheléselosztó tulajdonságainak, kattintson a **terheléselosztási szabályok** , majd **Hozzáadás**.
   
    ![Azure-tárolót szolgáltatás terheléselosztási szabály](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Töltse ki a load balancer űrlapot, és kattintson a **OK**.
   
   | Mező | Leírás |
   | --- | --- |
   | Név |A load balancer egy leíró nevet. |
   | Port |A nyilvános bejövő portot. |
   | Háttér-port |Irányíthatja a forgalmat a tároló belső nyilvános port. |
   | Háttérkészlet |Ebben a készletben tárolók lesz a cél a terheléselosztóhoz. |
   | Hálózatfigyelő |Annak meghatározásához, hogy a célként használt mintavételi a **háttérkészlet** működik megfelelően. |
   | Munkamenet megőrzését |Meghatározza, hogyan ügyféltől érkező forgalmat kell kezelni, a munkamenet időtartama alatt.<br><br>**Nincs**: ugyanazt az ügyfelet az egymást követő kéréseket bármely tárolóban kell kezelnie.<br>**Ügyfél IP**: az azonos ügyfél IP-címről egymást követő kéréseket kezeli a tárolóhoz.<br>**Ügyfél IP protokoll és**: az azonos ügyfél IP-protokoll kombináció az egymást követő kéréseket kezeli a tárolóhoz. |
   | Üresjárati időtúllépés |(Csak a TCP) A TCP/HTTP ügyfél megőrzésének ideje megnyitása (percben), anélkül, hogy az *életben tartási* üzeneteket. |

## <a name="add-a-security-rule-portal"></a>Adja hozzá a biztonsági szabály (portál)
A következő igazolnia kell, hogy irányítja a forgalmat a megnyitott portot a tűzfalon a biztonsági szabály hozzáadása.

1. Jelentkezzen be a portálra.
2. Az erőforráscsoport az Azure Tárolószolgáltatás számára központilag telepített található.
3. Válassza ki a **nyilvános** ügynök hálózati biztonsági csoport (amely hasonló nevű **XXXX-ügynök – nyilvános-nsg-XXXX**).
   
    ![Azure-tárolót szolgáltatás hálózati biztonsági csoport](./media/container-service-enable-public-access/agent-nsg.png)
4. Válassza ki **bejövő biztonsági szabályok** , majd **Hozzáadás**.
   
    ![Azure-tárolót szolgáltatás hálózati biztonsági csoportszabályok](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Töltse ki a nyilvános port engedélyezze, majd kattintson a tűzfalszabály **OK**.
   
   | Mező | Leírás |
   | --- | --- |
   | Név |A tűzfalszabály egy leíró nevet. |
   | Prioritás |A szabály prioritása dimenziószáma. Minél kisebb számot annál magasabb a prioritás. |
   | Forrás |A bejövő IP-címtartomány engedélyezett vagy letiltott Ez a szabály korlátozása. Használjon **bármely** nem adhatja meg a korlátozás. |
   | Szolgáltatás |Válassza ki a biztonsági szabály csak az előre meghatározott szolgáltatásokat foglal. Ellenkező esetben használja **egyéni** saját. |
   | Protokoll |Korlátozzák a forgalmat a alapján **TCP** vagy **UDP**. Használjon **bármely** nem adhatja meg a korlátozás. |
   | Porttartomány |Ha **szolgáltatás** van **egyéni**, ez a szabály érintett portok tartományát határozza meg. Használhatja például egyetlen port, **80**, vagy egy tartományt, például **1024-1500**. |
   | Műveletek |Adatforgalom engedélyezéséhez vagy letiltásához, amely megfelel a követelményeknek. |

## <a name="next-steps"></a>Következő lépések
További tudnivalók a különbség a között [nyilvános és titkos DC/OS-ügynökök](container-service-dcos-agents.md).

További információt olvashat [a DC/OS-tárolók kezelése](container-service-mesos-marathon-ui.md).

