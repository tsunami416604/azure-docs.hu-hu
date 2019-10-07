---
title: Beágyazott virtualizáció engedélyezése sablonbeli virtuális gépen Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a beágyazott virtualizálás sablonbeli virtuális gépen Azure Lab Services-ben.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981994"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Beágyazott virtualizálás engedélyezése sablonbeli virtuális gépen Azure Lab Services
Ez a cikk bemutatja, hogyan állíthatja be a beágyazott virtualizációt egy sablonban lévő virtuális gépen Azure Lab Servicesban. A beágyazott virtualizálás Azure Lab Services van használatban, ha az osztályban lévő tanulók több gépet igényelnek.
 
## <a name="considerations"></a>Megfontolandó szempontok
A tesztkörnyezet beágyazott virtualizálás előtti beállítása előtt néhány dolgot figyelembe kell venni.

- Új Labor létrehozásakor válassza a **közepes (beágyazott virtualizálás)** vagy a **nagyméretű** méretek lehetőséget a virtuális gép méretétől függően. Ezek a virtuálisgép-méretek támogatják a beágyazott virtualizálás szolgáltatást. 
- Válasszon egy olyan méretet, amely a gazdagép és az ügyfél virtuális gépei számára is jó teljesítményt nyújt.  Ne feledje, hogy a virtualizálás használatakor a kiválasztott méretnek megfelelőnek kell lennie a nem csak egy géphez, hanem a gazdagéphez és a párhuzamosan futtatandó ügyfélszámítógépekhez is.
- Az ügyfél virtuális gépei nem férhetnek hozzá az Azure-erőforrásokhoz, például az Azure-beli virtuális hálózat DNS-kiszolgálóihoz.
- A gazdagép virtuális gépén a telepítőnek engedélyeznie kell, hogy az ügyfélszámítógép internetkapcsolattal rendelkezzen. 
- Az ügyfél virtuális gépei független gépekként vannak engedélyezve. A Microsoft-üzemeltetési rendszerek és termékek licencelésével kapcsolatos információkért tekintse meg a [Microsoft licencelését](https://www.microsoft.com/licensing/default) ismertető témakört. A sablon számítógépének beállítása előtt a használatban lévő egyéb szoftverek licencelési szerződéseit is megvizsgálhatja.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Beágyazott virtualizálás engedélyezése sablonos virtuális gépen
Az ebben a szakaszban ismertetett lépések a Windows Server 2016 vagy a Windows Server 2019 rendszerhez készült beágyazott virtualizálás beállítására összpontosítanak. A Hyper-V használatával parancsfájlt kell beállítani a sablonhoz. Automatizált megoldásért lásd: parancsfájlok a [labor Services Hyper-V parancsfájljaiban](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). A következő lépések végigvezetik a parancsfájl használatának lépésein.

1. Ha az Internet Explorert használja, előfordulhat, hogy `https://github.com` értéket kell hozzáadnia a megbízható helyek listájához. 
    1. Nyissa meg az Internet Explorert.
    1. Válassza a fogaskerék ikont, majd az **Internetbeállítások**lehetőséget.  
    1. Amikor megjelenik az **Internetbeállítások** párbeszédpanel, válassza a **Biztonság**, majd a **megbízható helyek**lehetőséget, kattintson a **helyek** gombra.
    1. Amikor megjelenik a **megbízható helyek** párbeszédpanel, vegye fel `https://github.com` elemet a megbízható webhelyek listára, és válassza a **Bezárás**lehetőséget.

        ![Megbízható helyek](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Töltse le a git-tárház fájljait az alábbi lépésekben leírtak szerint.  Azt is megteheti, hogy a git-tárház klónozása [https://github.com/Azure/azure-devtestlab.git -](https://github.com/Azure/azure-devtestlab.git)ből történik. 
    1. Lépjen a [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Kattintson a ***klón vagy a letöltés** gombra.
    1. Kattintson a **zip letöltése**elemre.
    1. ZIP-fájl kibontása
1. Indítsa el a **PowerShellt** **rendszergazdai** módban.
1. A PowerShell ablakban navigáljon a letöltött parancsfájlt tartalmazó mappához. Ha az adattár fájljainak felső mappájából navigál, a parancsfájl `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` helyen található.
1. Előfordulhat, hogy a parancsfájl sikeres futtatásához módosítania kell a végrehajtási házirendet. Futtassa a következő parancsot:
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. Futtassa a szkriptet:
    
    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Előfordulhat, hogy a parancsfájl újraindítását igényli. Kövesse a parancsfájl utasításait, és futtassa újra a parancsfájlt, amíg a **parancsfájl nem fejeződött** be a kimenetben.
1. Ne felejtse el alaphelyzetbe állítani a végrehajtási házirendet. Futtassa a következő parancsot: 

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Összegzés
Most a sablon-számítógép készen áll a Hyper-V virtuális gépek létrehozására. A Hyper-V virtuális gépek létrehozásával kapcsolatos útmutatásért tekintse meg [a virtuális gép létrehozása a Hyper-v-ben](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) című témakört. Továbbá tekintse meg a [Microsoft próbaverzióját](https://www.microsoft.com/evalcenter/) a rendelkezésre álló operációs rendszerek és szoftverek ellenőrzéséhez.  

## <a name="next-steps"></a>További lépések 
Lásd az alábbi cikkeket:

- [Beágyazott virtualizálás engedélyezése Azure-beli virtuális gépen](../../virtual-machines/windows/nested-virtualization.md) 
- [A Hyper-V szerepkör telepítése a Windows Serveren](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [A Lab Services Hyper-V parancsfájljai](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
