---
title: "Windows virtuális gép létrehozása az Azure-portálon speciális VHD-ről |} Microsoft Docs"
description: "Hozzon létre egy új Windows virtuális gép olyan virtuális merevlemezről az Azure portálon."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 33e94777047ea8a116ae6f393439521356a53509
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Virtuális gép létrehozása az egy VHD-ről az Azure portál használatával


Több módon is létrehozott egy virtuális gépet az Azure-ban. Ha már rendelkezik egy VHD-fájlt használja, vagy másolja a virtuális merevlemez és a meglévő virtuális gép használja, létrehozhat egy új virtuális Gépet a virtuális Merevlemezt az operációs rendszer lemezeként csatolásával. Ez a folyamat *csatol* a VHD-fájlt az operációs rendszer lemezeként egy új virtuális Gépet.

Létrehozhat egy új virtuális Gépet a virtuális Merevlemezt a virtuális gépek, a törölt is. Például ha egy Azure virtuális gép nem működik megfelelően, is töröl a virtuális gép, és hozzon létre egy új virtuális Gépet a virtuális merevlemez használatával. Felhasználhatja az azonos virtuális Merevlemezt, vagy létre a virtuális merevlemez másolatának pillanatkép létrehozásával, majd hozza létre egy új felügyelt lemezes a pillanatképből. Ez végrehajtja néhány további műveleteket, de biztosítja hálózati adaptere esetében megtarthatja az eredeti virtuális Merevlemezt és is lehetővé teszi egy pillanatkép visszaállni szükség esetén.

Egy helyszíni virtuális Gépre, amelyhez a virtuális gép létrehozása az Azure-ban is rendelkezik. A virtuális merevlemez feltöltéséhez, és mellékelje egy új virtuális Gépet. A virtuális merevlemez feltöltéséhez szüksége a PowerShell vagy egy másik eszköz használatával töltse fel a tárfiókot, majd hozzon létre egy felügyelt lemezes virtuális merevlemezről. További információkért lásd: [a speciális virtuális merevlemez feltöltéséhez](create-vm-specialized.md#option-2-upload-a-specialized-vhd)

Ha azt szeretné, a virtuális gép vagy a virtuális merevlemez létrehozásához használandó több virtuális géphez, majd azt ne használja ezt a módszert. Nagyobb telepítések esetén érdemes [lemezkép létrehozása](capture-image-resource.md) , majd [lemezkép segítségével több virtuális gép létrehozása](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>A lemezek másolása

Pillanatkép létrehozása, majd hozzon létre egy lemezt a pillanatképből. Ez lehetővé teszi, hogy az eredeti virtuális merevlemez megtartása vissza egy alá esik.

1. A bal oldali menüben kattintson a **összes erőforrás**.
2. Az a **mindenfajta** legördülő, vonja **válassza ki az összes** majd görgessen lefelé, és válassza a **lemezek** található a rendelkezésre álló lemezek.
3. Kattintson a lemezre, amelyet használni szeretne. A **áttekintése** lapon, a lemez megnyitása.
4. A – áttekintés oldalra, a menü felső részén kattintson **+ létrehozás pillanatkép**. 
5. Adjon meg egy nevet a pillanatkép.
6. Válasszon egy **erőforráscsoport** a pillanatkép. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
7. Válassza ki, hogy (HDD) standard vagy Premium (SDD) tárolási.
8. Amikor elkészült, kattintson a **létrehozása** a pillanatkép létrehozásához.
9. A pillanatkép létrehozása után kattintson a **+ hozzon létre egy erőforrást** a bal oldali menüben.
10. Írja be a keresési sávon **felügyelt lemezes** válassza **kezelt lemezek** a listából.
11. Az a **kezelt lemezek** kattintson **létrehozása**.
12. Írjon be egy nevet a lemeznek.
13. Válasszon egy **erőforráscsoport** a lemezhez. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ez lesz az erőforráscsoport ahol a virtuális Gépet hoz létre a lemezről.
14. Válassza ki, hogy (HDD) standard vagy Premium (SDD) tárolási.
15. A **adatforrástípust**, győződjön meg arról, hogy **pillanatkép** van kiválasztva.
16. Az a **forrás pillanatkép** legördülő listából válassza ki a használni kívánt pillanatképet.
17. Ellenőrizze a beállításokat, szükség szerint, és kattintson a **létrehozása** hozhat létre a lemezt.

## <a name="create-a-vm-from-a-disk"></a>Virtuális gép létrehozása lemez

Miután a felügyelt lemezes virtuális Merevlemezt szeretne használni, a virtuális Gépet a portálon is létrehozhat.

1. A bal oldali menüben kattintson a **összes erőforrás**.
2. Az a **mindenfajta** legördülő, vonja **válassza ki az összes** majd görgessen lefelé, és válassza a **lemezek** található a rendelkezésre álló lemezek.
3. Kattintson a lemezre, amelyet használni szeretne. A **áttekintése** lapon, a lemez megnyitása.
A – áttekintés oldalra, ügyeljen arra, hogy **lemez állapota** jelenik meg, **Unattached**. Ha nem, előfordulhat, hogy szeretné vagy válassza le a lemezt a virtuális Gépet, vagy törölje a virtuális gép felszabadítja-e a lemez.
4. Kattintson az ablaktábla tetején a menü **+ létrehozás VM**.
5. Az a **alapjai** lapon a új virtuális gép számára adjon meg egy nevet, és válasszon ki vagy egy meglévő erőforráscsoportot vagy hozzon létre egy újat.
6. Az a **mérete** lapon, válassza ki a lap egy virtuális gép méretét, majd kattintson a **válasszon**.
7. Az a **beállítások** lap, vagy hogy minden új erőforrás létrehozása a portálon, vagy válasszon egy meglévő **virtuális hálózati** és **hálózati biztonsági csoport**. A portál mindig hozzon létre egy új hálózati adapter és a nyilvános IP-címet az új virtuális gép számára. 
8. A figyelési lehetőségek végezze el a módosításokat, és szükség szerint adja hozzá a kiterjesztést.
9. Ha elkészült, kattintson az **OK** gombra. 
10. Ha a Virtuálisgép-konfiguráció ellenőrzése sikeres, kattintson a **OK** a telepítés elindításához.

## <a name="next-steps"></a>További lépések

Használhatja a PowerShell [virtuális merevlemez feltöltése az Azure-ba, és hozzon létre egy speciális virtuális Gépet](create-vm-specialized.md).


