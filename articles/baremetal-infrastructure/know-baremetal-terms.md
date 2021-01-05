---
title: Ismerje meg az Azure BareMetal-infrastruktúra feltételeit
description: Ismerje meg az Azure BareMetal-infrastruktúra feltételeit.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829152"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>A BareMetal-infrastruktúra feltételeinek megismerése

Ebben a cikkben néhány fontos BareMetal-kifejezést ismertetünk.

- **Változat**: két különböző Stamp-változat szerepel a BareMetal-példányok bélyegzői esetében. Minden verzió különbözik az Azure-beli virtuális gépek gazdagépének architektúrájában és közelségében:
    - **3. változat** (Rev 3): az eredeti terv.
    - **4. változat** (Rev 4): egy új kialakítás, amely az Azure-beli virtuális gépek (VM) gazdagépek közelségét biztosítja, és csökkenti az Azure-beli virtuális gépek és a BareMetal-példányok közötti késleltetést. 
    - **4,2** -es változat (Rev 4,2): a legújabb, a meglévő Rev 4 architektúrát használó BareMetal-infrastruktúra. A BareMetal-példányok a Azure Portalon keresztül érhetők el és kezelhetők.  

- **Stamp**: a Microsoft belső telepítési méretének meghatározása a BareMetal-példányok számára. A példányok üzembe helyezésének megkezdése előtt a számítási, hálózati és tárolási állványokból álló BareMetal-példányokat egy adatközpont-helyen kell üzembe helyezni. Ilyen üzembe helyezés neve BareMetal-példány bélyegzője vagy 4,2-es verzió.

- **Bérlő**: a BareMetal-példányban üzembe helyezett ügyfelek elkülönítettek egy *bérlőhöz.* A bérlő a többi bérlő hálózati, tárolási és számítási rétegében el van különítve. A különböző bérlők számára hozzárendelt tárolási és számítási egységek nem látják egymást, és nem tudnak kommunikálni egymással a BareMetal-példány Stamp szintjén. Egy ügyfél dönthet úgy is, hogy a központi telepítések különböző bérlők között vannak. Még akkor sem, ha a bérlők között nincs kommunikáció a BareMetal-példány Stamp szintjén.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan azonosíthatja és kezelheti a BareMetal-példányok egységeit a [Azure Portalon](workloads/sap/baremetal-infrastructure-portal.md)keresztül.


 