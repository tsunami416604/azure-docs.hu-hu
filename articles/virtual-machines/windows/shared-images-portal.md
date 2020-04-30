---
title: Azure közös rendszerkép-katalógus létrehozása a portál használatával
description: Megtudhatja, hogyan hozhat létre és oszthat meg virtuálisgép-lemezképeket a Azure Portal használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: 1560a67d73b712ba1f295992fce4f7f1a2ae75bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100702"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Azure közös rendszerkép-katalógus létrehozása a portál használatával

A [megosztott képgyűjtemény](shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni lemezképek olyan központi telepítési feladatok indítására használhatók, mint az alkalmazások, az alkalmazások konfigurációja és más operációsrendszer-konfigurációk. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását a szervezeten belül vagy régiókban, egy HRE-bérlőn belül. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. Több gyűjteményt is létrehozhat, hogy logikailag csoportosítsa a megosztott rendszerképeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes körű szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A lemezképek telepíthetők, és eldöntheti, hogy az egyes lemezkép-verziókat egy másik Azure-régióba replikálja-e. A katalógus csak felügyelt lemezképekkel működik.

A megosztott képkatalógus funkció több erőforrástípust is tartalmaz. Ebben a cikkben a következő lépéseket fogjuk használni vagy felépíteni:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Olyan alapszintű rendszerkép, amely önmagában vagy rendszerkép- **verzió** létrehozásához használható képgyűjteményben. A felügyelt lemezképek [általánosított](shared-image-galleries.md#generalized-and-specialized-images) virtuális gépekről jönnek létre. A felügyelt rendszerkép olyan speciális VHD-típus, amellyel több virtuális gép hozható létre, és most már használható a megosztott rendszerkép-verziók létrehozásához is. |
| **Pillanatkép** | Egy virtuális merevlemez másolata, amely alkalmas a **rendszerkép verziójának**elkészítésére. A pillanatképek olyan [speciális](shared-image-galleries.md#generalized-and-specialized-images) virtuális gépekből is készíthetők (amelyek nem lettek általánosítva), majd önállóan vagy adatlemez-pillanatképekkel is használhatók a speciális rendszerkép-verziók létrehozásához.
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus **is a lemezképek** kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A lemezképek a katalógusban vannak definiálva, és a rendszerképekkel és a szervezeten belüli használattal kapcsolatos követelményekkel kapcsolatos információkat hordoznak. Olyan információkat is tartalmazhat, mint például a rendszerkép általánosított vagy specializált, az operációs rendszer, a minimális és a maximális memória követelményei, valamint a kibocsátási megjegyzések. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerkép verziója** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |

<br>


> [!IMPORTANT]
> A speciális lemezképek jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Ismert előzetes verzió korlátai** A virtuális gépeket csak speciális rendszerképekből lehet létrehozni a portál vagy az API használatával. A nem támogatja a CLI-t vagy a PowerShellt az előzetes verzióhoz.

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy általánosított virtuális gép vagy egy speciális virtuális gép pillanatképét tartalmazó meglévő felügyelt képpel. Követheti az [oktatóanyagot: hozzon létre egy Azure-beli virtuális gép egyéni rendszerképét Azure PowerShell](tutorial-custom-images.md) használatával felügyelt rendszerkép létrehozásához, vagy [hozzon létre egy pillanatképet](snapshot-copy-managed-disk.md) egy speciális virtuális géphez. A felügyelt lemezképek és Pillanatképek esetében az adatlemez mérete nem haladhatja meg az 1 TB-ot.

A cikkben végzett munka során szükség esetén cserélje le az erőforráscsoportot és a virtuális gépek nevét.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Virtuális gépek létrehozása

Most már létrehozhat egy vagy több új virtuális gépet. Ez a példa egy *myVM*nevű virtuális gépet hoz létre a *MyResourceGroup*az *USA keleti* adatközpontjában.

1. Nyissa meg a rendszerkép definícióját. Az erőforrás-szűrőt használhatja az összes elérhető képdefiníció megjelenítéséhez.
1. A rendszerkép definíciójának lapján válassza a **virtuális gép létrehozása** elemet az oldal tetején található menüből.
1. Az **erőforráscsoport**területen válassza az **új létrehozása** lehetőséget, és írja be a *myResourceGroup* nevet.
1. A **virtuális gép neve**mezőbe írja be a következőt: *myVM*.
1. A régió területen válassza az *USA keleti* **régiója**lehetőséget.
1. A **rendelkezésre állási beállítások beállításnál**hagyja meg az alapértelmezett *infrastruktúra-redundancia*hiányát.
1. A **rendszerkép értékét** a rendszer automatikusan kitölti a `latest` rendszerkép verziójával, ha a rendszerkép definíciójának oldaláról indult.
1. A **méret**beállításnál válassza ki a virtuális gép méretét az elérhető méretek listájából, majd válassza a **kiválasztás**lehetőséget.
1. Ha a rendszerkép általánosítva van, a **rendszergazdai fiók**területen meg kell adnia egy felhasználónevet, például az *azureuser* -t és a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm). Ha a rendszerkép speciális volt, a Felhasználónév és a jelszó mezők szürkén jelennek meg, mert a forrás virtuális gép felhasználóneve és jelszava van használatban.
1. Ha engedélyezni szeretné a virtuális gép távoli elérését, a **nyilvános bejövő portok**területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd az **RDP (3389)** lehetőséget a legördülő menüből. Ha nem szeretné engedélyezni a virtuális gép távoli elérését, hagyja a **nincs** kiválasztva lehetőséget a **nyilvános bejövő portokhoz**.
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

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](troubleshooting-shared-images.md)című témakört.

