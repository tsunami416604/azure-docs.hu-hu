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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171093"
---
A virtuális gépek egyetlen régióban való elhelyezése csökkenti a példányok közötti fizikai távolságot. Ha egyetlen rendelkezésre állási zónába helyezi őket, az is fizikailag szorosabban fog működni. Mivel azonban az Azure-lábnyom növekszik, az egyetlen rendelkezésre állási zóna több fizikai adatközpontra is kiterjedhet, ami az alkalmazást érintő hálózati késést eredményezhet. 

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy közelségi elhelyezési csoporton belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.


- Kis késleltetés az önálló virtuális gépek között.
- Kis késleltetés egyetlen rendelkezésre állási csoportba tartozó virtuális gépek és egy virtuálisgép-méretezési csoport között. 
- Kis késleltetés az önálló virtuális gépek, több rendelkezésre állási csoportba tartozó virtuális gépek vagy több méretezési csoport között. A többrétegű alkalmazások összevonásához több számítási erőforrás is tartozhat egyetlen elhelyezési csoportban. 
- Kis késleltetés több alkalmazás-réteg között különböző típusú hardvereszközök használatával. Például, ha a hátteret a rendelkezésre állási csoportban található M-sorozattal és a D sorozatú példányon lévő előtérrel futtatja, egy méretezési csoport egyetlen közelségi elhelyezési csoportjában.


![Proximity elhelyezési csoportok grafikája](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Proximity elhelyezési csoportok használata 

A közelségi elhelyezési csoport egy új erőforrástípus az Azure-ban. Először létre kell hoznia egyet, mielőtt más erőforrásokkal kellene használnia. A létrehozást követően a virtuális gépekkel, rendelkezésre állási csoportokkal vagy virtuálisgép-méretezési csoportokkal használható. Ha a közelségi elhelyezési csoport AZONOSÍTÓját biztosító számítási erőforrásokat hoz létre, a közelségi elhelyezési csoportot kell megadnia. 

Egy meglévő erőforrást is át lehet helyezni egy közelségi elhelyezési csoportba. Ha az erőforrást egy közelségi elhelyezési csoportba helyezi át, akkor először állítsa le (szabadítsa fel) az eszközt, mert a régió egy másik adatközpontjában is elérhetővé válik, hogy az megfeleljen az elhelyezési korlátozásnak. 

A rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok esetében az egyes virtuális gépek helyett az erőforrás szintjén kell beállítani a közelségi elhelyezési csoportot. 

A közelségi elhelyezési csoport a rögzítési mechanizmus helyett egy elhelyezési megkötés. Egy adott adatközpontba van rögzítve, amely az első erőforrás központi telepítését használja. Ha a közelségi elhelyezési csoportot használó összes erőforrás le lett állítva (fel van foglalva) vagy törölve lett, már nem rögzített. Ezért, ha a közelségi elhelyezési csoportot több virtuálisgép-sorozattal használja, fontos, hogy az összes szükséges típust megadja a sablonban, amikor lehetséges, vagy egy központi telepítési sorozatot követve, amely javítja a sikeres üzembe helyezés esélyét. Ha a telepítés sikertelen, indítsa újra a telepítést a virtuálisgép-mérettel, amely az első üzembe helyezési méretnél meghiúsult.


## <a name="best-practices"></a>Ajánlott eljárások 
- A legkisebb késés érdekében használja a közeli elhelyezési csoportokat a gyorsított hálózatkezeléssel együtt. További információ: [Linux rendszerű virtuális gép gyorsított hálózatkezeléssel való létrehozása](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [Windows rendszerű virtuális gép létrehozása gyorsított hálózatkezeléssel](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Minden virtuálisgép-méret üzembe helyezése egyetlen sablonban. Ha el szeretné kerülni, hogy a hardver nem támogatja a szükséges összes virtuálisgép-t és méretet, vegye fel az összes alkalmazási szintet egyetlen sablonba, hogy azok mind egyszerre legyenek telepítve.
- Ha a PowerShell-lel, a CLI-vel vagy az SDK-val végez parancsfájl-használatot, lefoglalási hibaüzenetet kaphat `OverconstrainedAllocationRequest`. Ebben az esetben le kell állítania/fel kell szabadítania az összes meglévő virtuális GÉPET, és módosítania kell a telepítési parancsfájlban szereplő sorozatot, hogy a sikertelen virtuálisgép-SKU/-méretekkel kezdődjön. 
- Ha olyan meglévő elhelyezési csoportot kíván újra használni, amelyből a virtuális gépek törölve lettek, várjon, amíg a törlés teljes mértékben befejeződik, mielőtt hozzáadja a virtuális gépeket.
- Ha a késés az első prioritás, a virtuális gépeket egy közelségi elhelyezési csoportba és a teljes megoldásba helyezheti egy rendelkezésre állási zónában. Ha azonban a rugalmasság a legfontosabb prioritás, a példányokat több rendelkezésre állási zónában is eloszthatja (az egyetlen közelségi elhelyezési csoport nem terjedhet ki a zónákra).