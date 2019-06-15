---
title: VMware megoldást, CloudSimple – az Azure virtual machines – áttekintés
description: További tudnivalók CloudSimple virtuális gépek és azok előnyeit.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77a2901ae2d81f42780110b1576c1f32c7ff397b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209492"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple virtual machines – áttekintés

CloudSimple lehetővé teszi, hogy a VMware virtuális gépek kezelése az Azure Portalról.  A fürt vagy a vSphere-fürtből származó erőforráskészlet felügyelt Azure-on keresztül az előfizetéshez leképezésével.  CloudSimple virtuális gép önkiszolgáló felügyelete a VMware virtuális gépek az Azure Portalról elérhetővé teszi.  

CloudSimple virtuális gép létrehozása az Azure-ból, a Virtuálisgép-sablonok a Magánfelhő vCenter léteznie kell.  A sablon segítségével testre szabhatja az operációs rendszer és alkalmazások.  A sablon virtuális gép is lehet megfelelni a vállalati biztonsági szabályzatok megerősített.  A sablon segítségével virtuális gépeket hozhat létre, és felhasználhatják az önkiszolgáló modell segítségével az Azure Portalról.

## <a name="benefits"></a>Előnyök

CloudSimple virtuális gépek az Azure Portalról egy olyan mechanizmust, az önkiszolgáló felhasználók létrehozásához és kezeléséhez a VMware virtuális gépek számára.

* A Magánfelhő vcenter CloudSimple virtuális gép létrehozása
* Virtuális gép tulajdonságainak kezelése
  * Lemezek hozzáadása/eltávolítása
  * Hálózati adapter hozzáadása/eltávolítása
* Virtuálisgép-CloudSimple Power műveletek
  * Bekapcsolása és kikapcsolása
  * Virtuális gép alaphelyzetbe állítása
* Virtuális gép törlése

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [felhasználása a VMware virtuális gépek az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* Ismerje meg, hogyan [leképezése az Azure-előfizetés](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)