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
ms.openlocfilehash: 59f5438bbedea2ff7793a5df95f1d3df58b9bba6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576985"
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
* Ismerje meg, hogyan [leképezése az Azure-előfizetés](https://docs.azure.cloudsimple.com/azuresubscriptionmapping/)