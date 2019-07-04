---
title: Megosztott Azure virtuálisgép-rendszerképek létrehozása a portál használatával Linux rendszeren |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és oszthat meg virtuálisgép-lemezképek az Azure portal használata.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e5518d019800d2d194369225a060b9b728e21520
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465451"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Hozzon létre egy megosztott lemezkép-katalógus az Azure portal használatával

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


## <a name="before-you-begin"></a>Előkészületek

A példában ez a cikk a végrehajtásához egy meglévő felügyelt kép kell rendelkeznie. Követheti [oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure PowerShell-lel](tutorial-custom-images.md) hozhat létre egyet, ha szükséges. Ha a felügyelt rendszerkép egy adatlemezt tartalmaz, a az adatlemez mérete nem lehet több mint 1 TB.

Ha ez a cikk végighalad cserélje le az erőforráscsoportot és a virtuális gépek neveit, ahol szükséges.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Virtuális gépek létrehozása egy rendszerképből

A rendszerkép verziószámát befejeződése után létrehozhat egy vagy több új virtuális gépeket. 

> [!IMPORTANT]
> A portál nem használhat egy másik azure-bérlőhöz egy rendszerképből egy virtuális gép üzembe helyezéséhez. Virtuális gép létrehozása a bérlők között megosztott kép, kell használnia a [Azure CLI-vel](shared-images.md#create-a-vm) vagy [Powershell](../windows/shared-images.md#create-vms-from-an-image).


Ez a példa létrehoz egy virtuális gép nevű *myVMfromImage*, a a *myResourceGroup* a a *USA keleti Régiójában* adatközpont.

1. A lemezkép-verzió oldalán válassza **hozzon létre virtuális gép** az oldal felső részén látható menüben.
1. A **erőforráscsoport**válassza **új létrehozása** , és írja be *myResourceGroup* neve.
1. A **virtuális gép neve**, típus *myVM*.
1. A **régió**válassza *USA keleti Régiójában*.
1. A **rendelkezésre állási beállítások**, hagyja bejelölve az alapértelmezett *szükséges infrastruktúra redundancia*.
1. Az érték **kép** kell automatikusan kitölti a lapon, a lemezkép-verzió elindítását.
1. A **mérete**, válassza ki a virtuális gép méretét az elérhető méretek a listából, és kattintson a "Kiválasztás".
1. A **rendszergazdai fiók**válassza **jelszó** vagy **nyilvános SSH-kulcs**, majd adja meg az adatokat.
1. Ha szeretne távoli hozzáférés lehetővé tételéhez a virtuális gép a **nyilvános bejövő portok**, válassza a **lehetővé teszi a kiválasztott portok** , majd **SSH (22)** a legördülő listából. Ha nem szeretné a távoli hozzáférés lehetővé tételéhez a virtuális Gépet, ne **nincs** számára kiválasztott **nyilvános bejövő portok**.
1. Amikor elkészült, válassza ki a **felülvizsgálat + létrehozása** gombra a lap alján.
1. Miután a virtuális gép ellenőrzése sikeres, válassza ki **létrehozás** az üzembe helyezés indítása az oldal alján.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a **Törlés** elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

Ha törölni szeretné az egyes erőforrásokat, törölheti őket fordított sorrendben szüksége. Például egy rendszerkép definíciójában törléséhez szüksége lemezképből létrehozott összes lemezkép törlése.

## <a name="next-steps"></a>További lépések

Lemezkép-katalógusában a megosztott erőforrás-sablonok használatával is létrehozhat. Nincsenek elérhető számos Azure gyorsindítási sablonok: 

- [Hozzon létre egy megosztott lemezkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Kép definíció létrehozása egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Hozzon létre egy lemezkép verziója egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Lemezkép verziója egy virtuális gép létrehozása](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Megosztott kép katalógusokkal kapcsolatos további információkért lásd: a [áttekintése](shared-image-galleries.md). Ha problémákat tapasztal, tekintse meg [hibaelhárítás megosztott kép katalógusok](troubleshooting-shared-images.md).

