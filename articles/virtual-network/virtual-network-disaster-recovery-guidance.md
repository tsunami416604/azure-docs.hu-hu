---
title: Virtuális hálózat üzletmenet-folytonossága | Microsoft dokumentumok
description: Ismerje meg, mi a teendő az Azure virtuális hálózatokat érintő Azure-szolgáltatáskimaradás esetén.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876174"
---
# <a name="virtual-network--business-continuity"></a>Virtuális hálózat – Üzletmenet-folytonosság

## <a name="overview"></a>Áttekintés
A virtuális hálózat (VNet) a hálózat logikai ábrázolása a felhőben. Lehetővé teszi a saját privát IP-címterület meghatározását és a hálózat alhálózatokra való szegmentálását. Virtuális hálózatok megbízhatósági határként szolgál a számítási erőforrások, például az Azure virtuális gépek és a felhőszolgáltatások (webes/feldolgozói szerepkörök) üzemeltetéséhez. A virtuális hálózat közvetlen privát IP-kommunikációt tesz lehetővé a benne tárolt erőforrások között. A virtuális hálózat vpn-átjárón vagy ExpressRoute-on keresztül a helyszíni hálózathoz kapcsolható.

A virtuális hálózat egy régió hatókörén belül jön létre. Két különböző régióban (például us east and US West) azonos címtérrel rendelkező virtuális hálózatokat hozhat *létre,* de mivel ugyanaz a címterületük, nem kapcsolhatja össze őket. 

## <a name="business-continuity"></a>Üzleti folytonosság

Lehet, hogy több különböző módon, hogy az alkalmazás megszakadhat. Egy régiót egy természeti katasztrófa vagy részleges katasztrófa miatt teljesen elvághatnak, több eszköz vagy szolgáltatás meghibásodása miatt. A virtuális hálózat szolgáltatásra gyakorolt hatás az egyes helyzetekben eltérő.

**K: Ha egy teljes régióban kimaradás történik, mit tegyek? Például, ha egy régió teljesen el van vágva természeti katasztrófa miatt? Mi történik a régióban üzemeltetett virtuális hálózatokkal?**

A: A virtuális hálózat és az erőforrások az érintett régióban továbbra is elérhetetlen a szolgáltatás kimaradása idején.

![Egyszerű virtuális hálózati diagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**K: Mit tehetek újra létrehozni ugyanazt a virtuális hálózatot egy másik régióban?**

A: A virtuális hálózatok meglehetősen könnyű erőforrások. Meghívhatja az Azure API-kat, hogy hozzon létre egy virtuális hálózatot ugyanazzal a címtérrel egy másik régióban. Az érintett régióban lévő környezet újbóli létrehozásához API-hívásokat kell kezdeményeznia a Cloud Services webes és feldolgozói szerepkörök, valamint a virtuális gépek újratelepítéséhez. Ha helyszíni kapcsolattal rendelkezik, például egy hibrid telepítésben, telepítenie kell egy új VPN-átjárót, és csatlakoznia kell a helyszíni hálózathoz.

Virtuális hálózat létrehozásáról a [Virtuális hálózat létrehozása](manage-virtual-network.md#create-a-virtual-network)című témakörben látható.

**K: Egy virtuális hálózat replikáját egy adott régióban lehet újra létrehozni egy másik régióban előre?**

V: Igen, hozhat létre két virtuális hálózatok ugyanazt a privát IP-cím tér és erőforrások két különböző régióban előre. Ha a virtuális hálózatban internetkapcsolattal kapcsolatos szolgáltatásokat üzemeltet, beállíthatja a Traffic Managert, hogy a forgalmat az aktív régióba irányítsa. Azonban nem tud csatlakozni két virtuális hálózat azonos címtérrel a helyszíni hálózathoz, mert ez útválasztási problémákat okozna. Egy katasztrófa és egy virtuális hálózat elvesztése az egyik régióban, csatlakoztathatja a másik virtuális hálózat a rendelkezésre álló régióban, a megfelelő címterület a helyszíni hálózathoz.

Virtuális hálózat létrehozásáról a [Virtuális hálózat létrehozása](manage-virtual-network.md#create-a-virtual-network)című témakörben látható.

