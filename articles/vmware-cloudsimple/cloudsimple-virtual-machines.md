---
title: Azure VMware-megoldás CloudSimple – Virtual Machines – áttekintés
description: Ismerje meg a CloudSimple virtuális gépeket és azok előnyeit.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 213ab51dae20d281a1a0e0f8ea18f4bde888e64d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877898"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple Virtual Machines – áttekintés

A CloudSimple lehetővé teszi a VMware virtuális gépek (VM-EK) kezelését a Azure Portal.  A vSphere-fürtről származó fürt vagy erőforráskészlet az Azure-on keresztül felügyelhető az előfizetéshez való hozzárendeléssel.

Ha CloudSimple virtuális gépet szeretne létrehozni az Azure-ból, egy virtuálisgép-sablonnak léteznie kell a saját Felhőbeli vCenter.  A sablon az operációs rendszer és az alkalmazások testreszabására szolgál.  A sablon virtuális gépe megerősíthető, hogy megfeleljen a vállalati biztonsági házirendeknek.  A sablon segítségével virtuális gépeket hozhat létre, majd önkiszolgáló modell használatával felhasználhatja azokat a Azure Portal.

## <a name="benefits"></a>Előnyök

A Azure Portal CloudSimple virtuális gépei olyan önkiszolgáló mechanizmust biztosítanak, amellyel a felhasználók VMware virtuális gépeket hozhatnak létre és kezelhetnek.

* CloudSimple virtuális gép létrehozása a saját Felhőbeli vCenter
* Virtuális gép tulajdonságainak kezelése
  * Lemezek hozzáadása/eltávolítása
  * Hálózati adapterek hozzáadása/eltávolítása
* A CloudSimple virtuális gép energiagazdálkodási műveletei
  * Bekapcsolás és kikapcsolás
  * Virtuális gép alaphelyzetbe állítása
* Virtuális gép törlése

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan használhatja [a VMWare virtuális gépeket az Azure](quickstart-create-vmware-virtual-machine.md) -ban
* Ismerje meg, hogyan [képezhető le Azure](azure-subscription-mapping.md) -előfizetése
