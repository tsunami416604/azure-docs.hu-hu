---
title: "Hálózati adapterek az Azure-ban | Microsoft Docs"
description: "Az Azure hálózati adaptereinek ismertetése az Azure Resource Manager-alapú üzemi modellben."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3244d5b52785d820698bf26f9bf189de93ef64e4
ms.openlocfilehash: 691b79d7739246dad7191195fa049fd58340c8ff


---
# <a name="network-interfaces-in-azure"></a>Hálózati adapterek az Azure-ban
A hálózati adapter (NIC) a virtuális gép (VM) és a mögöttes szoftverhálózat közötti kapcsolat. Ez a cikk ismerteti a hálózati adaptereket és azok használatát az Azure Resource Manager-alapú üzemi modellben.

A Microsoft a Resource Manager-alapú üzemi modellt javasolja az új erőforrások üzembe helyezéséhez, de a hálózati kapcsolattal rendelkező virtuális gépek üzembe helyezését a [klasszikus](virtual-network-ip-addresses-overview-classic.md) üzemi modellel is elvégezheti. Ha a klasszikus modellt ismeri, akkor fontos különbségeket fedezhet fel a virtuális gépek hálózati használatával kapcsolatban a Resource Manager-alapú üzemi modell esetében. További részletek a különbségekkel kapcsolatban: [Virtuális gépek hálózati használata – Klasszikus](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

A hálózati adapter az Azure-ban:

1. Létrehozható, törölhető és saját konfigurálható beállításokkal rendelkező erőforrás.
2. Létrehozásakor csatlakoztatni kell egy Azure virtuális hálózat (VNet) egyik alhálózatához. Ha még nem ismeri a virtuális hálózatokat, a [Virtuális hálózatok áttekintése](virtual-networks-overview.md) című cikkben további információkat olvashat. A hálózati adaptert csatlakoztatni kell egy virtuális hálózathoz, amely a hálózati adapterrel megegyező Azure [helyen](https://azure.microsoft.com/regions) és [előfizetésen](../azure-glossary-cloud-terminology.md#subscription) belül szerepel. A hálózati adapter létrehozása után módosíthatja, hogy az melyik alhálózathoz csatlakozzon, a meglévő virtuális hálózathoz való csatlakozás módosítása azonban nem lehetséges.
3. Hozzárendelt névvel rendelkezik, amely a hálózati adapter létrehozását követően nem módosítható. A névnek egyedinek kell lennie az adott Azure-[erőforráscsoporton](../azure-resource-manager/resource-group-overview.md#resource-groups) belül, de nem kell egyedinek lennie az előfizetésben, az Azure-helyen, ahol létrehozták, illetve a virtuális hálózaton belül, amelyhez csatlakozik. Egy Azure-előfizetésen belül általában számos hálózati adaptert hoznak létre. Ezért ajánlott valamilyen elnevezési konvenció használata, amely lényegesen megkönnyíti több hálózati adapter egyidejű kezelését az alapértelmezett nevek használatához képest. Az [Ajánlott elnevezési konvenciók Azure-erőforrásokhoz](../guidance/guidance-naming-conventions.md) című cikkben javaslatokat találhat.
4. Csatlakoztatható egy virtuális géphez, de csak egyetlen, az adapterrel megegyező helyen lévő virtuális géphez.
5. MAC-címmel rendelkezik, amely mindaddig a hálózati adapterhez tartozik, amíg az a virtuális géphez csatlakozik. A MAC-cím akkor is megmarad, ha a virtuális gépet újraindítja (az operációs rendszerből), vagy ha leállítja (megszünteti a hozzárendelést), majd megint elindítja az Azure Portal, az Azure PowerShell vagy az Azure parancssori felület segítségével. Ha a hálózati adaptert leválasztja egy virtuális gépről és csatlakoztatja egy másikhoz, akkor másik MAC-címet kap. Ha törli a hálózati adaptert, a rendszer más hálózati adapterekhez rendeli a MAC-címet.
6. Legalább egy elsődleges **magánhálózati** *Ipv4* statikus vagy dinamikus IP-címet hozzá kell rendelni.
7. Egy nyilvános IP-cím erőforrás rendelhető hozzá.
8. Támogatja az egygyökerű I/O-virtualizálással (SR-IOV) gyorsított hálózatkezelést a Microsoft Windows Server operációs rendszer adott verzióját futtató adott virtuálisgép-méretek esetében. Ha többet szeretne megtudni erről az ELŐZETES VERZIÓJÚ szolgáltatásról, olvassa el a [Virtuális gép gyorsított hálózatkezelése](virtual-network-accelerated-networking-powershell.md) című cikket.
9. Képes nem a hozzá rendelt magánhálózati IP-címekre irányuló forgalom fogadására, ha a hálózati adapter IP-továbbítása engedélyezve van. Ha egy virtuális gép tűzfalszoftvert futtat, akkor a saját IP-címétől eltérő címekre irányuló csomagokat átirányítja. A virtuális gépen továbbra is futtatnia kell a forgalom átirányítására vagy továbbítására képes szoftvert, de ehhez előbb engedélyeznie kell az IP-továbbítást egy hálózati adapteren.
10. Gyakran ugyanabban az erőforráscsoportban hozzák létre, amelyben a csatlakoztatott virtuális gép is van, vagy ugyanabban a virtuális hálózatban, amelyhez kapcsolódik, de ez nem feltétlenül szükséges.

## <a name="vms-with-multiple-network-interfaces"></a>Több hálózati adapterrel rendelkező virtuális gépek
Egy virtuális géphez több hálózati adapter is csatlakoztatható, viszont ilyen esetben ügyeljen a következőkre:  

* A virtuális gép méretének támogatnia kell több hálózati adapter használatát. Ha többet szeretne megtudni a több hálózati adapter kezelésére is alkalmas virtuálisgép-méretekről, olvassa el a [Windows Server virtuális gépek mérete](../virtual-machines/virtual-machines-windows-sizes.md) vagy a [Linux virtuális gépek mérete](../virtual-machines/virtual-machines-linux-sizes.md) című cikkeket.
* A virtuális gépet legalább két hálózati adapterrel kell létrehozni. Ha a virtuális gépet csak egy hálózati adapterrel hozta létre, akkor a létrehozása után akkor sem csatlakoztathat további hálózati adaptereket, ha a virtuális gép mérete támogatja több hálózati adapter használatát. Ha a virtuális gépet legalább két hálózati adapterrel hozta létre, akkor a létrehozása után további hálózati adaptereket csatlakoztathat hozzá, ha a virtuális gép mérete támogatja a kettőnél több hálózati adapter használatát.  
* Leválaszthatja a virtuális gépről a másodlagos hálózati adaptereket (az elsődleges hálózati adaptert nem lehet leválasztani), ha virtuális gép legalább három csatlakoztatott hálózati adapterrel rendelkezik. A leválasztásra nincs lehetősége, ha a virtuális géphez kettő vagy kevesebb hálózati adapter csatlakozik.  
* A hálózati adapterek sorrendje a virtuális gépen belül véletlenszerű, és az Azure-infrastruktúra frissítései során is változhat. Az IP-címek és a megfelelő ethernet MAC-címek azonban megmaradnak. Feltételezzük például, hogy az operációs rendszer az Azure NIC1 hálózati adaptert Eth1-ként azonosítja. Az Eth1 IP-címe 10.1.0.100, MAC-címe pedig 00-0D-3A-B0-39-0D. Az Azure infrastruktúra frissítése és újraindítása után az operációs rendszer Eth2-ként azonosítja majd az Azure NIC1-et, az IP- és a MAC-címek viszont ugyanazok maradnak, mint amikor az operációs rendszert Eth1-ként azonosította az Azure NIC1-et. Az ügyfél által kezdeményezett újraindítás esetén a hálózati adapterek sorrendje a operációs rendszeren belül változatlan marad.  
* Ha a virtuális gép tagja egy [rendelkezésre állási csoportnak](../azure-glossary-cloud-terminology.md#availability-set), akkor a rendelkezésre állási csoporton belül minden virtuális gépnek egy, vagy mindnek több hálózati adapterrel kell rendelkeznie. Ha a virtuális gépek több hálózati adapterrel rendelkeznek, azok számának már nem kell megegyeznie, amíg az egyes virtuális gépekhez legalább két hálózati adapter csatlakozik.

## <a name="next-steps"></a>Következő lépések
* A [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikkből megtudhatja, hogyan hozhat létre virtuális gépet egyetlen hálózati adapterrel.
* A [Virtuális gép létrehozása több hálózati adapterrel](virtual-network-deploy-multinic-arm-ps.md) című cikkből megtudhatja, hogyan hozhat létre virtuális gépet több hálózati adapterrel.
* Ha több IP-konfigurációval szeretne hálózati adaptert létrehozni, olvassa el a [Több IP-cím használata Azure virtuális gépekhez](virtual-network-multiple-ip-addresses-powershell.md) című cikket.




<!--HONumber=Jan17_HO5-->


