---
title: "Virtuális hálózat az üzletmenet folytonossága |} Microsoft Docs"
description: "Ismerje meg, mi a teendő az Azure-szolgáltatások becsukódjon érintő Azure virtuális hálózatok."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: d993144006d1fb17d79ffee4f2da538264a309a4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="virtual-network--business-continuity"></a>Virtuális hálózat – az üzletmenet folytonossága

## <a name="overview"></a>Áttekintés
Egy virtuális hálózatot (VNet) logikai reprezentációjává a hálózaton, a felhőben. Lehetővé teszi a saját privát IP-címtér meghatározását, és a hálózati szegmenseket alhálózatokra. Vnetek egy megbízhatósági kapcsolat határán a számítási erőforrásokat, például az Azure virtuális gépek és Felhőszolgáltatások (webes/munkavégző szerepkörök) futtatására szolgál. Egy VNet lehetővé teszi, hogy a közvetlen saját integrációs csomaggal folytatott kommunikációhoz a benne tárolt erőforrások között. Egy a helyszíni hálózathoz egy VPN-átjáró vagy ExpressRoute egy virtuális hálózathoz társíthatja.

A virtuális hálózat egy régiót hatókörén belül jön létre. Ugyanazt a címtartományt, (például US East és Velünk West) két különböző régiókban Vnetek hozhatja létre, de azokat nem lehet összekapcsolni. 

## <a name="business-continuity"></a>Üzleti folyamatok fenntarthatósága

Előfordulhat, hogy több különböző módon, hogy az alkalmazás sikerült-e működni. A régió sikerült kell teljesen levágási természeti katasztrófa vagy részleges katasztrófa miatt több eszközön vagy szolgáltatások működése miatt. A virtuális hálózat szolgáltatás gyakorolt hatás nem azonos a helyzetekben.

**K: nem tervezett kimaradás esetén egy teljes régió, mi a teendő? Ha például egy régiót teljesen levágási természeti katasztrófa miatt? Mi történik a virtuális hálózatok régióban üzemeltetett?**

V: a virtuális hálózat és az erőforrások, az érintett régióban elérhetetlen maradjon az időre, amíg a szolgáltatás megszűnésének.

![Egyszerű virtuális hálózati diagramja](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**K: milyen lehetőségeket újra létre kell hoznia az azonos virtuális hálózatban egy másik régióban?**

V: virtuális hálózatok viszonylag könnyű erőforrások találhatók. Hozzon létre egy Vnetet ugyanazt a címtartományt egy másik régióban Azure API-hívhat meg. Újbóli létrehozásához, amely az érintett régió szerepel ugyanabban a környezetben, ellenőrizze az API-hívásokban újratelepíteni a Felhőszolgáltatások webes és feldolgozói szerepkörök és a virtuális gépek volt. Ha a helyi kapcsolat van, többek között az egy hibrid telepítésben kell egy új VPN-átjáró üzembe helyezéséhez, és a helyszíni hálózathoz.

Virtuális hálózat létrehozásához lásd: [hozzon létre egy virtuális hálózatot](manage-virtual-network.md#create-a-virtual-network).

**K: egy replikát készít a virtuális hálózat egy adott régióban lehet hozza létre újra egy másik régióban időben?**

A: Igen két Vnetek időben két különböző régiókban azonos privát IP-címtér és erőforrások használatával is létrehozhat. Az üzemeltetett virtuális internetre irányuló szolgáltatások, ha sikerült állította be a Traffic Manager földrajzi-útvonal forgalmát a régiót, amelyben aktív. Azonban csatlakozhat két Vnetek ugyanazt a címtartományt, a helyszíni hálózat, útválasztási problémák okozna. Egy olyan vészhelyzet esetén, és egy Vnetet egy régióban adatvesztés a megfelelő címtartomány a helyszíni hálózathoz kapcsolatba léphet a más virtuális hálózatot a rendelkezésre álló terület.

Virtuális hálózat létrehozásához lásd: [hozzon létre egy virtuális hálózatot](manage-virtual-network.md#create-a-virtual-network).

