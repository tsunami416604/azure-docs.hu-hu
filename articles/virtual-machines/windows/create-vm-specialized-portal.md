---
title: Windows virtuális gép létrehozása speciális virtuális merevlemezből az Azure Portalon
description: Hozzon létre egy új Windows virtuális gép egy virtuális merevlemezaz Azure Portalon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: 587acd37f465dda7e514b595f8451afe3d259dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039144"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Virtuális gép létrehozása virtuális merevlemezről az Azure Portal használatával

Virtuális gépet (VM) többféleképpen is létrehozhat az Azure-ban: 

- Ha már rendelkezik egy virtuális merevlemez (VHD) használni, vagy szeretné másolni a virtuális merevlemezt egy meglévő virtuális gép használata, hozhat létre egy új virtuális gépet *csatolva* a virtuális merevlemez az új virtuális gép operációs rendszer ként. 

- Létrehozhat egy új virtuális gép a virtuális gép, amely törölt. Például ha egy Azure virtuális gép, amely nem működik megfelelően, törölheti a virtuális gép, és a virtuális merevlemez használatával hozzon létre egy új virtuális gép. Használhatja fel újra ugyanazt a virtuális merevlemezt, vagy létrehozhat egy másolatot a virtuális merevlemezről egy pillanatkép létrehozásával, majd egy új felügyelt lemez létrehozásával a pillanatképből. Bár egy pillanatkép létrehozása néhány további lépést vesz igénybe, megőrzi az eredeti virtuális merevlemezt, és tartalékot biztosít.

- Vegyünk egy klasszikus virtuális gép, és a virtuális merevlemez segítségével hozzon létre egy új virtuális gép, amely az Erőforrás-kezelő telepítési modell és a felügyelt lemezek. A legjobb eredmény érdekében **állítsa le** a klasszikus virtuális gép az Azure Portalon a pillanatkép létrehozása előtt.
 
- Létrehozhat egy Azure virtuális gép egy helyszíni virtuális merevlemez feltöltésével a helyszíni virtuális merevlemez, és csatolja egy új virtuális gép. A PowerShell vagy egy másik eszköz segítségével feltöltheti a virtuális merevlemezt egy tárfiókba, majd hozzon létre egy felügyelt lemezt a virtuális merevlemezről. További információt a [Speciális virtuális merevlemez feltöltése](create-vm-specialized.md#option-2-upload-a-specialized-vhd)című témakörben talál. 

Ne használjon speciális lemezt, ha több virtuális gépet szeretne létrehozni. Ehelyett nagyobb központi telepítések esetén [hozzon létre egy lemezképet,](capture-image-resource.md) majd [használja ezt a lemezképet több virtuális gép létrehozásához.](create-vm-generalized-managed.md)

Azt javasoljuk, hogy korlátozza az egyidejű központi telepítések számát 20 virtuális gépre egyetlen pillanatképből vagy virtuális merevlemezből. 

## <a name="copy-a-disk"></a>Lemez másolása

Hozzon létre egy pillanatképet, majd hozzon létre egy lemezt a pillanatképből. Ez a stratégia lehetővé teszi, hogy az eredeti virtuális merevlemezt tartalékként tartsa:

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali menü **Minden szolgáltatás**lehetőséget.
2. A **Minden szolgáltatás** keresőmezőbe írja be a **lemezeket,** majd válassza a **Lemezek** lehetőséget az elérhető lemezek listájának megjelenítéséhez.
3. Válassza ki a használni kívánt lemezt. Megjelenik a lemez **Lemez** lapja.
4. A lap tetején található menüben válassza a **Pillanatkép létrehozása lehetőséget.** 
5. Adja meg a pillanatkép **nevét.**
6. Válasszon **erőforráscsoportot** a pillanatképhez. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat.
7. A **Fióktípus mezőben**válassza a **Standard (HDD)** vagy **a Premium (SSD)** tároló lehetőséget.
8. Ha elkészült, válassza a **Létrehozás** gombot a pillanatkép létrehozásához.
9. A pillanatkép létrehozása után válassza az **Erőforrás létrehozása lehetőséget** a bal oldali menüben.
10. A keresőmezőbe írja be a **felügyelt lemezt,** és válassza a **listából** a Felügyelt lemezek elemet.
11. A **Kezelt lemezek** lapon válassza a **Létrehozás gombot.**
12. Adja meg a lemez **nevét.**
13. Válasszon **erőforráscsoportot** a lemezhez. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat. Ez a beállítás is használják, mint az erőforráscsoport, ahol a virtuális gép a lemezről.
14. A **Fióktípus mezőben**válassza a **Standard (HDD)** vagy **a Premium (SSD)** tároló lehetőséget.
15. A **Forrástípusban**győződjön meg arról, hogy a **Pillanatkép** ki van jelölve.
16. A **Forrás pillanatkép** legördülő legördülő menüben válassza ki a használni kívánt pillanatképet.
17. Szükség szerint végezs el minden egyéb módosítást, majd a Lemez létrehozásához válassza a **Létrehozás** gombot.

## <a name="create-a-vm-from-a-disk"></a>Virtuális gép létrehozása lemezről

Miután rendelkezik a használni kívánt felügyelt lemez virtuális merevlemezével, létrehozhatja a virtuális gép a portálon:

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali menü **Minden szolgáltatás**lehetőséget.
2. A **Minden szolgáltatás** keresőmezőbe írja be a **lemezeket,** majd válassza a **Lemezek** lehetőséget az elérhető lemezek listájának megjelenítéséhez.
3. Válassza ki a használni kívánt lemezt. Megnyílik a lemez **Lemez** lapja.
4. Az **Áttekintés** lapon ellenőrizze, hogy a **LEMEZÁLLAPOT** fel van-e **kapcsolva.** Ha nem, előfordulhat, hogy le kell választania a lemezt a virtuális gépről, vagy törölnie kell a virtuális gépről a lemez felszabadításához.
4. A lap tetején található menüben válassza a **Virtuálisgép létrehozása**lehetőséget.
5. Az új virtuális gép **Alapjai** lapján adja meg a **virtuális gép nevét,** és válasszon ki egy meglévő **erőforráscsoportot,** vagy hozzon létre egy újat.
6. A **Méret csoportban**válassza **a** Méret lap eléréséhez válassza a Méret **módosítása** lehetőséget.
7. Jelöljön ki egy virtuális gép méretsorát, majd válassza **a Kijelölés lehetőséget.**
8. A **Hálózat lapon megadhatja,** hogy a portál hozza létre az összes új erőforrást, vagy kijelölhet egy meglévő **virtuális hálózatot** és **hálózatbiztonsági csoportot.** A portál mindig létrehoz egy új hálózati adaptert és nyilvános IP-címet az új virtuális géphez. 
9. A **Kezelés** lapon módosítsa a figyelési beállításokat.
10. A **Vendég konfigurációs** lapon szükség szerint adja hozzá a bővítményeket.
11. Ha elkészült, válassza **a Véleményezés + létrehozás**lehetőséget. 
12. Ha a virtuális gép konfigurációja megfelel az ellenőrzésen, válassza a **Létrehozás** lehetőséget a központi telepítés elindításához.


## <a name="next-steps"></a>További lépések

A PowerShell segítségével [virtuális merevlemezt is feltölthet az Azure-ba, és létrehozhat egy speciális virtuális gépet.](create-vm-specialized.md)


