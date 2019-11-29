---
title: Beágyazott virtualizáció engedélyezése sablonbeli virtuális gépen Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre több virtuális géppel rendelkező sablonos virtuális gépet a rendszeren belül.  Más szóval engedélyezze a beágyazott virtualizációt a sablonban lévő virtuális gépen Azure Lab Servicesban.
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
ms.openlocfilehash: 64097a5b3b62bcd5a84f4472a844bb95cf24cd6f
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555073"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Beágyazott virtualizálás engedélyezése sablonbeli virtuális gépen Azure Lab Services

Jelenleg a Azure Lab Services lehetővé teszi egy sablonban lévő virtuális gép beállítását egy laborban, és az egyes felhasználók számára egyetlen másolatot készíthet. Ha Ön olyan professzor, amely hálózatkezelési, biztonsági vagy informatikai osztályokat tanít, előfordulhat, hogy minden tanulót egy olyan környezettel kell megadnia, amelyben több virtuális gép is tud kommunikálni egymással hálózaton keresztül.

A beágyazott virtualizálás lehetővé teszi, hogy több virtuális gépre kiterjedő környezetet hozzon létre a labor sablonjának virtuális gépén. A sablon közzétételével minden felhasználó számára elérhetővé válik a laborban egy virtuális gép, amelyben több virtuális gép van beállítva.  Ez a cikk bemutatja, hogyan állíthatja be a beágyazott virtualizálás szolgáltatást egy Azure Lab Services található sablonos gépen.

## <a name="what-is-nested-virtualization"></a>Mi az a beágyazott virtualizálás?

A beágyazott virtualizálás lehetővé teszi, hogy virtuális gépeket hozzon létre egy virtuális gépen belül. A beágyazott virtualizálás a Hyper-V-n keresztül történik, és csak Windows rendszerű virtuális gépeken érhető el.

A beágyazott virtualizálás szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Beágyazott virtualizálás az Azure-ban](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Beágyazott virtualizálás engedélyezése Azure-beli virtuális gépen](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Megfontolandó szempontok

A tesztkörnyezet beágyazott virtualizálás előtti beállítása előtt néhány dolgot figyelembe kell venni.

- Új Labor létrehozásakor válassza a **közepes (beágyazott virtualizálás)** vagy a **nagy (beágyazott virtualizációs)** méretek lehetőséget a virtuális gép méretének kiválasztásához. Ezek a virtuálisgép-méretek támogatják a beágyazott virtualizálás szolgáltatást.
- Válasszon egy olyan méretet, amely a gazdagép és az ügyfél virtuális gépei számára is jó teljesítményt nyújt.  Ne feledje, hogy a virtualizálás használatakor a kiválasztott méretnek megfelelőnek kell lennie a nem csak egy géphez, hanem a gazdagéphez és a párhuzamosan futtatandó ügyfélszámítógépekhez is.
- Az ügyfél virtuális gépei nem férhetnek hozzá az Azure-erőforrásokhoz, például az Azure-beli virtuális hálózat DNS-kiszolgálóihoz.
- A gazdagép virtuális gépén a telepítőnek engedélyeznie kell, hogy az ügyfélszámítógép internetkapcsolattal rendelkezzen.
- Az ügyfél virtuális gépei független gépekként vannak engedélyezve. A Microsoft-üzemeltetési rendszerek és termékek licencelésével kapcsolatos információkért tekintse meg a [Microsoft licencelését](https://www.microsoft.com/licensing/default) ismertető témakört. A sablon számítógépének beállítása előtt a használatban lévő egyéb szoftverek licencelési szerződéseit is megvizsgálhatja.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Beágyazott virtualizálás engedélyezése sablonalapú virtuális gépen

Ez a cikk azt feltételezi, hogy létrehozott egy labor-fiókot és egy labort.  Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md). A labor létrehozásával kapcsolatos további információkért tekintse [meg a tanterem Lab-oktatóanyagának beállítása](tutorial-setup-classroom-lab.md)című témakört.

>[!IMPORTANT]
>A tesztkörnyezet létrehozásakor válassza a **nagyméretű (beágyazott virtualizálás)** vagy **közepes (beágyazott virtualizálás** ) lehetőséget a virtuális gép méretének kiválasztásához.  A beágyazott virtualizálás más módon nem működik.  

A sablonhoz való kapcsolódáshoz lásd: [tantermi sablon létrehozása és kezelése](how-to-create-manage-template.md). 

Az ebben a szakaszban ismertetett lépések a Windows Server 2016 vagy a Windows Server 2019 rendszerhez készült beágyazott virtualizálás beállítására összpontosítanak. A Hyper-V használatával parancsfájlt kell beállítani a sablonhoz.  A következő lépések végigvezetik a [labor Services Hyper-V parancsfájljainak](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)használatán.

1. Ha az Internet Explorert használja, előfordulhat, hogy `https://github.com`t kell hozzáadnia a megbízható helyek listájához.
    1. Nyissa meg az Internet Explorert.
    1. Válassza a fogaskerék ikont, majd az **Internetbeállítások**lehetőséget.  
    1. Amikor megjelenik az **Internetbeállítások** párbeszédpanel, válassza a **Biztonság**, majd a **megbízható helyek**lehetőséget, kattintson a **helyek** gombra.
    1. Amikor megjelenik a **megbízható helyek** párbeszédpanel, adja hozzá `https://github.com` a megbízható webhelyek listához, majd kattintson a **Bezárás**gombra.

        ![Megbízható helyek](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1. Töltse le a git-tárház fájljait az alábbi lépésekben leírtak szerint.
    1. Lépjen [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Kattintson a **klón vagy a letöltés** gombra.
    1. Kattintson a **zip letöltése**elemre.
    1. ZIP-fájl kibontása

    >[!TIP]
    >A git-tárházat a [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)címen is klónozással végezheti el.

1. Indítsa el a **PowerShellt** **rendszergazdai** módban.
1. A PowerShell ablakban navigáljon a letöltött parancsfájlt tartalmazó mappához. Ha az adattár fájljainak legfelső mappájából navigál, a szkript a következő helyen található: `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Előfordulhat, hogy a parancsfájl sikeres futtatásához módosítania kell a végrehajtási házirendet. Futtassa az alábbi parancsot:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Futtassa a szkriptet:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Előfordulhat, hogy a parancsfájl újraindítását igényli. Kövesse a parancsfájl utasításait, és futtassa újra a parancsfájlt, amíg a **parancsfájl nem fejeződött** be a kimenetben.
1. Ne felejtse el alaphelyzetbe állítani a végrehajtási házirendet. Futtassa az alábbi parancsot:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Összegzés

Most a sablon-számítógép készen áll a Hyper-V virtuális gépek létrehozására. A Hyper-V virtuális gépek létrehozásával kapcsolatos útmutatásért tekintse meg [a virtuális gép létrehozása a Hyper-v-ben](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) című témakört. Továbbá tekintse meg a [Microsoft próbaverzióját](https://www.microsoft.com/evalcenter/) a rendelkezésre álló operációs rendszerek és szoftverek ellenőrzéséhez.  
