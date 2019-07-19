---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850326"
---
A virtuális gépek egyetlen régióban való elhelyezése csökkenti a példányok közötti fizikai távolságot. Ha egyetlen rendelkezésre állási zónába helyezi őket, az is fizikailag szorosabban fog működni. Mivel azonban az Azure-lábnyom növekszik, az egyetlen rendelkezésre állási zóna több fizikai adatközpontra is kiterjedhet, ami az alkalmazást érintő hálózati késést eredményezhet. 

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy közelségi elhelyezési csoporton belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.


- Kis késleltetés az önálló virtuális gépek között.
- Kis késleltetés egyetlen rendelkezésre állási csoportba tartozó virtuális gépek és egy virtuálisgép-méretezési csoport között. 
- Kis késleltetés az önálló virtuális gépek, több rendelkezésre állási csoportba tartozó virtuális gépek vagy több méretezési csoport között. A többrétegű alkalmazások összevonásához több számítási erőforrás is tartozhat egyetlen elhelyezési csoportban. 
- Kis késleltetés több alkalmazás-réteg között különböző típusú hardvereszközök használatával. Például, ha a hátteret a rendelkezésre állási csoportban található M-sorozattal és a D sorozatú példányon lévő előtérrel futtatja, egy méretezési csoport egyetlen közelségi elhelyezési csoportjában.

> [!IMPORTANT]
> A közelségi elhelyezési csoportok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A közelségi elhelyezési csoportok ezekben a régiókban nem érhetők el az előzetes verzió alatt: **Kelet-japán**, **Kelet-Ausztrália** és **Közép-India**.


## <a name="best-practices"></a>Ajánlott eljárások 
- A legkisebb késés érdekében használja a közeli elhelyezési csoportokat a gyorsított hálózatkezeléssel együtt. További információ: [Linux rendszerű virtuális gép gyorsított hálózatkezeléssel való létrehozása](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [Windows rendszerű virtuális gép létrehozása gyorsított hálózatkezeléssel](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Minden virtuálisgép-méret üzembe helyezése egyetlen Resource Manager-sablonban. Ha el szeretné kerülni, hogy a hardver nem támogatja a szükséges összes virtuálisgép-t és méretet, vegye fel az összes alkalmazási szintet egyetlen sablonba, hogy azok mind egyszerre legyenek telepítve.
- Ha az üzembe helyezést a PowerShell, a CLI vagy az SDK használatával végezi el, lefoglalási hibaüzenetet `OverconstrainedAllocationRequest`kaphat. Ebben az esetben le kell állítania/fel kell szabadítania az összes meglévő virtuális GÉPET, és módosítania kell a telepítési parancsfájlban szereplő sorozatot, hogy a sikertelen virtuálisgép-SKU/-méretekkel kezdődjön. 
- Ha olyan meglévő elhelyezési csoportot kíván újra használni, amelyből a virtuális gépek törölve lettek, várjon, amíg a törlés teljes mértékben befejeződik, mielőtt hozzáadja a virtuális gépeket.
- Ha a késés az első prioritás, a virtuális gépeket egy közelségi elhelyezési csoportba és a teljes megoldásba helyezheti egy rendelkezésre állási zónában. Ha azonban a rugalmasság a legfontosabb prioritás, a példányokat több rendelkezésre állási zónában is eloszthatja (az egyetlen közelségi elhelyezési csoport nem terjedhet ki a zónákra).