---
title: Megosztott Azure-beli virtuálisgép-rendszerképek létrehozása Linuxra a portál használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és oszthat meg virtuálisgép-lemezképeket a Azure Portal használatával.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 8be4890f01ae2c0d893bb7c45f29c6f8178844f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082115"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Megosztott rendszerkép létrehozása a Azure Portal használatával

A [megosztott képgyűjtemény](shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni lemezképek olyan központi telepítési feladatok indítására használhatók, mint az alkalmazások, az alkalmazások konfigurációja és más operációsrendszer-konfigurációk. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását a szervezeten belül vagy régiókban, egy HRE-bérlőn belül. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. Több gyűjteményt is létrehozhat, hogy logikailag csoportosítsa a megosztott rendszerképeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes körű szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A lemezképek telepíthetők, és eldöntheti, hogy az egyes lemezkép-verziókat egy másik Azure-régióba replikálja-e. A katalógus csak felügyelt lemezképekkel működik.

A megosztott képkatalógus funkció több erőforrástípust is tartalmaz. Ebben a cikkben a következő lépéseket fogjuk használni vagy felépíteni:

| Resource | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Ez egy alapszintű rendszerkép, amely önmagában vagy rendszerkép- **verzió** létrehozásához használható egy képgyűjteményben. A felügyelt lemezképek általánosított virtuális gépekről jönnek létre. A felügyelt rendszerkép olyan speciális VHD-típus, amellyel több virtuális gép hozható létre, és most már használható a megosztott rendszerkép-verziók létrehozásához is. |
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus is a lemezképek kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A lemezképek a katalógusban vannak definiálva, és a rendszerképekkel és a belső használattal kapcsolatos követelményekkel rendelkeznek. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerkép verziója** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |


## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy meglévő felügyelt képpel. A következő [oktatóanyagot végezheti el: Hozzon létre egy egyéni rendszerképet egy Azure-](tutorial-custom-images.md) beli virtuális gépről, és hozzon létre egy Azure PowerShell, ha szükséges. Ha a felügyelt lemezkép adatlemezt tartalmaz, az adatlemez mérete nem haladhatja meg az 1 TB-ot.

A cikkben végzett munka során szükség esetén cserélje le az erőforráscsoportot és a virtuális gépek nevét.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Virtuális gépek létrehozása rendszerképből

Miután a rendszerkép verziója elkészült, létrehozhat egy vagy több új virtuális gépet. 

> [!IMPORTANT]
> A portálon nem lehet virtuális gépet üzembe helyezni egy másik Azure-bérlő lemezképéről. Ha a bérlők között megosztott rendszerképből szeretne virtuális gépet létrehozni, az [Azure CLI](shared-images.md#create-a-vm) -t vagy [](../windows/shared-images.md#create-vms-from-an-image)a PowerShellt kell használnia.


Ez a példa egy *myVMfromImage*nevű virtuális gépet hoz létre az *USA keleti* adatközpontjának *myResourceGroup* .

1. A rendszerkép verziójának lapján válassza a **virtuális gép létrehozása** elemet az oldal tetején található menüből.
1. Az **erőforráscsoport**területen válassza az **új létrehozása** lehetőséget, és írja be a *myResourceGroup* nevet.
1. A **virtuális gép neve**mezőbe írja be a következőt: *myVM*.
1. A **régió**területen válassza az *USA keleti*régiója lehetőséget.
1. A **rendelkezésre állási beállítások beállításnál**hagyja meg az alapértelmezett *infrastruktúra*-redundancia hiányát.
1. A **rendszerkép** értékét automatikusan ki kell tölteni, ha a képverzió oldaláról indult el.
1. A **méret**beállításnál válassza ki a virtuális gép méretét az elérhető méretek listájából, majd kattintson a "kiválasztás" gombra.
1. A **rendszergazdai fiók**területen válassza a **jelszó** vagy az **SSH nyilvános kulcs**lehetőséget, majd adja meg az adatait.
1. Ha engedélyezni szeretné a virtuális gép távoli elérését, a **nyilvános bejövő portok**területen válassza a **kiválasztott portok engedélyezése** lehetőséget, majd válassza az **SSH (22)** lehetőséget a legördülő menüből. Ha nem szeretné engedélyezni a virtuális gép távoli elérését, hagyja a **nincs** kiválasztva lehetőséget a **nyilvános bejövő portokhoz**.
1. Ha elkészült, kattintson a lap alján található **felülvizsgálat + létrehozás** gombra.
1. Miután a virtuális gép áthaladt az ellenőrzésen, válassza a **Létrehozás** lehetőséget a lap alján a telepítés elindításához.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a **Törlés** elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

Ha törölni szeretné az egyes erőforrásokat, akkor azokat fordított sorrendben kell törölnie. A rendszerkép definíciójának törléséhez például törölnie kell az adott rendszerképből létrehozott összes rendszerkép-verziót.

## <a name="next-steps"></a>További lépések

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Megosztott Képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerkép-verzió létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képtárakkal kapcsolatos további információkért tekintse [](shared-image-galleries.md)meg az áttekintést. Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](troubleshooting-shared-images.md)című témakört.

