---
title: "Mi a teendő, egy Azure virtuális hálózatot érintő Azure-szolgáltatások becsukódjon |} Microsoft Docs"
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
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network--business-continuity"></a>Virtuális hálózat – az üzletmenet folytonossága
## <a name="overview"></a>Áttekintés
Egy virtuális hálózatot (VNet) logikai reprezentációjává a hálózaton, a felhőben. Lehetővé teszi a saját privát IP-címtér meghatározását, és a hálózati szegmenseket alhálózatokra. Vnetek egy megbízhatósági kapcsolat határán a számítási erőforrásokat, például az Azure virtuális gépek és Felhőszolgáltatások (webes/munkavégző szerepkörök) futtatására szolgál. Egy VNet lehetővé teszi, hogy a közvetlen saját integrációs csomaggal folytatott kommunikációhoz a benne tárolt erőforrások között. Egy virtuális hálózatot is lehet társítani egy helyi hálózaton keresztül, például a VPN-átjáró vagy ExpressRoute a hibrid lehetőségek közül.

A virtuális hálózat egy régiót hatókörén belül jön létre. Ugyanazt a címtartományt, két különböző régiókban Vnetek hozhat létre (azaz Velünk East és Velünk nyugati, de nem csatlakoztassa őket egy másik közvetlenül). 

## <a name="business-continuity"></a>Üzleti folyamatok fenntarthatósága
Előfordulhat, hogy több különböző módon, hogy az alkalmazás sikerült-e működni. Egy adott régió sikerült teljesen vágható természeti katasztrófa vagy több eszközök vagy szolgáltatások egy miatti részleges katasztrófa miatt. A virtuális hálózat szolgáltatás gyakorolt hatás nem azonos a helyzetekben.

**K: Mit kell tennie egy teljes régióban kimaradás esetén? például ha egy régiót teljesen levágási természeti katasztrófa miatt? Mi történik a virtuális hálózatok régióban üzemeltetett?**

V: a virtuális hálózat és az érintett régióban erőforrás elérhetetlen maradjon az időre, amíg a szolgáltatás megszűnésének.

![Egyszerű virtuális hálózati diagramja](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**K: milyen lehetőségeket újra létre kell hoznia az azonos virtuális hálózatban egy másik régióban?**

V: virtual Network (VNet) viszonylag könnyű erőforrás. Hozzon létre egy Vnetet ugyanazt a címtartományt egy másik régióban Azure API-hívhat meg. Újra létre kell hoznia, az érintett régió szerepel ugyanabban a környezetben, hogy helyezze újra üzembe a Felhőszolgáltatásokat (webes/munkavégző szerepkörök) és a virtuális gépek volt az API-hívásokat. Akkor is rendelkezik majd lépett fel a VPN-átjáró és a helyszíni hálózat csatlakozni, ha a helyi kapcsolat (például egy hibrid telepítés).

Egy VNet létrehozására vonatkozó utasításokat találhatók [Itt](virtual-networks-create-vnet-arm-pportal.md). 

**K: egy replikát készít a virtuális hálózat egy adott régióban lehet hozza létre újra egy másik régióban időben?**

A: Igen két Vnetek időben két különböző régiókban azonos privát IP-címtér és erőforrások használatával is létrehozhat. Ha az ügyfél internetre irányuló szolgáltatások, a Vneten belül lett üzemeltet, azok sikerült állította be Traffic Manager földrajzi-útvonal forgalmát a régiót, amelyben aktív. Azonban az ügyfél nem tud csatlakozni két Vnetek a helyszíni hálózathoz ugyanazt a címtartományt, útválasztási problémák okozna. Egy olyan vészhelyzet esetén, és egy Vnetet egy régióban megszűnését az ügyfél csatlakozhatnak a rendelkezésre álló terület egyéb VNet-címterek a helyszíni hálózat megfelelő.

Egy VNet létrehozására vonatkozó utasításokat találhatók [Itt](virtual-networks-create-vnet-arm-pportal.md).

