---
title: Távoli asztal engedélyezése Linuxhoz az Azure Lab Servicesben | Microsoft dokumentumok
description: Ismerje meg, hogyan engedélyezheti a távoli asztal linuxos virtuális gépekhez egy laborban az Azure Lab Servicesben.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270861"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Távoli asztal engedélyezése Linuxos virtuális gépekhez egy laborban az Azure Lab Services-ben
Ez a cikk a következő feladatok elvégzését mutatja be:

- Távoli asztal engedélyezése Linux os virtuális géphez
- Hogyan tud a tanár csatlakozni a sablon virtuális géphez a Távoli asztali kapcsolaton (RDP) keresztül?

## <a name="enable-remote-desktop-for-linux-vm"></a>Távoli asztal engedélyezése Linux os virtuális géphez
A labor létrehozása során a tanárok engedélyezhetik a **távoli asztali kapcsolatot** **linuxos** lemezképekhez. A **Távoli asztali kapcsolat engedélyezése** beállítás akkor jelenik meg, ha linuxos lemezkép van kiválasztva a sablonhoz. Ha ez a beállítás engedélyezve van, a tanárok csatlakozhatnak a sablon virtuális gép hez és a diákok virtuális gépeihez az RDP (Távoli asztal) segítségével. 

![Távoli asztali kapcsolat engedélyezése Linux-lemezképhez](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

A **Távoli asztali kapcsolat engedélyezése** üzenetmezőben válassza a **Folytatás távoli asztallal**lehetőséget. 

![Távoli asztali kapcsolat engedélyezése Linux-lemezképhez](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> A **távoli asztali kapcsolat** engedélyezése csak linuxos gépeken nyitja meg az **RDP-portot.** Ha az RDP már telepítve van és konfigurálva van a virtuális gép lemezképén (például Ubuntu Data Science Virtuálisgép-lemezkép), ön/diákok rdp-n keresztül csatlakozhatnak a virtuális gépekhez anélkül, hogy további lépéseket követne.
> 
> Ha a virtuális gép lemezképe nem rendelkezik RDP telepítve és konfigurálva, először kell csatlakoznia a Linux-géphez az SSH használatával, és rdp- és GRAFIKUS-csomagokat kell telepítenie, hogy ön/diákok később rdp használatával csatlakozhassanak a Linux-géphez. További információt a [Távoli asztal telepítése és konfigurálása linuxos virtuális géphez való csatlakozáshoz az Azure-ban című témakörben talál.](../../virtual-machines/linux/use-remote-desktop.md) Ezután közzéteszi a lemezképet, hogy a diákok RDP-ben a tanuló Linux virtuális gépek. 

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Jelenleg a távoli asztali kapcsolat a következő operációs rendszereken támogatott:

- openSUSE Ugrás 42.3
- CentOS-alapú 7.5
- Debian 9 "Nyújtás"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Csatlakozás a virtuálisgép-sablonhoz 
A tanároknak először az SSH használatával kell csatlakozniuk a sablonvirtuális géphez, és rdp- és grafikus felhasználói felület-csomagokat kell telepíteniük rá. Ezután a tanárok rdp segítségével csatlakozhatnak a sablon virtuális gép: 

1. Ha az eszköztáron a **Sablon testreszabása** látható, jelölje ki. Ezután válassza a **Folytatás** gombot a **Sablon testreszabása** párbeszédpanelen. Ez a művelet elindítja a sablon virtuális gép.  

    ![Sablon testreszabása](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. A sablon virtuális gép indítása után kiválaszthatja a **Csatlakozás sablont,** majd az **SSH-n keresztül iconnect** lehetőséget az eszköztáron. 

    ![Csatlakozás sablonhoz RDP-n keresztül a labor létrehozása után](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. A Csatlakozás a **virtuális géphez** párbeszédpanel a következő. A szövegdoboz melletti **Másolás** gombra kattintva másolja azt a vágólapra. Mentse az SSH kapcsolati karakterláncot. Használja ezt a kapcsolati karakterláncot egy SSH terminálról (például [a Putty)-ról](https://www.putty.org/)a virtuális géphez való csatlakozáshoz.
 
    ![SSH kapcsolati karakterlánc](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. RdP- és grafikus felhasználói felület-csomagok at telepíthet, hogy ön/diákok később rdp használatával kapcsolódhassanak a Linux-géphez. További információt a [Távoli asztal telepítése és konfigurálása linuxos virtuális géphez való csatlakozáshoz az Azure-ban című témakörben talál.](../../virtual-machines/linux/use-remote-desktop.md) Ezután közzéteszi a lemezképet, hogy a diákok RDP-ben a tanuló Linux virtuális gépek.
5. A csomagok telepítése után használhatja a **Csatlakozás sablont** az eszköztáron, majd válassza a **Csatlakozás RDP-n keresztül** lehetőséget a virtuális gép rdp-n keresztüli sablonhoz való csatlakozáshoz. Mentse az RDP-fájlt, és használja a sablon virtuális géphez való csatlakozáshoz rdp-n keresztül. 

## <a name="next-steps"></a>További lépések
Miután egy oktató engedélyezte a távoli asztali kapcsolat funkciót, a diákok rdp/SSH-n keresztül csatlakozhatnak a virtuális gépeikhez. További információ: [Távoli asztali számítógép használata Linuxos virtuális gépekhez egy tantermi laborban.](how-to-use-remote-desktop-linux-student.md) 