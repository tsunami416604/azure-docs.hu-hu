---
title: Kapcsolódás Azure Lab Services virtuális géphez Mac rendszerből | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503087"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Kapcsolódás virtuális géphez RDP használatával Mac gépen
Ez a szakasz azt mutatja be, hogy egy tanuló hogyan tud csatlakozni egy osztályterem Lab virtuális géphez Mac-ről RDP használatával.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Microsoft Távoli asztal telepítése Mac gépen
1. Nyissa meg az App Store-t a Mac számítógépen, és keressen rá **Microsoft távoli asztal**.

    ![Microsoft távoli asztal](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Telepítse a Microsoft Távoli asztal legújabb verzióját. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>A virtuális gép elérése Mac-kiszolgálóról RDP használatával
1. Nyissa meg a számítógépen letöltött **RDP** -fájlt, **Microsoft távoli asztal** telepítve van. Meg kell kezdenie a virtuális géphez való csatlakozást. 

    ![Kapcsolódás egy virtuális géphez](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Válassza a **Folytatás** lehetőséget, ha a következő figyelmeztetést kapja. 

    ![Tanúsítványra vonatkozó figyelmeztetés](../media/how-to-use-classroom-lab/certificate-error.png)
1. A virtuális gépet látnia kell. 

    > [!NOTE]
    > Az alábbi példa egy CentOS Linux rendszerű virtuális gép. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>További lépések
A Linux rendszerű virtuális gépek RDP használatával történő kapcsolódásával kapcsolatos információkért lásd: a [Távoli asztal használata Linux rendszerű virtuális gépekhez](how-to-use-remote-desktop-linux-student.md)


