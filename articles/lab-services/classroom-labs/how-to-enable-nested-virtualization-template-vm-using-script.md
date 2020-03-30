---
title: Beágyazott virtualizálás engedélyezése sablonvirtuális gépen az Azure Lab Services (Script) szolgáltatásban | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre sablon virtuális gépet több virtuális gép belsejében.  Más szóval engedélyezze a beágyazott virtualizálást egy sablon virtuális gépen az Azure Lab Servicesben.
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
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503035"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Beágyazott virtualizálás engedélyezése egy sablon virtuális gépen az Azure Lab Services-ben parancsfájl használatával

A beágyazott virtualizáció lehetővé teszi, hogy több virtuális gép környezetet hozzon létre a tesztkörnyezet sablonvirtuális gépén belül. A sablon közzététele biztosítja a labor minden felhasználó számára egy virtuális gép beállítása több virtuális gép belül.  A beágyazott virtualizálásról és az Azure Lab Servicesről a [Beágyazott virtualizálás engedélyezése sablonalapú virtuális gépen az Azure Lab Servicesben című témakörben](how-to-enable-nested-virtualization-template-vm.md)olvashat bővebben.

A cikkben ismertetett lépések a Windows Server 2016 vagy a Windows Server 2019 beágyazott virtualizálásának beállítására összpontosítanak. Egy parancsfájlt fog használni a sablongép Hyper-V-vel történő beállításához.  A következő lépések végigvezetik a [Lab Services Hyper-V parancsfájljainak használatán.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)

>[!IMPORTANT]
>Válassza ki a **Nagy (beágyazott virtualizálás)** vagy **közepes (beágyazott virtualizálás)** a virtuális gép mérete a labor létrehozásakor.  A beágyazott virtualizáció egyébként nem fog működni.  

## <a name="run-script"></a>Szkript futtatása

1. Ha Az Internet Explorer t használja, `https://github.com` előfordulhat, hogy fel kell adnia a megbízható helyek listájához.
    1. Nyissa meg az Internet Explorert.
    1. Válassza a fogaskerék ikont, majd **az Internet beállítások lehetőséget**.  
    1. Amikor megjelenik az **Internetbeállítások** párbeszédpanel, válassza a **Biztonság**lehetőséget, válassza **a Megbízható helyek**lehetőséget, kattintson a **Helyek** gombra.
    1. Amikor megjelenik a Megbízható `https://github.com` **helyek** párbeszédpanel, vegye fel a megbízható webhelyek listájára, és válassza a **Bezárás**gombot.

        ![Megbízható helyek](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Töltse le a Git-tárház fájljait a következő lépésekben ismertetett módon.
    1. Nyissa [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)meg a.
    1. Kattintson a **Klónozás vagy a Letöltés** gombra.
    1. Kattintson **a ZIP letöltése gombra.**
    1. Zip-fájl kibontása

    >[!TIP]
    >A Git-tárházat klónozhatja is a helyen. [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)

1. Indítsa el a **PowerShellt** **rendszergazdai** módban.
1. A PowerShell ablakban keresse meg a mappát a letöltött parancsfájlt. Ha a tárházfájlok felső mappájából navigál, a parancsfájl `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`a helyen található.
1. Előfordulhat, hogy módosítania kell a végrehajtási házirendet a parancsfájl sikeres futtatásához. Futtassa az alábbi parancsot:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Futtassa az alábbi parancsfájlt:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Előfordulhat, hogy a parancsfájl újraindítását igényli. Kövesse a parancsfájl utasításait, és futtassa újra a parancsfájlt, amíg a **parancsfájl be nem fejeződik** a kimenetben.
1. Ne felejtsd el visszaállítani a végrehajtási szabályzatot. Futtassa az alábbi parancsot:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Összegzés

Most a sablongép készen áll a Hyper-V virtuális gépek létrehozására. A Hyper-V virtuális gépek létrehozásáról a Virtuális gép létrehozása [a Hyper-V-ben](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) című témakörben talál útmutatást. Az elérhető operációs rendszerek és szoftverek megtekintéséről a Microsoft Evaluation Center című [témakörben](https://www.microsoft.com/evalcenter/) is részt veheti.  

## <a name="next-steps"></a>További lépések

A következő lépések gyakoriak a tesztkörnyezet beállításában.

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail regisztrációs linkek diákok](how-to-configure-student-usage.md#send-invitations-to-users)