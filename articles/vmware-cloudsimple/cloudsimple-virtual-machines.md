---
title: Virtuális gépek – áttekintés
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerje meg a CloudSimple virtuális gépeket és azok előnyeit.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024908"
---
# <a name="cloudsimple-virtual-machines-overview"></a>A CloudSimple virtuális gépek áttekintése

A CloudSimple lehetővé teszi a VMware virtuális gépek (VM-ek) kezelését az Azure Portalról.  A virtuális gömbfürtből származó fürt vagy erőforráskészlet kezelése az Azure-on keresztül történik az előfizetéshez való hozzárendeléssel.

CloudSimple virtuális gép létrehozásához az Azure-ból, a virtuális gép sablon léteznie kell a private cloud vCenter.  A sablon az operációs rendszer és az alkalmazások testreszabására szolgál.  A sablon virtuális gép lehet megerősített, hogy megfeleljen a vállalati biztonsági házirendek.  A sablon segítségével virtuális gépeket hozhat létre, majd az Azure Portalon egy önkiszolgáló modell használatával használhatja fel őket.

## <a name="benefits"></a>Előnyök

Az Azure Portalcloudtól származó CloudSimple virtuális gépek önkiszolgáló mechanizmust biztosítanak a felhasználók számára a VMware virtuális gépek létrehozásához és kezeléséhez.

* CloudSimple virtuális gép létrehozása a privát felhőbeli vCenteren
* Virtuálisgép-tulajdonságok kezelése
  * Lemezek hozzáadása/eltávolítása
  * Hálózati adapterek hozzáadása/eltávolítása
* A CloudSimple virtuális gép energiagazdálkodási műveletei
  * Be- és kikapcsolás
  * Virtuális gép alaphelyzetbe állítása
* Virtuális gép törlése

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [használható vmware virtuális gépek az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* Az [Azure-előfizetés leképezése](azure-subscription-mapping.md)
