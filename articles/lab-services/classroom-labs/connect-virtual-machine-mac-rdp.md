---
title: Csatlakozás egy Azure Lab Services virtuális géphez Macről | Microsoft dokumentumok
description: Ez a cikk
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503087"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Csatlakozás virtuális géphez asztali gép használatával Macen
Ez a szakasz bemutatja, hogyan csatlakozhat egy tanuló egy tantermi laborvirtuális géphez egy Macről az RDP használatával.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>A Microsoft Remote Desktop telepítése Macre
1. Nyissa meg az App Store-t a Macen, és keressen a **Microsoft Remote Desktop kifejezésre.**

    ![Microsoft távoli asztal](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Telepítse a Microsoft Remote Desktop legújabb verzióját. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>A virtuális gép elérése a Macről az RDP használatával
1. Nyissa meg a számítógépre letöltött **RDP-fájlt,** amelyen telepítve van a **Microsoft Remote Desktop.** Meg kell kezdeni a virtuális géphez való csatlakozást. 

    ![Kapcsolódás egy virtuális géphez](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Válassza a **Folytatás** lehetőséget, ha a következő figyelmeztetés jelenik meg. 

    ![Tanúsítványfigyelmeztetés](../media/how-to-use-classroom-lab/certificate-error.png)
1. Látnia kell a virtuális gép. 

    > [!NOTE]
    > A következő példa egy CentOS Linux virtuális gép. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>További lépések
Ha meg szeretné tudni, hogyan csatlakozhat Linux-virtuális gépekhez rdp használatával, olvassa el [a Távoli asztal használata Linuxos virtuális gépekhez című témakört.](how-to-use-remote-desktop-linux-student.md)


