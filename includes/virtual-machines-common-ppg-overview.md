---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73171093"
---
Virtuális gépek elhelyezése egyetlen régióban csökkenti a példányok közötti fizikai távolságot. Ha egyetlen rendelkezésre állási zónán belül helyezi el őket, fizikailag közelebb kerülnek egymáshoz. Azonban az Azure-lábnyom növekedésével egyetlen rendelkezésre állási zóna több fizikai adatközpontra is kiterjedhet, ami az alkalmazásra hatással lehet a hálózati késés. 

A virtuális gépek lehető legközelebb, a lehető legalacsonyabb késés elérése érdekében, telepítenie kell őket egy közelség elhelyezési csoporton belül.

A közelségelhelyezési csoport egy logikai csoportosítás, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag egymáshoz közel helyezkednek el. A közelségelhelyezési csoportok olyan számítási feladatokhoz hasznosak, ahol az alacsony késés követelmény.


- Alacsony késleltetés az önálló virtuális gépek között.
- Alacsony késés a virtuális gépek között egyetlen rendelkezésre állási csoportban vagy egy virtuálisgép-méretezési csoportban. 
- Alacsony késés az önálló virtuális gépek, több rendelkezésre állási csoportban lévő virtuális gépek vagy több méretezési csoport között. Egy többrétegű alkalmazás egyesítéséhez több számítási erőforrás is lehet egyetlen elhelyezési csoportban. 
- Alacsony késés több alkalmazásréteg között, különböző hardvertípusok használatával. Például fut a háttér-a háttér-használatával M-sorozat egy rendelkezésre állási csoport ban és az előtér egy D-sorozat példány, egy méretezési csoportban, egyetlen közelség elhelyezési csoportban.


![A közelségelhelyezési csoportok ábrája](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Közelségelhelyezési csoportok használata 

A közelségelhelyezési csoport egy új erőforrástípus az Azure-ban. Létre kell hoznia egyet, mielőtt más erőforrásokkal használná. Létrehozása után virtuális gépekkel, rendelkezésre állási készletekkel vagy virtuálisgép-méretezési készletekkel használható. A közelségelhelyezési csoport megadása a közelségelhelyezési csoport azonosítójának megadásához szükséges számítási erőforrások létrehozásakor. 

Egy meglévő erőforrást egy közelségelhelyezési csoportba is áthelyezhet. Amikor egy erőforrást egy közelségelhelyezési csoportba helyez át, először le kell állítania (felkell osztania) az eszközt, mivel potenciálisan átlesz telepítve a régió egy másik adatközpontjába, így kielégítheti a helymegosztási korlátozást. 

Rendelkezésre állási csoportok és a virtuálisgép-méretezési csoportok esetén a közelség-elhelyezési csoportot az erőforrás szintjén kell beállítani, nem pedig az egyes virtuális gépek. 

A közelségelhelyezési csoport inkább helymegosztási kényszer, mint rögzítési mechanizmus. Egy adott adatközponthoz van rögzítve az első erőforrás üzembe helyezésével. Miután a közelségelhelyezési csoportot használó összes erőforrás leállt (felszabadított) vagy törölt, a továbbiakban nem lesz rögzítve. Ezért ha több virtuálisgép-sorozattal rendelkező közelségelhelyezési csoportot használ, fontos, hogy az összes szükséges típust előre adja meg egy sablonban, ha lehetséges, vagy kövessen egy központi telepítési sorozatot, amely javítja a sikeres üzembe helyezés esélyét. Ha a központi telepítés sikertelen, indítsa újra a központi telepítést a virtuális gép mérete, amely nem sikerült az első telepítendő méret.


## <a name="best-practices"></a>Ajánlott eljárások 
- A legkisebb késés érdekében használjon közelségelhelyezési csoportokat a gyorsított hálózatkezeléssel együtt. További információ: [Create a Linux virtual machine with Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) or [Create a Windows virtual machine with Accelerated Networking](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Telepítse az összes virtuálisgép-méretet egyetlen sablonban. Annak elkerülése érdekében, hogy a szükséges virtuálisgép-termékközponti műveleteket és méreteket nem támogató hardverekre való leszállásne így elkerülhető legyen, az összes alkalmazásréteget egyetlen sablonba kell foglalni, hogy azok egyszerre legyenek telepítve.
- Ha a központi telepítést PowerShell, CLI vagy az SDK használatával `OverconstrainedAllocationRequest`írja le, foglalási hiba jelenhet meg. Ebben az esetben le kell állítania/fel kell osztania az összes meglévő virtuális gépet, és módosítania kell a központi telepítési parancsfájl ban a virtuális gép termékváltozatának/méretének, amelyek sikertelenek kezdődnek. 
- Amikor újra felhasznál egy meglévő elhelyezési csoportot, amelyből a virtuális gépeket törölték, várja meg, amíg a törlés teljesen befejeződik, mielőtt virtuális gépeket ad hozzá.
- Ha a késés az elsődleges prioritás, tegye a virtuális gépek egy közelség elhelyezési csoport és a teljes megoldás egy rendelkezésre állási zónában. De ha a rugalmasság a legfontosabb prioritás, a példányokat több rendelkezésre állási zónára is el kell osztania (egyetlen közelségelhelyezési csoport nem terjedhet ki a zónákra).