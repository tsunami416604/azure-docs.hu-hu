---
title: CloudSimple VMware-megoldás – az Azure Virtual Machines áttekintése
description: Ismerje meg a CloudSimple virtuális gépeket és azok előnyeit.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f4967bbd12107bf6a04cb80537d4425c75c5f46
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812494"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple Virtual Machines – áttekintés

A CloudSimple lehetővé teszi a VMware virtuális gépek kezelését a Azure Portal.  A vSphere-fürtről származó fürt vagy erőforráskészlet az Azure-on keresztül felügyelhető az előfizetéshez való hozzárendeléssel.  A CloudSimple Virtual Machine a VMware virtuális gépek önkiszolgáló felügyeletét teszi lehetővé a Azure Portal.  

Ha CloudSimple virtuális gépet szeretne létrehozni az Azure-ból, egy virtuálisgép-sablonnak léteznie kell a saját Felhőbeli vCenter.  A sablon az operációs rendszer és az alkalmazások testreszabására szolgál.  A sablon virtuális gépe megerősíthető, hogy megfeleljen a vállalati biztonsági házirendeknek.  A sablonnal létrehozhat virtuális gépeket, és az önkiszolgáló modell használatával felhasználhatja őket a Azure Portalból.

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
* Ismerje meg, hogyan [képezhető le Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/) -előfizetése