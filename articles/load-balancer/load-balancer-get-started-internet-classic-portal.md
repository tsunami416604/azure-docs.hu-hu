---
title: "Internetkapcsolattal rendelkező terheléselosztó létrehozása – klasszikus Azure Portal | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó klasszikus üzembehelyezési modellel a klasszikus Azure portál használatával"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: a022154f5eca6de2d2dbfc1b9aa30d2ea0a7d650
ms.contentlocale: hu-hu
ms.lasthandoff: 01/24/2017

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Bevezetés az internetkapcsolattal rendelkező terheléselosztó (klasszikus) létrehozásába a klasszikus Azure portál használatával

> [!div class="op_single_selector"]
> * [klasszikus Azure portál](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-classic-rm.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. A különféle eszközök dokumentációit a cikk tetején található fülekre kattintva tekintheti meg. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [azt is megismerheti, hogyan lehet internetkapcsolattal rendelkező terheléselosztót létrehozni az Azure Resource Manager használatával](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Internetkapcsolattal rendelkező terheléselosztó beállítása virtuális gépekhez

Az internetről érkező hálózati forgalom terheléselosztásának megvalósításához a felhőszolgáltatás virtuális gépei között, létre kell hoznia egy elosztott terhelésű készletet. Ez az eljárás feltételezi, hogy már létrehozta a virtuális gépeket, és az összes ugyanazon a felhőszolgáltatáson belül van.

**Virtuális gépek elosztott terhelésű készletének konfigurálása**

1. A klasszikus Azure portálon kattintson a **Virtuális gépek** lehetőségre, majd kattintson a virtuális gép nevére az elosztott terhelésű készletben.
2. Kattintson a **Végpontok**, majd a **Hozzáadás** lehetőségre.
3. A **Végpont hozzáadása egy virtuális géphez** lapon kattintson a jobbra mutató nyílra.
4. Az **Adja meg a végpont adatait** lapon:

   * A **Név** mezőbe írjon be egy nevet a végpont számára, vagy válassza ki a nevet a közös protokollok előre definiált végpontjainak listáján.
   * A **Protokoll** részben szükség szerint válassza ki a végpont típusa által megkövetelt protokollt (TCP vagy UDP).
   * A **Nyilvános port és magánhálózati port** részbe írja be azokat a portszámokat, amelyeket a virtuális gépnek kell használnia. A magánhálózati portot és a tűzfalszabályokat a virtuális gépen a forgalomnak az alkalmazása számára megfelelő átirányítására használhatja. A magánhálózati port és a nyilvános port azonos lehet. A webes (HTTP) forgalom egyik végpontjához például hozzárendelhető a 80-as port mind nyilvános, mind magánhálózati portként.

5. Válassza ki az **Elosztott terhelésű készlet létrehozása** lehetőséget, majd kattintson a jobbra mutató nyílra.
6. Az **Elosztott terhelésű készlet konfigurálása** lapon írja be az elosztott terhelésű készlet nevét, majd rendelje hozzá az Azure Load Balancer mintavételi viselkedésének értékeit. A terheléselosztó a mintavételezők használatával határozza meg, hogy az elosztott terhelésű készlet virtuális gépei rendelkezésre állnak-e bejövő forgalom fogadására.
7. Kattintson a pipa jelre az elosztott terhelésű végpont létrehozásához. Az **Igen** feliratot fogja látni a virtuális gép **Végpontok** lapjának **Elosztott terhelésű készlet neve** oszlopában.
8. A portálon kattintson a **Virtuális gépek** lehetőségre, majd kattintson egy további virtuális gép nevére az elosztott terhelésű készletben, kattintson a **Végpontok**, majd a **Hozzáadás** lehetőségre.
9. A **Végpont hozzáadása egy virtuális géphez** lapon kattintson a **Végpont hozzáadása egy meglévő elosztott terhelésű készlethez** lehetőségre, válassza ki az elosztott terhelésű készlet nevét, majd kattintson a jobbra mutató nyílra.
10. Az **Adja meg a végpont adatait** lapon írjon be egy nevet a végpont számára, majd kattintson a pipa jelre.

Az elosztott terhelésű készlet további virtuális gépeinek esetében ismételje meg a 8–10. lépést.

## <a name="next-steps"></a>Következő lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

