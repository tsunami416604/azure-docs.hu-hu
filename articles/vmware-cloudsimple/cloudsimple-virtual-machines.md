---
title: Azure VMware Solutions (AVS) – Virtual Machines – áttekintés
description: Ismerkedjen meg az AVS Virtual Machines szolgáltatással és azok előnyeivel.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024908"
---
# <a name="avs-virtual-machines-overview"></a>AVS Virtual Machines – áttekintés

Az AVS lehetővé teszi a VMware virtuális gépek (VM-EK) kezelését a Azure Portal. A vSphere-fürtről származó fürt vagy erőforráskészlet az Azure-on keresztül felügyelhető az előfizetéshez való hozzárendeléssel.

Az Azure-ból származó AVS virtuális gép létrehozásához egy virtuálisgép-sablonnak léteznie kell az AVS Private Cloud vCenter. A sablon az operációs rendszer és az alkalmazások testreszabására szolgál. A sablon virtuális gépe megerősíthető, hogy megfeleljen a vállalati biztonsági házirendeknek. A sablon segítségével virtuális gépeket hozhat létre, majd önkiszolgáló modell használatával felhasználhatja azokat a Azure Portal.

## <a name="benefits"></a>Előnyök

A Azure Portal rendszerhez készült AVS Virtual Machines önkiszolgáló mechanizmust biztosít a felhasználók számára a VMware virtuális gépek létrehozásához és kezeléséhez.

* AVS-alapú virtuális gép létrehozása az AVS Private Cloud vCenter
* Virtuális gép tulajdonságainak kezelése
  * Lemezek hozzáadása/eltávolítása
  * Hálózati adapterek hozzáadása/eltávolítása
* Az AVS-alapú virtuális gép energiagazdálkodási műveletei
  * Bekapcsolás és kikapcsolás
  * Virtuális gép alaphelyzetbe állítása
* Virtuális gép törlése

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan használhatja [a VMWare virtuális gépeket az Azure](quickstart-create-vmware-virtual-machine.md) -ban
* Ismerje meg, hogyan [képezhető le Azure-előfizetése](azure-subscription-mapping.md)
