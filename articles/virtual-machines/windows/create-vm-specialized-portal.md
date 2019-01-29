---
title: Windows virtuális gép létrehozása az Azure Portalon speciális virtuális merevlemezből |} A Microsoft Docs
description: Hozzon létre egy új Windows virtuális gép VHD-vel az Azure Portalon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: b24435501ed1312e91ebec9b9c434971dbc94b55
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163449"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Virtuális gép létrehozása VHD-fájlból az Azure portal használatával

Többféleképpen is lehet létrehozni egy virtuális gépet (VM) az Azure-ban: 

- Ha már rendelkezik egy virtuális merevlemez (VHD), vagy másolja a VHD-t használja a meglévő virtuális gépről szeretne, létrehozhat egy új virtuális gép által *csatolása* a VHD-az új virtuális gép operációsrendszer-lemezként. 

- A törölt virtuális gépek VHD-fájlból egy új virtuális Gépet is létrehozhat. Például ha egy Azure virtuális Gépen, amely nem megfelelően működik, törölje a virtuális Gépet szabadon a virtuális merevlemez használatával hozzon létre egy új virtuális Gépet. Újból felhasználhatja a virtuális Merevlemezzel, vagy a virtuális merevlemez másolatának létrehozása egy pillanatkép létrehozásához, és majd hozzon létre az új felügyelt lemez a pillanatképből. Pillanatkép létrehozása eltarthat néhány további lépést, de megőrzi az eredeti virtuális merevlemez, és egy tartalék biztosít.

- Vegyünk egy klasszikus virtuális gép, és a virtuális merevlemez használatával hozzon létre egy új virtuális Gépet, amely a Resource Manager üzemi modell és a felügyelt lemezeket használ. A legjobb eredmények **leállítása** a pillanatkép létrehozása előtt az Azure Portalon a klasszikus virtuális gép.
 
- A helyi VHD feltöltése és csatolása a új virtuális gép által a helyszíni virtuális merevlemezről hozhat létre egy Azure virtuális Gépen. Használhatja az PowerShell vagy egy másik eszköz a virtuális merevlemez feltöltéséhez egy tárfiókba, és ezután egy felügyelt lemezt hoz létre a VHD-ből. További információkért lásd: [egyéni VHD feltöltése](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Ne használjon egy speciális lemez, ha azt szeretné, hogy több virtuális gép létrehozása. Ehelyett a nagyobb telepítések esetén [hozzon létre egy rendszerképet](capture-image-resource.md) , majd [rendszerkép használatával több virtuális gép létrehozása](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Lemez másolása

Pillanatkép létrehozása, és ezután hozzon létre egy lemezt a pillanatképből. Ez a stratégia lehetővé teszi az eredeti virtuális merevlemez tartalékként megtartása:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali menüben válassza ki a **minden szolgáltatás**.
2. Az a **minden szolgáltatás** írja be a keresőmezőbe **lemezek** majd **lemezek** elérhető lemezek listájának megjelenítéséhez.
3. Válassza ki a használni kívánt lemezt. A **lemez** oldala jelenik meg a lemezen.
4. A felső menüben válassza ki a **létrehozás pillanatkép**. 
5. Adjon meg egy **neve** elkészíteni a pillanatképet.
6. Válasszon egy **erőforráscsoport** elkészíteni a pillanatképet. Használjon vagy egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
7. A **fiók típusa**, válasszon **standard szintű (HDD)** vagy **prémium (SSD)** storage.
8. Ha elkészült, válassza ki a **létrehozás** a pillanatkép létrehozásához.
9. A pillanatkép létrehozását követően válassza ki a **erőforrás létrehozása** a bal oldali menüben.
10. A Keresés mezőbe írja be a **felügyelt lemez** majd **Managed Disks** a listából.
11. Az a **Managed Disks** lapon jelölje be **létrehozás**.
12. Adjon meg egy **neve** a lemezhez.
13. Válasszon egy **erőforráscsoport** a lemezhez. Használjon vagy egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ez a beállítás lesz is az erőforráscsoport, ahol a virtuális Gépet hoz létre a lemezről.
14. A **fiók típusa**, válasszon **standard szintű (HDD)** vagy **prémium (SSD)** storage.
15. A **adatforrástípust**, győződjön meg, hogy **pillanatkép** van kiválasztva.
16. Az a **forráspillanatkép** legördülő menüben válassza ki a használni kívánt pillanatképet.
17. Ellenőrizze a beállításokat igény szerint, majd **létrehozás** létrehozza a lemezt.

## <a name="create-a-vm-from-a-disk"></a>Egy lemezt a virtuális gép létrehozása

Miután a virtuális Merevlemezt, amely a használni kívánt felügyelt lemez, a virtuális Gépet a portálon hozhat létre:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali menüben válassza ki a **minden szolgáltatás**.
2. Az a **minden szolgáltatás** írja be a keresőmezőbe **lemezek** majd **lemezek** elérhető lemezek listájának megjelenítéséhez.
3. Válassza ki a használni kívánt lemezt. A **lemez** oldala nyílik meg a lemezen.
4. Az a **áttekintése** lapon, győződjön meg arról, hogy **lemez állapota** állapottal **azokat**. Ha nem, szüksége lehet, vagy válassza le a lemezt a virtuális Gépet, vagy szabadítson fel a lemezt a virtuális gép törlése.
4. Válassza a lap tetején lévő menüben **hozzon létre virtuális gép**.
5. A a **alapjai** az új virtuális gép lap, adja meg egy **virtuális gép neve** és vagy válasszon egy meglévő **erőforráscsoport** , vagy hozzon létre egy újat.
6. A **mérete**, jelölje be **méretének módosítása** való hozzáférést a **mérete** lapot.
7. Jelöljön ki egy virtuális gép mérete sort, és válassza a **kiválasztása**.
8. Az a **hálózatkezelés** lapon engedélyezheti, hogy vagy a portálon, minden új erőforrásokat hozhat létre vagy válasszon egy meglévő **virtuális hálózati** és **hálózati biztonsági csoport**. A portál mindig létrehoz egy új hálózati adapter és a nyilvános IP-címet az új virtuális gép számára. 
9. Az a **felügyeleti** lapon, ne módosítsa a figyelési beállításokat.
10. Az a **Vendég config** lap, igény szerint adja hozzá a kiterjesztést.
11. Ha elkészült, válassza ki a **felülvizsgálat + létrehozása**. 
12. Ha a virtuális gép konfigurációjának ellenőrzése sikeres, válassza ki a **létrehozás** a üzembe helyezésének megkezdéséhez.

## <a name="next-steps"></a>További lépések

Is használhatja a PowerShell-lel [VHD feltöltése az Azure-ba, és a egy specializált virtuális gép létrehozása](create-vm-specialized.md).


