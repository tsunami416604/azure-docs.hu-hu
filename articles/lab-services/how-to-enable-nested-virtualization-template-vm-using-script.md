---
title: Beágyazott virtualizáció engedélyezése sablonbeli virtuális gépen Azure Lab Services (szkript) | Microsoft Docs
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
ms.openlocfilehash: 95b6407463cb0ab4eb436044cc68d8e35bd0b746
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895919"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Beágyazott virtualizálás engedélyezése Azure Lab Services sablonban lévő virtuális gépen parancsfájl használatával

A beágyazott virtualizálás lehetővé teszi, hogy több virtuális gépre kiterjedő környezetet hozzon létre a labor sablonjának virtuális gépén. A sablon közzétételével minden felhasználó számára elérhetővé válik a laborban egy virtuális gép, amelyben több virtuális gép van beállítva.  További információ a beágyazott virtualizálás és a Azure Lab Services használatáról: a [beágyazott virtualizálás engedélyezése sablonbeli virtuális gépen Azure Lab Servicesban](how-to-enable-nested-virtualization-template-vm.md).

A cikk lépései a Windows Server 2016, a Windows Server 2019 vagy a Windows 10 rendszerhez készült beágyazott virtualizálás beállítására összpontosítanak. A Hyper-V használatával parancsfájlt kell beállítani a sablonhoz.  A következő lépések végigvezetik a [labor Services Hyper-V parancsfájljainak](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)használatán.

>[!IMPORTANT]
>A tesztkörnyezet létrehozásakor válassza a **nagyméretű (beágyazott virtualizálás)** vagy **közepes (beágyazott virtualizálás** ) lehetőséget a virtuális gép méretének kiválasztásához.  A beágyazott virtualizálás más módon nem működik.  

## <a name="run-script"></a>Szkript futtatása

1. Ha az Internet Explorert használja, előfordulhat, hogy hozzá kell adnia `https://github.com` a megbízható helyek listájához.
    1. Nyissa meg az Internet Explorert.
    1. Válassza a fogaskerék ikont, majd az **Internetbeállítások**lehetőséget.  
    1. Amikor megjelenik az **Internetbeállítások** párbeszédpanel, válassza a **Biztonság**, majd a **megbízható helyek**lehetőséget, kattintson a **helyek** gombra.
    1. Amikor megjelenik a **megbízható helyek** párbeszédpanel, adja hozzá `https://github.com` a megbízható webhelyek listához, majd kattintson a **Bezárás**gombra.

        ![Megbízható helyek](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Töltse le a git-tárház fájljait az alábbi lépésekben leírtak szerint.
    1. Ugrás a következőre: [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) .
    1. Kattintson a **klón vagy a letöltés** gombra.
    1. Kattintson a **zip letöltése**elemre.
    1. ZIP-fájl kibontása

    >[!TIP]
    >A git-tárházat a következő helyen is klónozással is elvégezheti: [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Indítsa el a **PowerShellt** **rendszergazdai** módban.
1. A PowerShell ablakban navigáljon a letöltött parancsfájlt tartalmazó mappához. Ha az adattár fájljainak felső mappájából navigál, a parancsfájl a következő helyen található: `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
1. Előfordulhat, hogy a parancsfájl sikeres futtatásához módosítania kell a végrehajtási házirendet. Futtassa az alábbi parancsot:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Futtassa az alábbi parancsfájlt:

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

## <a name="next-steps"></a>További lépések

A következő lépések közösek a laborok beállításához.

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)