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
ms.date: 12/11/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 1e51d55fcc20230dff20ca18d58a13e6ed32905b
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192204"
---
# <a name="preview-create-a-shared-image-gallery-with-azure-powershell"></a>Előzetes verzió: Hozzon létre egy megosztott lemezkép-katalógus az Azure PowerShell használatával 

A [megosztott Képkatalógus](shared-image-galleries.md) egyszerűbbé teszi az egyéni rendszerkép megosztása a szervezetben. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Egyéni rendszerképek üzembe helyezési feladatokat, mint az alkalmazások, alkalmazás-konfigurációt és más operációsrendszer-konfigurálások konfigurálások elindíthat használható. 

A megosztott lemezkép-katalógus lehetővé teszi a VM-rendszerképeit megosztja másokkal a szervezetében, vagy régióban, az AAD-bérlőn belül is. Győződjön meg arról, azokat az elérhető, és kikre meg szeretné osztani azokat a kívánt mely régiókban, melyik képek meg szeretné osztani. Több katalógusok hozhat létre, így logikusan csoportosíthatja a megosztott-lemezképeket. 

A katalógus által biztosított teljes szerepköralapú hozzáférés-vezérlés (RBAC) legfelsőbb szintű erőforráshoz. Lemezképek lehetnek rendszerverzióval ellátott, és dönthet úgy, hogy minden lemezkép-verzió replikálása számára egy másik Azure-régióban. A katalógus csak felügyelt képekkel működik.

A megosztott Képkatalógus funkció több erőforrástípusok rendelkezik. A Microsoft fog használatával vagy ezek ebben a cikkben létrehozásához:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Ez a alapvető-önmagában, vagy létrehozásához használt lemezkép egy **lemezkép verziója** egy rendszerkép-katalógusában. Felügyelt lemezképek általánosított virtuális gépek jönnek létre. Egy felügyelt rendszerképet egy speciális típusú virtuális Merevlemezt, amely használható, hogy több virtuális gép, és most már használható létrehozásához megosztott kép verzió. |
| **Lemezkép-katalógusában** | Az Azure piactéren, például egy **lemezkép-katalógusában** egy adattár a kezelése és megosztása a rendszerképeket, de Ön szabályozza, ki férhet. |
| **Rendszerkép definíciójában** | Képek ugyanazon a katalóguson belül határozza meg, és a lemezkép és a belső útmutatójához követelményeivel kapcsolatos információkat. Ez magában foglalja, hogy a kép Windows vagy Linux rendszerű, kibocsátási megjegyzések és minimális és maximális memóriára vonatkozó követelményeknek. Egy lemezkép definíciója. |
| **Lemezkép verziója** | Egy **lemezkép verziója** meg használni a virtuális gép létrehozása katalógus használata során. Kép különböző verzióinak rendelkezhet saját környezetéhez szükséges módon. Használata esetén, egy felügyelt rendszerképet, például egy **lemezkép verziója** hozhat létre virtuális Gépet, a rendszerkép verziószámát új lemezeket a virtuális gép létrehozásához használt. Lemezkép verziója többször is használható. |


## <a name="before-you-begin"></a>Előkészületek

A példában ez a cikk a végrehajtásához egy meglévő felügyelt kép kell rendelkeznie. Követheti [oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure PowerShell-lel](tutorial-custom-images.md) hozhat létre egyet, ha szükséges. Ha ez a cikk végighalad cserélje le az erőforráscsoportot és a virtuális gépek neveit, ahol szükséges.

[!INCLUDE [virtual-machines-common-shared-images-ps](../../../includes/virtual-machines-common-shared-images-powershell.md)]
 
## <a name="create-vms-from-an-image"></a>Virtuális gépek létrehozása egy rendszerképből

Ha a rendszerkép verziószámát complte, létrehozhat egy vagy több új virtuális gépeket. Az egyszerűsített paraméter beállítása a [New-AzureRMVM]() parancsmagot, egyszerűen adja meg a lemezkép-azonosító, a rendszerkép verziószámát. 

Ez a példa létrehoz egy virtuális gép nevű *myVMfromImage*, a a *myResourceGroup* a a *USA keleti Régiójában* adatközpont.

```azurepowershell-interactive
New-AzureRmVm `
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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancsmaggal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myGalleryRG
```

## <a name="next-steps"></a>További lépések

Lemezkép-katalógusában a megosztott erőforrás-sablonok használatával is létrehozhat. Nincsenek elérhető számos Azure gyorsindítási sablonok: 

- [Hozzon létre egy megosztott lemezkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Kép definíció létrehozása egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Hozzon létre egy lemezkép verziója egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Lemezkép verziója egy virtuális gép létrehozása](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Megosztott kép katalógusokkal kapcsolatos további információkért lásd: a [áttekintése](shared-image-galleries.md). Ha problémákat tapasztal, tekintse meg [hibaelhárítás megosztott kép katalógusok](troubleshooting-shared-images.md).

