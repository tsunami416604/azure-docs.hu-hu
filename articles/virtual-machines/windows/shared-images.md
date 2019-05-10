---
title: Megosztott VM-rendszerképek létrehozása az Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure PowerShell-lel megosztott virtuálisgép-rendszerképek létrehozása az Azure-ban
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 0a44f7d9c18e406850e2dbfb091088be0b8c2113
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235893"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Hozzon létre egy megosztott lemezkép-katalógus az Azure PowerShell használatával 

A [megosztott Képkatalógus](shared-image-galleries.md) egyszerűbbé teszi az egyéni rendszerkép megosztása a szervezetben. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Egyéni rendszerképek üzembe helyezési feladatokat, mint az alkalmazások, alkalmazás-konfigurációt és más operációsrendszer-konfigurálások konfigurálások elindíthat használható. 

A megosztott lemezkép-katalógus lehetővé teszi a VM-rendszerképeit megosztja másokkal a szervezetében, vagy régióban, az AAD-bérlőn belül is. Győződjön meg arról, azokat az elérhető, és kikre meg szeretné osztani azokat a kívánt mely régiókban, melyik képek meg szeretné osztani. Több katalógusok hozhat létre, így logikusan csoportosíthatja a megosztott-lemezképeket. 

A katalógus által biztosított teljes szerepköralapú hozzáférés-vezérlés (RBAC) legfelsőbb szintű erőforráshoz. Lemezképek lehetnek rendszerverzióval ellátott, és dönthet úgy, hogy minden lemezkép-verzió replikálása számára egy másik Azure-régióban. A katalógus csak felügyelt képekkel működik.

A megosztott Képkatalógus funkció több erőforrástípusok rendelkezik. A Microsoft fog használatával vagy ezek ebben a cikkben létrehozásához:

| Resource | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Ez a alapvető-önmagában, vagy létrehozásához használt lemezkép egy **lemezkép verziója** egy rendszerkép-katalógusában. Felügyelt lemezképek általánosított virtuális gépek jönnek létre. Egy felügyelt rendszerképet egy speciális típusú virtuális Merevlemezt, amely használható, hogy több virtuális gép, és most már használható létrehozásához megosztott kép verzió. |
| **Lemezkép-katalógusában** | Az Azure piactéren, például egy **lemezkép-katalógusában** egy adattár a kezelése és megosztása a rendszerképeket, de Ön szabályozza, ki férhet. |
| **Rendszerkép definíciójában** | Képek ugyanazon a katalóguson belül határozza meg, és a lemezkép és a belső útmutatójához követelményeivel kapcsolatos információkat. Ez magában foglalja, hogy a kép Windows vagy Linux rendszerű, kibocsátási megjegyzések és minimális és maximális memóriára vonatkozó követelményeknek. Egy lemezkép definíciója. |
| **Lemezkép verziója** | Egy **lemezkép verziója** meg használni a virtuális gép létrehozása katalógus használata során. Kép különböző verzióinak rendelkezhet saját környezetéhez szükséges módon. Használata esetén, egy felügyelt rendszerképet, például egy **lemezkép verziója** hozhat létre virtuális Gépet, a rendszerkép verziószámát új lemezeket a virtuális gép létrehozásához használt. Lemezkép verziója többször is használható. |

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

A példában ez a cikk a végrehajtásához egy meglévő felügyelt kép kell rendelkeznie. Követheti [oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure PowerShell-lel](tutorial-custom-images.md) hozhat létre egyet, ha szükséges. Ha a felügyelt rendszerkép egy adatlemezt tartalmaz, a az adatlemez mérete nem lehet több mint 1 TB.

Ha ez a cikk végighalad cserélje le az erőforráscsoportot és a virtuális gépek neveit, ahol szükséges.

[!INCLUDE [virtual-machines-common-shared-images-powershell](../../../includes/virtual-machines-common-shared-images-powershell.md)]
 
## <a name="create-vms-from-an-image"></a>Virtuális gépek létrehozása egy rendszerképből

A rendszerkép verziószámát befejeződése után létrehozhat egy vagy több új virtuális gépeket. Az egyszerűsített paraméter beállítása a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmagot, egyszerűen adja meg a lemezkép-azonosító, a rendszerkép verziószámát. 

Ez a példa létrehoz egy virtuális gép nevű *myVMfromImage*, a a *myResourceGroup* a a *USA keleti Régiójában* adatközpont.

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $imageVersion.Id `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>További lépések
[Az Azure az Image Builder (előzetes verzió)](image-builder-overview.md) segítségével automatizálhatja a lemezkép-verzió létrehozását, még akkor is használhatja, frissíteni és [hozzon létre egy új lemezkép verziója egy meglévő kép verzióból](image-builder-gallery-update-image-version.md). 

Lemezkép-katalógusában a megosztott erőforrás-sablonok használatával is létrehozhat. Nincsenek elérhető számos Azure gyorsindítási sablonok: 

- [Hozzon létre egy megosztott lemezkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Kép definíció létrehozása egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Hozzon létre egy lemezkép verziója egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Lemezkép verziója egy virtuális gép létrehozása](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Megosztott kép katalógusokkal kapcsolatos további információkért lásd: a [áttekintése](shared-image-galleries.md). Ha problémákat tapasztal, tekintse meg [hibaelhárítás megosztott kép katalógusok](troubleshooting-shared-images.md).

