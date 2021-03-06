---
title: Megosztott virtuálisgép-lemezképek használata méretezési csoport létrehozásához Azure PowerShell
description: Megtudhatja, hogyan hozhat létre a virtuálisgép-méretezési csoportok Azure-ban való üzembe helyezéséhez használható megosztott virtuálisgép-rendszerképeket a Azure PowerShell használatával.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: a35413bbc0169e320589459a841db59ab25a7daf
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518690"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Megosztott rendszerképek létrehozása és használata virtuálisgép-méretezési csoportokhoz a Azure PowerShell

Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. A megosztott rendszerkép-katalógus szolgáltatás nagymértékben leegyszerűsíti az Egyéni rendszerképek megosztását a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását a szervezeten belül vagy régiókban, egy HRE-bérlőn belül. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. Több gyűjteményt is létrehozhat, hogy logikailag csoportosítsa a megosztott rendszerképeket. 

A gyűjtemény egy legfelső szintű erőforrás, amely teljes körű Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít. A lemezképek telepíthetők, és eldöntheti, hogy az egyes lemezkép-verziókat egy másik Azure-régióba replikálja-e. A katalógus csak felügyelt lemezképekkel működik. 

A megosztott képkatalógus funkció több erőforrástípust is tartalmaz. 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amellyel új virtuálisgép-példányokat hozhat létre.

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy meglévő felügyelt képpel. A következő [oktatóanyagot követheti: hozzon létre és használjon egyéni rendszerképet a virtuálisgép-méretezési csoportokhoz, Azure PowerShell](tutorial-use-custom-image-powershell.md) segítségével hozzon létre egyet, ha szükséges. Ha a felügyelt lemezkép adatlemezt tartalmaz, az adatlemez mérete nem haladhatja meg az 1 TB-ot.

A cikkben végzett munka során cserélje le az erőforráscsoportot és a virtuális gépek nevét, ahol szükséges.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>Következő lépések

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerképverzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](../virtual-machines/troubleshooting-shared-images.md)című témakört.
