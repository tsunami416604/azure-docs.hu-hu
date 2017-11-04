---
title: "Windows virtuális gépek képek kapcsolatos |} Microsoft Docs"
description: "További tudnivalók a lemezképek Windows virtuális gépek Azure-ban való használatának módját."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="about-images-for-windows-virtual-machines"></a>A Windows virtuális gépek képek kapcsolatos
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Keresés és a Resource Manager modellt a lemezképek használatával kapcsolatos információkért lásd: [Itt](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Lemezképek használata

Az Azure PowerShell-modult, és az Azure-portál használatával a rendelkezésre álló lemezképek kezelése az Azure-előfizetéshez. Az Azure PowerShell modul további parancs lehetőségeket is kínál, így pontosan mit szeretne megtekintéséhez, vagy hajtsa végre tudja. Az Azure portál egy grafikus felhasználói Felülettel biztosít a mindennapos felügyeleti feladatok.

Íme néhány példa, amely az Azure PowerShell modullal.

* **Az összes kép lekérése**:`Get-AzureVMImage`az aktuális előfizetésben elérhető összes lemezkép listáját adja vissza: a lemezképeket és az Azure vagy partnerek által kínáltak mellett. Lehet, hogy nagy a listájában. A következő példák bemutatják, hogyan rövidebb listáját.
* **Első kép családok**:`Get-AzureVMImage | select ImageFamily` kép családok listájának lekérése jelenít meg karakterláncok **ImageFamily** tulajdonság.
* **Egy megadott termékcsalád összes képek beolvasása**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Virtuálisgép-rendszerképek található**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` ennek a parancsmagnak a DataDiskConfiguration tulajdonság, amely csak a Virtuálisgép-rendszerképek vonatkozik szűréssel működik. Ebben a példában is csak a címke és a lemezkép nevét a kimeneti szűrők.
* **Általános lemezkép mentéséhez**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **A speciális képének mentéséhez**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > A OSState paraméter megadása kötelező a Virtuálisgép-lemezképet, amely tartalmazza az operációsrendszer-lemez és adatlemezek csatolt létrehozásához. Ha nem adja meg a paramétert, a parancsmag létrehoz egy operációsrendszer-lemezképben. A paraméter azt jelzi, hogy a kép általánosítva van, vagy kifejezetten, alapján e előkészítették az operációsrendszer-lemez újbóli.

* **Lemezkép törlése**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Következő lépések
Emellett [hozzon létre egy Windows gépet az Azure portál használatával](tutorial.md).
