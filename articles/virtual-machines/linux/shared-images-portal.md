---
title: Megosztott Azure-beli Virtuálisgép-lemezképek létrehozása a portál használatával
description: Ismerje meg, hogyan hozhat létre és oszthat meg Linux-virtuálisgép-lemezképeket az Azure Portal használatával.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 74c87d589f1c50551ac5685fe0fa126a82bffbde
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758438"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Azure megosztott képtár létrehozása a portál használatával

A [megosztott képtár](shared-image-galleries.md) leegyszerűsíti az egyéni képmegosztást a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Egyéni lemezképek segítségével rendszerindításközponti telepítési feladatok, például az alkalmazások előtöltése, alkalmazáskonfigurációk és egyéb operációs rendszer konfigurációk. 

A megosztott képtár lehetővé teszi, hogy az egyéni virtuálisgép-képeket megoszthatja a szervezet másaival, a régiókon belül vagy régiók között, egy AAD-bérlőn belül. Válassza ki, hogy mely képeket szeretné megosztani, mely régiókban szeretné elérhetővé tenni őket, és kivel szeretné megosztani őket. Több galériát is létrehozhat, így logikusan csoportosíthatja a megosztott képeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A rendszerképek verziószámba állíthatók, és kiválaszthatja, hogy az egyes lemezképek verzióit az Azure-régiók egy másik készletére replikálja.Images can be versioned, and you can choose to replicate each image version to a different set of Azure regions. A galéria csak felügyelt képekkel működik.

A Megosztott képtár szolgáltatás több erőforrástípussal rendelkezik. Mi lesz használ vagy épület ezek ebben a cikkben:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt kép** | Egyszerű kép, amely önmagában használható, vagy **képverzió** létrehozására használható egy képtárban. Felügyelt lemezképek [általános virtuális](shared-image-galleries.md#generalized-and-specialized-images) gépekből jönnek létre. A felügyelt lemezkép egy speciális típusú virtuális merevlemez, amely több virtuális gép létrehozásához használható, és most már használható megosztott lemezkép-verziók létrehozásához. |
| **Pillanatkép** | A virtuális merevlemez másolata, amely a **lemezkép-verzió**hozására használható. Pillanatképek készíthető egy [speciális](shared-image-galleries.md#generalized-and-specialized-images) virtuális gép (az egyik, hogy még nem általánosították), majd önmagában vagy adatlemezek pillanatképeivel, hozzon létre egy speciális lemezkép-verzió.
| **Képgaléria** | Az Azure Marketplace-hez hasonlóan a **képgaléria** is egy tárház a képek kezeléséhez és megosztásához, de ön szabályozza, hogy ki rendelkezik hozzáféréssel. |
| **Képdefiníció** | A képek egy galérián belül vannak definiálva, és a lemezképre és a szervezeten belüli használatra vonatkozó követelményekre vonatkozó információkat tartalmaznak. Olyan információkat is megadhat, mint például, hogy a lemezkép általános vagy specializált-e, az operációs rendszer, a minimális és maximális memóriakövetelmények, valamint a kiadási megjegyzések. Ez egy fajta kép definíciója. |
| **Kép verziószáma** | A **lemezkép-verzió** az, amit a virtuális gép létrehozásához használ katalógus használatakor. A környezetnek szüksége van egy lemezkép több verziójára is. A felügyelt lemezképhez hasonlóan, amikor egy **lemezkép-verziót** használ a virtuális gép létrehozásához, a rendszerkép-verzió a virtuális gép új lemezeinek létrehozásához használatos. A képverziótöbbször is használható. |

<br>

> [!IMPORTANT]
> A speciális képek jelenleg nyilvános előzetes verzióban jelennek meg.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
> **Ismert előnézeti korlátozások** Virtuális gépek csak a portál vagy api használatával speciális lemezképek ből hozhatók létre. A nem CLI vagy PowerShell támogatja az előzetes verziót.


## <a name="before-you-begin"></a>Előkészületek

A jelen cikkben szereplő példa végrehajtásához rendelkeznie kell egy általánosgéppel ellátott virtuális gép meglévő felügyelt rendszerképével, vagy egy speciális virtuális gép pillanatképével. Az oktatóanyag ot [követheti: Hozzon létre egy azure-beli virtuális gép egyéni lemezképét az Azure PowerShell használatával](tutorial-custom-images.md) felügyelt lemezkép létrehozásához, vagy [hozzon létre egy pillanatképet](../windows/snapshot-copy-managed-disk.md) egy speciális virtuális géphez. Felügyelt képek és pillanatképek esetén az adatlemez mérete nem lehet több 1 TB-nál.

Ebben a cikkben végzett munka során cserélje le az erőforráscsoport és a virtuális gép nevét, ahol szükséges.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Virtuális gépek létrehozása 

Most már létrehozhat egy vagy több új virtuális gépet. Ebben a példában létrehoz egy *myVMfromImage*nevű virtuális gép, a *myResourceGroup* az *USA keleti adatközpontjában.*

1. Nyissa meg a képdefiníciót. Az erőforrásszűrő segítségével megjelenítheti az összes elérhető képdefiníciót.
1. A képdefiníciólapján válassza a lap tetején található menü **Virtuális gép létrehozása lehetőséget.**
1. Az **Erőforráscsoport csoportban**válassza **az Új létrehozása lehetőséget,** és írja be a névhez a *myResourceGroup* parancsot.
1. A **Virtuálisgép neve**mezőbe írja be a *myVM*nevet.
1. A **Régió**területen válassza *az USA keleti régiója*lehetőséget.
1. A **Rendelkezésre állási beállításoknál**hagyja meg a *Nincs szükséges infrastruktúra-redundancia*alapértelmezett beállítását .
1. Ha a képdefiníció oldaláról `latest` indította el az oldalt, a **képverzió** automatikusan kitölti a képverziót.
1. A **Méret listában**válasszon egy virtuális gép méretet az elérhető méretek listájából, majd válassza a Kijelölés **lehetőséget.**
1. Ha a **rendszergazdai fiók csoportban**a forrásvirtuális gép általánosított, írja be a **felhasználónevet** és az **SSH nyilvános kulcsot.** Ha a forrás virtuális gép specializált, ezek a beállítások szürkén jelennek meg, mert a forrás virtuális gép a forrásból származó információkat használja.
1. Ha engedélyezni szeretné a virtuális gép távoli elérését, a **Nyilvános bejövő portok csoportban**válassza **a Kijelölt portok engedélyezése lehetőséget,** majd válassza az **SSH (22)** elemet a legördülő menüből. Ha nem szeretné engedélyezni a távoli hozzáférést a virtuális géphez, hagyja a **Nincs** lehetőséget a **nyilvános bejövő portokhoz.**
1. Ha végzett, válassza a **Véleményezés + létrehozás** gombot az oldal alján.
1. Miután a virtuális gép megfelel az ellenőrzésen, válassza a Lap alján a **létrehozás** lehetőséget a központi telepítés elindításához.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a **Törlés** elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

Ha törölni szeretné az egyes erőforrásokat, azokat fordított sorrendben kell törölnie. Ha például törölni szeretne egy képdefiníciót, törölnie kell az adott képből létrehozott összes képverziót.

## <a name="next-steps"></a>További lépések

A megosztott képtár erőforrást sablonok használatával is létrehozhatja. Számos Azure-gyorsindítási sablon áll rendelkezésre: 

- [Megosztott képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Képdefiníció létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Képverzió létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása a rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képgalériákról további információt az [Áttekintés című témakörben talál.](shared-image-galleries.md) Ha problémákba ütközik, olvassa el A [megosztott képgalériák hibaelhárítása című témakört.](troubleshooting-shared-images.md)

